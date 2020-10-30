![0.0](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/part-0.svg)

**ReactDOM.render**

Начнем с вызова ReactDOM.render

Это точка вхождения. Я создал простой компонент `<ExampleApplication/>` для дебага. **Первое, что происходит это трансформация JSX в элементы React (React elements).** Они достаточны простые: почти простые объекты с простой структурой. И представляют то, что было возвращено из рендера компонента, ничего более. Некоторые элементы уже возможно вам знакомы: пропсы (props), ключи (keys), рефы (refs). Свойство "type" относится к объекту разметки (markup object), описанного JSX. В нашем случае, это класс `ExampleApplication`, но этим могла бы быть и строка button для тэга Button и так далее. Также, во время воздания React element, React объеденит (merge) `defaultProps` с `props` (если таковые были указаны) и подтвердит (validate) `propTypes`.

Для деталей:

`src\isomorphic\classic\element\ReactElement.js`

**ReactMount**

Модуль, названный `ReactMount`(01), содержит бизнес-логику для монтирования компонентов. На самом деле, никакой бизнес-логики в `ReactDOM` нет, это просто интерфейс для работы с `ReactMount`, так что, когда вы вызываете `ReactDOM.render`, вы технически вызываете `ReactMount.render`. Так о чем же это монтирование?

>Монтирование — это процесс инициализации React компонентов путем создания представляющий из DOM элементов и добавления их в предоставленный контейнер (container).

По крайней мере комментарии в коде именно так это и представляют. Но что это все значит на самом деле? Представьте следующую трансформацию

![0.1_jsx](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/mounting-scheme-1-small.svg)

React должен **трансформировать ваши компоненты в HTML** для того, чтобы вставить их в документ (DOM document). Как он этого добивается? Правильно, ему нужно обработать все пропсы **(props), обработчики событий (event handlers), вложенные компоненты (nested components)** и логику компонента. Ему необходимо разбить ваше высокоуровневое описание (компонент) в действительно низкоуровневый формат (HTML), который может быть вставлен на веб-страницу. Это и есть монтирование.

![0.1_jsx](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/mounting-scheme-1-big.svg)

Продолжим. Но сначала интересный факт! Украсим наше путешествие, чтобы проходить его было веселее.

>Интересный факт: убедись, что скроллинг (scroll) под контроллем (02)

>Интересная штука, во время первого рендеринга (rendering) корневого компонента (root component) React инициализирует обработчики скроллинга (scroll listeners) и кеширует значение скролла для того, чтобы код приложения имел доступ к ним без повторного запуска. Правда, из-за различных имплементаций рендера в разных браузерах некоторые DOM значения не статичны, они высчитываются каждый раз при их использовании в коде. Конечно, это негативно воздействует на производительность. Эта проблема присуща по большей части для старых браузеров, которые не поддерживают `pageX` и `pageY`. React пытается оптимизировать этот процесс в том числе. 

**Создание экземпляра React компонента**

Посмотрите на схему (03), описывающую создание экземпляра. Чтож, пока еще рано говорить о создании экземпляра `<ExampleApplication />`. На самом деле создается `TopLevelWrapper` (скрытый класс React)

![0.3](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/jsx-to-vdom.svg)

Здесь иллюстрированны 3 фазы: JSX через элементы React будет конвертирован в один из внутренних типов компонента React: `ReactCompositeComponent` (для наших собственных компонентов), `ReactDOMComponent` (для HTML тэгов) и `ReactDOMTextComponent` (для текствовых узлов). Мы пропустим `ReactDOMTextComponent` и сконцетрируемся на первых двух.


Внутренние компоненты? Чтож, это интересно. Вы уже слышали **`Virtual DOM`**, ведь так? `Virtual DOM` это своеобразное представление DOM, которое используется React для того, чтобы не работать с DOM напрямую в течении работы алгоритма сверки (diff computations). Это и делает React таким быстрым! Но, на самом деле, в исходном коде React нет файлов или классов называющихся 'Virtual DOM'. Забавно! Дело в том, что V-DOM — это всего лишь концепт, подход работы с реальным DOM. Virtual DOM, в частности, относится к трем классам: 1ReactCompositeComponent`, `ReactDOMComponent`, `ReactDOMTextComponent`. Позже вы увидите как именно.


Давайте доведем наше создание экземпляра до конца. Создастся экземпляр `ReactCompositeComponent`, но не потому что мы помещаем `<ExampleApplication/>` в `ReactDOM.render`. React всегда начинает рендеринг дерева компонента с `TopLevelWrapper`. Это почти что пассивный враппер, его `рендеринг` вернет `<ExampleApplication />`, и все.

```javascript
//src\renderers\dom\client\ReactMount.js#277
TopLevelWrapper.prototype.render = function () {
  return this.props.child;
};
```

И так, только `TopLevelWrapper` был создан, ничего более на данном этапе. Двигаемся дальше. Но... сначала интересный факт!

>Интересный факт: Валидация вложенных DOM узлов

>Почти каждый раз, когда происходит рендер вложенных компонентов, они проходят валидацию специальным модулем предназначенным для валидации HTML, называемым `validateDOMNesting`. Такая валидация означает валидацию иерархии тэгов `child -> parent`. Например, если `<select>` — это родительский тэг, то дочерним тэгом может быть лишь один следующих тэгов: `option`, `optgroup` или `#text`. Эти правила описаны здесь: 
https://html.spec.whatwg.org/multipage/syntax.html#parsing-main-inselect
Вам наверное уже приходилось видеть работу этого модуля, он генерирует подобные ошибки: `*"<div> cannot appear as a descendant of <p>"*`


Мы разобрались с **Part 0**.

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

![0.3_simplified](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/part-0-A.svg)

Еще упростим:

![0.3_more_simplified](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/part-0-B.svg)

Отлично. Вот, в принципе, все, что здесь происходит. Так что мы можем взять существенное знание из этой части и использовать его для окончательной схемы `mounting`:

![0.3_essential](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/0/part-0-C.svg)

Ву а ля!
