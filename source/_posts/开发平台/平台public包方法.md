---
title: 平台public包方法
categories: 开发平台 
date: 2023-11-06 10:22:33
tags: 
  - public	
---

## 从配置文件取值：Config

Config类用于获取`config.properties`和该文件import的配置文件的值，构造方法如下所示：

```java
private Config() {
    // 加载config.properties配置文件
	this.loadProperties("/config.properties");
	String importproperties = ConvertUtil.convertToString(properties.getProperty("import.propertiesfile"));
    // 加载config配置文件引入的配置文件
	if (!importproperties.isEmpty()) {
		String[] arrays = importproperties.split(";");
		for (String temp : arrays) {
			this.loadProperties(temp);
		}
	}
}
```

其中加载配置文件方法如下所示：

```java
private void loadProperties(String configpath) {
    InputStream inputStream = null;
    try {
       inputStream = getClass().getResourceAsStream(new String(configpath));
       properties.load(inputStream);
    } catch (Exception exception) {
       System.out.println("Can't read the properties file. ");
    } finally {
       try {
          if (inputStream != null) {
             inputStream.close();
          }
       } catch (Exception e) {
          throw new BusinessException(e);
       }
    }
}
```

我们可以通过`getInstance()`方法来实例化Config对象，进而调用`getValue/getIntValue`方法来获取配置文件的值。

## 取配置参数(从数据库)：ParameterCache

数据库中有一张表CBOSYSPARAM，在其中可以配置一些系统参数值（系统层面上的），比如最大重试次数、最大重推数量等等。通过`ParameterCache.getValue`来获取，其逻辑如下：

```java
public static String getValue(String code, String defaultValue) {
	try {
		String value = cache.get(code, new Callable<String>() {
			@Override
			public String call() throws Exception {
              	  // 从数据库中的sysParameter表查询 
				ICboSysParamService cboSysParamService = (ICboSysParamService) SpringUtil.getBean("cboSysParamService");
				StringBufferProxy sql = new StringBufferProxy();
				sql.appendSingle(" code = '{0}' ", code);
				List<CboSysParamEntity> listEntity = cboSysParamService.queryByWhere(sql.toString());
				if (listEntity.size() > 0) {
					return ConvertUtil.convertToString(listEntity.get(0).getParamvalue());
				} else {
					return defaultValue;
				}
			}
		});
		return value;
	} catch (Exception e) {
		throw new BusinessException(e);
	}
}
```

## 从数据库取值：DatacodeCache

数据库中有两张表CBOITEMS和CBOITEMDETAILS，对应一些编码值，ITEMS表存储编码名(消费者状态、是否有效等)，DETAILS表存储编码对应的代码值列表(-1代表无效，1代表有效)，

### 转换显示值

```java
public String getValueDynamic(String itemCode, String fieldName, String value, String inCol, String outCol) {
	// 加载item明细，如果提前没有调用loadItemDetailCache进行缓存，那么还是获取原源码，
    // 如果提前缓存了，那么使用组件key进行获取缓存
	List<Map<String, Object>> mapList = null;
	if (this.itemDetailCache.containsKey(itemCode)) {
		this.itemDetailCache.put(itemCode, mapList);
	} else {
		mapList = this.itemDetailCache.get(itemCode);
	}
	if (mapList == null || mapList.size() <= 0) {
		return "";
	}
	// 转换值
	/********************* 并行实现 *********************/
	Optional<Map<String, Object>> resultOptional = mapList.parallelStream()
			.filter(dataMap -> {
				String tempValue = ConvertUtil.convertToString(dataMap.get(inCol));
				return tempValue.equals(value);
			})
			.findFirst();
	if (resultOptional.isPresent()) {
		Map<String, Object> resultDataMap = resultOptional.get();
		if (resultDataMap != null) {
			return ConvertUtil.convertToString(resultDataMap.get(outCol));
		}
	}
	return "";
```

上述方法就可以实现将`itemCode`对应的代码值列表中的`inCol`的值转化成`outCol`对应的值。

重构了批量转换显示值和无field的方法。

### 获取代码明细集合

根据itemCode找到要查的表对应的service方法，然后查询

```java
public List<Map<String, Object>> getCodeListMap(String itemCode, String sqlWhere, String order) {
    // 查询item对象
    CboItemsEntity item = this.getItemEntity(itemCode);
    if (item == null) {
       return new ArrayList<Map<String, Object>>();
    }
    // 获取service对象
    IBasicService<?, ?> service = this.getServiceInstance(item);
    // 查询item明细
    StringBufferProxy sql = new StringBufferProxy();
    if (item.getTargettable().equals(CboItemDetailsEntity.tableName)) {
       sql.appendLineSingle(" itemid = '{0}'", item.getId());
    } else {
       sql.appendLineSingle(" 1=1 ");
    }
    if (!StringUtil.isEmpty(item.getTargetcondition())) {
       sql.appendSingle(" and {0} ", item.getTargetcondition());
    }
    if (!StringUtil.isEmpty(sqlWhere.trim())) {
       sql.appendSingle(" and {0} ", sqlWhere);
    }
    if (!StringUtil.isEmpty(order)) {
       sql.appendSingle(" order by {0} ", order);
    }
    List<String> filedList = new ArrayList<String>();
    filedList.add(item.getIdcol() + " as id");
    filedList.add(item.getCodecol() + " as code");
    filedList.add(item.getNamecol() + " as name");
    List<Map<String, Object>> mapList = service.queryMapFieldsByWhere(sql.toString(), filedList);
    return mapList;
}
```

重构了不含`order`和不含`sqlWhere`的方法

### 加载编码明细并保存到缓存中

`loadItemDetailCache`方法

调用`getCodeListMap`方法，将`itemCode`对应的编码明细查询出来并放入`itemDetailCache`中

### 获取代码对象实体与service实例

- getItemEntity：根据`itemCode`获取代码对象实体
- getServiceInstance：根据`itemCode`获取服务实例

