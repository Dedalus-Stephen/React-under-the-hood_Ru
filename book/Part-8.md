## Part 8

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)



<em>8.0 Part 8 (clickable)</em>



### `this.setState`

Мы уже знаем как происходит монтирование, но сейчас давайте зайдет с другой стороны. Метод  `setState`, еще одна важная часть.

Во-первых, зачем мы вообще зовем какой-то там метод, названный `setState`? Что ж, мы расширили наш компонент от `ReactComponent`. Хорошо, теперь будет легко найти этот метод в исходных файлах React.

```javascript

//src\isomorphic\modern\class\ReactComponent.js#68

this.updater.enqueueSetState(this, partialState)
```

Как видите, здесь присутствует интерфейс `updater`. Во время `mountComponent`, экземпляр компонента получает `updater` в качестве ссылки на `ReactUpdateQueue` (`src\renderers\shared\stack\reconciler\ReactUpdateQueue.js`).

Внутри метода `enqueueSetState` (1) видим, что сначала он помещает частичное состояние (объект, передаваемый в `this.setState`) в `_pendingStateQueue` (2) внутреннего экземпляра (напоминание: публичный экземпляр это наш компонент `ExampleApplication`, а внутренний -- `ReactCompositeComponent`, созданный во время монтирования), потом мы вызываем `enqueueUpdate`, который проверяет то, что обновления уже в прогрессе и тогда помещает компонент в лист `dirtyComponents`, если нет -- объявляет транзакцию и только тогда помещает в список `dirtyComponents`. 

Обобщая это все, каждый компонент имеет свой список ожидающих состояний, что означает, что каждый раз, когда вы вызываете `setState` в одной транзакции, вы помещаете этот объект в очередь, потом, позже они будут слиты в состояние компонента один за другим. И, при вызове `setState`, добавляете свой компонент в `dirtyComponents`. Возможно вы уже задались вопросом как эти `dirtyComponents` обрабатываются. Мы рассмотрим это в следующей части.

### Отлично! Мы разобрались с **Часть 8**

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)

<em>8.1 Part 8 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)

<em>8.2 Part 8 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)

<em>8.3 Part 8 essential value (clickable)</em>

[To the next page: Part 9 >>](./Part-9.md)

[<< To the previous page: Part 7](./Part-7.md)


[Home](../../README.md)
