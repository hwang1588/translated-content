---
title: Присваивание логического ИЛИ (||=)
slug: Web/JavaScript/Reference/Operators/Logical_OR_assignment
tags:
  - JavaScript
  - Language feature
  - Logical Operator
  - Operator
  - Reference
browser-compat: javascript.operators.logical_or_assignment
---

{{jsSidebar("Operators")}}

Оператор присваивания логического ИЛИ (`x ||= y`) присваивает значение переменной `x` только в случае, если её текущее значение {{Glossary("falsy", "ложноподобно")}}.

{{EmbedInteractiveExample("pages/js/expressions-logical-or-assignment.html")}}

## Синтаксис

```js
expr1 ||= expr2
```

## Описание

### Сокращённое вычисление

Оператор [логического ИЛИ](/ru/docs/Web/JavaScript/Reference/Operators/Logical_OR) работает следующим образом:

```js
x || y;
// возвращает x, если x истинноподобен
// возвращает y, если x не истинноподобен
```

Оператор логического ИЛИ OR производит вычисление по сокращённой схеме: второй операнд будет вычисляться только в случае, если при вычислении первого операнда не удалось получить значение.

Оператор присваивания логического ИЛИ также поддерживает сокращённое вычисление, т.е. выполнит операцию присваивания только при вычислении правого выражения. Проще говоря, `x ||= y` эквивалентно следующему коду:

```js
x || (x = y);
```

В отличие от следующего примера, где операция присваивания произойдёт в любом случае:

```js example-bad
x = x || y;
```

Обратите внимание, что математические и побитовые операторы присваивания работают по-разному.

## Примеры

### Определение значения по умолчанию

Если в элементе с идентификатором "lyrics" нет содержимого, то следующее выражение вставит в него надпись:

```js
document.getElementById('lyrics').textContent ||= 'Нет слов.'
```

В данном примере сокращённое вычисление очень сильно выручает, поскольку элемент будет обновляться только при необходимости, что позволит избежать подобных эффектов вроде ненужного рендеринга, потери фокуса и т.д.

Примечание: обратите внимание на значение в левом операнде. Если это пустая строка ({{Glossary("falsy", "ложноподобное")}} значение), то должно использовать оператор `||=`, в противном случае — оператор `??=` (если ожидается получение значения {{jsxref("null")}} или {{jsxref("undefined")}}).

## Спецификации

{{Specifications}}

## Поддержка браузерами

{{Compat}}

## Смотрите также

- [Логическое ИЛИ(||)](/ru/docs/Web/JavaScript/Reference/Operators/Logical_OR)
- [Оператор нулевого слияния (`??`)](/ru/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)
- [Оператор присваивания побитового ИЛИ (`|=`)](/ru/docs/Web/JavaScript/Reference/Operators/Bitwise_OR_assignment)
- {{Glossary("Truthy", "Истиноподобное значение")}}
- {{Glossary("Falsy", "Ложноподобное значение")}}