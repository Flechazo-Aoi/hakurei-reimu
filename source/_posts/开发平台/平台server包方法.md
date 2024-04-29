---
title: 平台server包方法
categories: 开发平台 
date: 2023-10-23 10:22:33
tags: 
  - server
  - 后端
---

# BasicService

## setDispFields

设置字段显示值。效果为把某个字段的code转化成name、把状态的-1/1转化成无效/有效等等。具体逻辑大致如下所示：

```java
public void setDispFields(Map<String, Object> mapRow, DatacodeCache datacodeCache) {
    String temp;
    if (mapRow.get(TmbMsgPushTaskEntity.FieldRegister) != null) {
        temp = ConvertUtil.convertToString(mapRow.get(TmbMsgPushTaskEntity.FieldRegister));
        mapRow.put("disp" + TmbMsgPushTaskEntity.FieldRegister, this.getNameFromSysCode(temp));
    }
    if (mapRow.get(TmbMsgPushTaskEntity.FieldPushsts) != null) {
        temp = ConvertUtil.convertToString(mapRow.get(TmbMsgPushTaskEntity.FieldPushsts));
        mapRow.put("disp" + TmbMsgPushTaskEntity.FieldPushsts, datacodeCache.getValueDynamic("Pushsts", temp, CboItemDetailsEntity.FieldCode, CboItemDetailsEntity.FieldName));
    }
}
```

可以看到在此过程中，如果该字段的的值对应关系已经存储在了`CboItemDetails`表中(还有CboItems表)，那么直接调用`datacodeCache.getValueDynamic`方法即可，反之需要自己重写字段转化具体实现方法(如`getNameFromSysCode`)

## update

有`protected void updateBefore(map)`、`protected void updateAfter(map)`和`public int update(map)`三个方法。三个方法在同一事务中。

```java
@Override
@Transactional(rollbackFor = Exception.class)
	public int update(Map<String, Object> map) {
	updateBefore(map);
	int i = dao.update(map);
	updateAfter(map);
	return i;
}
```

可以重写这三个方法来自定义更新前、更新、更新后的具体操作，这三个方法的入参是同一个map。

只需要注意重新更新操作时，需要调用父类方法或者手动调用before方法和after方法，否则不会执行对应逻辑。

- `updateBatch`、`updateBatchMap`方法也有更新前、更新后的处理方法；
- `updateEntity`是将实体类转化成map在调用update方法；
- 领域更新`updateField`和条件更新`updateByWhere`则没有更新前、更新后的处理逻辑。

## delete

有一个条件删除方法`deleteByWhere`方法，与更新方法类似，也有删除前和删除后的处理逻辑

```java
@Override
@Transactional(rollbackFor = Exception.class)
public int deleteByWhere(String whereSql) {
	int resultCount = 0;
	deleteBefore(whereSql);
	resultCount = dao.deleteByWhere(whereSql);
	deleteAfter(whereSql);
	return resultCount;
}
```

- deleteById：根据id删除
- deleteByIds：根据ids批量删除

## insert

同update方法，有插入前和插入后的处理逻辑

```java
@Override
@Transactional(rollbackFor = Exception.class)
public T insert(BasicEntity entity) {
    beforeInsert(entity);
    entity = dao.insert(entity);
    afterInsert(entity);
    return (T) entity;
}
```

批量插入`insertBatch`方法类似。

需要注意的是，在底层`TapMyBatisDao`的`beforeInsert`的实现中，默认将enabled置为1，导致新增对象的enabled总为1.

## query

有以下几个方法：

- queryById(String id)：根据id查询
- queryByIds(String ids)：根据ids批量查询
- queryByWhere(String whereSql)：条件查询
- queryForPage(Map<String, Object> paraMap)：分页查询
- queryObjectByWhere(String whereSql)：条件查询单个对象(`list.get(0)`)
- queryMapById(String id)：根据id查询，返回值由entity变为map
- queryMapByIds(String ids)：根据ids批量查询，返回值由entity变为map
- queryMapByWhere(String whereSql)：条件查询，返回值由entity变为map
- queryMapForPage(Map<String, Object> paraMap)：分页查询，返回值由entity变为map
- queryMapFieldsByWhere(String whereSql, List<String> filedList)：条件领域查询

## 拓展方法

以上的CRUD方法还大都拥有其对应的拓展方法

- 根据xml中的selectName执行对应方法：`update(Map<String, Object> paraMap, String selectName)`
- 直接执行sql语句的方法：`updateExecute(String updateSql)`

# TapMyBatisDao

## insert

平台定义了唯一性校验功能，配置后，在插入操作前，会进行唯一性校验

```java
@Override
protected void beforeInsert(BasicEntity entity) {
	try {
		super.beforeInsert(entity);
		TapEntity tapEntity = (TapEntity) entity;
        // 平台默认的会把新创建的实体enable置成1
		tapEntity.setEnabled(1);
        // 如果能得到用户信息，就赋值
		if (this.getSessionUserBean() != null) {
			tapEntity.setCreateuser(this.getSessionUserBean().getUser().getId());
			tapEntity.setModifieduser(this.getSessionUserBean().getUser().getId());
			tapEntity.setCreateorgid(this.getSessionUserBean().getOrg().getId());
		}
		// 唯一性校验
		ICboUniqueControlDao cboUniqueControlDao = (ICboUniqueControlDao) SpringUtil.getBean("cboUniqueControlDao");
		String tableName = ((TapEntity) this.getEntityClass().newInstance()).getTableName();
		List<CboUniqueControlEntity> listUniqueControl = cboUniqueControlDao.queryByWhere(" controltable = '" + tableName + "' ");
		this.checkFieldUnique(entity.convertToMap(), listUniqueControl);
		// 数据操作日志记录
		addTableLog(CboTableLogOperateTypeEnum.Insert, entity.convertToMap());
	} catch (Exception ex) {
		throw new DaoException(ex.getMessage());
	}
}
```

```java
private void checkFieldUnique(Map<String, Object> map, List<CboUniqueControlEntity> listUniqueControl) {
    // 没有校验，直接返回
	if (listUniqueControl == null || listUniqueControl.size() <= 0) {
		return;
	}
    // 有校验，就直接遍历
	for (CboUniqueControlEntity uniqueControl : listUniqueControl) {
        // 这里就要求配置时，用;隔开要校验的字段
		String[] arraySplitfieldName = uniqueControl.getControlfield().split(";");
		boolean isNotExists = false;
        // 这里是判断有没有这个字段，如果有，就break
		for (String splitfieldName : arraySplitfieldName) {
			if (!map.keySet().contains(splitfieldName)) {
				isNotExists = true;
				break;
			}
		}
		if (isNotExists) {
			continue;
		}
        // 拼接条件就是，所有字段都相同，但是id不同，查出来数量大于1就抛异常
		StringBufferProxy sql = new StringBufferProxy();
		sql.appendSingle(" 1=1 ");
		for (String splitfieldName : arraySplitfieldName) {
			sql.appendSingle(" and {0}='{1}' ", splitfieldName, ConvertUtil.convertToString(map.get(splitfieldName)));
		}
		sql.appendSingle(" and id != '{0}' ", ConvertUtil.convertToString(map.get("id")));
		if (!StringUtil.isEmpty(uniqueControl.getCondition())) {
			sql.appendSingle(uniqueControl.getCondition());
		}
		int count = this.getCount(sql.toString());
		if (count > 0) {
			throw new DaoException(uniqueControl.getTipmsg());
		}
	}
}
```

在这里进行配置，可以指定数据库和字段名

![1714359849955](https://hanser373.oss-cn-beijing.aliyuncs.com/img/202404291104612.png)
