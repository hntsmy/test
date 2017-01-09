# GeoNode people模块API接口文档

## geonode.people描述

geonode.people模块主要用来建模与储存包括使用GeoNode网站的用户和该系统外被列为特定图层联系人的用户信息。这是GeoNode的基本模块。通过提供的一系列表单，用户不仅可以编辑和管理自己的配置文件，而且能够查看其他用户的配置文件并与之交互。

字段说明：

参考来源：http://blog.chinaunix.net/uid-21633169-id-4352928.html

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| username | 用户名（可以包含 @ 、 + 、 . 和 - 字符） | 小于等于 30 个字符 | Y |
| first\_name | 姓氏 | 小于等于 30 个字符 | N |
| last\_name | 名称 | 小于等于 30 个字符 | N |
| email | 电子邮箱地址 |   | N |
| password | 密码（哈希值，元数据，Django 不储存原始密码。参见 &quot;密码&quot;一节） | 原始密码可以是任意长度的，包含任何字符。 | Y |
| is\_staff | 布尔值。指明这个用户是否可以进入管理站点。 |   |   |
| is\_active | 布尔值。指明这个用户帐户是否是活动的。我们建议把这个标记设置为 False 来代替删除用户帐户，这样就不会影响指向用户的外键。 |   |   |
| is\_superuser | 布尔值。指明用户拥有所有权限（包括显式赋予和非显式赋予的）。 |   |   |
| last\_login | 缺省情况下设置为用户最后一次登录的日期时间。 |   |   |
| date\_joined | 缺省情况下设置为用户帐户创建的日期时间。 |   |   |

### 注册（Django内部实现）

/account/signup/

描述：用户注册接口

使用Django自带模块——django.contrib.auth

参见官方文档：https://docs.djangoproject.com/en/1.8/ref/contrib/auth/

http请求方式：POST

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| username | 用户名（可以包含 @ 、 + 、 . 和 - 字符） | 小于等于 30 个字符 | Y |
| password | 密码 |   | Y |
| Email | 邮箱地址 |   | Y |

### 登录（Django内部实现）
/account/login/

描述：用户注册接口

http请求方式：POST

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| username | 用户名（可以包含 @ 、 + 、 . 和 - 字符） | 小于等于 30 个字符 | Y |
| password | 密码 |   | Y |
### 忘记用户名
/people/forgotname

描述：通过填写邮箱地址，帮助用户找回用户名。

http请求方式：POST

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| Email | 邮箱地址 |   | Y |
正常：如果请求方式合法，且用户名存在，则组装信息"您在**网的用户名已经通过邮件正常发送。"

异常：提示用户根据邮箱地址未找到用户名。

返回内容：（Json类型）

'message': message, 信息内容

'form': username_form 用户名表单

### 忘记密码（Django内部实现）

account/password/reset/

描述：通过填写邮箱地址，帮助用户找回密码。

http请求方式：POST

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| Email | 邮箱地址 |   | Y |

### 个人信息编辑

/people/edit/

描述：对个人信息进行编辑

http请求方式：POST

返回说明：

如果用户名为None，则获取用户名，若获取失败，重定向到people/profile_list.html。

参照urls.py

>url(r'^$', TemplateView.as_view(template_name='people/profile_list.html'),
                           name='profile_browse')

否则，调用Django中的get_object_or_404方法，抛出404异常。

正常：

如果用户名验证正确，且表单提交合法，则保存新的用户名，弹出正确提示并跳转到profile_detail页面，如果表单提交方式不合法，则重新跳转到信息编辑页面。

否则，提示"无法修改其它用户的用户名"。

提交数据格式：form表单

http请求方式：POST

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| first\_name | 名字 |   | N |
| last\_name | 姓氏 |   | N |
| email | 电邮地址 |   | N |
| organization | 组织名称 |   | N |
| profile | 个人简介 |   | N |
| position | 职位 |   | N |
| voice | 电话 |   | N |
| fax | 传真 |   | N |
| delivery | 邮递地址 |   | N |
| city | 城市 |   | N |
| area | 省份 |   | N |
| zipcode | 邮编 |   | N |
| country | 国家 |   | N |
| keywords | 关键字（使用逗号或空格隔开） |   | N |

返回页面：people/profile_detail.html 用户详情信息页

返回参数：profile 返回内容为所有查询集的结合

字段（geonode\people\enumerations.py）

>CONTACT_FIELDS = [
    "name",
    "organization",
    "position",
    "voice",
    "facsimile",
    "delivery_point",
    "city",
    "administrative_area",
    "postal_code",
    "country",
    "email",
    "role"
]

>ROLE_VALUES = (
    ('author', _('party who authored the resource')),
    ('processor', _('party who has processed the data in a manner such that the resource has been modified')),
    ('publisher', _('party who published the resource')),
    ('custodian', _('party that accepts accountability and responsibility for the data and ensures \
        appropriate care and maintenance of the resource')),
    ('pointOfContact', _('party who can be contacted for acquiring knowledge about or acquisition of the resource')),
    ('distributor', _('party who distributes the resource')),
    ('user', _('party who uses the resource')),
    ('resourceProvider', _('party that supplies the resource')),
    ('originator', _('party who created the resource')),
    ('owner', _('party that owns the resource')),
    ('principalInvestigator', _('key party responsible for gathering information and conducting research')),
)

### geonode构成体系框架图

![geonode构成体系框架图][1]

### 头像管理

django-avatar：一款应用于django的头像插件


参考链接：

- [django-avatar(github)](https://github.com/grantmcconnaughey/django-avatar)
- [django-avatar(github doc)](http://django-avatar.readthedocs.org/en/latest/%23management-commands)
- [django-avatar插件的使用-简书](http://www.jianshu.com/p/da3de42a96b5http:/www.jianshu.com/p/da3de42a96b5) 

部分接口模块展示：

/avatar/change/

/avatar/delete/

/avatar/add/ post

### 搜索

/search/

类型：form表单

描述：通过填写关键字，实现用户站内搜索地图、图层、用户功能。

http请求方式：GET

链接示例：/search/?q=china&exclude=&type=all&start_date=&end_date=

| 参数名称 | 参数说明 | 长度要求 | 是否必须 |
| --- | --- | --- | --- |
| q | 查询关键字 |   | Y |
| exclude | 排除关键字 |   | N |
| type | 可以搜索的关键字类型，包括地图、图层、用户（default = all） |   | N |
| start\_date | 元数据开始日期 |   | N |
| end\_date | 元数据结束日期 |   | N |

注：之前理解有误，在查看demo之后发现group是设置分组查看权限，不是用户权限设置。

>'Public: Any registered user can view and join a public group.
'public-invite (invite-only):Any registered user can view the group.  '
                         'Only invited users can join. '
'Private: Registered users cannot see any details about the group, including membership.  '
                         'Only invited users can join.')


  [1]: ./images/geonode_component_architecture.png "geonode_component_architecture.png"
