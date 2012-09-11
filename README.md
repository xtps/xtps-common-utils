####一、`convertToJson(Object obj)`方法说明
* a) 转换单个对象：
  * 代码：`JsonConvert.convertToJson(lstAccS.get(0));`
	* 输出：{"name":"张三","status":"正常"}
* b) 转换集合对象：
	* 代码：`JsonConvert.convertToJson(lstAccS);`
	* 输出：[{"name":"张三","status":"正常"},{"name":"李四","status":"异常"},{"name":"王五","status":"正常"},{"name":"赵六","status":"异常"}]


####二、`convertToJson(Object obj, String[] strProperties, QueryParameter parameter)`方法说明
* 1、第二和第三个参数为null的时候，该方法和`convertToJson(Object obj)`方法一致：
	* a) 转换单个对象：
		* 代码：`JsonConvert.convertToJson(lstAccS.get(0), null, null);`
		* 输出：{"name":"张三","status":"正常"}
	* b) 转换集合对象：
		* 代码：`JsonConvert.convertToJson(lstAccS, null, null);`
		* 输出：[{"name":"张三","status":"正常"},{"name":"李四","status":"异常"},{"name":"王五","status":"正常"},{"name":"赵六","status":"异常"}]
* 2、第二个参数指出序列化过程中需要保留的属性，当第二个参数不为空时，输出的json字符串只保留所需要的属性值
	* 假设：仅保留参数：name
	* a) 转换单个对象：
		* 代码：`JsonConvert.convertToJson(lstAccS.get(0), new String[]{"name"}, null);`
		* 输出：{"name":"张三"}
	* b) 转换集合对象：
		* 代码：`JsonConvert.convertToJson(lstAccS, new String[]{"name"}, null);`
		* 输出：[{"name":"张三"},{"name":"李四"},{"name":"王五"},{"name":"赵六"}]
	* c) 对象嵌套情况下保留属性：
		* 代码：`JsonConvert.convertToJson(person, new String[]{"id", "account.name"}, null);`
		* 输出：{"id":"DEV001","account.name":"张三"}
* 3、当且仅当第一个参数为Page对象时，才需要指定第三个参数，第三个参数为查询该Page对象的QueryParameter条件，该模式下，第二个参数不可以为空
	* a) 转换所有属性：
		* 代码：`JsonConvert.convertToJson(page, new String[]{"name","status"}, parameter);`
		* 输出：{"totalpages":2,"currpage":1,"totalrecords":4, "result":[{"status":"正常","name":"张三"},{"status":"异常","name":"李四"},{"status":"正常","name":"王五"},{"status":"异常","name":"赵六"}]}
	* b) 仅包含name属性：
		* 代码：`JsonConvert.convertToJson(page, new String[]{"name"}, parameter);`
		* 输出：{"totalpages":2,"currpage":1,"totalrecords":4, "result":[{"name":"张三"},{"name":"李四"},{"name":"王五"},{"name":"赵六"}]}
* 4、对象内包含嵌套对象集合情况下的转换
	* JSON源串：{"person":{"id":"DEV001","account":{"name":"张三","status":"正常"}},"lstPhoneS":[{"type":"中国移动","number":"18700000000","status":"正常"},{"type":"中国联通","number":"18600000000","status":"异常"},{"type":"中国电信","number":"18500000000","status":"正常"}]}
	* 假设仅需要保留phone集合中的number属性以及contact中person对象中account对象里的name属性，则：
	* 代码：`JsonConvert.convertToJson(contact, new String[]{"person.account.name","lstPhoneS.{number}"}, null);`
	* 输出：{"lstPhoneS.{number}":"[{\"number\":\"18700000000\"},{\"number\":\"18600000000\"},{\"number\":\"18500000000\"}]","person.account.name":"张三"}


####三、`convertListJsonToPageJson(String listJson, QueryParameter parameter)`方法说明
* List集合的JSON:[{"name":"张三","status":"正常"},{"name":"李四","status":"异常"},{"name":"王五","status":"正常"},{"name":"赵六","status":"异常"}]
* a) 转换包含所有属性的Page对象：
	* 代码：`JsonConvert.convertListJsonToPageJson(JsonConvert.convertToJson(lstAccS), generateParameter());`
	* 输出：{"totalpages":2,"currpage":1,"totalrecords":4, "result":[{"name":"张三","status":"正常"},{"name":"李四","status":"异常"},{"name":"王五","status":"正常"},{"name":"赵六","status":"异常"}]}
* b) 转换仅包含name属性的Page对象：
	* 代码：`JsonConvert.convertListJsonToPageJson(JsonConvert.convertToJson(lstAccS, new String[]{"name"}, null), generateParameter());`
	* 输出：{"totalpages":2,"currpage":1,"totalrecords":4, "result":[{"name":"张三"},{"name":"李四"},{"name":"王五"},{"name":"赵六"}]}


####四、`convertToMap(String jsonData)` 方法说明
* 原始JSON字符串为：{"name":"张三","status":"正常"}
* 转换成Map集合类型：`JsonConvert.convertToMap(strOrigin);`
* 转换后集合大小：1，name = 张三
* 转换前的json字符串不管是来自于单个对象还是来自于对象集合，转换后都是Map集合


####五、`convertToMap(String jsonData, boolean isOnlyLastField)`  方法说明
* Person对象的原始JSON：{"id":"DEV001","account":{"name":"张三","status":"正常"}}
* a) 当`isOnlyLastField`为`False`时，该方法和`convertToMap(String jsonData)`一致
	* 代码：`JsonConvert.convertToMap(strOrigin, false);`
	* 转换后集合大小：1，name = 张三
	* 此时如果需要取得嵌套对象内的属性值，则需要通过Map类型取得嵌套对象后再获取属性值，如Map.class.cast(lstMapS1.get(0).get("account")).get("name")
* b) 当`isOnlyLastField`为`True`时，JSON反序列化时仅保留最后一层属性名称，如`account.name`将会被转化成`name`
	* 在此模式下，确保源JSON串是自定义了要保留的属性之后的JSON字符串
	* 代码：`JsonConvert.convertToMap(strOrigin, true);`
	* 转换后集合大小：1，name = 张三


####六、convertToMapIgnoreLevels(String jsonData)方法和convertToMap(String jsonData, boolean isOnlyLastField)方法中isOnlyLastField为True时一致
