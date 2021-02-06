## Part 13

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)

<em>13.0 Part 13 (clickable)</em>

### Получение следующего компонента (элемента, если быть точнее)


При помощи `ReactReconciler.receiveComponent` React на самом деле вызывает `receiveComponent` из `ReactDOMComponent` и передает туда следующий элемент. Затем переназначается экземпляр DOM компонента и вызывается обновление. Метод `updateComponent` выполняет два важных действия: обновление свойств DOM и дочерних компонентов DOM на основании `prev` и `next` пропов. К счастью, мы уже изучили метод `_updateDOMProperties`  (`src\renderers\dom\shared\ReactDOMComponent.js#946`). Как вы помните, этот метод по сути обрабатывает свойства и атрибуты HTML элементов, стили, обработчики событий и тому подобное. Осталось разобраться с `_updateDOMChildren` (`src\renderers\dom\shared\ReactDOMComponent.js#1076`).

### Отлично! Мы разобрались с *Часть 13*

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)

<em>13.1 Part 13 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)

<em>13.2 Part 13 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)

<em>13.3 Part 13 essential value (clickable)</em>


[To the next page: Part 14 >>](./Part-14.md)

[<< To the previous page: Part 12](./Part-12.md)


[Home](../../README.md)



