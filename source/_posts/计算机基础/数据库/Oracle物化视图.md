---
title: Oracle物化视图
categories: 计算机基础
date: 2024-06-05 15:22:33
tags: 
- oracle
- 物化视图
---

## 概述

要想创建物化视图，至少具有`CREATE MATERIALIZED VIEW`权限

```sql
-- 权限查询，非 DBA 用户，则使用 user_sys_privs 即可
SELECT * FROM dba_sys_privs t WHERE t.privilege LIKE '%MATERIALIZED%';
grant create materialized view to scott; -- 授权
revoke create materialized view from scott; -- 回收授权
```

## 创建 

创建物化视图有很多选项，知道常用的即可（比如build、refresh、on等）

```sql
create materialized view 物化视图名        -- 1. 创建物化视图
build [immediate | deferred] 			  -- 2. 创建方式，默认 immediate
refresh [force | fast | complete | never] -- 3. 物化视图刷新方式，默认 force
on [commit | demand] 					  -- 4. 刷新触发方式
start with 开始时间						  -- 5. 设置开始时间
next 间隔时间				              -- 6. 设置间隔时间
with [primary key | rowid]                -- 7. 类型，默认 primary key
[enable | disable] query rewrite          -- 8. 是否启用查询重写
as	                                      -- 9. 关键字
查询语句;                                  -- 10. select 语句

```

选项注释：

```sql
1. "创建 build" 的方式
	(1) 'immediate'：立即生效，默认。
	(2) 'deferred' : 延迟至第一次 refresh 时才生效
2. "刷新 refresh" 的方式
	(1) force	：默认。如果可以 '快速刷新' 就 '快速刷新'，否则执行 '完全刷新'
	(2) fast	：'快速刷新'。只刷新 '增量' 部分（前提：创建 '物化日志'）
	(3) complete: '完全刷新'。刷新时更新全部数据，包括视图中已经生成的原有数据
	(4) never	: 从不刷新	
3. "触发" (请注意，on demand 中，才需要设置 '开始时间' 和 '间隔时间') -- 冲突
	(1) on commit：基表有 commit 动作时，刷新刷图（"不能跨库执行"）
	(2) on demand：在需要时刷新
			       [1] 根据后面设定的 '开始时间' 和 '结束时间' 进行刷新
			       [2] 手动调用 dbms_mview 包中的过程进行刷新			       
4. 基于基表的 primary key 或 rowid 创建
	(1) 如果是基于 rowid，则不能对基表执行 '分组函数'、'多表连接' 等需要把
	    多个 rowid 合成一行的操作（理由很简单：到底以哪个 rowid 为准呢？）
5. enable query rewrite 启用查询重写（请注意， '开始时间' 和 '间隔时间' 不支持）-- 冲突
	(1) 不支持的理由也很简单。
		所谓的 '重写'，就是讲对基表的查询定位到物化视图上，
		而 '开始时间' 和 '间隔时间' 会造成物化视图上部分数据延迟，所以，不能重写
	(2) 参数: query_rewrite_enabled (可通过 v$parameter 视图查询)
```

### 示例

创建一个每三分钟刷新一次，采用默认刷新方式的物化视图

#### 创建物化视图

根据基表项目表创建物化视图，每三分钟刷新一次

```sql
CREATE MATERIALIZED VIEW mvw_prj_info 
BUILD IMMEDIATE
REFRESH FORCE
ON DEMAND
START WITH SYSDATE
NEXT SYSDATE + 3/1440
AS
SELECT prj.code,    
       prj.name
  FROM project prj;
```

#### 对project表进行修改

新增、删除、修改project表的内容，等待3分钟左右后，观察物化视图前后数据差异

发现物化视图中的内容与基表数据保持一致

## 删改查

### 查询

```sql
select * from mvw_prj_info
```

### 修改

修改只能修改物化视图的刷新规则，不能直接对数据进行修改

```sql
alter materialized view 物化视图名
refresh [force | fast | complete | never]
on [commit | demand]
start with 开始时间
next 间隔时间
```

### 删除

```sql
drop materialized view 物化视图名;
```

## 拓展

### 手动更新

```sql
begin
	DBMS_MVIEW.REFRESH (
	list => 'pccmvaccountinginfo',	 -- 要刷新的视图，支持传多个
	Method =>'C',					-- 全量刷新
	refresh_after_errors => True);
end;
```

### 创建物化视图日志

待完善，暂时未使用

### Oracle时间计算

在oracle中，`sysdate`单位是天

```sql
select sysdate + 3/1440 from dual   -- 当前时间后三分钟
```

#### A类型

代表A天

比如`sysdate + 1`：当前时间一天

#### A/B类型

代表n小时，A表示天数，B表示小时，`n = A×24/B`

比如`sysdate + 3/1440`：当前时间后三分钟

#### A/B/C类型

表示m分钟，A表示天数，B表示小时，C表示分钟，`m = A × 24/B × 60/C`

如`sysdate + 1/24/60`代表当前时间后一分钟