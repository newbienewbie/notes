# Symfony Translation

翻译的过程可以理解为从`source`到`target`的过程。Symfony/Translation这个组件的工作流程大致可以分为三步：

0. 创建翻译器
1. 为翻译器加载资源: 利用资源加载器加载资源(`source`到`target`的消息映射关系）
2. 根据`domain`、`locale`及对应的`message`给出译文`translation`。  

## 创建翻译器

翻译器的创建非常简单，仅需提供一个默认的`locale`值：

```PHP
use Symfony\Component\Translation\Translator;

$translator=new Translator('fr_FR');    #默认locale='fr_FR'
```

所谓`locale`，大致可以当成一个指代用户语言和国家/地区的字符串。推荐用
`ISO639-1LanguageCode_ISO3166-1Alpha-2CountryCode>`
这样的格式来表示。比如：`fr_FR`。

## 翻译资源的加载


### Translation Resources

Translation Resources定义了一组从源(`source`)到目标`target`的消息映射关系。这种映射关系可以用不同的形式来表达，比如XML：

```XML
<?xml version="1.0"?>
<xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
    <file source-language="en" datatype="plaintext" original="file.ext">
        <body>
            <trans-unit id="1">
                <source>Symfony is great</source>
                <target>J'aime Symfony</target>
            </trans-unit>
            <trans-unit id="2">
                <source>symfony.great</source>
                <target>J'aime Symfony</target>
            </trans-unit>
        </body>
    </file>
</xliff>
```

又如YAML：
```YAML
Symfony is great: J'aime Symfony
symfony.great:    J'aime Symfony
```

再如PHP的Array：
```
return array(
    'Symfony is great' => 'J\'aime Symfony',
    'symfony.great'    => 'J\'aime Symfony',
);
```

当然，还支持JSON等其他形式——只要他们表达了这种映射关系，即可以被当做一个Translation Resources。

#### 消息占位符

上面例子中的`%...%`这种表达（如`%name%`）是一种消息占位符。这种占位符机制可以动态映射一些message到translation。


#### 复数形式

借助占位符和管道符可以很好的表达复数形式。

```
'There is one apple|There are %count% apples'
```

有时候我们需要借助于区间获取更多的控制：

'{0} There are no apples|{1} There is one apple|]1,19] There are %count% apples|[20,Inf[ There are many apples'

区间表达遵循`ISO 311-11`的记法规则,可以是两端(可以借助于-Inf和+Inf表达正负无穷)之间的数字，还可以是有限个数字的集合如: 
```
{1,2,3}
```


### 资源加载的过程：

1. 设置资源加载器
2. 利用资源加载器加载资源

Symfony支持不同的资源加载方法，:

* ArrayLoader
* CsvFileLoader
* IcuDatFileLoader
* PhpFileLoader
* XliffFileLoader
* JsonFileLoader
* YamlFileLoader
* ...

所有的文件加载器都依赖Symfony/Config组件。

加载Translation Resources的示例代码为:

```PHP
$translator->addLoader('xlf',new XliffFileLoader());
$translator->addResource('xlf','message.fr.xlf','fr_FR');    # 默认domain为'messages'
$translator->addResource('xlf','message.fr.xlf','fr_FR','admin');
$translator->addResource('xlf','navigation.fr.xlf','fr_FR','navigation');

```

## 翻译过程

翻译实际上是分成两步完成的：

1. 从translation resources中加载翻译好的message一览表(catalog)
2. 从catalog中定位message并返回对应的翻译。如果定位不到，则返回原始message。

可以通过调用`ITranslator`接口提供两个关键的方法`trans()`或者`transChoice()`来执行这个过程。

```PHP
public function trans($id, array $parameters = array(), $domain = null, $locale = null);
public function transChoice($id, $number, array $parameters = array(), $domain = null, $locale = null);
```

如果不提供locale，trans()方法在默认情况下会使用fallback的locale，

```PHP
$translator->trans('hello, %name%',array('name'=>'Chicago'),'admin','fr_FR');
```


## 例子

来自官方文档的[一个例子](http://symfony.com/doc/current/components/translation/usage.html)：

```
use Symfony\Component\Translation\Translator;
use Symfony\Component\Translation\Loader\ArrayLoader;

$translator = new Translator('fr_FR');
$translator->addLoader('array', new ArrayLoader());
$translator->addResource('array', array(
    'Symfony is great!' => 'J\'aime Symfony!',
), 'fr_FR');

var_dump($translator->trans('Symfony is great!'))
```
