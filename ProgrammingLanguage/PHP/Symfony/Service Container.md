# Symfony Service Container

SOA(service-oriented architecture)是个比较有意思的想法。但是如何实例化这些服务类？直接在需要使用的地方实例化会产生严重的依赖，造成类与类之间的过度耦合。

根据IoC原则，我们需要把Service类实例化的控制权及义务交给第三方(即IoC Container)去完成,而具体服务的使用者只是负责使用服务完成相关逻辑。

## 在容器中创建/配置服务

```YAML
# app/config/services
parameters: 
    parameter_name: value

services:
    service_name:
        class: AppBundle\Directory\ClassName
        arguments: ["@another_service_name", "plain_value", "%parameter_name%"]
```

当然，我们还可以把其他Bundle的services配置文件导入到app/config/config.yml中去。

```YAML
# app/config/config.yml
imports: 
    - { resource: "@AcmeHelloBundle/Resources/config/services.yml" }
```

除了使用`imports`指令,还可以使用bundle内部的`service container extension`来导入配置。
这个service container extension是有bundle作者创建的一个PHP类，用以完成两件事：

1. 导入这个bundle所有需要的services container Resources
2. 提供语义化的、直截了当的配置，使得bundle可以在不用和bundle container configuration的扁平参数交互的情况下被配置。


## Dependency Injection

to be continued...





