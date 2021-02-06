[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)




<em>6.0 Part 6 (clickable)</em>




### Создание дочерних компонентов.


Похоже на то, что с созданием самого элемента покончено, так что теперь мы может перейти к его дочерним компонентам. Здесь будет два этапа: дочерние компоненты монтируются (`this.mountChildren`)(1) и соединяются с родительским (`DOMLazyTree.queueChild`)(2). Перейдем к первому шагу.

Для работы с дочерними компонентами существует отдельный модуль `ReactMultiChild` (`src\renderers\shared\stack\reconciler\ReactMultiChild.js`). Разберем метод `mountChildren`. Он выполняет две основные задачи. В первую очередь, инстанциирует дочерние компоненты и монтирует их. Что именно мы понимаем под дочерними компонентами? Это может быть простой HTML тэг или другой компонент. Для обработки HTML инстанциируется `ReactDOMComponent`, а для дочернего компонента `ReactCompositeComponent`.


### Еще раз

Если вы все еще читаете эту книгу, возможно пришло время для прояснить и пересмотреть процесс в целом еще раз. Возьмем паузу и вспомним последовательность происходящего.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)]
(https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)




<em>6.1 Overall mounting scheme (clickable)</em>

1) React инстанциирует `ReactCompositeComponent` для каждого вашего компонента.

2) В процессе монтирования, в первую очередь, создается экземпляр компонента.

3) Затем вызывается renred метод (для простоты условимся, что он возвращает `div`) и `React.createElement` создает React элементы. Он может быть вызван напрямую или после парсинга JSX Babel-ом и заменой тэгов. Но это не совсем то, что нам нужно.

4) Нам нужен DOM компонент для нашего `div`. Поэтому в процессе инстантизации мы создаем экземпляры `ReactDOMComponent` из элементов-объектов (упомянутых выше).

5) Затем нам нужно монтировать DOM компонент. Это означает создание DOM элементов, обработчиков событий и т.д.

6) Затем обрабатываются дочерние компоненты нашего DOM элемента. Мы создаем их экземпляры и монтируем их. В зависимости от типа дочернего компонента шаги 1) или 5) повторяются рекурсивно.


Вот и все. Все просто, как видите.

Так что монтирование по сути завершено. В очередь вставляется метод `componentDidMount`. 


### Отлично! Мы разобрались с **Часть 6**

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)

<em>6.2 Part 6 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)

<em>6.3 Part 6 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)

<em>6.4 Part 6 essential value (clickable)</em>


[To the next page: Part 7 >>](./Part-7.md)

[<< To the previous page: Part 5](./Part-5.md)


[Home](../../README.md)
