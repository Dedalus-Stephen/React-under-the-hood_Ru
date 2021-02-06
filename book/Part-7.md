## Part 7

![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7.svg)

<em>7.0 Part 7 (clickable)</em>



### Back to the beginning

После монтирования мы получаем HTML элементы, готовые для вставки в DOM. На самом деле генерируется `markup`, а `mountComponent`, несмотря на свое имя, не является HTML разметкой. Это структура данных с полями `children`, `node` (DOM узлы) и т.д. Но нам нужно вставить наш HTML элемент в контейнер (в тот самый, который определяется при вызове `ReactDOM.render`). Добавляя его в DOM, React удалит все, что было до него. `DOMLazyTree`(2) это вспомогательный класс, который осуществляет операции с древовидными структурами данных, чем мы и занимаемся при работе с DOM.

В конце работает `parentNode.insertBefore(tree.node)`(3), где `parentNode` это `div` контейнер и `tree.node` это узел нашего `ExampleAppliication`. Отлично, HTML элементы наконец созданы и помещены на свои места.

Что, все? Не совсем. Как вы помните, вызов `mount` был завернут в транзакцию. Это означает, что ее нужно завершить. Взглянем на обертки `close`. Нам нужно восстановить замороженное поведение: `ReactInputSelection.restoreSelection()`, `ReactBrowserEventEmitter.setEnabled(previouslyEnabled)`, вызвать коллбеки `this.reactMountReady.notifyAll`(4), которые были помещены в очередь `transaction.reactMountReady`. Один из них это наш любимый `componentDidMount`, который будет вызван `close` обертщиком.

Теперь вы знаете, что на самом деле означает ‘component did mount’!

Что ж, это не единственная транзакция. Мы забыли про `ReactMount.batchedMountComponentIntoNode`.

Необходимо вызывать `ReactUpdates.flushBatchedUpdates`(5), который обработает "грязные компоненты" ("`dirtyComponents`"). Звучит интересно, да? Но это было наше первое монтирования, так что никаких "грязных компонентов". На данный момент это пустой вызов.

### Отлично! Мы разобрались с **Часть 7**

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)

<em>7.1 Part 7 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)

<em>7.2 Part 7 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)

<em>7.3 Part 7 essential value (clickable)</em>

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)

<em>7.4 Mounting (clickable)</em>

[To the next page: Part 8 >>](./Part-8.md)

[<< To the previous page: Part 6](./Part-6.md)


[Home](../../README.md)
