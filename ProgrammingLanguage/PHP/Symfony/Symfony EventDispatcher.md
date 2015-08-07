# Symfony EventDispather

## 简介

EventDispather的dispatch()方法会根据某一个事件对应的Listeners按优先级逐一进行调用。EventDispather支持两套风格的添加事件处理程序的绑定。最常用的是addListener(),这是一种快速编码的回调风格，类似于JavaScript中的回调函数。还有一种使用Subscriber对象的风格。

## Listener

添加监听器的方法原型为：
```PHP
addListener($eventName,$listener,$priority=0)
```

传递给addListener的第二个参数是callable对象,类似于JavaScript的回调函数:

```PHP

$dispather->addListener(
    'foo.action',
    array($listener,'onFooAction')       //a PHP callable
);

$dispather->addListener(
    'bar.action',
    function(Event $event){              //a PHP callable
        //...
    }
);
```

## Subscriber

EventDispather还支持另外一种风格的监听绑定：

```PHP
addSubscriber(EventSubscriberInterface $subscriber);
```
此方法接受一个实现了EventSubscriberInterface接口的对象作为参数。该接口非常简单：


```PHP
interface EventSubscriberInterface
{
    public static function getSubscribedEvents();
}
```
   
接口方法getSubscribedEvents()返回一个数组，该数组可以是三种方式中的一种：
    
 * array('eventName' => 'methodName')
 * array('eventName' => array('methodName', $priority))
 * array('eventName' => array(array('methodName1', $priority), array('methodName2'))
    
addSubscriber()方法会自动检测接收到的$subscriber对象并解析出合适的方法名，再通过过addListener()方法完成事件绑定监听。其源码及实现分析如下：

```PHP

    public function addSubscriber(EventSubscriberInterface $subscriber)
    {
        foreach ($subscriber->getSubscribedEvents() as $eventName => $params) {

            //$subscripter->getSubscribedEvents   返回的数组类似于: 
            //    array(
            //        'eventName'=>"methodName",
            //         //... 
            //    )
            if (is_string($params)) {
                $this->addListener($eventName, array($subscriber, $params));
            }

            //$subscripter->getSubscribedEvents   返回的数组类似于: 
            //    array(
            //        'eventName'=>array("methodName",$priority),
            //         //... 
            //    )
             elseif (is_string($params[0])) {      
                $this->addListener($eventName, array($subscriber, $params[0]), isset($params[1]) ? $params[1] : 0);
             } 

            //$subscripter->getSubscribedEvents   返回的数组类似于: 
            //    array(
            //        'eventName'=>array(
            //            array("methodName1",$priority)
            //            array("methodName22")
            //         ),
            //        //... 
            //    )
             else {
                foreach ($params as $listener) {
                    $this->addListener($eventName, array($subscriber, $listener[0]), isset($listener[1]) ? $listener[1] : 0);
                }
            }
        }
    }
```

可以看到，addSubscriber()始终都是把形如`array($subscriber,$methodName)`这样的callable传递给addListener。显而易见，一个Subscrber应该有如下的形式：

```PHP
class StoreSubscriber implements EventSubscriberInterface{
    
    public static function getSubscribedEvents(){
        return array(
            'kernel.repsonse'=>array(
                array('onKernelResponsePre',10),
                array('onKernelResponseMid',5),
                array('onKernelResponsePost',0),
            ),
            'store.order'=>array('onStoreOrder',0)
        );
    }


    public static function onKernelResponsePre{
        //...
    }

    public static function onKernelResponseMid{
        //...
    }

    public static function onKernelResponsePost{
        //...
    }

    public static function onStoreOrder{
        //...
    }
}
```





