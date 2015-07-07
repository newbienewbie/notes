# Symfony Service Container

SOA(service-oriented architecture)是个比较有意思的想法。但是如何实例化这些服务类？直接在需要使用的地方实例化会产生严重的依赖，造成类与类之间的过度耦合。

根据IoC原则，我们需要把Service类实例化的控制权及义务交给第三方(即IoC Container)去完成,而具体服务的使用者只是负责使用服务完成相关逻辑。

IoC有三种常见实现方案，这里只专注于DI。

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
    ->addMethodCall('setMailer',array(new Reference()));


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




