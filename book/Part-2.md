[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)

<em>2.0 Part 2 (clickable)</em>

### Еще одна транзакция

На этот раз это `ReactReconcileTransaction`. Как мы уже поняли, нас особенно здесь интересуют врапперы транзакции. Их тут три:

```javascript
//\src\renderers\dom\client\ReactReconcileTransaction.js#89
var TRANSACTION_WRAPPERS = [
  SELECTION_RESTORATION,
  EVENT_SUPPRESSION,
  ON_DOM_READY_QUEUEING,
];
``` 

Как видим, эти врапперы по большей части используются **для того, чтобы сохранить состояние компонента**, сохранить изменяемые величины перед вызовом методов и вернуть их после. React гарантирует, что, например, текстовый ввод не изменит своего значения во время транзакции. Так же он блокирует ивенты (blur/focus), которые мошли бы непреднамеренно выполнится в процессе высокоуровневых DOM манипуляций (например, временное удаление `text input` из DOM) путем **деактивации `ReactBrowserEventEmitter`** на этапе `initialize` и активации на этапе `close`.

Что ж, мы подобрались достаточно близко к процессу монтирования компонента, который вернет нам разметку  готовую для вставки в DOM. Помните, что `ReactReconciler.mountComponent` это всего лишь враппер или, точнее будет назвать его 'посредник'. Он делегирует монтирующийся метод модулям компонента. Это важно, поэтому подчеркнем:

> Модуль `ReactReconciler` вызывается всегда в тех случаях когда имплементация какой-либо логики **зависит от платформы**, как в этом конкретном случае. Монтирование различается в зависимость от платформы, поэтому 'главный модуль' сообщается с `ReactReconciler`, а последний уже знает, что делать дальше.


Хорошо, перейдем к методу `mountComponent`. Скорее всего вы уже о нем слышали. Он инициализирует компонент, рендерит разметку и регистрирует обработчиков событий (event listeners). Вот — тернистый путь и мы наконец дошли до вызова метода монтирования. После его вызова мы должны получить HTML элементы, которые могут быть вставлены в документ.


### И так, мы разобрались с *Part 2*.

Повторим пройденное, еще раз взглянем за схему, уберем незначительные части из нее и получим это:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)

<em>2.1 Part 2 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)

<em>2.2 Part 2 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)

<em>2.3 Part 2 essential value (clickable)</em>

Готово!
