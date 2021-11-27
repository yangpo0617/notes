最近在项目中遇到了关于PHPUnit测试时速度优化的问题，记录下来，防止以后再踩坑。

##问题描述
项目采用的是[lumen](https://lumen.laravel.com)框架。程序采用六边形架构，核心代码分为三层：
```
core
| ------ Application
| ------ Domain
| ------ Infrastructure
```
一开始我才用的是默认方法，使用sqlite在内存中跑测试用例，一直都没有遇到问题， 配置如下
```
config/database.php
'connections' => [
    'testing' => [
        'driver' => 'sqlite',
        'database' => ':memory:',
    ]
]
```
在跑Domain Layer的测试用例的时候不会出现什么问题，因为写的都是Unit Test, 数据全都是Mock的，不会跟数据库产生任何交互，所以跑起来速度很快。
但是Application Layer 和Infrastructure Layer写的是Integration Test， 是要跟数据库产生交互的，这就导致跑完全部测试会花费很长时间， 尤其是当项目越来越大，测试越写越多，这个问题尤其明显。

##问题分析
当使用内存运行测试的时候，当运行每一条测试之前，内存都会被释放清空，这就导致我们需要在运行每一条测试之前，重新构建数据库。PHPUnit的`setUp()`方法可以让我们填充在每一条测试开始前要运行的代码。
我最开始是在`setUp()`里面重构数据库，以便在每一个测试开始之前，在一个新的内存环境中拥有一个初始化的数据库以便跑测试。

这里我用的是doctrine
```
public function setUp()
{
    parent::setUp();
    $this->artisan('doctrine:schema:create');
}
```
这样做没有任何问题，但是随着项目越做越大，测试越写越多，每一条测试开始之前都要重复进行重构数据库的工作，这就导致了跑完整个测试需要非常多的时间。

##问题解决
这里有两个问题需要解决优化
1. 内存中跑测试
2. 每一个测试开始前都要重构数据库

因为用内存跑的话，每一个测试开始前都要释放内存，这就导致了我们不得不重构数据库。解决办法是在自己创建的file中跑测试，这个方法的好处是可以在整个测试过程中保留这个文件，从而保留数据库结构。

首先更改设置让PHPUnit跑在指定文件里
```
config/database.php
'connections' => [
    'testing' => [
        'driver' => 'sqlite',
        'database' => 'base_path('database/testing-database.db')', // 路径及文件名自定义
    ]
]
```
测试文件你可以提前手动创建，也可以在程序初始化的时候自动创建，我选择是在bootstrap/app.php中创建
```
if (env('TEST_ENV')) { //判断是否为测试环境，环境变量TEST_ENV在phpunit.xml中定义
    $testingDbFile = base_path('database/testing-database.db');
    if (!file_exists($testingDbFile)) {
        touch(base_path('database/testing-database.db'));
    }
}
```

在运行PHPUnit的时候，跑**首个**测试之前，我们需要清空文件，因为文件中可能包含上一次测试的数据，然后在文件中构建我们的数据库（只会运行一次，即跑第一个测试前）
```
protected static $setUpDb = false;

public function setUp(): void
{
    parent::setUp();
    if (!self::$setUpDb) {
        $dbFile = base_path('database/testing-database.db');
        $file = fopen($dbFile, 'w+');
        ftruncate($file, 0);
        fclose($file);
        $this->artisan('doctrine:schema:create');
        self::$setUpDb = true;
    }
}
```
这样，第一个问题我们就解决了，我们使用文件来代替内存跑测试，但是第二个问题我们只解决了一半。数据库是不用在每个测试开始前重构了，但是这会导致下一个测试开始的时候，数据库里依然存在上一个测试创建的数据，这会导致很多潜在的问题，我们希望每个测试开始前，数据库都是干干净净的。
解决方式是在每一个测试结束后rollback所有的transaction，还原数据库到测试开始前的样子。
在`setUp()`中加入：
```
public function setUp(): void
{
    // ...
    $registry = app(IlluminateRegistry::class);
    $this->entityManager = $registry->getManager('default');
    $this->entityManager->getConnection()->beginTransaction()；
}
```
这样会在每一个测试开始前创建事务

PHPUnit还有一个方法`tearDown()`，可以让你在每个测试结束后自定义要运行的代码，这里我们定义在每个测试结束后，回滚所有事务
```
public function tearDown(): void
{
    parent::tearDown();
    $this->entityManager->getConnection()->rollBack();
    $this->entityManager->getConnection()->close();
}
```
##总结
在做完以上优化后，我跑测试的时间从原来的5分钟左右缩短到了不到一分钟，可以说是质的变化。

总的来说就是优化了两个问题：
1. 用文件代替内存
2. 每个测试开始前不用重构数据库，所有测试数据在这次测试结束后回滚，保持数据库干净，以便接下来的测试

虽然我举得代码的例子是针对lumen的，但是这个思路可以应用在所有框架。
