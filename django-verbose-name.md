### 遇到django models创建时使用verbose_name时，数据库中没有comment
修改/opt/microMonitor/lib/python3.10/site-packages/django/db/backends/base/schema.py  
在 'table_sql'函数最后的return上方添加  
```python
        if model._meta.db_tablespace:
            tablespace_sql = self.connection.ops.tablespace_sql(
                model._meta.db_tablespace)
            if tablespace_sql:
                sql += ' ' + tablespace_sql
        # new add model._meta.verbose_name mysql table comments
        if self.connection.client.executable_name == 'mysql' and model._meta.verbose_name:
            sql += " COMMENT '%s'" % model._meta.verbose_name

        return sql, params

```  
在'column_sql'函数最后的return上方添加
```python
        # Optionally add the tablespace if it's an implicitly indexed column
        tablespace = field.db_tablespace or model._meta.db_tablespace
        if tablespace and self.connection.features.supports_tablespaces and field.unique:
            sql += " %s" % self.connection.ops.tablespace_sql(tablespace,
                                                              inline=True)
        # Return the sql
        
        # new add field.verbose_name mysql table comments
        if self.connection.client.executable_name == 'mysql' and field.verbose_name:
            sql += " COMMENT '%s'" % field.verbose_name

        return sql, params
```