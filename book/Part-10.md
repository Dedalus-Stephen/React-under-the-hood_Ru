## Part 10

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)

<em>10.0 Part 10 (clickable)</em>

### Dirty components

Как видите, React проходит через `dirtyComponents`(1), и вызывает `ReactUpdates.runBatchedUpdates`(2) через транзакцию. Опять транзакция? Новая. Но зачем?

Тип этой транзакции `ReactUpdatesFlushTransaction` и, мы уже говорили об этом,  необходимо проверить`wrappers`, чтобы понять, что эта транзакция делает. 

Небольшая подсказка из кода: 
> ‘ReactUpdatesFlushTransaction's wrappers will clear the dirtyComponents array and perform any updates enqueued by mount-ready handlers (i.e., componentDidUpdate)’

Но нам нужны доказательства. Здесь имеем два враппера: `NESTED_UPDATES` и `UPDATE_QUEUEING`. На студии `initialize` мы сохраняем `dirtyComponentsLength` (3) и React  проверяет не изменилось ли количество “dirty components”, потому что в таком случае придется запустить `flushBatchedUpdates` еще раз. Никакой магии, все просто.

Хотя… немного волшебства все же есть. `ReactUpdatesFlushTransaction` перезаписывает (overrides) метод `Transaction.perform`, потому что… он требует  `ReactReconcileTransaction` (транзакция используется в процессе монтирования). Внутри метода `ReactUpdatesFlushTransaction.perform` `ReactReconcileTransaction` также используется, метод обертывается еще раз.

Технически это выглядит так:

```javascript
[NESTED_UPDATES, UPDATE_QUEUEING].initialize()
[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].initialize()

method -> ReactUpdates.runBatchedUpdates

[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].close()
[NESTED_UPDATES, UPDATE_QUEUEING].close()
```

Мы вернемся к транзакции в конце, чтобы убедиться, что она действительно помогает завершить работу метода. А сейчас давайте изучим `ReactUpdates.runBatchedUpdates`(2) (`\src\renderers\shared\stack\reconciler\ReactUpdates.js#125`) детальнее.

Первое, что происходит — это сортировка массива `dirtyComponets`.  Но в каком порядке? По `mount order` (целочисленное значение присвоенное компоненту при создании экземпляра). Это означает, что родительские элементы, очевидно монтированные раньше, будут обновлены первыми и дальше вниз по рекурсии.

Следующий шаг, инкрементируется `updateBatchNumber` — это что-то вроде ID данной сверки (reconciliation). Согласно комментарию в коде: 

> ‘Any updates enqueued while reconciling must be performed after this entire batch. Otherwise, if dirtyComponents is [A, B] where A has children B and C, B could update twice in a single batch if C's render enqueues an update to B (since B would have already updated, we should skip it, and the only way we can know to do so is by checking the batch counter).’

>’Всякие обновления в очереди во время сверки должны быть осуществлены после  обработки всей партии (batch). В противном случае, если dirtyComponent, скажем A, который имеет дочерними компонентами B и C,  может привести к повторному обновлению B, если рендер C ставит в очередь обновление B (постольку поскольку B уже обновлен, мы пропустим его и единственный способ узнать так ли это это проверить batch counter).’

В конце, проходим по `dirtyComponents` и передаем каждый компонент в `ReactReconciler.performUpdateIfNecessary` (5), где метод `performUpdateIfNecessary` будет вызван из экземпляра `ReactCompositeComponent`. И так вернемся к `ReactCompositeComponent` и его методу `updateComponent`.  Здесь есть кое-что интересное, что погрузит нас еще глубже.

### Отлично! Мы разобрались с *Часть 10*

Давайте резюмируем. Взглянем на схему еще раз, удалим ненужные менее важные кусочки и получим:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)

<em>10.1 Part 10 simplified (clickable)</em>

Уберем пробелы и выровняем все:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)

<em>10.2 Part 10 simplified & refactored (clickable)</em>

Отлично. Вот и все, что здесь происходит. Возьмем самое существенное:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)

<em>10.3 Part 10 essential value (clickable)</em>


[To the next page: Part 11 >>](./Part-11.md)

[<< To the previous page: Part 9](./Part-9.md)


[Home](../../README.md)



