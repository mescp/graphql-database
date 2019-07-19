
# 业务模型设定义

> 模型设计参加 prisma [设计文档](https://www.prisma.io/docs/datamodel-and-migrations/datamodel-MYSQL-knul/) 

# 业务模型设计约束

## 必选字段

    """
    基础接口
    """
    interface Base {
        "唯一ID"
        id: ID! @unique
        "创建时间"
        createdAt: DateTime!
        "更新时间"
        updatedAt: DateTime!
    }

## Schema 设计建议

1. 字段命令

  编码字段
  > 在本模型中的编码,用code,引用外部模型中的code,写[type]Code形式.如:

``` graphql
    """
    配方
    """
    type Formula {
        "ID"
        id: ID! @unique
        "配方编码"
        code: String!              --- 本模块内的编码
        "产品编码"
        productCode: String!       --- 引用外模块的编码
    }
```

2. 枚举

    对于模型属性是可枚举的数据,采用枚举方式,如:

``` graphql
    """
    性别
    """
    enum SexType{
        "男"
        MALE
        "女"
        FEMALE
        "其他"
        OTHER
    }
```

3. 基础数据

    对于模型属性是基础数据的应用,并且后期可能会有变化情况,采用字典表.
    字典表使用以group分组,code为唯一键维护,在审计跟踪相关的表结构中使用以code为存储内容.
    如:

``` graphql

    """
    配方追溯
    """
    type TraceFormula {
        "工作中心"
        workCenter: String!
        "工作站"
        workStation: String!
    }


```

4. 添加注释

    schema对象的注释用`"""`表示可以换行,字段的注释有`"`不能换行

``` graphql
    """
    用户组
    用户分组，菜单和功能操作权限对应用户组进行分配
    """
    type UserGroup implements Base {
        "ID"
        id: ID! @unique
        "用户组编码，不能重复"
        code: String! @unique
        "用户组名称"
        name: String!
        "用户列表"
        users: [User!]!
        "其他描述信息"
        description: String
        "创建时间"
        createdAt: DateTime!
        "更新时间"
        updatedAt: DateTime!
    }
```

5. 添加默认值

    通常枚举类型和布尔类型的字段都需要默认值

``` graphql
    """
    人员信息
    一个人员对应一个用户
    """
    type Person implements Base {
        "性别"
        sex:SexType! @default(value:MALE)
    }


    """
    配方物料
    """
    type FormulaMaterial {
        "是否车间称量"
        isMfgWeight: Boolean! @default(value:true)
        "是否包装间称量"
        isPackWeight: Boolean! @default(value:true)
    }

```

# 模块与模块设计约束

> 原则: 高内聚,松耦合

1. 模块间关联

    - 重要数据进行冗余复制,包括编码复制
        如: 称量和配方关系
    - 模块间引用需求通过编码关联
        如: 称量中的称量用户,通过编码关联

2. 模块与数据字典

    数据字典通过引用关系完成. 例如:workCenter

3. 模块内关系

    模块内的对象间通过引用完成
    如: 物料与配方关系

# 多对多关联

> 结合数据存储和业务需求,多对多实体关系删除操作如下约束:

- 多对多,服务端在数据存储上是不包含强制引用关系的,所有也不会自动抛出错误.
- 反言之,服务端在数据存储上是互相强制引用关系,那么添加后的数据将永远删除不了.

所以,对多对多的数据操作要判断: 什么时候是断开链接,是什么时候是删除.

1. 在表1中操作表2时应该是断开链接
2. 在表2中操作表1时应该是断开链接
3. 在表1中删除表1时要判断是否表2中有引用,如有引用不能删除
4. 在表2中删除表2时要判断是否表1中有引用,如有引用不能删除

