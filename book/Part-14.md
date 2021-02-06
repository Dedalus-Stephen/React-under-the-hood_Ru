## Part 14

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)

<em>14.0 Part 14 (clickable)</em>

### Финишная прямая

Метод `_updateDOMChildren` перепроверяет дочерние компоненты с помощью их различных свойств. Несмотря на то, что здесь возможны разные сценарии, технически, нас интересуют два основных случая. Либо дочерние компоненты обладают сложной структурой, в том смысле, что React придется рекурсивно пройтись по их слоям до слоя, содержащего контент, либо они представляют из себя простые типы вроде строк или чисел (контент). 

В нашем случае, имеем `ExampleApplication` с трется дочерними компонентами: `button`, `ChildCmp` и `text string`.

Давайте посмотрим как это работает.

Очевидно, что их тип это не ‘контент’, как таковой. Так что на лицо сценарий с рекурсивным обходом. По сути, для каждого из дочерних компонентов мы проделываем тоже самое, что делали для родительского компонента. Кстати, секция с `shouldUpdateReactComponent`(2) может запутать. Она выглядит как проверка на необходимость обновления, но на самом деле она проверяет либо  на обновление, либо на создание и удаление. Также, после, мы проверяем предыдущие и новые дочерние компоненты и, если какие-либо из них были удалены демонтируем компонент.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)

<em>14.1 Children update (clickable)</em>

И так, на второй итерации обрабатывается `button`. Тут все просто, потому что тип его `children` это всего лишь ‘text’, потому что `button` содержит только надпись ‘set state button’. Затем проверяется на равенство предыдущее состояния с текущим. Текст не был изменен, значит нам нет необходимости обновлять `button`, так? Так. Теперь “возможности VirtualDom” звучит уже не так абстрактно.
Думаю, вы уже уловили идею, затем мы регистрируем обновление `ChildCmp` и его потомков, и так вплоть до нижних уровней дерева. Тут уже произошло изменение, как помните: `this.props.message` обновляется при клике вызовом `setState`.

```javascript
//...
onClickHandler() {
	this.setState({ message: 'click state message' });
}

render() {
    return <div>
		<button onClick={this.onClickHandler.bind(this)}>set state button</button>
		<ChildCmp childMessage={this.state.message} />
//...

```

Мы собираемся обновить содержимое элемента, более, заменить его. Что именно из себя представляет это обновление? Это что-то вроде конфигурационного объекта, который будет обработан и установленное действие будет запущено. В нашем случае, с обновление текста, это будет выглядеть так: 

```javascript
{
  afterNode: null,
  content: "click state message",
  fromIndex: null,
  fromNode: null,
  toIndex: null,
  type: "TEXT_CONTENT"
}
```
Видите, он почти пуст. С обновлением текста все просто. Хотя в других случаях все может оказаться сложнее.

Посмотрим на код.


```javascript
//src\renderers\dom\client\utils\DOMChildrenOperations.js#172
processUpdates: function(parentNode, updates) {
    for (var k = 0; k < updates.length; k++) {
      var update = updates[k];

      switch (update.type) {
        case 'INSERT_MARKUP':
          insertLazyTreeChildAt(
            parentNode,
            update.content,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'MOVE_EXISTING':
          moveChild(
            parentNode,
            update.fromNode,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'SET_MARKUP':
          setInnerHTML(
            parentNode,
            update.content
          );
          break;
        case 'TEXT_CONTENT':
          setTextContent(
            parentNode,
            update.content
          );
          break;
        case 'REMOVE_NODE':
          removeChild(parentNode, update.fromNode);
          break;
      }
    }
  }
```
Наш случай — это ‘TEXT_CONTENT’. И это последний шаг. Вызывается `setTextContent` (3) и изменяется содержимое реального DOM.

Отлично сработано. Все на месте, осталось вызвать `componentDidUpdate`. Как вы помните, отложенные функции обратного вызова обрабатываются за счет транзакции, обновление грядного компонента обернуто при помощи `ReactUpdatesFlushTransaction`, где один из его врапперов содержит функционал для `this.callbackQueue.notifyAll()`. Он и вызовет  `componentDidUpdate`. Nice!

Похоже, что наше путешествие закончилось. Совсем.


###Мы разобрались с **Часть 14**.

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)
<em>14.2 Part 14 simplified (clickable)</em>

Еще упростим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)
<em>14.3 Part 14 simplified & refactored (clickable)</em>

И еще

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)


[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)


