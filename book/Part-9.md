## Part 9

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)

<em>9.0 Part 9 (clickable)</em>

### But let’s move back..

Как видите на схеме, вызов метода `setState` может быть инициирован несколькими способами, а еще точнее, с или без внешнего воздействия (с действием пользователя). Рассмотрим оба случая: в первом случае вызов метода спровоцирован кликом мышки, а во втором вызовом `setTimeout` в `componentDidMount`. 

В чем именно заключается разница? Что ж, как вы помните, React обрабатывает обновления группами (`batches`), что означает, что список обновлений должен как-то быть сгруппирован, после зачищен (`flushed`). Идея в том, что как только появляется событие мыши (клик), оно обрабатывается на верхнем уровне и затем, пройдя через несколько слоев обертки, обновления начинаются. Между прочем, как видите, это происходит только если `ReactEventListener` активен (`enabled`) (1). И, как помните, на фазе монтирование, один из `ReactReconcileTransaction` врапперов, деактивирует его, гарантируя безопасное монтирование. Но, что насчет `setTimeout`? Так же просто! Перед тем, как пометить компонент как `dirtyComponent`, React убедится, что транзакция открыта, чтобы потом закрыть ее и очистить буфер обновлений.

React использует синтетические события (Synthetic events) — синтаксический сахар, который, по сути, оборачивает простые события (native events).
Комментарий из кода: 

> ‘To help development we can get better dev tool integration by simulating a real browser event’


```javascript
var fakeNode = document.createElement('react');

ReactErrorUtils.invokeGuardedCallback = function (name, func, a) {
      var boundFunc = func.bind(null, a);
      var evtType = 'react-' + name;

      fakeNode.addEventListener(evtType, boundFunc, false);

      var evt = document.createEvent('Event');
      evt.initEvent(evtType, false, false);

      fakeNode.dispatchEvent(evt);
      fakeNode.removeEventListener(evtType, boundFunc, false);
};
```

И так, подход таков: 

Вызов setState
Открытие транзакции
Добавление компонентов в `dirtyComponents`
Закрытие транзакции вызовом `ReactUpdates.flushBatchedUpdates`, что означает обработку всего, что находится в  `dirtyComponents`’.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)

<em>9.1 `setState` start (clickable)</em>

### Отлично! Мы разобрались с *Часть 9*

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)

<em>9.2 Part 9 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)

<em>9.3 Part 9 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)

<em>9.6 Part 9 essential value (clickable)</em>


[To the next page: Part 10 >>](./Part-10.md)

[<< To the previous page: Part 8](./Part-8.md)


[Home](../../README.md)


