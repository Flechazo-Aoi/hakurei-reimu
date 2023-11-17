---
title: 平台service包方法
categories: 开发平台 
date: 2023-10-26 10:22:33
tags: 
  - service
  - 后端
---

# TapController

## insert

与TapService内方法一样，也有插入前、插入后的处理逻辑。具体逻辑如下：

```java
@Override
@PostMapping(value = "/insert")
public ResponseResult<Object> insert(@RequestBody(required = false) Map<String, Object> paraMap) {
    try {
       Map<String, Object> dataMap = paraMap;
       // 插入前执行逻辑
       beforeInsert(dataMap, paraMap);
       Class<T> entityClass = this.entityClass;
       TapEntity entity = entityClass.newInstance();
       entity.convertFromMap(dataMap);
       entity = this.service.insert(entity);
       dataMap = entity.convertToMap();
       // 插入后执行逻辑
       afterInsert(dataMap, paraMap);
       return ResponseResult.ok(dataMap);
    } catch (Exception ex) {
       return handleControllerException(ex);
    }
}
```

需要注意，因为调用service层方法时会改变map的值，所以将`paraMap`复制了一份为`dataMap`，处理的都是`dataMap`

入参为前端提交的表单，新增事件调用此接口。

## update

更新操作与insert操作一致，区别在于不需要转化入参。

```java
@Override
@PutMapping(value = "/update")
public ResponseResult<Object> update(@RequestBody(required = false) Map<String, Object> paraMap) {
    try {
       Map<String, Object> dataMap = paraMap;
       beforeUpdate(dataMap, paraMap);
       this.service.update(dataMap);
       afterUpdate(dataMap, paraMap);
       return ResponseResult.ok("修改成功");
    } catch (Exception ex) {
       return handleControllerException(ex);
    }
}
```

## delect

删除操作接口，只有删除之后处理拓展方法，且是按照id批量删除。

```java
@Override
@DeleteMapping(value = "/delete")
public ResponseResult<Object> delete(@RequestBody(required = false) Map<String, Object> paraMap) {
    try {
       paraMap = BaseUtil.decodeSecureMap(paraMap);
       String ids = ConvertUtil.convertToString(paraMap.get("ids"));
       this.service.deleteByWhere(" id in (" + ids + ")");
       afterDelete(paraMap);
       return ResponseResult.ok("删除成功");
    } catch (Exception ex) {
       return handleControllerException(ex);
    }
}
```

## list

列表查询方法，用于界面上的列表查询，逻辑是拼接查询条件sql(`spellListSql`方法)，调用service方法根据查询条件查数量、分页查询

```java
@Override
@GetMapping(value = "/list")
public ResponseResult<Object> list(@RequestParam(required = false) Map<String, Object> paraMap) {
    try {
       paraMap = BaseUtil.decodeSecureMap(paraMap);
       Query query = new Query(paraMap);
       String sqlWhere = this.spellListSql(paraMap);
       int totalCount = this.service.getCount(sqlWhere);
       List<Map<String, Object>> tempList = null;
       if (query.getPageSize() > 0) {
          tempList = this.service.queryMapForPage(sqlWhere, query.getCurrentPage(), query.getPageSize(),
                query.getSidx(), query.getSord());
       } else
          tempList = this.service.queryMapByWhere(sqlWhere);
       // 需要转化字段显示值则调用this.service.setDispFields(tempList);
       Page page = new Page(tempList, totalCount, query.getPageSize(), query.getCurrentPage());
       return ResponseResult.ok(page);
    } catch (Exception ex) {
       return handleControllerException(ex);
    }
}
```

拼接sql语句方法长这样(前端传的参数key都是以`qry_`开头的)：

```java
protected String spellListSql(Map<String, Object> paraMap) {
    StringBufferProxy sql = new StringBufferProxy();
    sql.appendSingle(" 1=1 ");
    if (paraMap.containsKey("qry_name")) {
       String value = paraMap.get("qry_name").toString();
       if (!StringUtil.isEmpty(value))
          sql.appendSingle(" and name like '%{0}%' ", value);
    }
}
```

## refList

其他界面引用本类界面时调用的方法（比如消费组界面查看组内消费者信息时），与列表查询方法逻辑一样。

```java
@Override
@GetMapping(value = "/refList")
public ResponseResult<Object> refList(@RequestParam(required = false) Map<String, Object> paraMap) {
	try {
		paraMap = BaseUtil.decodeSecureMap(paraMap);
		Query query = new Query(paraMap);
		String sqlWhere = this.spellRefListSql(paraMap);
		int totalCount = this.service.getCount(sqlWhere);
		List<Map<String, Object>> tempList = null;
		if (query.getPageSize() > 0) {
			tempList = this.service.queryMapForPage(sqlWhere, query.getCurrentPage(), query.getPageSize(),
					query.getSidx(), query.getSord());
		} else
			tempList = this.service.queryMapByWhere(sqlWhere);
		// 需要转化字段显示值则调用 this.service.setDispFields(tempList);
		Page page = new Page(tempList, totalCount, query.getPageSize(), query.getCurrentPage());
		return ResponseResult.ok(page);
	} catch (Exception ex) {
		return handleControllerException(ex);
	}
}
```

拼接查询条件的方法长这样：

```java
protected String spellRefListSql(Map<String, Object> paraMap) {
    StringBufferProxy sql = new StringBufferProxy();
    sql.appendSingle(" 1 = 1 ");
    if (paraMap.containsKey(MbcsSystemConst.MsgPushTaskListParm.QRY_MSG_RECEIVE_ID)) {
        String value = paraMap.get(MbcsSystemConst.MsgPushTaskListParm.QRY_MSG_RECEIVE_ID).toString();
        if (!StringUtil.isEmpty(value)) {
            sql.appendSingle(" and {0} = '{1}' ", TmbMsgPushTaskEntity.FieldMsgreceiveid, value);
        }
    }
}
```

## load

查看表中某一项的详细内容，前端对应着`openViewDialog`事件和`openEditDialog`事件。

```java
@Override
@GetMapping(value = "/load")
public ResponseResult<Object> load(@RequestParam(required = false) Map<String, Object> paraMap) {
	try {
		paraMap = BaseUtil.decodeSecureMap(paraMap);
		Map<String, Object> dataMap = this.service.queryMapById(paraMap.get("id").toString());
		afterLoad(dataMap);
		return ResponseResult.ok(dataMap);
	} catch (Exception ex) {
		return handleControllerException(ex);
	}
}
```

方法逻辑是根据前端row.id查询对应记录详细信息。

有一个afterLoad拓展方法，可以在查询后对查询结果做处理（比如设置字段显示值）

## dataCodeList

获取数据编码，将其存储在前端dataCodeList中，这样前端列表选择就可以映射值。大概长这样：

```java
@Override
@GetMapping(value = "/dataCodeList")
public ResponseResult<Object> dataCodeList(@RequestParam(required = false) Map<String, Object> paraMap) {
    super.dataCodeList(paraMap);
    Map<String, Object> codeMap = new HashMap<>(1);
    List<Map<String, Object>> consumerList = tmbMsgSubscribeService.getAllSubsSysCodeOfUser(this.getUserBean().getUser().getCode());
    codeMap.put("consumerList", consumerList);
    List<Map<String, Object>> pushstsList = datacodeCache.getCodeListMap("Pushsts", "", CboItemDetailsEntity.FieldSortcode);
    codeMap.put("pushstsList", pushstsList);
    return ResponseResult.ok(codeMap);
}
```

两种方式：

- 直接获取定义好的编码：`datacodeCache.getCodeListMap`
- 自定义方法：如`getAllSubsSysCodeOfUser`

前者要求，已经配置好编码列表(在`CboItems`和`CboItemDetails`表中)，后者可以自己定义。