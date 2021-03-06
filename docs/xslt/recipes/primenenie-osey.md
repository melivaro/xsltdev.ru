---
description: Требуется отобрать узлы XML-дерева с учетом сложных взаимосвязей в иерархической структуре
---

# Применение осей

## Задача

Требуется отобрать узлы XML-дерева с учетом сложных взаимосвязей в иерархической структуре.

## Решение

Во всех приведенных ниже примерах используются оси. В каждой группе для демонстрации берется некий XML-документ, в котором контекстный узел выделен полужирным шрифтом. Поясняется, что является результатом вычисления пути, при этом показано, какие элементы отбираются относительно выделенного контекста. В некоторых случаях для иллюстрации тонкостей вычисления конкретного выражения рассматриваются и другие узлы, помимо контекстного.

### Дочерняя ось и ось потомков

Дочерняя ось принимается в XPath по умолчанию. Иными словами, явно указывать ось `child::` необязательно, но, если вы хотите быть педантом, то можете и указать. Спуститься по XML-дереву глубже, чем на один уровень, позволяют оси `descendant::` и `descendant-or-self::`. Первая не включает сам контекстный узел, вторая – включает.

```xml hl_lines="2"
<Test id="descendants">
	<parent>
		<X id="1"/>
		<X id="2"/>
		<Y id="3">
			<X id="3-1"/>
			<Y id="3-2"/>
			<X id="3-3"/>
		</Y>
		<X id="4"/>
		<Y id="5"/>
		<Z id="6"/>
		<X id="7"/>
		<X id="8"/>
		<Y id="9"/>
	</parent>
</Test>
```

---

Отобрать все дочерние элементы с именем `X`: `X`, то же, что `child::X`

Результат:

```xml
<X id="1"/> <X id="2"/> <X id="4"/> <X id="7"/> <X id="8"/>
```

---

Отобрать первый дочерний элемент с именем `X`: `X[1]`

Результат:

```xml
<X id="1"/>
```

---

Отобрать последний дочерний элемент с именем `X`: `X[last()]`

Результат:

```xml
<X id="8"/>
```

---

Отобрать первый дочерний элемент при условии, что его имя `X`. Иначе пусто: `*[1][self::X]`

Результат:

```xml
<X id="1"/>
```

---

Отобрать последний дочерний элемент при условии, что его имя `X`. Иначе пусто: `*[last()][self::X]`

Результат: пусто

---

Отобрать последний дочерний элемент при условии, что его имя `Y`. Иначе пусто: `*[last()][self::Y]`

Результат:

```xml
<Y id="9"/>
```

---

Отобрать всех потомков с именем `X`: `descendant::X`

Результат:

```xml
<X id="1"/> <X id="2"/> <X id="3-1"/> <X id="3-3"/>
<X id="4"/> <X id="7"/> <X id="8"/>
```

---

Отобрать контекстный узел, если его имя `X`, а также всех потомков с именем `X`: `descendant-or-self::X`

Результат:

```xml
<X id="1"/> <X id="2"/> <X id="3-1"/> <X id="3-3"/>
<X id="4"/> <X id="7"/> <X id="8"/>
```

---

Отобрать контекстный узел и всех его потомков: `descendant-or-self::*`

Результат:

```xml
<parent> <X id="1"/> <X id="2"/> <Y id="3"> <X id="3-1"/>
<Y id="3-2"/> <X id="3-3"/> </Y> <X id="4"/> <Y id="5"/> <Z id="6"/>
<X id="7"/> <X id="8"/> <Y id="9"/> </parent>
```

### Оси братьев

Оси братьев называются `preceding-sibling::` и `following-sibling::`. Ось `preceding-sibling` содержит братьев, предшествующих контекстному узлу, а ось `following-sibling` – следующих за ним. Братьями, естественно, называются дети одного родителя. Почти во всех примерах ниже используется ось `preceding-sibling::`, но вам не составит труда вычислить результат и для оси `following-sibling::`.

Имейте в виду, что в позиционном выражении вида `preceding-sibling::*[1]` вы ссылаетесь на непосредственно предшествующего брата в порядке обратного отсчета от контекстного узла, а не первого брата в порядке документа. Некоторых смущает тот факт, что результирующая последовательность возвращается в порядке документа вне зависимости от того, используется ось `preceding-sibling::` или `following-sibling::`. В выражении `../X`, строго говоря, никакая ось не указана; это просто способ отобрать предшествующего и последующего брата с именем `X`, а также сам контекстный узел, если он называется `X`. Формально это сокращенная запись выражения `parent::node()/X`. Отметим, что выражения `(preceding-sibling::*)[1]` и `(following-sibling::*)[1]` отбирают первого предшествующего или последующего брата в порядке документа.

```xml hl_lines="9"
<!-- Тестовый документ с выделенным контекстным узлом -->
<Test id="preceding-siblings">
	<A id="1"/>
	<A id="2"/>
	<B id="3"/>
	<A id="4"/>
	<B id="5"/>
	<C id="6"/>
	<A id="7"/>
	<A id="8"/>
	<B id="9"/>
</Test>
```

---

Отобрать всех братьев с именем `A`, предшествующих контекстному узлу: `preceding-sibling::A`

Результат:

```xml
<A id="1"/> <A id="2"/> <A id="4"/>
```

---

Отобрать всех братьев с именем `A`, следующих за контекстным узлом: `following-sibling::A`

Результат:

```xml
<A id="8"/>
```

---

Отобрать всех братьев, предшествующих контекстному узлу: `preceding-sibling::*`

Результат:

```xml
<A id="1"/> <A id="2"/> <B id="3"/> <A id="4"/> <B id="5"/>
<C id="6"/>
```

---

Отобрать первого предшествующего брата с именем `A` в обратном порядке документа: `preceding-sibling::A[1]`

Результат:

```xml
<A id="4"/>
```

---

Отобрать первого предшествующего брата в обратном порядке документа при условии, что его имя `A`: `preceding-sibling::*[1][self::A]`

Результат: пусто

Если бы контекстным был узел `<A id="8"/>`, то в результате мы получили бы `<A id="7"/>`

---

Отобрать всех предшествующих братьев, кроме элементов с именем `A`: `preceding-sibling::*[not(self::A)]`

Результат:

```xml
<B id="3"/> <B id="5"/> <C id="6"/>
```

В следующих примерах используется такой тестовый документ:

```xml hl_lines="12"
<Test id="preceding-siblings">
	<A id="1">
		<A/>
	</A>
	<A id="2"/>
	<B id="3">
		<A/>
	</B>
	<A id="4"/>
	<B id="5"/>
	<C id="6"/>
	<A id="7"/>
	<A id="8"/>
	<B id="9"/>
</Test>
```

---

Элемент, непосредственно предшествующий контекстному узлу при условии, что у того есть дочерний элемент с именем `A`: `preceding-sibling::*[1][A]`

Результат: пусто

---

Первый из предшествующих контекстному узлу элементов, у которого есть дочерний элемент с именем `A`: `preceding-sibling::*[A][1]`

Результат:

```xml
<B id="3"/>
```

XPath 2.0 позволяет более гибко выбирать элементы с учетом пространств имен. В следующих примерах используется такой XML-документ:

```xml hl_lines="4"
<Test xmlns:NS="http://www.ora.com/xsltckbk/1" xmlns:NS2="http://www.ora.com/xsltckbk/2">
	<NS:A id="1"/>
	<NS2:A id="2"/>
	<NS:B id="3"/>
	<NS2:B id="3"/>
</Test>
```

---

Отобрать всех предшествующих братьев контекстного узла, для которых пространство имен ассоциировано с префиксом `NS`: `preceding-sibling::NS:*`

Результат:

```xml
<NS:A id="1"/>
```

---

Отобрать всех предшествующих братьев контекстного узла, для которых локальное имя равно `A`: `preceding-sibling::*:A`

Результат:

```xml
<NS:A id="1"/> <NS2:A id="2"/>
```

### Родительская ось и ось предков

Родительская ось (`parent::`) относится к родителю контекстного узла. Не путайте выражение `parent::X` с `../X`. Первое порождает последовательность, которая содержит в точности один элемент, если имя родителя контекстного узла – `X`, и пуста в противном случае. Второе – это сокращенная запись выражения `parent::node()/X`, которое отбирает всех братьев контекстного узла с именем `X`, в том числе и сам этот узел, если он называется `X`.

С помощью осей `ancestor::` и `ancestor-or-self::` можно перемещаться вверх по XML-дереву (переходя к родителю, деду, прадеду и т. д.). В первом случае сам контекстный узел исключается, во втором – включается.

---

Возвращает родителя контекстного узла, при условии, что он называется `X`, в противном случае – пустую последовательность: `parent::X`

Возвращает родителя контекстного узла. Результат может быть пустым, только если контекстный узел является элементом верхнего уровня: `parent::*`

Возвращает родителя, если его пространство имен ассоциировано с префиксом `X`. Префикс должен быть определен, иначе произойдет ошибка: `parent::NS:*`

Возвращает родителя независимо от его пространства имен при условии, что локальное имя равно `X`: `parent::*:X`

Возвращает всех предков (включая родителя) с именем `X`: `ancestor::X`

Возвращает контекстный узел, если он называется `X`, а также всех предков с именем `X`: `ancestor-or-self::X`

### Оси предшественников и последователей

Оси `preceding::` и `following::` могут отбирать очень много узлов, поскольку учитываются все узлы, предшествующие контекстному (или следующие за ним) в порядке документа, исключая предков в случае оси `following::` или потомков для оси `preceding::`. Не забывайте также, что обе оси не включают узлы, соответствующие пространствам имен и атрибутам.

---

Все предшествующие узлы с именем `X`: `preceding::X`

Ближайший предшественник с именем `X`: `preceding::X[1]`

Самый дальний последователь с именем `X`: `following::X[last()]`

## Обсуждение

В языке XPath понятие оси служит для того, чтобы выделить в дереве документа различные подмножества узлов относительно некоторого узла, называемого контекстным. В общем случае эти подмножества пересекаются, но оси `ancestor`, `descendant`, `following`, `preceding` и `self` разбивают документ на непересекающиеся части, в совокупности содержащие все узлы (за исключением тех, что соответствуют пространствам имен и атрибутам). Контекстный узел устанавливается языком, в который погружен XPath. В случае XSLT контекстный узел устанавливают следующие конструкции:

- сопоставление с шаблоном (`<xsl:template match="x">…</xsl:template>`);
- `xsl:for-each`;
- `xsl:apply-templates`.

Свободное владение языком написания путевых выражений – необходимое условие для выполнения как простых, так и сложных преобразований. Опыт программирования на традиционных языках часто служит причиной путаницы и ошибок при работе с XPath. Например, я часто ловил себя на том, что писал что-то типа `<xsl:if test="precedingsibling::X[1]"> </xsl:if>`, имея в виду `<xsl:if test="precedingsibling::*[1][self::X]"> </xsl:if>`. Возможно, это объясняется тем, что последнее выражение – интуитивно менее понятный способ выразить такое условие: «если имя непосредственно предшествующего брата равно `X`».

Конечно, нет никакой возможности показать все полезные комбинации осей в путевых выражениях. Но, если вы поймете приведенные выше примеры, то сможете разобраться, что означает выражение `preceding-sibling::X[1]/descendant::Z[A/B]` и еще более сложные.
