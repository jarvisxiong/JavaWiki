有意义的名称        变量名应该尽量表达他表示什么，用来干什么以及怎么用。
            避免误导， 比如两个意思差不多的变量，拼写也差不多。或者L和o很容易与1和0混淆
            做有意义的区分，主要是方法名，  比如getAccount()和getAccountInfo()就不知道有什么区别，看起来像一个方法。
            用能够读得出来的词，不用自创的词
            避免魔鬼数字
            不要用前缀表示成员变量或者是static等变量。编译器会改变颜色的。
            区分接口和实现，接口用I开头或者是able结尾，用Impl结尾。最新的版本又说不要加I
            类名应该是比较有具体意义的名词，不要用太广泛的名词，或者是动词
            方法名：动词或者是动词短语。   
            如果是用来构造自己对象的方法应该在名称中描述参数：Complex fulcrumPoint = Complex.FromRealNumber(23.0);
            用一致的名字风格，比如get都表示提取，set都表示赋值，在不同的类和包中都是用这样的命名规范。
            在满足上一条的同时要注意一些双关的此，不要引起误会。
            使用业内的术语名称
            使用其他领域的名称，可以通过问其他领域的人了解到。比如IMS可以百度到。
            使用方法名添加语境。比如在这样的情况下if(){对多个变量赋值}else if(){对多个变量赋另外一组值}可以把赋值过程提升为方法。(代码重构中有讲过)
             添加语境，比如firstName 是一个地址的还是一个人名的，好的方式是封装到类里面， 如果不行可以加上addr前缀
            去除不必要的语境，比如一个地理系统，所有的类名都加上GS。。。这样很难查找。

方法：小，不多于20行
        只做一件事，  做一件事的代码一般无法被合理的分割为多个区段。
        if语句应该只有一行，如果实在是太多条件，那么就换成一个带有很好名字的方法。
        方法重的语句在同一个层级，比如有的是getHtml()中的这种高层级的，或者String a = b.getA()这样中层级的，和a.append()的很低层级的。
        向下原则：每个函数后面都跟着位于下一层级的函数，这样查看函数列表的时候就能够按照顺序阅读了。
        多态代替switch， switch违反了单一职责和开放关闭原则。 加入工厂模式
        方法的名称要很好的描述方法的功能
        方法参数，应该尽量少，多个可以在封装为一个类。不要使用跟方法名不是同一个级别的参数，比如用StringBuffer。
        方法不要传入状态等标志参数，特别是boolean值，这种应该分成两个方法。
        方法要没有副作用，即在进行计算的同时修改了我们本来不想修改的值。
        把指令与询问分开， 既boolean set("username", "b")分成两个
        用异常代替错误码。这个有问题，异常和错误码是个很难分的问题
        单独找个地方处理异常， 实际上就是为了异常处理单独弄一个方法而该方法第一行是try，最后是catch
        使用异常还有一个好的地方是不用重新编译和全部部署。
        try catch应该包的尽可能准确，并且异常应该尽可能的小。
 
Comments
不给bad code加注释，不需要为不好的代码费尽心思去说明用途和解释，而是应该重构他。
自注释，需要加注释的地方可能就是需要提出为方法的地方。
一些应该加的注释：
    法律效益的，比如作者，公司，权限等等。
    资料性的，比如对方法返回值的说明，如果方法名不能正确的表达出返回值的意思，则可以使用注释，又比如该方法是格式化日期的，但是格式化之后的格式是无法再方法名中体现的，所以需要注释。
    警告意味的，比如大开销的方法。
    TODO
    增大地位，比如通常的trim方法很不起眼，但是一旦疏忽将会引起bug.
    分支注释
    公共API中的注释
不好的comments
    含糊不清的
    多余的：比如跑出了一个异常
    因为规定而加的：比如有的要求每个方法必须有注释。
    清单注释：记录所有的修改记录。这个应该用svn管理，而不是注释。
    条幅注释：比如我爱用的//------------------getter,setter,不要乱用
    }后注释：标记一个try或者是一个分支已经结束了。  这个应该使用缩进，并且这将意味着方法国语复杂。
    HTML注释：应该是doc生成工具的责任。
 
格式化
    单个类500行
    可以按照期望的阅读报纸或者杂志的格式来写。清楚的标题，提纲和详尽的细节。
    方法间单行换行
    关系比较近的类应该也放的比较近，尽量避免让阅读者读一个功能在不同的包和类之间跳来跳去。
    局部变量的声明应该尽量接近他们的用法，应该让他们看起来生命周期越短越好。    
    从属：调用者在被调用者之上
    亲和力代码：比如名字相近，所有的get,或者是功能相近的比如junit中的assert方法族。
    不要对齐=因为容易看串行
    缩进，必须的，并且用空格而不是tab
 
对象和结构
    对象应该隐藏数据而暴露操作。
    DTO， 这个是个例外，暴露了结构。这个类不应该包括操作，而是应该用单独的类来定义操作。
    模块不应该了解他所操作的对象的内部结构
    上面这一条带来了一个问题是火车调用a.getB().operator3()。这种，如果getB()仅仅是一个数据结构不含有操作还是没有问题的。可以这样看，如果能够使用#{a.b.c}来标示的就不会有问题。
    
错误处理
    用异常不用返回值。有可能丢失错误产生者。
    异常要包含足够的信息找到异常源，和其他有用的信息。
    异常要有比较好的分类，比如Io
    打包第三方包的异常，这是一个比较好的做法，可以降低对第三方包的依赖
    用默认代替异常分支处理。catch部分要写的赋值就应该是默认值。就是把try(a = b.getA(); c = a.getOne())catch(){c = b.getTow()}这个改称为a.getOne()的正常时返回one,异常时返回tow.
    不要传递null作为参数
    返回列表的时候用空的代替null。可以防止异常。
 

## 边界
   第三方api， 比如map，我们可能不想删除但是有clear对象，这个时候我们可以用代理模式进行封装，只开放getf方法。
    

## 单元测试
    TDD三定律：编写不能通过的单元测试前，不编写生产代码。
        只编写刚好不通过的单元测试
        只编写刚好单元测试通过的代码

类
    类的结构：从上往下是啥是啥。
    类要小
    类The Single Responsibility Principle
    要有凝聚力。成员变量和方法紧密的组合成了一个整体的东西。
    依赖倒置
    开放关闭          开放关闭和单一职责的这两个，  很难理解，其实可以理解成面向接口编程， 常用多肽，把新增方法改成新增一个实现类。
    单一职责

## 一些味道
### 注释
不必要的信息，  比如更新记录，作者等，这些应该是在git中
废弃或者过时的注释
冗余   就是不能表示比代码更多的注释
注释掉的代码
### 环境
  快速构建 maven
快速测试 JUNIT ，Suit . robotframeword等

### 函数
 过多参数
标志参数
死方法  永不被调用的方法

### 一般性的问题
  明显的行为没有被实现， 最简单的场景是  jar包实用的人期望有常量或者枚举的，但是没有，需要猜。
不正确的边界行为，  做边界测试。
重复
在错误的抽象层级上的代码， 一般是抽象类的层级较高， 派生类较低。
子方法靠近被调用的对象
本地变量作用域尽量短
前后不一致              一些列相关的方法是用一致的命名规范
人为耦合  引起的原因是把变量，常量方法放到了最方便使用的地方，但是不是应该在的地方。要换点心思想一想啊，在什么地方声明方法，变量，常量
特性依恋。 比如我们的NewsRo.trans() NewsRo依赖了News. 这个trans方法其实更应该
用多个方法代替标志参数
使用解释性变量， 比如add(a.split("/")[0], a.split("/")[1])改为 先用两个变量接收一下a.split然后再add(value1, value2)
要理解方法包含的具体算法
逻辑依赖而不是物理依赖。 这个是说  a类中依赖b，就应该是全依赖，而不能假定b中的情况，比如a中有这样的代码if(b.getSize() < SIZE)这个SIZE就应该是b.getMaxSize()这样的结构，而不是假定他。
多台代替ifelse和switch
防魔鬼数字
封装条件， 就是把if(a && b) 中的a && b替换成一个方法
尽量不要再条件中用！这个符号
强制时序    比如有两个方法，在另外一个方法中可以调用a();b();也可以b();a()但是后面这种是要报错的。 可以让a()放回一个b需要的参数，这样来保证时序。  c = a(), b(c);
边界条件集中于一点，不要散列。   比如 if(a + 1) {b(a + 1)}就应该先a + 1赋值给b.
常量值放在较高的层次。  
用直接的方法代替传递方法， 比如用a.calcute() 代替 a.getA().getB().add()这样的结构

### Java
通过使用通配符避免过长的导入清单就是，import cn.thecover.*
不要集成常量，直接用  类名.
枚举代替常量
    