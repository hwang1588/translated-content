---
title: Semantics
slug: Glossary/Semantics
translation_of: Glossary/Semantics
original_slug: Глоссарий/Semantics
---

В программировании, **Семантика** означает _значение_ фрагмента кода - например, «К какому результату приведёт выполнение этой строки JavaScript?», или «Каково предназначение или какая роль у этого элемента HTML?», а не «Как он выглядит?».

## Семантика в JavaScript

Для JavaScript, рассмотрим функцию, которая принимает строку в качестве параметра, и возвращает элемент {{htmlelement("li")}} с этой строкой в качестве свойства `textContent`. Нужно ли вам смотреть на код, чтобы понять, что делает функция `build('Peach')`, или `createLiWithContent('Peach')`?

## Семантика в CSS

Для CSS, рассмотрим стилизацию списка элементов `li`, в которых содержатся разные виды фруктов. Вы бы поняли, какая часть DOM выбрана с помощью селектора `div > ul > li`, или `.fruits__item`?

## Семантика в HTML

В HTML, например, элемент {{htmlelement("h1")}} является семантическим, что даёт тексту внутри него роль (или значение) "заголовка первого уровня на вашей странице".

```html
<h1>This is a top level heading</h1>
```

По умолчанию, [стандартные стили](/ru/docs/Web/CSS/Cascade#User-agent_stylesheets) большинства браузеров зададут {{htmlelement("h1")}} большой размер шрифта, чтобы он _выглядел_ как заголовок (хотя вы можете стилизировать его так, как сочтёте нужным).

С другой стороны, вы можете сделать любой элемент _похожим_ на заголовок первого уровня. Рассмотрим следующее:

```html
<span style="font-size: 32px; margin: 21px 0;">Is this a top level heading?</span>
```

Эти стили сделают элемент похожим на заголовок первого уровня, но он не будет иметь семантического значения, поэтому не получит никаких дополнительных преимуществ, описанных выше. Поэтому хорошей практикой является выбор подходящего элемента HTML для каждого отдельного случая.

HTML должен предоставлять структуру информации, которой он будет наполняться, и не должен полагаться на стандартные свойства стилей. Стилизация (то, как эта информация должна выглядеть), полностью ложится на плечи [CSS](/ru/docs/Web/CSS).

Некоторые из преимуществ использования семантической разметки:

- Поисковые системы будут рассматривать содержимое такой разметки как важные ключевые слова, влияющие на рейтинг страницы в результатах поиска (смотри {{glossary("SEO")}})
- Программы для чтения с экрана могут использовать её как указатель, чтобы помочь слабовидящим пользователям ориентироваться на странице
- Среди бесконечных блоков `div` гораздо проще найти блок семантического кода, чем блок с семантическим классом или классом, принадлежащим пространству имён.
- Указывает разработчику на тип данных, которые будут размещены
- Семантическая разметка отражает правильное именование элементов / компонентов

Принимая решение об использовании той или иной разметки, спросите себя: «Какой элемент (элементы) лучше всего описывает / представляет данные, которые я собираюсь использовать?» Например, это список данных ?; упорядоченный, неупорядоченный ?; это статья с разделами и дополнительной информацией ?; в ней перечислены определения ?; это рисунок или изображение, которое требует подписи ?; должен ли он иметь собственные `header` и `footer`?; и т.п.

## Семантические элементы

Вот _некоторые_ из примерно 100 доступных семантических [элементов](/ru/docs/Web/HTML/Element):

- {{htmlelement("article")}}
- {{htmlelement("aside")}}
- {{htmlelement("details")}}
- {{htmlelement("figcaption")}}
- {{htmlelement("figure")}}
- {{htmlelement("footer")}}
- {{htmlelement("header")}}
- {{htmlelement("main")}}
- {{htmlelement("mark")}}
- {{htmlelement("nav")}}
- {{htmlelement("section")}}
- {{htmlelement("summary")}}
- {{htmlelement("time")}}

## Learn more

- [HTML element reference](/ru/docs/Web/HTML/Element#Inline_text_semantics) on MDN
- [Using HTML sections and outlines](/ru/docs/Web/Guide/HTML/Using_HTML_sections_and_outlines#Problems_solved_by_HTML5) on MDN
- {{interwiki("wikipedia", "Semantics#Computer_science", "The meaning of semantics in computer science")}} on Wikipedia

<!---->

- [MDN Web Docs Glossary](/ru/docs/Glossary)

  - {{Glossary("SEO")}}

- Some of semantic elements in HTML

  - {{htmlelement("article")}}
  - {{htmlelement("aside")}}
  - {{htmlelement("details")}}
  - {{htmlelement("figcaption")}}
  - {{htmlelement("figure")}}
  - {{htmlelement("footer")}}
  - {{htmlelement("header")}}
  - {{htmlelement("main")}}
  - {{htmlelement("mark")}}
  - {{htmlelement("nav")}}
  - {{htmlelement("section")}}
  - {{htmlelement("summary")}}
  - {{htmlelement("time")}}