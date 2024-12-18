---
title: "`:is()`"
description: "Как изящно выбрать сразу несколько элементов на странице, не перечисляя селекторы через запятую."
authors:
  - teplostanski
keywords:
  - псевдокласс
  - функция
related:
  - css/has
  - css/where
  - css/vendor-prefixes
tags:
  - doka
---

## Кратко

Функция-псевдокласс `:is()` принимает один или несколько селекторов в качестве аргумента. Браузер применяет стили к каждому из перечисленных селекторов.

## Пример

Стили применятся при наведении курсора на абзацы, вложенные в теги [`<header>`](/html/header/), [`<main>`](/html/main/) или [`<footer>`](/html/footer/):

```css
:is(header, main, footer) p:hover {
  color: red;
  cursor: pointer;
}
```

Без `:is()` пришлось бы перечислять селекторы:

```css
header p:hover,
main p:hover,
footer p:hover {
  color: red;
  cursor: pointer;
}
```

## Как пишется

Слева от двоеточия можно ничего не писать. Внутри скобок селекторы перечисляются через запятую.

Псевдокласс `:is()` можно использовать с любыми CSS-селекторами:

- `:is(.btn, .link)` — применяет стили к элементам с классами `btn` и `link`.
- `:is(#header, .nav, a)` — применяет стили к элементам с идентификатором `header`, классом `nav` и ко всем ссылкам.
- `:is(h1, h2, h3)` — применяет стили ко всем заголовкам уровня 1, 2 и 3.

Можно комбинировать `:is()` с другими селекторами для более сложных условий:

```css
article :is(h1, h2, h3) {
  margin-bottom: 1rem;
}
```

Этот код применяет нижнее поле ко всем заголовкам уровня 1, 2 и 3 внутри элемента `<article>`.

Псевдокласс `:is()` можно комбинировать с другими псевдоклассами и атрибутами для более гибких селекторов:

```css
:is(a:hover, button:focus) {
  outline: 2px solid blue;
}
```

Этот код применяет обводку к ссылкам при наведении и кнопкам при фокусировке.

Псевдокласс `:is()` не принимает произвольные строки. Он должен содержать валидные CSS-селекторы. Например, значение `:is(text)` будет недействительным, так как `text` не является селектором.

С помощью `:is()` не получится обратиться к псевдоэлементам.

Вот так не сработает:

```css
.cat-list:is(::before, ::after) {
  text-transform: uppercase;
}
```

И так тоже не сработает:

```css
:is(.cat-list::before, .cat-list::after) {
  text-transform: uppercase;
}
```

Придётся перечислять селекторы так:

```css
.cat-list::before,
.cat-list::after {
  text-transform: uppercase;
}
```

## Как понять

Псевдокласс `:is()` помогает упростить CSS-код, делая его более лаконичным и удобочитаемым. Он улучшает поддержку сложных селекторов, но важно помнить, что он лишь облегчает написание селекторов, не влияя на их базовую функциональность и вес. Проверка корректности написания селекторов и поддержка в браузерах — это ключевые аспекты его использования.

## Разница специфичности между `:is()` и `:where()`

`:is()` по весу будет равен самому специфичному из перечисленных в скобках селекторов.

Вычисленный вес в этом примере будет 111, потому что таким будет вес селектора `h1.title#title`:

```css
:is(h1, #title, h1.title#title) {
  font-size: 3rem;
}
```

В отличии от этого [`:where()`](/css/where/) всегда будет иметь вес 0, какие бы селекторы не перечислялись в скобках.

Разница этих поведений может сказаться на итоговых стилях. Учитывайте это.

## Прощающие селекторы

Псевдоклассы `:where()` и `:is()` относятся к [«прощающему списку селекторов»](https://drafts.csswg.org/selectors-4/#typedef-forgiving-selector-list). Невалидный селектор в списке аргументов _не приведёт_ к игнорированию валидных селекторов.

Стили ниже применятся к [`<span>`](/html/span/), а нерабочий селектор `:some-unsupported-feature` будет проигнорирован:

```css
:where(span, :some-unsupported-feature) {
  color: blue;
}
```

Тут та же история:

```css
:is(span, :some-unsupported-feature) {
  color: blue;
}
```

А вот если указать неподдерживаемое свойство `:some-unsupported-feature` в перечислении, то всё сломается, не сработает ни один из селекторов:

```css
span,
:some-unsupported-feature {
  color: red;
}
```

## Подсказки

💡 `:is()` полностью поддерживается во всех стабильных браузерах с января 2021 года. Для старых браузеров можно использовать устаревшие версии селектора с префиксами `:-webkit-any()`, `:-moz-any()`, и `:matches()`. Подробнее смотрите на [Can I Use](https://caniuse.com/css-matches-pseudo).
