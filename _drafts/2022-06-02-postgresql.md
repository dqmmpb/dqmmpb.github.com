---
layout: post
title:  "postgresql"
date:   2022-06-02 09:00:00 +0800
categories: jekyll update
---

## postgresql

### 安装

请参考官网安装[PostgreSQL官网](https://www.postgresql.org/)

### 配置

- 支持ip访问
> 修改配置文件`pg_hba.conf`
> > ```text
> > host    all             all             0.0.0.0/0            scram-sha-256
> > ```
- 中文message乱码
> 修改`postgresql.conf`文件，蒋如下4项改为C，确保使用英文模式，避免系统默认检测为中文
> > ```text
> > # These settings are initialized by initdb, but they can be changed.
> > lc_messages = 'C'			# locale for system error message
> > # strings
> > lc_monetary = 'C'			# locale for monetary formatting
> > lc_numeric = 'C'			# locale for number formatting
> > lc_time = 'C'				# locale for time formatting
> > ```


### postgresql判断database是否存在

postgresql没有提供database的exists判断，使用dblink扩展

```postgresql
CREATE EXTENSION IF NOT EXISTS dblink;

-- if postgresql >= 9.0
DO $$
BEGIN
    PERFORM dblink_exec('dbname=postgres user=postgres password=postgres', 'CREATE DATABASE pgtest');
    EXCEPTION WHEN duplicate_database THEN RAISE NOTICE '%, skipping', SQLERRM USING ERRCODE = SQLSTATE;
    PERFORM dblink_exec('dbname=pgtest user=postgres password=postgres', 'DROP SCHEMA IF EXISTS test CASCADE');
END $$;


-- -- if postgresql < 9.0
-- CREATE function createDatabase() returns void as $$
-- BEGIN
--     PERFORM dblink_exec('dbname=postgres user=postgres password=postgres', 'CREATE DATABASE pgtest');
--     EXCEPTION WHEN duplicate_database THEN RAISE NOTICE '%, skipping', SQLERRM USING ERRCODE = SQLSTATE;
--     PERFORM dblink_exec('dbname=pgtest user=postgres password=postgres', 'DROP SCHEMA IF EXISTS test CASCADE');
-- END $$ LANGUAGE plpgsql;
-- SELECT createDatabase();
-- DROP FUNCTION createDatabase();
```

常用语法， 参考官网[Documentation](https://www.postgresql.org/docs/)
```postgresql
DROP DATABASE pgtest;
CREATE DATABASE pgtest;
CREATE SCHEMA IF NOT EXISTS test;
DROP SCHEMA IF EXISTS test CASCADE;
```

### 参考资料

- [PostgreSQL官网](https://www.postgresql.org/)
- [postgresql中文](http://www.postgres.cn/index.php/v2/home)
- [PostgreSQL 12.2 手册](http://www.postgres.cn/docs/12/)
