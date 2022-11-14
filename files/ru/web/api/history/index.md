---
title: History
slug: Web/API/History
translation_of: Web/API/History
---
{{ APIRef("HTML DOM") }}

_**History** интерфейс позволяет манипулировать историей браузера в пределах сессии, а именно историей о посещённых страницах в пределах вкладки или фрейма загруженного внутри страницы._

## Свойства

_History интерфейс не наследует никакие родительские свойства._

- {{domxref("History.length")}} {{readOnlyInline}}
  - : Возвращает целочисленное значение типа Integer, которое характеризует собой количество записей в истории сессии, включая текущую загруженную страницу. Для примера, история новой вкладки с загруженной страницей равна 1.
- {{domxref("History.current")}} {{readOnlyInline}} {{ non-standard_inline() }} {{ obsolete_inline(26) }}
  - : Возвращает {{domxref ("DOMString")}}, представляющий собой активный URL элемент в истории сессии. Это свойство никогда не было доступно для веб-контента и более не поддерживается никакими браузерами. Используйте {{domxref ("location.href")}} вместо него.
- {{domxref("History.next")}} {{readOnlyInline}} {{ non-standard_inline() }} {{ obsolete_inline(26) }}
  - : Возвращает {{domxref ("DOMString")}}, представляющий собой следующий URL элемент в истории сессии. Это свойство никогда не было доступно для веб-контента и более не поддерживается другими браузерами.
- {{domxref("History.previous")}} {{readOnlyInline}} {{ non-standard_inline() }} {{ obsolete_inline(26) }}
  - : Возвращает {{domxref ("DOMString")}}, представляющий собой предыдущий URL элемент в истории сессии. Это свойство никогда не было доступно для веб-контента и более не поддерживается другими браузерами.
- {{domxref("History.state")}} {{readOnlyInline}} {{ gecko_minversion_inline("2.0")}}
  - : Возвращает какое-либо значение, представляющее собой состояние в вершине истории стека. Это способ посмотреть на состояние без ожидания {{event("popstate")}} события.

## Методы

_History интерфейс не наследует никаких родительских методов._

- {{domxref("History.back()")}}

  - : Делает вызов предыдущей страницы из истории, если она существует. Эквивалент метода `history.go(-1)`.

    > **Примечание:** Вызов этого метода, чтобы вернуться к первой странице в истории сессии не имеет никакого эффекта и не вызывает исключений.

- {{domxref("History.forward()")}}

  - : Переход к следующей странице в истории сессии, то же самое действие, как и при нажатии пользователем кнопки Forward в браузере. Эквивалентно history.go(1).

    > **Примечание:** Вызов этого метода, чтобы перейти к последней странице в истории сессии не имеет никакого эффекта и не вызывает исключений.

- {{domxref("History.go()")}}
  - : Загружает страницу из истории сессии, определяя её положение относительно текущей страницы, например: -1 для предыдущей страницы или 1 для следующей страницы. Когда _integerDelta_ выходит за предел (например, -1, когда нет ранее посещённых страниц в истории сессии), метод не делает ничего, и не вызывает исключение. Вызов Go () без параметров или с не целочисленным аргументом не имеет никакого эффекта (в отличие от Internet Explorer, [который поддерживает строки URL в качестве аргумента](<http://msdn.microsoft.com/en-us/library/ms536443(VS.85).aspx>)).
- {{domxref("History.pushState()")}} {{ gecko_minversion_inline("2.0") }}

  - : Помещает полученные данные в стек истории сессии с определённым заголовком и, при наличии , URL. Данные рассматриваются DOM как непрозрачные; вы можете задать любой объект JavaScript, который может быть сериализован. Обратите внимание, что в настоящее время Firefox игнорирует параметр заголовка; для получения дополнительной информации см. [управление историей браузера](/en/DOM/Manipulating_the_browser_history).

    > **Примечание:** В Gecko 2.0 {{ geckoRelease("2.0") }} до Gecko 5.0 {{ geckoRelease("5.0") }}, передаваемый объект сериарилизовался в JSON. Начиная с Gecko 6.0 {{ geckoRelease("6.0") }}, объект обрабатывается по [алгоритму структурированного клонирования](/ru/docs/Web/API/Web_Workers_API/Structured_clone_algorithm). Он позволяет передавать более широкое разнообразие объектов.

- {{domxref("History.replaceState()")}} {{ gecko_minversion_inline("2.0") }}

  - : Обновляет последнюю запись в стеке истории содержащий определённые данные, заголовок и, при наличии, URL. Данные рассматриваются DOM как непрозрачные; вы можете задать любой объект JavaScript, который может быть сериализован. Обратите внимание, что в настоящее время Firefox игнорирует параметр заголовка; для получения дополнительной информации см.[управление историей браузера](/en/DOM/Manipulating_the_browser_history).

    > **Примечание:** В Gecko 2.0 {{ geckoRelease("2.0") }} до Gecko 5.0 {{ geckoRelease("5.0") }}, передаваемый объект сериарилизовался в JSON. Начиная с Gecko 6.0 {{ geckoRelease("6.0") }}, объект обрабатывается по [алгоритму структурированного клонирования](/ru/docs/Web/API/Web_Workers_API/Structured_clone_algorithm). Он позволяет передавать более широкое разнообразие объектов.

## Спецификации

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- {{domxref("window.history")}} свойство, возвращающее историю текущей сессии.