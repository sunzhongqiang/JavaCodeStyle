# 代码风格
## 分层和职责以及命名

* controllerweb：接受参数，调用业务逻辑实现功能。
* service业务逻辑接口层，定义业务逻辑接口，其实现层为：Service.impl
* dao/repository数据访问层，定义数据访问接口，其实现层为：dao.impl/repository.impl
* model:业务实体层，定义所有的业务实体。
* util:工具类包

> 如果模块较多时,先划分模块再划分包
 
## 类的命名

* controller层的类的命名为：Model+Controller
* service层那个的类的命名为：Model+Service
* service.impl层的类的命名为：Model+Service+Impl
* dao->Model+Dao
* dao.impl->Model+Dao+Impl
* repository->Model+Repository此层需要继承JpaRepository接口，不需要实现
* model->Model需要符合JPA规范
* util->工具名称

## 包的导入
> 代码写完之后应该检查有无导入多余的包，如果有需要进行重新组织一下包的导入,排列顺序按照阿拉伯字母的顺序进行排列
## WebController层规范

> 需要调用的业务类写在顶部,以后业务类只能调用Service

## 业务方法命名：
* save/update:保存或者修改数据
* edit/add：编辑和新增
* delete/remove/del:删除
* list/query:列表查询
* exits:判断是否存在
* loadBy:
* findBy:
> 其他：业务名称动词getter/setter的特殊性：赋值，传值，可以直接暴露给页面上的数据，所以处理的业务方法不能使用get和set开头。

## 参数

* 接收前台页面参数，全部放在参数里面。
1.分页查询时，一般查询参数pageable放在最后
2.保存和修改的时候，使用实体来接收参数
3.编辑时、删除时，传递主键
4.如果需要传递多个（4个以上）参数，应该使用实体或者map来接收参数，应该避免逐一罗列参数。总体参数个数保持5个一下。

* 特殊情况：
> 时间参数的传递和处理：如果时间组件可以传Date，用date接受，如果不能使用String接收，然后使用工具类转换。
文件上传

## 结果和页面返回
> 返回页面和数据，使用ModelAndView,页面和数据放在ModelAndView里
## 业务处理
一般调用Service或者其他层来完成自己的逻辑，主要是接受参数，处理参数根据逻辑调用相应层完成需要的功能，或者获得需要的数据，并返回数据给页面。

## Servcie层业务服务类

> 为controller层提供服务，可以调用其他Service和其余支持dao层的类和repository层的类方法，参数，业务处理准守webcontroller层Dao,Repository层数据访问类
## 数据访问：
1、只写接口JpaRespositity
2、写jpql、hibernateCriteria
3、写SQL

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
