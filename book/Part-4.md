## Part 4

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)

<em>4.0 Part 4 (clickable)</em>

### Монтирование дочерних компонентов

Давайте продолжим исследование  `mount` метода.
 
И так, если `_tag` содержит тэг ‘complex’ (1), например, видео, формы, textarea и так далее, то потребуется дополнительное обертывание.  Оно добавляет больше обработчиков событий для каждого медиа события, например ‘volumechange’ для `audio` тэгов или просто оборачивает нативное поведение таких тэгов как `select`, `textarea` и так далее.
Есть несколько врапперов для таких элементов, таких как `ReactDOMSelect` и `ReactDOMTextarea` (в src\renderers\dom\client\wrappers\). В нашем случае это просто `div`, поэтому дополнительной обработки не будет.

### Валидация пропсов

Следующий метод валидации вызывается просто для того, чтобы убедить что внутренние `props` установлены корректно, в противном случае метод спродуцирует ошибки.

Например, если `props.dangerouslySetInnerHTML` установлен (обычно это результат вставки в HTML из строки) и ключ объекты `__html` пропущен, будет сгенерированна такая ошибка:

> `props.dangerouslySetInnerHTML` must be in the form `{__html: ...}`.  Please visit https://fb.me/react-invariant-dangerously-set-inner-html for more information.

### Создание HTML элемента

Потом сам HTML элемент будет создан (3) при помощи `document.createElement`, который инстанциирует 'настоящий' HTML `div`. До этого мы работали только с виртуальной репрезентацией и теперь видим реальный элемент впервые.


### Alright, we’ve finished *Part 4*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)

<em>4.1 Part 4 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)

<em>4.2 Part 4 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 4* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)

<em>4.3 Part 4 essential value (clickable)</em>

And then we're done!


[To the next page: Part 5 >>](./Part-5.md)

[<< To the previous page: Part 3](./Part-3.md)


[Home](../../README.md)
