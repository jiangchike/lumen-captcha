# Captcha for Lumen

本项目修改自 [Captcha for Laravel 5](https://github.com/mewebstudio/captcha).


## 预览效果图
![Preview](http://i.imgur.com/HYtr744.png)

## 安装
* 项目必须启用缓存才能使用，因为验证码和验证码绑定的uuid都是保存在缓存的。 project's composer.json.
```json
composer require aishan/lumen-captcha
```


or

```json
{
    "require": {
        "laravel/lumen-framework": "5.3.*",
        "aishan/lumen-captcha": "v1.3"
    },
    "minimum-stability": "dev"
}
```

## 使用

在`bootstrap/app.php`中注册Captcha Service Provider：

```php
    //验证码
    $app->register(Aishan\LumenCaptcha\CaptchaServiceProvider::class);
    class_alias('Aishan\LumenCaptcha\Facades\Captcha','Captcha');
```


## 配置

在`bootstrap/app.php`中可以配置各种自定义类型的验证码属性：
更多详细配置请查看https://github.com/mewebstudio/captcha
```php
/**
 * captcha配置
 */
config(
    [
        'useful_time'=>5,//验证码有效时间，单位（分钟）
        'captcha_characters'=>'2346789abcdefghjmnpqrtuxyzABCDEFGHJMNPQRTUXYZ',
        'sensitive' =>false,//验证码大小写是否敏感
        'login'   => [//登陆验证码样式
            'length'    => 4,//验证码字数
            'width'     => 120,//图片宽度
            'height'    => 36,//字体大小和图片高度
            'angle'    => 10,//验证码中字体倾斜度
            'lines'    => 2,//生成横线条数
            'quality'   => 90,//品质
            'invert'    =>false,//反相
            'bgImage'   =>true,//是否有背景图
            'bgColor'   =>'#ffffff',
            'blur'   =>0,//模糊度
            'sharpen'   =>0,//锐化
            'contrast'   =>0,//反差
            'fontColors'=>['#339900','#ff3300','#9966ff','#3333ff'],//字体颜色
        ],
    ]
);
或者在config目录下创建captch.php配置文件：
/**
 * captcha配置
 */
return [
    'useful_time'=>5,//验证码有效时间，单位（分钟）
    'captcha_characters'=>'2346789abcdefghjmnpqrtuxyzABCDEFGHJMNPQRTUXYZ',
    'sensitive' =>false,//验证码大小写是否敏感
    'login'   => [//登陆验证码样式
        'length'    => 4,//验证码字数
        'width'     => 120,//图片宽度
        'height'    => 30,//字体大小和图片高度
        'angle'    => 30,//验证码中字体倾斜度
        'lines'    => 5,//生成横线条数
        'quality'   => 100,//品质
        'invert'    =>false,//反相
        'bgImage'   =>true,//是否有背景图
        'bgColor'   =>'#ffffff',
        'blur'   =>0,//模糊度
        'sharpen'   =>0,//锐化
        'contrast'   =>0,//反差
        'fontColors'=>['#339900','#ff3300','#9966ff','#3333ff'],//字体颜色
    ]
];
```
在`bootstrap/app.php`中加入
```php
$app->configure('captcha');
```

当然，也可以不配置，默认就是default的样式，验证码有效时间5分钟。
## 使用范例
有两种使用方式
1. 使用接口{站点域名}/captcha/{type}/{captchaId}直接获取验证码图片
2. 使用接口{站点域名}/captchaInfo/{type?}获取图片uuid和data-url格式的图片信息
因为lumen一般写的都是无状态的API，所以此处验证码的图片必须绑定一个uuid，获取图片验证码时，先获取验证码url地址和uuid，然后在验证时，提交验证码和uuid一并验证码。
### 生成验证码
生成验证码(直接返回验证码图片)：
```
{站点域名}/captcha/{type}/{captchaId}
```
例：www.baidu.com/captcha/default/mycaptch


获取验证码信息：
```
{站点域名}/captchaInfo/{type?}
```
其中`type`就是在配置文件中定义的验证码类型（如果你定义了的话），当然也可以不指定`type`，则默认为`default`，返回信息：
```json
{
"code": 200,
"captchaUrl": "http:///captcha/{站点域名}/login/f3472e6b-498c-0109-0a13-b7a8b96e43f2",//目前没有用, 可以重写程序,将图片保存下来
"captchaUuid": "f3472e6b-498c-0109-0a13-b7a8b96e43f2",
"captchaImg"://直接在img标签的src里使用 "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAHgAAAAeCAYAAADnydqVAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAVy0lEQVRogX2beZBdVZ3HP3d79777lt7oztKQDZB9GZQRBkY2xbBDAqI4MhrF0hlnFHUWRmpqdEZGKQoVZ6qQskYGjOOCiSaEGBJEHBaJMqJCQFlCEpKQ7k663/7uPn+8/E6f+7rxVnX16/fuO/ec3/L9fX/fc9p46KGHMtM0MQwDwzBI05QkSQAwTRPLsrBtG0DdYxhG7m/9dZZlZFmGYRjYtq1e6/fKpT9Xv0+u/rH1e+M4Jo5j0jTFsiwKhQKO42CaJlmWkaYpWZblnuU4zpzxZbxWq0W73QbAsiw1H9d1cRwH13VJkoQwDDFNE8/z1HODIFDfT5IE27axbVvZslAoUK1W8X0fy7LUfGSeSZKQpilpmhLHMVEUEQQBYRhiGAaO4+A4DpZlYVkWxWIR3/fVGKZpAqj1RlFEGIaEYYhdKBRI05QoitQNumMtyyJNU/7YJU4Vw6dpqoxjWZZyZL9h9Wu+9/S5GIahJp6mqXrf8zxs21aLlEvm8PjXTlTvvfMfXlXPyrJMGTKOY7V2cZppmti2jdhHnJtlmQqkIAiYmZmh1WrR6XSU8cVmhUJBBUIcxzQaDYrFIsViUY0pP3EcE4YhQRDkgqtQKKiAkb/FpnJflmVqjCiK1HoMw8DYuHFjJlEgESIG7c+s+TJMn+h8RtJ/ZCxxnIzR/9swDPW5ZKoswjAMCoWCyiwdMfSx5X09uLZ9ecWcAPrzT72gskecpyOWfF+CXWzTaDQ4ePAg7XZbzcfzPPV927aVfSQg9UA0TVNlLkAQBHS7XQBs28ZxHAqFAsViUWWvzEvsIPbpdru5IJXvW5aFsWnTpkxgQDesOFcmoztWjC0ZKxHVHyS6gf9YsPS/lvvSNCUIAtI0xXEcZUC9FMgzdJgSGNQDSeAwDEMAXNfFdV0e+tcj5zj9wr97WY0rWSzzCcOQbrdLt9sliiIVaJJZgAoWCQzJ4iiK1FoKhQITExl794LrhoyO1onjWGW4BImM2b/WLMvodrvMzMwQxzGe51EsFnMZbpomtuu6uQzqr1v6+3qmyn364nTHSdT2O0936HzvSwTKM8WAEjAS8WJAea4ejPK63ylBEADgeR6e55FEJiddNsGh13vPGz2mzejRAY/cfswcp1/w2ZeIokhBZalUwjAMgiBQc5CMFAST8iElsLcOh61bbX74wwIvv2yr8ZcvH+aWW+qMjs4Ghh6o8jpJEqIootvtMj09TbvdplwuUyqV8DyPtZ8u8Zd3BcpHtrzQiYXuJN2Y8rdEtkxCYEIIRr/D9Ut/T4ds/X2BICFpkhVynwSOHmjzkTCpad1ulzRNKRaLlMtlXNdl9699XnnsCJLQwh8KCFsO+38zzDHnTXPJrXtyBo3jmK1fOnaO06/5t0lc16Xdbivnl0olHMdRc4rjGNM0KRaLvPCCwVe+Usw5Vq6dO21uuWWAr3/9DapVS9lY7CR8odvtqnUFQUC1WmV0dLQXsIfJca6Mbtu2LQNoxw1ej35P0SpxpHO8gl49ewSC9UwKgoAkSXLZKk42TTMXFPMRLvms3+nzZXj/vf33i0O63S6tVktlTalUUgw2iiL2PjvAqz9bzIKTp1hx7iH8qkUSGzz7/cXU97tc+Jnd2M7seDpM60ix/tbROY667kvTKpsdx1G1cetWjzvv9EnT3riVSsZ554WUyxGbNhVpNHoBtWpVxM03x7kSJeSy2WzSarUAKJVKDAwMMDAwoAJKbP3ff+vywa/3SpEtk94d7uBb+z/Hsd7bWLPoNlzXVXDXarUU3IgBJct0ti0kQodzHWpkAkJA9EsvDW9Wn+VvCTAJGt2p3W4XwzDwPI/BwUFc11UQ2el0AIPdT40xvKzFSZfMYBiHs8UzGT+tzszrY4RNC2swUXPtRyMhlld+/g0AxTkAfvCPw3OcDrD2xYw0NTCMjCuv7HDDDTVcN8GyLM46K+XmmytkmcGGDTbve19KpdJgZmZGtWBBEFAul1mwYAGVSkV1KIIwEnRyKRSUtiaMevWpndXwPI8oimg0GgrepI2Svs51XQXtOjvUa6NEls70hHSoYDBSnp1+jB21pzgUTuCaHkvKx3PG8AWM+3NrYT/xk/ZFesZqtUqlUqFQKBBFEa1WS9VF13VJQ5s4sCmNtHKoEscxQcPBLce41ZggCFWrIkikZ7DMob8HXX3bQRWItm0Tx3EPOlW2G/C6zyO3+7znyzO4rsuZZ5pcdFHCtm02cWxw//0Rq1a9wQefPAWAu05+gqVLlzI0NEShUFBBFsfxHGIM8P47W9z/qRI3fq2LLdlkx70IbMTTTE5OKugFFP4Xi0VKpVKuxkqfKBmlQ3Oz2VRsUz6DWVjfFTzPd/b8O/s6r+acuP3gFh7Y9TX+dOTdfPjYL+A7lZyDxbCyIOmH+1sJIWk6qzcKBuWxkDd2lFl+VhOr2O4FbqfIa9urrDh3ijRNcoxUb0F0h8oc+vmBfC7Ch+M4XPn5GXbssDj99EgJF/d9clCtawXw0bfCPc/AhoUVNjwJ33jrr1i6dCkDAwM54UZn1IIccRznxBEo9YQO+bDRqgPQTKYPQxlq0uPj4xSLRRU50mdJ8ZfGX8bqdDpKFSoUCpTL5TmQ/FztCb655xaSrAcvi4rLOaZyOkHSZkftaZrxDNsPbmEyeJ1/Pm0tjuWqxYnB+9sycaxeOnSDiANOuWyaX37nCB7/xiLKC1sU/ITmAZ8sMchCn6nfeyw+PiS1e2uJokghgAS22EZXknSkk55aEADgbW/LMAxHOf+Kf5mm1WopCP6rZ9/OR8ngmZ4jn3kGngE+8NVOjlQKAcuyLNe2ATlfNBoNjO99/7vZTDDFc+FjPBn/AIC3m9eQOQmx06FDg79Y8jkG7CPmCAdZlikYkklK1ksvpzNygfRaOskX/3ADURZgYHDjils5f8F16t4oC7n35c/z+MSPAbj6qI+zaukncsKA7jTdkP2Ch04KBdaCIKB+KGLqxWGa+4do7K3gD8V45ZQoMGlOWhhmxnEXT7DwpIZCCF3qFINLQMn4YmQpYf2EUNBQHHvTL8+Yjfp7eo4dGsrYuDFQ37n/U0X6r+tvr9FsNlUvXiqVME2TTqej/LDltqXY/zn9MVrM5L78dLoeAno/wEw0QYlBFZ2SwbZtKxgS1i0wXCgU1IKiKFKGcByHDVN3E2W9wa856hNcuOj6HHM2M4+b3vJFXmn8lv2dnWzdt5aVY2uIo9ln6JkkTb8YVi4xusxBMrzVahGmAQtOi+hOjDG8JOSsG6dI0t59nZrF7360mN9vHWP8BKgOzZJHqakypmjcIiPKfbJmz/PIsiwnicZxzA0/Ow6A9ZfsplQqcc89VR44PO/zzktUQpimyQe+2lHBJK3S9/7+SGAg57d3/9Mu5QtJNLtijUAGBdNlOj4AwBkDFzHuHUvZHqRkDlIMhqi1awA5OS5JEgXd4kBhtEJ8hMSJIUI6/LqxDYCqM8LlR63JOUoWlqYpZw69mw2du2kldV5rvMCR3rE5BBHio0O1ZKk8W+p1HMe02201fqVSIdi3hJk9LmfftJtGs62gb3DM5qR3tXh67TC1PRWqI53ceuRZInroNV6/T9okfa5XPjgOwLqVu1Rf3mqZbNpkHUaGjGuuidS6hLGL07Iso1Qq8b47GsRxTLPZVOrcltuOVs4+bc3TnP7h7dgfW/BVCoUCU+zmztdu6n1YuYATvLNVD+a6Ll65p/7oOq8QEFmksOVarUaSJIpJ+76vIvy55hNk9KBoiX8c+9u7WOAvwbNnBXiJ9hFrsZrwgfZuFtrLcz15f7slQrtuEMngJElwXRff95Uc+Nuf+xSHIuxSh2LRz0F9w+2N2263mJ6uqaD2fT/XOsmcpUsAlOBRLBbVei7fsEhlrL55kyQJDzxg0en0nnf22SnLlqVk2Wzg6jthEuDiG31X7dxP7tCEnwqlUqknVTqOwwAjs8as72FJciqu61KpVNTgepugR5jIgPIwmZAuwgvh2dn8nXrOc7Un+dyzV2NgMFQYY8w7ihF7nCFrISPOOBPhLnVvN27lsld+i6IjkmGxWFQkptlsYts21WqVcrmcm1PPcAZB06bsD+IWZx3WbLb53cMjWIWE4qIJCoWKQi65BAk6nY4KOH33xzRN3vHtkrp/3cpd6j7pRHrIAuvXzypb739/lCOMwJxyJFKltKyO4+TQTG9Vbdm6MjqF2UxwuoyOjs4hEv0/uuAvkxed17ZtisWi2jWRrbZ6eEg9x8QiJSEj41B4gEPhAeBXzHcN+2PKwGEY0m631dahjhLS+2ZZxtjYmOqJ9R0u2VZbfGLAvueLPPPDAU66uAFmxIGXHHY+tYBu3ebM904zvnxhrs8UbVs2DsSpwjmCIOCd3x9S2Sp2yQfWrNT6+OMWBw/2xj/ppJRTT80Iw0gRJdl+1Pt9UQ49z1P2kPoeBAHtdluxfls87pgFPLNMN23Spa7qiRArfS9SYE/2PGXyEmFCxPr3Jj3Pw7RnIfX2035CozPDG51dTIavcyjex1S0l6lwLweDfURZqO6tOiOqDOiOLZVK6tnS+Fer1RzxkdZBYFtKx/gpAZM7W+z6VYkDL/a4hGFmjJ8ccPLFdcojBoZh5vp8fZNFOoVut0u73eayHy8E4LsXvqSyULJVsksCPggCDMPgqafKao0rV7ap1Zqq/Lmuq7oUeb4kjaCHlAZd7BFW7fs+9sTEBJZlUS6XqdpDdMMmBzsHaDQaikh4nqciTpwujus/RSH1T8hIlmUUCgV83+85yjpCLWimO8mywRMYr64giiIlnMizJjt7mejuZjJ8nSMrx+S274TJ63VKSobO9GXOegtjGIZiwUdf0GDsZIvWlIvjZYytgMqAe5jtzraFAoPiNNGJJVsBHrl+JifRFgqFnFzbbrep1WrEcax2f3bunA348fEOl1++MIdcjz7aIIpStm51uewyC9ftlaVms6mURimJlUqFcrmseMJhe/UcGAQBHj3FqJXMqMiTzBDBW48uWXSr1VJMTje6zjCF8S0vnKYm/2zrEU5YeIbKRmGJ4uglxWNYHC+bs/Ehl9QZvTbr4oZo0yJVitolsCYqm1FMqC7t1UbvcCD2nxARqJV1CBveet2hnIKlz1O2Cqenp2k2myRJgu/7DA0NcfHFczXrT396lCeeCFRZDMOQzZtNvv3tMvv2WTQaEddd16Zer9NoNFTw+L6vjgTJ+sVndhAEHDrUq4sloxeNrayGaZq0222F63q/p+9xttttBSFy+kDfcDBNM8fG377kIjZOHc+u1ots3nsfpwyfw6kj5+ayTwx0sLufij2SO4+kO1d/LU6WOelwVS6X8TyPNE0VrMkZKtu2qVQqDA4OqpZP+luBUwnkbrfLFRt7zH7be6axLIsgCHJnz8TAQRDwjnf4QAGYlVrl2rBhH57n8fDDFe64o8ctli+frbPbt9vce2+FV16ZJWCbN1tccUWXLMvUXMvlsiLCEoi5Hbm1a9dmnudRqVT4WfM7bJm8FxOLL6zYQBYZqobJgDruC2T5vq+yQycQuhP0yH618Rxf/O2NhGkXE4t3Lb6B8xddy5LycTiOw1RnP4/tW8eG3fdwyvA53HzKXdiWM2c8WYyUBTnPJKc/ZIuw3W7TaDRoNpvU63WSJGFoaEjtzAh50ltAfTMDYOW63mbBw9ceVEEQhiGu63LppWNzHAjwwAOvqVIhe8OAIoW2bdPppFx3ncv0dA8Brr22yYsvujz33CxjL5VSrrqqzqWXzlCp9NamnyCRwNLJYJIktNttjEcffTQT57zS+g13/KHXC59XfS9XLf44vu8ThAH/O7Ge461zSMKeU6X96deZdQFeFiUkTZ6TpinPzzzFf7z4GVpxTX3XwMAxXcK0q947qvQWbj3jPoa9MaUCiYM7nQ71ek9Dl7kI6zQMg06nw8TEBBMTE7RaLWzb5ogjjmDx4sUMDg7m4FzGlN7ZNE3lVEDJiPNdW7dOEwQBjUaDKIrUWSoZT3eCjoKi269fb3P33QNzxnWcjJUra1x22QGGhnrtnmgROmnr78l1edjYvn17JtCVpin37P8sL7d+DcCy0okUjQqvtZ6nkzW5pPQxzhm5Ct/3VeOvb59J9OuH2OToik6eJLvr8UEe3PNNfv7Gj3KOBihaZS5ZeiOrlv81ZmLT6fTUJIlUQRHP86hWqzkttl6vMz09zczMDFEUUalUGBsbY8GCBTmxRt+hETh+s2xct253jnBKAIuIIl2FaZqs3rIs990tq6cU45YgFZm30+nguj4f+tAA+/aJhp5x/vl1Vq+eYsmSXn0V5VA4kJRCXbnrP0IMYPz0pz/NRKTwPI/Mjfiv3bfyUuv/cpM8yjuOVUf+DScOnKWiUh6iK0jyEBEVBEp0Gt9/8jDLMva1X2U6nCBMu4z5R7LIXU7QDdWhO/3cE/S07lKppBhto9GgVusJ8GJAqa3SNumH4S65ZO5pDAA+avDQ1T3JVhwnjgVUaRJH6exd2jTZDhQd+oL/qc7/rMPXkx8MePDBmNtu8zn77A4f+UiXZctmT6NKRuqnOnU9XN+X1w8lpmmKsW7duiyKIgVvMvE97T8wlexhoDTIMUOnMlY8Sg0qLNe2baampnKNt7BrvUWRANLv6a93Uk+kp5RJ604VtUjqVxzH1Ot1xeJF3B8eHqZarXL11XNPTMq1efMklmVx8QOzCt7mayZUCZH2o1wuq55TP5qkCzsSDHpw6wKPtI16efF9n3K51wP/2b3u3Alq1/aPJLnjyTr/EVvr6CAJZ9t271y0bNo3m01M01Tqj2SJfEl2ZMS4zWaTTqfD6OgoAwMDOUlNP8StH9rWGbauyQrT1iNfYFh3qkweZk96rllz6ryG2bTpQG6TXD8fJmx4y+opdb8uB+rMXNd7pYZK4On3weyesO5QvRToJUsP8v5xdJny7G/NqozzXb9YE+X8o2+GGBs3bsxEFtMbZTny0mw2c2eqdCdLEIgOLP/eoR9P1euBvgWWO32vQZDso0qkSh97001/8qYLXLdu95x/S5Grf59W1CbdseJ0nU/0S7J6APQfkRF4lnos69JbRdd11SkRcaDuVJ276LCs74xJNyPlRj6brwT8Yk0PaY0NGzZk8mAxrmz3qXNMh1UbaarlwTIZ+Z5er/ST/fMRsf56ITC9evXSeZ143307ck5QYvphlaj/fJROnizLUo7dfM2Eel83rPS+Ev16NukO1+u47OrogSRQqmvk+r+zzHe8Rz/EoKOaPv9+NFE1Vqv5ejDKM+yxsbGc0iTHdaTNqNVqDA8P97aebFu9ZxgGAwMDDA/3FJk4jnP/iyM1UT8srzM8MdL11x+dW9Bddz2p+stKpaL+jyeOZ3tV/cCB7gQxoN5CCBT/ZNVkLjgEYkV61XtJ+Vw3VD+UikNgVj0TJqv/l4EEhQ6ftm2rzYT+HTgJYpm/Xs6kruuoIHPub5Fk/v8PF7EhHEyJhSIAAAAASUVORK5CYII="
}
```
`captchaUrl`为验证码图片地址，`captchaUuid`为绑定验证码图片的uuid。
#### 验证验证码
两种方式
1.在请求中将验证码的值和captchaId随着你的post请求一起发到服务端，在接收参数的地方做验证即可：
2.在请求中将验证码的值和uuid随着你的post请求一起发到服务端，在接收参数的地方做验证即可：
```php
public function checkCaptcha(Request $request, $type = 'default',$captchaUuid)
    {
        $this->validate($request,[
            'captcha'=>'required|captcha:'.$captchaUuid
        ]);
        ...
    }
```


## Links
* [Intervention Image](https://github.com/Intervention/image)
* [L5 Captcha on Github](https://github.com/mewebstudio/captcha)
* [L5 Captcha on Packagist](https://packagist.org/packages/mews/captcha)
* [For L4 on Github](https://github.com/mewebstudio/captcha/tree/master-l4)
* [License](http://www.opensource.org/licenses/mit-license.php)
* [Laravel website](http://laravel.com)
* [Laravel Turkiye website](http://www.laravel.gen.tr)
* [MeWebStudio website](http://www.mewebstudio.com)

