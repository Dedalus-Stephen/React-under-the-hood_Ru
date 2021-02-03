## Part 5

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5.svg)

<em>5.0 Part 5 (clickable)</em>

### Обновление свойств DOM

Схема. Выглядит страшно :) Суть в том, чтобы применить diff алгоритм на предыдущих и новых пропсах эффективно. Взглянем на коммент из кода:

> *“Reconciles the properties by detecting differences in property values and updating the DOM as necessary. This function is probably the single most critical path for performance optimization.”*

> "Сверяет свойства путем обнаружения различий в значениях свойств и обновлением по необходимости DOM. Наверное это единственная функция с такой значимостью для оптимизации производительности."

Здесь два цикла. Первый проходит через предыдущие пропсы, второй через новые. В нашем случае, с монтированием, `lastProps` (предыдущие) пусты (очевидно потому что это первое присваивание пропсов), но мы все равно посмотрим, что тут происходит.

### Проход по предыдущим пропсам

Первым шагом мы проверяем содержит ли `nextProps` то же значение. Если да, мы их просто пропускаем, потому что это потом будет обработано в цикле для `nextProps`. Потом мы сбрасываем значения стилей (styles), удаляем обработчики событий (если присутствуют), удаляем атрибуты DOM и значения DOM свойств. Для атрибутов мы также убеждаемся, что не типа `RESERVED_PROPS`, что они `prop`, как например `children` или `dangerouslySetInnerHTML`.


### Проход по новым пропсам

Здесь первым шагом является проверка того, что `prop` изменился или нет. То есть является ли следующее значение отличным от текущего. Если нет, опять-таки, ничего не делаем. Для `styles` (вы наверное уже заметили, что React обращается с ними по особому) мы обновляем значения, которые изменились по сравнению с `lastProp`. Затем добавляем обработчики событий (`onClick`, например). Давайте углубимся в детали.

Важно понять, что в React приложении вся работа проходит через именованные 'синтетическими' события. Ничего особенного, это просто еще парочка врапперов для эффективной работы. Далее, модуль-посредник для управления обработчиками событий: `EventPluginHub` (`src\renderers\shared\stack\event\EventPluginHub.js`). Он содержит `listenerBank` map для кеширования и управления всеми обработчиками. Мы добавим наши собственные обработчики, но не сейчас. Смысл в том, что мы должны добавлять обработчики когда компонент и DOM элемент готовы для обработки событий. Кажется, что мы имеем тут отложенное исполнение, но вы наверное спросите, как мы понимаем, когда этот момент настает. Что ж, время для ответа! Помните, мы передавали `transaction` через все методы и вызовы? Верно! Мы делали это, потому что необходимо именно для подобных ситуаций. Посмотрим на доказательство в коде:

```javascript
//src\renderers\dom\shared\ReactDOMComponent.js#222
transaction.getReactMountReady().enqueue(putListener, {
    inst: inst,
    registrationName: registrationName,
    listener: listener,
});
```

Окей, после обработчиков событий мы устанавливаем DOM атрибут и значения  DOM свойств. Как и прежде, для атрибутов мы убеждаемся, что они не типа `RESERVED_PROPS`, что они `prop`, как например `children` или `dangerouslySetInnerHTML`.

### Alright, we’ve finished *Part 5*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-A.svg)

<em>5.1 Part 5 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-B.svg)

<em>5.2 Part 5 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 5* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-C.svg)

<em>5.3 Part 5 essential value (clickable)</em>

And then we're done!


[To the next page: Part 6 >>](./Part-6.md)

[<< To the previous page: Part 4](./Part-4.md)


[Home](../../README.md)
