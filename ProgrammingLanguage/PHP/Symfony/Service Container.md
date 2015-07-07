# Symfony Service Container

SOA(service-oriented architecture)是个比较有意思的想法。但是如何实例化这些服务类？直接在需要使用的地方实例化会产生严重的依赖，造成类与类之间的过度耦合。

更一般的讲，当一个类C中使用另外一个类S对象的时候，OOP的新手常常会在类C中直接实例化类S的对象或者寻找S的类对象(可称之为`依赖解析`)，从而产生了强烈的耦合关系。

IoC(Invention of Control)是一种能更好地解决这类问题的思路:

在C中只针对S声明一个接口对象,类C只负责接口编写逻辑代码,把对S的依赖解析过程从C中分离出去，交给第三方负责。

对比着两种思路，前者是C直接控制了S的实例化，然后再使用S;后者是只声明了SInterface接口对象，然后编写业务逻辑，并不负责S类依赖解析，甚至不管是不是S类(只针对接口编程)，换言之，是把对P依赖解析的控制权交给了第三方去完成。

这就是所谓的控制反转。根据IoC原则，我们需要把对Service类依赖解析的控制权及义务交给第三方(即IoC Container)去完成,而具体服务的使用者只是负责服务的使用。

IoC有三种常见实现方案，工厂模式是非常蛋疼的方式，这里只专注于DI。

## Dependency Injection
 
Symfony提供了DependencyInjection组件来负责依赖注入事宜。

对于一个类，
```PHP
class Mailer{


    private $transport;
    public function __construct($transport){

        $this->transport=$transport;
    }

}
```

可以利用这样的方法进行获得其服务对象：
```PHP
use Symfony\Component\DependencyInjection\ContainerBuilder;


$sc=new ContainerBuilder();
$sc->register('mailer',"Mailer")
    ->addArgument('sendmail');
```
对于已经注册为服务的类，可以用作其他服务的创建参数：
```PHP
use Symfony\Component\DependencyInjection\Reference;


$sc->register("newsletter_mgr","NewsLetterMgr")
    ->addArgument(new Reference('mailer'))
```

除了构造器注入，还支持setter注入：
```PHP

$sc->register('newsletter_mgr','NewsLetterMgr')
    ->addMethodCall('setMailer',array(new Reference('mailer')));


```

通过容易获取注册的服务对象很简单,利用get()即可：

```PHP
$svr=$sc->get('srv_name');
```


## 配置文件与依赖注入

除了使用PHP代码，我们还可以使用配置文件，如XML、YAML来定义服务。当然，这样一来，我们需要Symfony/Config组件。

加载XML配置文件：
```PHP
use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Loader\XmlFileLoader;
use Symfony\Config\FileLocator;


$sc=new ContainerBuilder();
$loader=new XmlFileLoader($sc,new FileLocator(__DIR__));
$Loader->load('services.xml');

```

加载YAML配置文件：
```PHP
use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Loader\YamlFileLoader;
use Symfony\Config\FileLocator;


$sc=new ContainerBuilder();
$loader=new YamlFileLoader($sc,new FileLocator(__DIR__));
$Loader->load('services.yaml');

```

如果确实想用PHP来创建服务：
```PHP
use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Loader\PhpFileLoader;
use Symfony\Config\FileLocator;


$sc=new ContainerBuilder();
$loader=new PhpFileLoader($sc,new FileLocator(__DIR__));
$Loader->load('services.php');

```

配置文件的格式类似于；

```YAML
parameters: 
    #...
    mailer.transport: sendmail

services: 
    mailer: 
        class: Mailer
        arguments: ["%mailer.transport%"]
    newsletter_mgr: 
        class: NewsLetterMgr
        calls:
            -[setMailer,["%mailer%"]]

```




