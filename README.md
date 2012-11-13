# Fest

## Данные и вывод

### fest:set

Объявить внутреннюю переменную

```xml
<fest:set name="name">John</fest:set>
```

```xml
<fest:set name="full_name">
  <fest:get name="name"/><fest:space/>F. Kennedy
</fest:set>
```

Для ```fest:set``` можно использовать атрибут ```test```. Операция выполнится, если его значение (js-выражение) истинно.

```xml
<fest:set name="name" test="false">should not be set</fest:set>
```

Внутри ```fest:set``` доступен контекст ```params```, передаваемый через ```fest:get```.

```xml
<fest:set name="line">
  Hello,<fest:space/><fest:value>params.username</fest:value>
</fest:set>
<fest:get name="line">{username: "John"}</fest:get>
```

### fest:get

Получить переменную, объявленную через ```fest:set```

```xml
<fest:get name="name"/>
```

```xml
<fest:get name="name">{'some': 'data'}</fest:get>
```

Если указать тег select, то выражение внутри выполнится и результирующая строка будет именем блока set.
```xml
<fest:script>
    var name = 'foo'
</fest:srcipt>
<fest:get select="name"/>
<fest:set name="foo">foo</fest:set>
<fest:set name="bar">bar</fest:set>
```

### fest:element

Вывод ноды с переменным именем

```xml
<fest:script>
    var variable = 'table';
</fest:script>
<fest:element select="variable">
    fest code
</fest:element>
<fest:element select="variable2">
    fest code
</fest:element>
```

Выведет

```xml
<table>fest code</table><div>fest code</div>
```

### fest:attributes, fest:attribute

Добавить атрибуты к родительскому тегу. Все ```fest:attribute``` должны быть внутри блока ```fest:attributes```, который должен быть первым внутри тега.

```xml
<a>
  <fest:attributes>
    <fest:attribute name="href"><fest:value>json.href</fest:value></fest:attribute>
  </fest:attributes>
  Some link
</a>
```

Быстрый способ вставить значение в атрибут

```xml
<a href="{json.href}">Some link</a>
```

### fest:value

Вывести значение js-выражения

```xml
<fest:value>json.value</fest:value>
<fest:value output="text"><![CDATA["<script/>"]]></fest:value>
<fest:value output="js">'"'</fest:value>
```

### fest:text

Вывод неформатированного текста

```xml
<fest:text>"Bla bla bla"</fest:text>
```

### fest:space

Пробел


## Управляющие конструкции

### fest:each

Итерация по объекту

```xml
<fest:script>var obj = {"foo": "bar"}</fest:script>
<fest:each iterate="obj" index="i">
  <fest:value>i</fest:value>=<fest:value>obj[i]</fest:value>
</fest:each>
<fest:each iterate="obj" index="i" value="v">
  <fest:value>i</fest:value>=<fest:value>v</fest:value>
</fest:each>
```

### fest:for

Итерация по массиву или по числовому ряду

```xml
<fest:script>json.items = ['a', 'b', 'c']</fest:script>
<fest:for iterate="json.items" index="i">
  <fest:value>json.items[i]</fest:value>
</fest:for>
<fest:for iterate="json.items" index="i" value="v">
  <fest:value>v</fest:value>
</fest:for>
<fest:for from="1" to="5" index="i">
  <fest:value>i</fest:value>
</fest:for>
```

### fest:if

Условный оператор

```xml
<fest:if test="true">
  true
</fest:if>
```

### fest:choose, fest:when, fest:otherwise

Ветвление. Если ни один ```fest:when``` не выполнен, будет выбрана ветвь ```fest:otherwise```.

```xml
<fest:choose>
  <fest:when test="1">
    <fest:text>one</fest:text>
  </fest:when>

  <fest:when test="2">
    <fest:text>two</fest:text>
  </fest:when>

  <fest:otherwise>
    <fest:text>More than 2</fest:text>
  </fest:otherwise>
</fest:choose>
```
## Интернационализация

### fest:plural

По умолчанию доступна поддержка плюрализации для русского и английского языка. В параметрах `fest.compile` можно передать любую другую функцию плюрализации.

```xml
<fest:plural select="json.n">рубль|рубля|рублей</fest:plural>
```
Или англоязычный вариант:

```xml
<fest:plural select="json.n">ruble|rubles</fest:plural>
```

### fest:message и fest:msg

Позволяет указать границы фразы для перевода и контекст для снятия многозначности. Например,

```xml
<fest:message context="растение">Лук</fest:message>
<fest:message context="оружие">Лук</fest:message>
```

Для каждого `fest:message`, `fest:msg`, обычного текста, заключенного между XML тегами, или текстового значения некоторых атрибутов компилятор вызывает функцию `events.message` (если такая была указана в параметрах). Данный механизм используется в `fest-build` утилите, для построения оригинального PO-файла.

## Остальное

### fest:cdata

Блок CDATA

```xml
<script>
  <fest:cdata>
    <![CDATA[alert ("2" < 3);]]>
  </fest:cdata>
</script>
```

### fest:comment

HTML комментарий

```xml
<fest:comment>comment</fest:comment>
```

### fest:doctype

Объявление doctype страницы

```xml
<fest:doctype>html</fest:doctype>
```

### fest:script

Выполнить javascript

```xml
<fest:script>
  <![CDATA[
    json.script = 2 < 3;
  ]]>
</fest:script>
```

```xml
<fest:script src="script.js"/>
```

### fest:include

Вставить содержимое другого шаблона с заданным контекстом.

```xml
<fest:script>json.list = ['a', 'b', 'c'];</fest:script>
<fest:include context="json.list" src="./include_foreach.xml"/>
```

### fest:insert

Вставить файл напрямую в шаблон

```xml
<style type="text/css">
  <fest:insert src="style.css"/>
<style>
```

# Примеры

## Установка

```
npm install fest
```

## Как использовать

compile():

```javascript
var fest = require('fest');

var data = {name: 'Jack "The Ripper"'},
    template = './templates/basic.xml';

var compiled = fest.compile(template, {beautify: false}),
    template = (new Function('return ' + compiled))();

console.log(template(data));
```

render():

```javascript
var fest = require('fest');

var data = {name: 'Jack "The Ripper"'},
    template = './templates/basic.xml';

console.log(fest.render(template, data, {beautify: false}));
```


basic.xml

```xml
<?xml version="1.0"?>
<fest:template xmlns:fest="http://fest.mail.ru" context_name="json">
  <h1>Hello,<fest:space/><fest:value output="text">json.name</fest:value></h1>

  <!-- По умолчанию все значения fest:value экранируются -->
  <!--
    Необходимо использовать fest:space или
    fest:text для явного указания строк с пробелами
  -->
</fest:template>
```

Результат

```html
<h1>Hello, Jack "The Ripper"</h1>
```

## Вложенные шаблоны

Данные на вход

```javascript
var data = {
  people: [
    {name: 'John', age: 20},
    {name: 'Mary', age: 21},
    {name: 'Gary', age: 55}
  ],

  append: '>>'
}
```

foreach.xml (основной шаблон)

```xml
<?xml version="1.0"?>
<fest:template xmlns:fest="http://fest.mail.ru" context_name="json">

    <!-- Контекст можно передавать во вложенные шаблоны -->
    <fest:include context_name="json" src="./person.xml"/>

    <!-- Значением iterate может быть любое js-выражение -->
    <fest:for iterate="json.people.reverse()" index="i">

    <!-- Передаваемые значения будут доступны в контексте params -->
    <fest:get name="person">json.people[i]</fest:get>
  </fest:for>
</fest:template>
```

person.xml

```xml
<?xml version="1.0"?>
<fest:template xmlns:fest="http://fest.mail.ru" context_name="json">

  <!--
    Используем set для объявления переменной,
    которую используем в родительском шаблоне
  -->
  <fest:set name="person">
    <p>
    <fest:script><![CDATA[
      var first = params.name[0],
          other = params.name.slice(1);
    ]]></fest:script>

    <fest:value>json.append</fest:value>
    <strong>
      <fest:value>first</fest:value>
    </strong>
    <fest:value>other</fest:value>
    </p>
  </fest:set>
</fest:template>
```

Результат

```html
<p>&gt;&gt;<strong>G</strong>ary</p>
<p>&gt;&gt;<strong>M</strong>ary</p>
<p>&gt;&gt;<strong>J</strong>ohn</p>
```

## Использование set и get

```xml
<?xml version="1.0"?>
<fest:template xmlns:fest="http://fest.mail.ru" context_name="json">
  <fest:set name="host">http://e.mail.ru</fest:set>
  <fest:set name="all">msglist</fest:set>
  <fest:set name="new">sentmsg?compose</fest:set>

  <fest:set name="all_link">
    <fest:get name="host"/>/<fest:get name="all"/>
  </fest:set>

  <fest:set name="new_link">
    <fest:get name="host"/>/<fest:get name="new"/>
  </fest:set>

  <ul>
    <!-- fest:attribute добавляет параметр к родительскому тегу -->

    <li><a>
      <fest:attributes>
        <fest:attribute name="href"><fest:get name="all_link"/></fest:attribute>
      </fest:attributes>
    Все сообщения
    </a></li>

    <li><a>
      <fest:attributes>
        <fest:attribute name="href"><fest:get name="new_link"/></fest:attribute>
      </fest:attributes>
    Написать письмо
    </a></li>
  </ul>
</fest:template>
```

Результат

```html
<ul>
  <li><a href="http://e.mail.ru/msglist">Все сообщения</a></li>
  <li><a href="http://e.mail.ru/sentmsg?compose">Написать письмо</a></li>
</ul>
```
