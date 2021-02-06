## Part 12

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)

<em>12.0 Part 12 (clickable)</em>

### Должны ли компоненты обновляться

И так, мы в самом начале обновления. Это означает, что подходящий момент для вызова хука `componentWillUpdate`, если он был определен (1). Затем перерисуем компонент и поставим в очередь более популярный метод `componentDidUpdate` (то есть, отложим вызов до конца обновления).
Что насчет ререндеринга? Что произойдет, на самом деле, это вызов метода `render` компонента и соответственное обновление DOM. Вызываем `render` и сохраняем результат (React элементы им возвращенные). Затем, сравниваем его с предыдущим результатом и проверяем нуждается ли DOM в обновлении.

Вы наверное уже знаете: происходящее это одна из самых впечатляющих возможностей React, улучшающих производительность.

Из комментария к `shouldUpdateReactComponent`(3) в коде:
> ‘determines if the existing instance should be updated as opposed to being destroyed or replaced by a new instance’.

> ‘определяет необходимо ли обновить существующий экземпляр или, напротив, заменить или уничтожить его’.

Так что, грубо говоря, метод проверяет нужно ли заменить элемент полностью, что означает последовательное его демонтирование, монтирование нового элемента (результат тендера нами сохраненного) и установка разметки, возвращенная методом `mount`, либо частично его обновить. Главными причинами, как правило, являются обстоятельства возвращаемого пустого нового элемента, либо замена типов, то есть, когда, скажем, `div` оказался заменен чем-то другим. Посмотрим на код.

```javascript
///src/renderers/shared/shared/shouldUpdateReactComponent.js#25

function shouldUpdateReactComponent(prevElement, nextElement) {
    var prevEmpty = prevElement === null || prevElement === false;
    var nextEmpty = nextElement === null || nextElement === false;
    if (prevEmpty || nextEmpty) {
        return prevEmpty === nextEmpty;
    }

    var prevType = typeof prevElement;
    var nextType = typeof nextElement;
    if (prevType === 'string' || prevType === 'number') {
        return (nextType === 'string' || nextType === 'number');
    } else {
        return (
            nextType === 'object' &&
            prevElement.type === nextElement.type &&
            prevElement.key === nextElement.key
        );
    }
}
```

Хорошо. В случае с нашим `ExampleApplication` мы просто обновили `state`, который особо не воздействует на `render`, так что происходящий сценарий это `update`.


### Отлично! Мы разобрались с *Часть 12*

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)

<em>12.1 Part 12 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)

<em>12.2 Part 12 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)

<em>12.3 Part 12 essential value (clickable)</em>

[To the next page: Part 13 >>](./Part-13.md)

[<< To the previous page: Part 11](./Part-11.md)


[Home](../../README.md)

