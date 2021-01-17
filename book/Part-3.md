## Part 3

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)

### Монтирование

Метод `componentMount` это одна из самых больших частей нашего путешествия! Конкретнее, метод, который нас интересует это `ReactCompositeComponent.mountComponent`(1).

Если вы помните, я уже упоминал, что **первый компонент, который вставляется в дерево компонентов** это `TopLevelWrapper` (внутренний компонент React). Сейчас мы собираемся монтировать его. Но... это, по сути, пустой враппер, так что дебажить его в каком-то смысле достаточно скучно. Он не влияет на общий процесс вообще, поэтому я предлагаю его пропустить сейчас и перейти к его дочерним классам.

Так монтирования дерева и происходит — монтируется родительский компонент, потом его дочерние, потом дочерние дочерних и так далее. Поверьте на слово: после того как `TopLevelWrapper` монтируется его дочерний класс (`ReactCompositeComponent`, который управляет`ExampleApplication` компонентом) будет вставлен в ту же фазу процесса.

И так, мы снова у первого шага (1). Посмотрим, что внутри. Здесь присутствуют ключевые действия, которые вот-вот произойдут, так что давайте обсудим имплементацию в деталях.

### Присвоение модуля обновления (`updater`)

Этот `updater` (2), возвращенный из `transaction.getUpdateQueue()`, на самом деле является модулем `ReactUpdateQueue`. Почему же тогда он **присвается здесь**? Потому что `ReactCompositeComponent` (класс, который мы рассматриваем сейчас) это класс одинаковый для всех платформ, однако `updater`-ы различаются, поэтому мы присваеваем его динамично во время монтирования в зависимости от платформы.

Отлично. Этот `updater` не особо нужен нам сейчас в нашем изучении, но помните, что он действительно **важен**, и будет использован вскоре в, всем хорошо известном, методе **`setState`**.

Кроме того, не только `updater` присвается экземпляру на этом этапе. Экземпляр нашего (написанного нами) компонента расширяется с `props`, `context`, и `refs`.

Взгляните на код: 

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#255
// These should be set up in the constructor, but as a convenience for
// simpler class abstractions, we set them up after the fact.
inst.props = publicProps;
inst.context = publicContext;
inst.refs = emptyObject;
inst.updater = updateQueue;
```

Так что потом вы можете получить доступ к `props` в своем коде с обрабатываемого экземпляра (`this.props`).

### Создание экземпляра ExampleApplication

Вызовом `_constructComponent` (3) и несколькими другими конструкциями, наконец, будет создан `new ExampleApplication()`. Это момент в котором конструктор из нашего кода будет вызван. Так что это первый момент, когда экосистема React прикасается к нашему коду!

### Осуществление первоначального монтирования

Двигаемся дальше по процессу mount (4). И первое, что должно произойти это вызов `componentWillMount` (если вызывается в нашем коде, конечно). Это первый их методов жизненного цикла, которые мы встречаем. Чуть ниже есть `componentDidMount`, но пока что он всего лишь был вставлен в очередь транзакции — он не вызывается напрямую. Его вызов произойдет только в самом конце, когда все операции монтирования уже выполнены. Теоретически вы также можете добавить вызов `setState` в `componentWillMount`. В этом случае состояние, конечно же, будет пересчитанно, но без вызова `render` метода (его вызов не имел бы смысла, потому что, ведь, компонент еще не был монтирован).

Официальная документация это подтверждает: 

> *`componentWillMount()` is invoked immediately before mounting occurs. It is called before `render()`, therefore setting state in this method will not trigger a re-rendering.*

> `componentWillMount()` вызывается непосредственно перед монтированием. То есть перед `render` методом, поэтому установка состояние здесь не провоцирует ререндеринга.

Взглянем на код. Просто, чтобы убедиться ;)

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#476
if (inst.componentWillMount) {
    //..
    inst.componentWillMount();

    // When mounting, calls to `setState` by `componentWillMount` will set
    // `this._pendingStateQueue` without triggering a re-render.
    if (this._pendingStateQueue) {
        inst.state = this._processPendingState(inst.props, inst.context);
    }
}
```

Все правильно. И так, когда `state` пересчитывается мы вызываем `render` метод. Да, тот самый, который мы описываем в наших компонентах! То есть происходит еще одно 'касание' React-ом нашего кода.

Следующая вещь, которая будет создана — это React component instance (экземпляр компонента React).

Эммм... что, опять? Кажется мы уже где-то видели вызов `this._instantiateReactComponent`(5). Все так, но тогда мы создавали `ReactCompositeComponent` для нашего компонента `ExampleApplication`. Теперь же собираемся создаать экземпляры VDOM для его дочерних компонентов основанные на том, что мы получили из `render` метода. В нашего конкретного случая, `render` метод возвращает `div`, так что VDOM представлением для этого будет `ReactDOMComponent`. Когда экземпляр создан, мы вызываем `ReactReconciler.mountComponent` снова, но на этот раз как `ReactReconciler.mountComponent` мы передаем новосозданный экземпляр `ReactDOMComponent`.

И, вызываем его ``mountComponent`...


### Alright, we’ve finished *Part 3*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)

<em>3.1 Part 3 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)

<em>3.2 Part 3 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 3* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)

<em>3.3 Part 3 essential value (clickable)</em>

And then we're done!


[To the next page: Part 4 >>](./Part-4.md)

[<< To the previous page: Part 2](./Part-2.md)


[Home](../../README.md)
