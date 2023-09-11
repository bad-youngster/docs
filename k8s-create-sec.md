### k8s 创建用户
```yml
cat << EOF > test-create-token.yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: kube-system

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: test-admin
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: test-admin-crb
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: test-admin
subjects:
- kind: ServiceAccount
  name: test-admin
  namespace: kube-system

---
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  annotations:
    kubernetes.io/service-account.name: test-admin
  name: test-admin-token
  namespace: kube-system
EOF
```
查询结果
```shell
kubectl apply -f kuboard-create-token.yaml 
kubectl -n kuboard get secret $(kubectl -n kuboard get secret kuboard-admin-token | grep kuboard-admin-token | awk '{print $1}') -o go-template='{{.data.token}}' | base64 -d
```