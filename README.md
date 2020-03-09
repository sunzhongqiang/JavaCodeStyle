# 架构风格规范(Java Project base on Spring boot 2.0)

-------
底层pom依赖：[pom.md](pom.md)
-------

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

* common:为公共层定义公共的数据和方法
* config:为配置层定时项目的配置
* model:业务实体层，定义所有的业务实体。
* dto:数据传输层，用于隔离实体；
* dao:数据访问层，主要是查询接口和需要性能优化的接口，定义数据访问接口，其实现层为
* dao.impl：为数据方式实现层，具体的数据访问层的实现，只完成数据的访问，数据的新增变更放到repository中
* repository 数据接口访问层对外暴露的所有的数据访问接口，对数据保存和修改的接口，同时代替dao层向外暴露接口
* service：业务逻辑接口层，定义业务逻辑接口
* service.impl 为service的具体实现层，具体的业务代码逻辑实现
* web.controller：接受页面传来的参数，调用业务逻辑实现功能，并返回给页面结果数据。
* web.interceptor ：
* web.filter等存放对应的类实现

* util:工具类包
* exception:封装的业务异常包
> 如果模块较多时,先划分模块再划分包
 
## 类的命名
* 类名使用大驼峰来进行命名，变量使用小驼峰进行定义
* model：根据数据库表名，按JPA的Entity规范进行定义
* dto：{Model}+Dto 数据传输对象
* dao：{Model}+Dao 数据访问接口对象
* dao.impl：{Model}+DaoImpl 数据接口访问实现
* repository：{Model}+Repository：数据仓库访问入口：此层需要继承JpaRepository接口，不需要具体的实现，jpa层会更加需要动态执行代码
* service：{Model}+Service：服务层接口
* service.impl：{Model}+ServiceImpl：服务层实现类
* controller：{Model}+Controller：web控制层类
* util：{Function}+Utils：工具类名称:功能聚合类的命名方式

## 包的导入
> 代码写完之后应该检查有无导入多余的包，如果有需要进行重新组织一下包的导入,排列顺序按照阿拉伯字母的顺序进行排列


## 方法命名规范：
> 方法的命名使用驼峰法，一般使用动词或动词+名词组合。
设置/获取某个值的方法，命名为set<Value>/get<Value>
返回长度的方法，命名为length
判断布尔值的方法，命名为is<Value>
将对象转换为某个特定类型的方法应该命名为toT
注意动词和名词的使用
 
以下的方法为推荐的用法：
* save/update:保存或者修改数据
* edit/add：编辑和新增
* delete/remove/del:删除
* list/query:列表查询
* exits:判断是否存在
* loadBy:
* findBy:
> 其他：业务名称动词getter/setter的特殊性：赋值，传值，可以直接暴露给页面上的数据，所以处理的业务方法不能使用get和set开头。

## 参数说明
全局参数如：Pageable，Page，Dto，Model，List，Map，基本数据类型，是用来进行层与层之间传输之用，各个层都可以使用；
局部参数如：ResultData，主要是用来在web层使用封装处理数据的结果使用，不要进行跨层使用；Model尽量不要用在Web层接受参数使用，而是使用Dto；


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
> 文件上传：请使用专用的文件上传接口，然后只传递文件路径，而不是重新编写文件上传接口

#### 业务处理
一般调用Service或者其他层来完成自己的逻辑，主要是接受参数，处理参数根据逻辑调用相应层完成需要的功能，或者获得需要的数据，并使用ResultData封装数据返回JSON给页面。

#### 结果和页面返回
> 返回页面和数据，使用ModelAndView,页面和数据放在ModelAndView里
> 前后端分离后请使用，简单结果直接返回，复杂结果使用ResultData封装后返回JSON


## Servcie层业务服务类

> 需要严格执行接口隔离原则，接口的定义和实现要分开，注释要写在接口上；
> 该层是业务逻辑服务层，一些复杂的业务逻辑在此层实现，但是不要处理web容器相关和数据层相关的逻辑：
> 容器相关的参数如Servlet，Session，Request，Response等，需要web容器层处理好后传递过来，不要直接传递到service，防止污染和依赖容器环境
> 数据层相关业务逻辑，需要调用数据层进行处理
> serviced调用Repository来获取和处理数据,此层不要在直接调用DAO层，而是使用Repository进行隔离数据层
> 为controller层提供服务，可以调用其他Service进行业务交互，也可以调用repository层的类方法进行数据操作和获取，参数，业务处理准守web层访问service层，service层访问Repository层的顺序

#### 参数的接受：
* 基本类型
* 实体类型
* 但是不要有容器相关的类型（请在web层，获得数据并以基本类型或者数据传输进行传递）
#### 业务处理：
* 数据的封装，数据的处理
* 业务逻辑的实现
* 事务的处理
* 其他接口的调用
#### 返回结果：
* 分页结果
* 单一结果
* List ，Map ，实体，Dto 等
* 尽量不要是多结果的聚合类，如ResultData

## repository 数据访问：
* 读写分离，repository负责写改删，负责简单的读取查询，dao负责复杂的查询，
* repository接口继承JpaRespositity 和 dao 接口 ，只有dao接口可以进行实现复杂的查询提供具体的方法
* 对外只暴露 repository接口
* 简单的写方法使用 repository 提供的接口方法


## Dao层规范

> 需要严格执行接口隔离原则，接口的定义和实现要分开，注释要写在接口上；接口统一有repository进行对外暴露；
> 该层是对数据的处理，主要是查询的方法，不要写数据修改的方法，请在Repository中进行数据修改；
> 单表查询写jpql、hibernateCriteria
> 复杂的查询写SQL，并对结果进行封装

#### 参数的接受：
接受上一层的参数，进行查询数据，并返回结果
#### 业务处理：
对数据的的curd
#### 返回结果：
* 分页数据
* 列表数据
* 实体数据
* Map数据
* 数组数据


## 日志处理
```
 privateLoglog=LogFactory.getLog(getClass());
 log.debug("功能没有数据库管理，允许访问");
 log.info("");
 log.error("");
 log.fatal("");
 ```
 日志基于logback的日志处理，日志输出到数据库中[logback setting](logback-setting.md)

## 事务管理
* 所有的事务在Service中定义，需要事务的时候使用@Transactional进行开始事务
* 如果需要捕获错误，try-catch记录错后后，继续抛出错误。

### 异常和错误处理规范

### [querydsl的使用](querydsl的使用)
