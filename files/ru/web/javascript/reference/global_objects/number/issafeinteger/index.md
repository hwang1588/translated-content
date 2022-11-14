---
title: Number.isSafeInteger()
slug: Web/JavaScript/Reference/Global_Objects/Number/isSafeInteger
tags:
  - ECMAScript6
  - JavaScript
  - Method
  - Number
translation_of: Web/JavaScript/Reference/Global_Objects/Number/isSafeInteger
---

{{JSRef}}

Метод **`Number.isSafeInteger()`** определяет, является ли переданное значение _безопасным целым числом_.

{{EmbedInteractiveExample("pages/js/number-issafeinteger.html")}}

Безопасное целое число это такое число, которое:

- может быть точно представлено числом IEEE-754 двойной точности и
- чьё представление IEEE-754 не может быть результатом округления любого другого целого числа, соответствующего представлению IEEE-754.

Например, число `253 - 1` является безопасным целым числом: оно может быть представлено точно и никакое другое целое число не округляется к нему ни в каком режиме округления IEEE-754. В противовес ему, число `253` _не является_ безопасным целым числом: оно может быть точно представлено в IEEE-754, но целое число `253 + 1` не может быть напрямую представлено в IEEE-754 и округляется к числу `253` в режимах округления к ближайшему и к нулю. Безопасные целые числа состоят из всех целых чисел в диапазоне от `-(253 - 1)` до `253 - 1` включительно (± `9007199254740991` или ± 9,007,199,254,740,991).

Обработка значений, больших или меньших чем \~9 квадриллионов, с высокой точностью требует использования [библиотеки, умеющей работать с длиной арифметикой](https://ru.wikipedia.org/wiki/%D0%94%D0%BB%D0%B8%D0%BD%D0%BD%D0%B0%D1%8F_%D0%B0%D1%80%D0%B8%D1%84%D0%BC%D0%B5%D1%82%D0%B8%D0%BA%D0%B0). Ознакомьтесь с [Что Каждому Программисту Необходимо Знать об Арифметике с Вещественными числами](http://floating-point-gui.de/) для получения дополнительной информации о представлении чисел, с плавающей запятой.

Для больших целочисленных значений, рассмотрите возможность использования типа {{jsxref("BigInt")}}.

## Синтаксис

```
Number.isSafeInteger(testValue)
```

### Параметры

- `testValue`
  - : Значение, проверяемое на целочисленную «безопасность».

### Возвращаемое значение

{{jsxref("Boolean")}} значение, сообщающее о том, является ли переданное значение безопасным целочисленным числом или же нет.

## Примеры

```js
Number.isSafeInteger(3);                    // true
Number.isSafeInteger(Math.pow(2, 53));      // false
Number.isSafeInteger(Math.pow(2, 53) - 1);  // true
Number.isSafeInteger(NaN);                  // false
Number.isSafeInteger(Infinity);             // false
Number.isSafeInteger('3');                  // false
Number.isSafeInteger(3.1);                  // false
Number.isSafeInteger(3.0);                  // true
```

## Полифил

```js
Number.isSafeInteger = Number.isSafeInteger || function (value) {
   return Number.isInteger(value) && Math.abs(value) <= Number.MAX_SAFE_INTEGER;
};
```

## Спецификации

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- Объект {{jsxref("Number")}}, которому принадлежит этот метод.
- {{jsxref("Number.MIN_SAFE_INTEGER")}}
- {{jsxref("Number.MAX_SAFE_INTEGER")}}
- {{jsxref("BigInt")}}