# 架构风格规范(Java Project base on Spring boot 2.0)

## 前后端分离

前端代码和后端结果要有清晰的边界，后端API提供接口和返回，使用Restful风格提供接口，使用JSON返回数据；
前后端通讯的格式和协议
```
{
  'tradeItem_query':{
    description:'对应的查询方法，综合查询',
    method:'GET',
    url:'/tradeItem'
  },
  'tradeItem_get':{
    description:'对应的单个对象方法，一般传递主键',
    method:'GET',
    url:'/tradeItem/{id}'
  }, 
  'tradeItem_save':{
    description:'对应的数据保存和修改方法，一般对应的业务数据',
    method:'POST',
    url:'/tradeItem'
  },
  'tradeItem_update':{
    description:'修改方法，参数在body中一般对应的业务数据',
    method:'PUT',
    url:'/tradeItem'
  },
  'tradeItem_delete':{
    description:'对应的数据删除方法，一般传递主键',
    method:'DELETE',
    url:'/tradeItem/{id}'
  }
}
```
## 驼峰命名

* 将所有字母都小写(包括缩写)，然后将单词的第一个字母大写。
* 每个单词的第一个字母都大写，来得到大驼峰式命名。
* 除了第一个单词，每个单词的第一个字母都大写，来得到(小)驼峰式命名。

## 分层和职责以及命名

* web.controller：接受页面传来的参数，调用业务逻辑实现功能，并返回给页面结果数据。
* web.interceptor ：
* web.filter等存放对应的类实现
* service：业务逻辑接口层，定义业务逻辑接口，其实现层为：service.impl
* dao:数据访问层，主要是查询接口和需要性能优化的接口，定义数据访问接口，其实现层为：dao.impl
* repository 数据接口访问层对外暴露的所有的数据访问接口，对数据保存和修改的接口，同时代替dao层向外暴露接口
* model:业务实体层，定义所有的业务实体。
* util:工具类包

> 如果模块较多时,先划分模块再划分包
 
## 类的命名
* 类名使用大驼峰来进行命名，变量使用小驼峰进行定义
* model 根据数据库表名，按JPA的Entity规范进行定义
* dao->Model+Dao
* dao.impl->Model+Dao+Impl
* repository->Model+Repository此层需要继承JpaRepository接口，不需要具体的实现，jpa层会更加需要动态执行代码
* controller层的类的命名为：Model+Controller
* service层那个的类的命名为：Model+Service
* service.impl层的类的命名为：Model+Service+Impl
* util->工具名称

## 包的导入
> 代码写完之后应该检查有无导入多余的包，如果有需要进行重新组织一下包的导入,排列顺序按照阿拉伯字母的顺序进行排列


## 业务方法命名：
* save/update:保存或者修改数据
* edit/add：编辑和新增
* delete/remove/del:删除
* list/query:列表查询
* exits:判断是否存在
* loadBy:
* findBy:
> 其他：业务名称动词getter/setter的特殊性：赋值，传值，可以直接暴露给页面上的数据，所以处理的业务方法不能使用get和set开头。


## web.controller层规范

> 在该层要处理请求的参数，进行必要的web层处理，session sevlet，request等参数的获取和验证，然后调用的业务类来进行业务处理，需要的业务类写在成员变量中只可以自己访问，不能暴露,只能调用Service业务类不能够调用 repository和dao层中的类和方法，业务类处理完业务返回数据，并对结果进行封装返回给请求方；

#### 参数

* 接收前台页面参数，全部放在参数里面。
* 分页查询时，一般查询参数pageable放在最后
* 保存和修改的时候，使用实体来接收参数
* 编辑时、删除时，传递主键
* 如果需要传递多个（4个以上）参数，应该使用实体或者map来接收参数，应该避免逐一罗列参数。总体参数个数保持5个一下。

* 特殊情况：
> 时间参数的传递和处理：如果时间组件可以传Date，用date接受，如果不能使用String接收，然后使用工具类转换。
文件上传

#### 业务处理
一般调用Service或者其他层来完成自己的逻辑，主要是接受参数，处理参数根据逻辑调用相应层完成需要的功能，或者获得需要的数据，并返回数据给页面。

#### 结果和页面返回
> 返回页面和数据，使用ModelAndView,页面和数据放在ModelAndView里


## Servcie层业务服务类

> 为controller层提供服务，可以调用其他Service进行业务交互，也可以调用repository层的类方法进行数据操作和获取，参数，业务处理准守web层访问service层，service层访问Repository层的顺序

## repository数据访问：
* 读写分离，repository负责写改删，负责简单的读取查询，dao负责复杂的查询，对外只暴露 repository接口
* repository接口继承JpaRespositity 和 dao 接口 ，只有dao接口可以进行实现复杂的查询提供具体的方法
* 简单的写方法使用 repository 提供的接口方法
* 单表查询写jpql、hibernateCriteria
* 写SQL

## 日志处理
```
 privateLoglog=LogFactory.getLog(getClass());
 log.debug("功能没有数据库管理，允许访问");
 log.info("");
 log.error("");
 log.fatal("");
 ```

## 事务管理
* 所有的事务在Service中定义，需要事务的时候使用@Transactional进行开始事务
* 如果需要捕获错误，try-catch记录错后后，继续抛出错误。
