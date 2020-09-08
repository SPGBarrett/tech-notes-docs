# 开发文档-El Admin后端权限机制

[TOC]

## 认证及鉴权流程

El-Admin安全框架使用的是 `Spring Security + Jwt Token`， 访问后端接口需在请求头中携带`token`进行访问，请求头格式如下

```text
# Authorization: Bearer 登录时返回的token
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbiIsImV4cCI6MTU1ODk2NzY0OSwiaWF0IjoxNTU4OTQ2MDQ5fQ.jsJvqHa1tKbJazG0p9kq5J2tT7zAk5B6N_CspdOAQLWgEICStkMmvLE-qapFTtWnnDUPAjqmsmtPFSWYaH5LtA
```

基于JWT的认证和鉴权模式常用流程如下图所示：

<img src="PicResDoNotDelete.assets/image-20200908103530567.png" alt="image-20200908103530567" style="zoom: 33%;" />



对于此El-Admin后端骨架，其认证及鉴权流程如下：

<img src="PicResDoNotDelete.assets/image-20200908104124382.png" alt="image-20200908104124382" style="zoom:33%;" />



## Postman实现接口调试

El-Admin默认对admin用户放行，这里以admin用户为例，实现postman访问后端接口。admin用户的账号与密码如下：

```xml
# 账号
admin
# 密码
123456
```

用于测试的接口代码如下：

```java
@Log("测试带权限的接口访问")
@ApiOperation("带有权限控制的接口访问及用户权限信息输出")
@GetMapping(value = "/UserAccessInfoTest")
@PreAuthorize("@el.check('user:list')")
public String getUserAccessInfoTest(HttpServletResponse response, DatabaseQueryCriteria criteria) throws IOException {
        String result = "This is a testing string initializer";
        return result;
    }
```

如果直接用postman访问该接口，由于http请求头中没有认证信息，接口将返回401错误：

```json
{
    "timestamp": 1599522784409,
    "status": 401,
    "error": "Unauthorized",
    "message": "Full authentication is required to access this resource",
    "path": "/Homepage/UserAccessInfoTest"
}
```

**如果要正常访问到该接口，步骤如下：**

**第一步： 获取验证码 （需要启动Redis）**

使用postman，GET方法，请求接口：http://127.0.0.1:8000/auth/code

返回值如下：

```json
{
    "uuid": "code-key-af48fbd85e7f4d2ba63c51839d6d060c",
    "img": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAG8AAAAkCAIAAAAIOPOYAAALg0lEQVR4Xu1ZCVCcVx0nbe0RM9rWibbWTFttrdOpTY3OqDM6o9ZaHbXqTO20TkeN2iVA0pCQkJKEkOY2F0maiyMBQgIhIYfmIAekgUCAwAJ7AFluFpawsMAusLAsx/P3vvft+65lYQrNjDP85j/Mt++97x2/738+gsgMpg9B6oYZTAEzbE4nZticTsywOZ34P2DTaiWVlaSxkXR0kMFBde99wBghNU7P9VbXpWZnSYd7aHRMPcKHybLp8ZDubtLaSurqiNlMKiqIyUSamsjoqHqkXzgHRsbfwwTYvJnodJKEhZH160lqKikuJk6nevD0osszHF1ie/aEMSiulMvsI2X/ym2yub3q0So23W7KUU4OycwkiYlk927y8cdkxQoSGqo4j1wwZmhIPocaF43ON2JrnosyLj/Vou6TIVPfHZrWbO1Wz+VykeBg9aJyWbeOpKURg4GMjKhenSqyrK6nUw1yHuXyeFJ5jq1X9YqCzeho9V4DSdjoP0JG8fDWGudfchpu3euTTwXAJP6e3BikK+VytMChGgN09Q//8WAdG/D0SoOlXWHM+fl0LXzahgZKGX5eukSSkqjCLlqk2M+qVcTrR10+I2DXDyXowdrDCfqw/OarLa4Lzc6LzU4wuMvQ/mRyBVNSQ9eA/C0Fm2x/2OiRI+TUKXL5Mrl5k9y5Q4xGUlNDWlqIw0FcfWOhudbn00xxVZ1H00fYSQ6WO9ASeqt52GfPTY6h+RsqGUe/2G35eqQBD4+GlZU2ueUr4uezUUY5499cbep2S2p26BCdv6BA9o4PsImqKnLmDDUgto3aWvWYz4Zuz8gTyeXga25KxZUWV8it5seOlHGt/F1W7Q1bLxvwyulKr8yFKdhctozuCS4/AEDZm5dqer30wHCg7BggGi1oRy/aja0DX1tB6YPoUpu8I2NZZhf7+eJaU79H9LVJBQ7wyxlfmmFlz386VMcGwHg//JDO39PjW94fLBZxGyrSEbKwMfTCfUGv4evLy9WCxma6ZQV2GtrB1Ky40mM1DpXTZLLgTFWs0c6e95rs/EUFm2vW0D1hYY7eXlJfT4qKqArs3EkOZ3qgg4xKAHGJHQM6Qgd7R9ALk//B5iqQ8uSyivw6yfzfS2hgZC1Jt4JfeEmuj+8faUALvvGbe2tYC9jHK5gWk2/aJO4EJnL3LrHZ1JE9I0PcBoyJA94gsMOVC04nx4/PV4Om32bV/v5KLWdwfmZlob2/sXeI/YRXZQ8vpJv4iwo2t2yhU4M1xBb40MWL1atC9pd3ssFjY2TfPrGxulqc4XBVx3s5DbMXU42D3tXaPXzydpf38fBytD8QXLpgE6WbSdS5Vj6mqKGfNf4zpYn4aGIcxcYqtgEzgkeCH0AIiowUG7Oy+ExqrxpYMFiu/iz4fGK2z/YZOCzd2kcjJGcTnvSrx6j3hNS5xGMq2GROKoD8eYW7WZgUOH9ebIQxenykofeZ44ZZwSJT39tYNeCVcqi4vE5OIuShRXq08F74UO4ffrbTgpa1a+n87e3URfK8AhrH2rXCTIRhyRLakp1NSkpIYSEpLSVlZQob1+upME8Cgdfi+KJAYmRR6yOJNBBBoCKsS5fXxFpAK3cCCFmsV8Em4ibbxHjy4vZ6lrtiZ9yOLlyQZkAvdvCUjxTIuwniPoigzj/Zfpe1f3lp+dVKcRPAFbNrzhLRh0Je21gFEjF5TAzt5Z5RLkg8t20j8fHST3lMZwp77lyg9E4ucCMcr1+0MJq4vJtNT8F95S8v1iDf5L03fKmSOnuHl0SCydcID6eGz54ROp9JNUD74Lk46Uikh4el15lufpDaJNfBLZfv8QF32wfhT0GWPBM6XtT1hRC9/JWXY8x5eXR+5tFgwtrzM+EWfeAAn4+CB/pJCjwyR25b3ywNm+G3rewZBo7SaNntFt6Ln+xFNZtEiC3Xr1PtQyUHbcrNFddDCxT+kLGTGxoca1ub4l3mNxsdQ8x1MoGjvGmREl3YvrwwOpTbwT0Dl3fi60+epEswAzx8WDp2VBRJSSFnz9IcDl+aOXdwyn03Az6DlrIAck/64hT7zR1fSqI50Cwhr3z1dCUj7jsZZlPXQGX34MNCNgqBk+WH8cOmHNC7jz6iiyFAgVnE69e32vkOVBkJj+l4PlHcJWfnlfX+0y5opZbKb6wytnQP7dpFl3AJzgBfVxsSYeaodJFFIc10aMoC7Hw896oVJABjAQvfmFIbo3JgeLTI3v+tdBNXzOA8KcOagE2umCwxxtl0oWOsBXFfDnm+yRDmy4HA155sKSmTA8WPikpk76eMnnfOkYWCCb+1lry/lexII9V3SWcnTY+gg9hVXBx13EuX0rsCvwBBWtbGE1WGpEVabRdTwwrHwAPxlMTXMqvwF0HC4pRcViA2uWLu3y+2wMTY8h8Ej+3J60FcI0LSgLpIVQsV1vezAP1YWFn6nS7flGqgS07lb/bVbs4fnneAJFSQLdvVZ45YQW7ckN6FiwebUEC/FwVTyZC0aOoTEyOknFwrYexI7+XDArHJ4gBUoFXICFFX8Di+M8GL2nyekCLgr6pO/6/ByYqc51ebyqyKUlKLTZfaGJXR/7HltxBQaRNmQkmmPbZOCNMc8ABouXZNauGYSobkF98+acZhUap/1+dDkd6rxozLJvwR/D2WQQrCANNmC2MHverbEwnXqlwsQP/hQF2PrOJmgHtCiu4ZVnipcKGmXJjc+PY5El8utR88qKYSEhIird7fT1ugnlpMJUPyCxbE15XYNujbGJsIUK39iouWcdlklzdQRha1UW7yheUFnBbzVtFbDNTj3OoZBr2jxwodCEfofSnarG+WdBYU/y2J3jbNWdvWKjAFszh+nNy+TdLT6c0LSkm47IQT4gZ4NuN0ii1dGl8ylQzJL2DUQUKmicqH50/pdYqF/bOJ47HbOaaYo6PSZd3Klf79FIf8voO1VLYNLs2wPiGUlVweCdUfzu3gb4F6WqQHl543OOGvIyLE5ZCTo369coVa5dVssZFHHuaLdLLSlmOKGZIWZZ1u0DfnaNlGfdtLGdTqIdElNvkY/2xi6zpBMW3C4GzfMSDQlMB4I1a8uQgSCqGf7hCLH7+CepxbfV2HBy3zN1XD0+mEStzvtQXKXwaE+OXLxUat18MnUV3aawVOAKsg0k6YIRHhez+luTyOKFRcgftnE6kcFtuxgz673eJNHWTNmomvuHddb9eyFkB+uLXa1uMtqOtDyAoS0qnYvTQJS0uj+oIqCIknNgAlDVtFgg9SQ4EUFUm7glit6m0QIZ9Deo9MGZkAlBc56RSvk1FBsljE5O3r9VU9iussP2zCUfJdJiaSY8ekn8XF6sFaNDmG+K2lX3lAk66j1nxwkVRZhoaJKS3CHVZHdomInHWLLNhATl4gycmK2pcNmyJNk4c8Q/pKSgXyUHmvHzbxGeV75bJhw8TmwJB826ElETI3omL9hbY2pxeVu7aXiy6c/oNk8oJ4dd/Arj65PJpY1jko3VP4YRNWo90xBFnb5LHvhl1eMiLQ782xu4ek27ng49JtsVzmRhh+HUV1MyKSJCSQf+8hYZHkr6FkUQgtLmNiaAWBQM/zntWr1ZfHnyvYjedek/3BeLFOl/+vzQ+b8Iw4hopK+Hv5XdFkcFrfjcLxR9uqkRh5R9RajZaf77KoqESFbrYNWBqJzlfJLAwlwetIyinqKCFnz1IT4btCGtTdrZr488ULQoX+/TO0rGRSbO/nvX7YZDCZFPuWVyDTha7+4ZdjzJzKX+2psfeKX8xiESPheIJSB8zyW+r7hi3l9+SW/lyacUTm/sZlkwhZJ5IVVBqxsWRA8Z/OacM9lxcxHal+UoFD65SRA336KU1fNm6kERzWjWwmLo7eXd1/HhlQkXxitr96uhKJJ6gsaFf83zsQmwzaQ85gPEzM5gwmjxk2pxMzbE4n/gfbBtK+x21Q4gAAAABJRU5ErkJggg=="
}
```

其中“img”中存放的是验证码图面的Base64格式，可以通过在线Base64转换工具转换成验证码图片，从而得到验证码信息。上面例子中的图片得到的验证码是“35”。

其中“uuid” 是Redis键值对中的key，通过这个key可以得到Redis中保存的这个验证码的值。

**第二步：调用登录接口**

由第一步中获取到的信息，可以构建接口调用的RequestBody。调用的接口为：http://127.0.0.1:8000/auth/login , 方法为POST

RequestBody为json格式，如下：

```json
{
    "username":"admin",
    "password": "smafdH0vVHxyxikWubDgVgb7IJgjqRCad05tUw5LT3NrL218Ro5P+nLbmnVuY/fkACna2nCgbh+Ho1hTv+6yWw==",
    "code": "35",
    "uuid": "code-key-af48fbd85e7f4d2ba63c51839d6d060c"
}
```

其中，password就是通过RSA方式加密的密码“123456”，可以通过在线的RSA加密工具通过公钥加密得到这个password。后台通过私钥解密这个password再进行后续的认证。在本El-admin骨架中使用的RSA公钥和私钥如下：

```xml
# 公钥
-----BEGIN PUBLIC KEY-----
MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBANL378k3RiZHWx5AfJqdH9xRNBmD9wGD
2iRe41HdTNF8RUhNnHit5NpMNtGL0NPTSSpPjjI1kJfVorRvaQerUgkCAwEAAQ==
-----END PUBLIC KEY-----

# 私钥
-----BEGIN PRIVATE KEY-----
MIIBUwIBADANBgkqhkiG9w0BAQEFAASCAT0wggE5AgEAAkEA0vfvyTdGJkdbHkB8mp0f3FE0GYP3AYPaJF7jUd1M0XxFSE2ceK3k2kw20YvQ09NJKk+OMjWQl9WitG9pB6tSCQIDAQABAkA2SimBrWC2/wvauBuYqjCFwLvYiRYqZKThUS3MZlebXJiLB+Ue/gUifAAKIg1avttUZsHBHrop4qfJCwAI0+YRAiEA+W3NK/RaXtnRqmoUUkb59zsZUBLpvZgQPfj1MhyHDz0CIQDYhsAhPJ3mgS64NbUZmGWuuNKp5coY2GIj/zYDMJp6vQIgUueLFXv/eZ1ekgz2Oi67MNCk5jeTF2BurZqNLR3MSmUCIFT3Q6uHMtsB9Eha4u7hS31tj1UWE+D+ADzp59MGnoftAiBeHT7gDMuqeJHPL4b+kC+gzV4FGTfhR9q3tTbklZkD2A==
-----END PRIVATE KEY-----

```

调用该接口，并成功通过认证之后，获得的返回值如下：

```json
{
    "user": {
        "user": {
            "createBy": null,
            "updatedBy": "admin",
            "createTime": 1534986716000,
            "updateTime": 1588644741000,
            "id": 1,
            "roles": [
                {
                    "id": 1,
                    "name": "超级管理员",
                    "level": 1,
                    "dataScope": "全部"
                }
            ],
            "jobs": [
                {
                    "id": 11,
                    "name": "全栈开发"
                },
                {
                    "id": 12,
                    "name": "软件测试"
                }
            ],
            "dept": {
                "id": 2,
                "name": "研发部"
            },
            "deptId": null,
            "username": "admin",
            "nickName": "管理员",
            "email": "201507802@qq.com",
            "phone": "18888888888",
            "gender": "男",
            "avatarName": null,
            "avatarPath": null,
            "enabled": true,
            "pwdResetTime": 1588495111000
        },
        "dataScopes": [],
        "roles": [
            "admin"
        ]
    },
    "token": "Bearer eyJhbGciOiJIUzUxMiJ9.eyJqdGkiOiIwYjQ0YTRmZjlkZWM0MTZkODI4MTdlZDZjYzk3OWQyYSIsImF1dGgiOiJhZG1pbiIsInN1YiI6ImFkbWluIn0.JI_MvMcsSeV1SXCOQe1vTiyeAhtbzeNc3_n5-UdWMMAbWbOP0UGSkWLeboRsGL7aTeBz7Y7f9HIORsoic952qA"
}
```

返回值中，最后的“token”就是需要添加在RequestHeader中的JWT Token了。

**第三步：调用后端接口**

将第二步中获得的token加到Header中，就能成功访问到接口了。如下图所示：

在Authorization标签页中，TYPE选择Bearer Token，Token中填入JWT Token（需要去掉前面的“Bearer ”）。

<img src="PicResDoNotDelete.assets/image-20200908111232051.png" alt="image-20200908111232051" style="zoom:50%;" />

填好之后，在Headers标签页中的Authorization项中可以看到添加的token。

<img src="PicResDoNotDelete.assets/image-20200908111402735.png" alt="image-20200908111402735" style="zoom:50%;" />

然后就能成功访问接口了。

<img src="PicResDoNotDelete.assets/image-20200908112050538.png" alt="image-20200908112050538" style="zoom:50%;" />













