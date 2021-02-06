## Part 11

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)

<em>11.0 Part 11 (clickable)</em>

### Update component

Комментарий из кода: 

>‘Perform an update to a mounted component. The componentWillReceiveProps and shouldComponentUpdate methods are called, then (assuming the update isn't skipped) the remaining update lifecycle methods are called and the DOM representation is updated. By default, this implements React's rendering and reconciliation algorithm. Sophisticated clients may wish to override this.

>’Осуществляет обновление монтированного компонента. Методы componentWillReceiveProps and shouldComponentUpdate вызываются, затем (если обновление не пропускается) оставшиеся методы обновления будут вызвана и DOM будет обновлен. По умолчанию, это происходит по алгоритму сверки. Умелые пользователи могут перезаписать это поведение.’

Первым делом мы проверяем изменения `props` (1), технически, метод `updateComponent` может быть вызван в разных случаях: вызов `setState` или изменение `props`. Если `props` действительно изменились, метод жизненного цикла `componentWillReceiveProps` будет вызван. Затем, React заново вычислит `nextState` (2) в зависимости от `pending state queue` (очередь объектов состояния, в нашем случае очередь будет чем-то вроде {message: "click state message"}]). Конечно же в случае изменения`props` состояние останется нетронутым.

Затем `shouldUpdate` выравнивается `true`(3). Именно поэтому, когда `shouldComponentUpdate` не определен компонент обновляется по умолчанию. Затем, проверяется является ли обновление принудительным (`force update`). Компонент можно обновить принудительно (`forceUpdate`) без изменения `state` или `props`, но, согласно официальной документации, этот метод не рекомендуется. В случае его вызова метод `shouldComponentUpdate` будет вызван и `shouldUpdate` будет переназначен результатом его работы. Если было вычислено, что компонент не нуждается в обновлении, React все же определит `props` и `state`, но опустит все остальное.

### Отлично! Мы разобрались с *Часть 11*

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)

<em>11.1 Part 11 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)

<em>11.2 Part 11 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)

<em>11.3 Part 11 essential value (clickable)</em>

[To the next page: Part 12 >>](./Part-12.md)

[<< To the previous page: Part 10](./Part-10.md)


[Home](../../README.md)

