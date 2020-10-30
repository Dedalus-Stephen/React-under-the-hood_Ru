**Схема, первое знакомство**
![scheme main](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/intro/all-page-stack-reconciler.svg)

Уделите немного времени, чтобы ознакомиться с ней. Она может показаться сложной и запутанной, но, на самом деле, она описывает всего лишь два процесса: mount (монтирование) и update (обновление). Я пропустил размонтирвоание (unmount), т.к. оно на самом деле является обратным монтированием и его остутствие упрощает схему. Также, здесь представлена схема не всего кода, но только его основные части, которые описывают архитектуру. В сумме, она (схема) представляет 60% кода, однако остальные 40% не внесут существенных визуальных изменений.

При первом ознакомлении вы возможно заметили разнообразные цвета, которые испульзуются в схеме. Каждый элемент (форма в схеме) окрашен в цвета его родительского модуля. Например, methodA будет окрашен в красный, если он был вызван из moduleB, цвет которого тоже красный. Нижеприведенная таблица описывает цвет каждого модуля и путь к каждому из них.

![legend](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/intro/modules-src-path.svg)

Приводя их к схематическому представлению, получаем схему зависимостей между модулями.

![module dependencies](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/intro/files-scheme.svg)

Вы наверное знаете: React создан для поддержки множества платформ.

*Mobile (ReactNative)
*Browser (ReactDOM)
*Server Rendering
*ReactART (для прорисовки векторной графики с использованием React)
*и так далее.


По этой причине количество файлов (в библиотеке?) на самом деле больше, чем в вышеприведенной схеме.
Ниже: та же схема файлов, но с множественной поддержкой (multi-support)

![platfrom dependencies](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/236a844a24f38edc8c3d5c2cb9be53d91e0031dc/stack/images/intro/modules-per-platform-scheme.svg)

На схеме несколько элементов кажутся продублированными. Это демонстрирует наличие отдельных имплементаций для каждой платформы. Рассмотрим, для примера, ReactEventListener. Очевидно, его имплементация различается для разных платформ. Технически, зависимые модули (dependent modules) должны быть каким-либо образом включены или подсоединены к данной диаграмме и, действительно, существует множество механизмов для такого взимодействия. Исходя из того, что их использование это часть паттерна "Компоновщик" (composite pattern), опять-таки для простоты я решил их пропустить.

Мы будем изучать эту диаграмму (logic flow) для React DOM в обычном браузере.


**Рабочий экземпляр**

Очевидно, лучшим способом изучения фреймворка или библиотеки является чтение-и-дебаг кода. Мы собираемся заняться дебагом двух процессов: ReactDOM.render и component.setState, которые отвечают за монтирование и обновление соответственно.

Давайте взглянем на код, с которого мы и начнем. Что нам нужно? Наверное несколько маленьких компонентов с простыми рендерами, так чтобы облегчить процесс дебага.

```javascript
class ChildCmp extends React.Component {
    render() {
        return <div> {this.props.childMessage} </div>
    }
}

class ExampleApplication extends React.Component {
    constructor(props) {
        super(props);
        this.state = {message: 'no message'};
    }

    componentWillMount() {
        //...
    }

    componentDidMount() {
        /* setTimeout(()=> {
            this.setState({ message: 'timeout state message' });
        }, 1000); */
    }

    shouldComponentUpdate(nextProps, nextState, nextContext) {
        return true;
    }

    componentDidUpdate(prevProps, prevState, prevContext) {
        //...
    }

    componentWillReceiveProps(nextProps) {
        //...
    }

    componentWillUnmount() {
        //...
    }

    onClickHandler() {
        /* this.setState({ message: 'click state message' }); */
    }

    render() {
        return <div>
            <button onClick={this.onClickHandler.bind(this)}> set state button </button>
            <ChildCmp childMessage={this.state.message} />
            And some text as well!
        </div>
    }
}

ReactDOM.render(
    <ExampleApplication hello={'world'} />,
    document.getElementById('container'),
    function() {}
);
```

И так, мы готовы начать. Давайте перейдем к первой части схемы. Шаг за шагом мы изучим ее всю.
