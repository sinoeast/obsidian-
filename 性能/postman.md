# 变量
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103145000.png)
# 关联
登录操作自动保存了cookie
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103202848.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103202901.png)

```json
{
    "data": [
        {
            "user_token": "9c6bec63-8fa9-405a-958d-91c63f6a95e33",
        }
    ]
}
```

```js
var t= pm.response.json().data[0].user_token 
pm.environment.set('Cookie',t)
```

![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103171205.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20230103171500.png)

# 断言

## 1、断言响应码为200

```
pm.test("断言响应码为200", function () {
    pm.response.to.have.status(200);
});
```

## 2、断言响应结果包含指定的字符串

```
pm.test("断言响应结果包含指定的字符串(系统管理员)", function () {
    pm.expect(pm.response.text()).to.include("系统管理员");
});
```

`console.log(pm.response.text())`
`console.log(pm.response.json())
``` 
{"data":[{"real_inst_id":"0","mac":"","tenant_name":"","lan_ip":"","kind_name":"","user_token":"1a6db920-43e8-4426-be70-2cff7e32ff881","operator_name":"系统管理员","user_type":"0","operator_auth_uri":"","session_id":"","opt_auths":{},"error_info":"login success","operator_code":"admin","wan_ip":"","session_timeout":1800,"env_name":"","error_no":"0","istemporary":"0","op_user_type":"0","op_org_id":"0_000000","ip_addr":"192.168.113.204","customer_id":"0","env_id":"001","system_no":"","operator_auth":"true","nick_name":"系统管理员","last_login_date_time":"2023-01-03 18:34:59","return_code":"0","org_name":"组织根","return_info":"login success","org_code":"000000","certificate_type":"","inst_id":"1111","sso_org_code":"","tenant_id":"","redirect_uri":"","operator_no":2}]}
```

## 3、对返回的结果做json检查

```
pm.test("对返回的结果做json检查", function () {
    var jsonData = pm.response.json();
    console.log(jsonData)
    pm.expect(jsonData.data[0].operator_name).to.eql('系统管理员');
});
//jsonData.value表示需要断言的响应结果中json的值（通过json键值对的方法获取），
//to.eql(100)表示期望响应结果的值
```

## 4、断言响应结果等于指定的字符串

```
pm.test("断言响应结果等于指定的字符串", function () {
    pm.response.to.have.body("期望的响应结果");
});
//一般用于响应结果固定的断言方式

例子：
pm.test("断言响应结果等于指定的字符串", function () {
    pm.response.to.have.body('{"return_code":-1,"error_code":"","error_message":"查询失败","error_properties":{},"result":null}');
});
```

## 5、断言响应头包含指定的响应头

```
pm.test("断言响应头包含指定的响应头", function () {
    pm.response.to.have.header("Content-Type");
});
//根据实际情况而定，content-type为实际情况需要的响应头内容
```

## 6、断言接口响应时间小于200ms

```
pm.test("断言接口响应时间小于200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});
//响应时间根据需要进行自定义
```

## 7、断言响应码在指定范围内

```
pm.test("断言响应码在指定范围内", function () {
    pm.expect(pm.response.code).to.be.oneOf([201, 202]);
});
//根据接口自身情况定义范围，比较常用
```

8、断言响应码包含指定的字符串

```
pm.test("断言响应码包含指定的字符串", function () {
    pm.response.to.have.status("OK");
});
//接口成功一般返回的都是 "ok"
```
