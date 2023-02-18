# Программирование

- [Принципы ООП](#принципы-ооп)
    - [Инкапсуляция](#инкапсуляция)
    - [Полиморфизм](#полиморфизм)
    - [Наследование](#наследование)
    - [Абстракция](#абстракция)
    - [Обмен сообщениями](#обмен-сообщениями) 
- [SOLID](#solid)
    - [Single responsibility principle (SRP)](#single-responsibility-principle-srp)
    - [Open-closed principle (OCP)](#open-closed-principle-ocp)
    - [Liskov substitution principle (LSP)](#liskov-substitution-principle-lsp)
    - [Interface segregation principle (ISP)](#interface-segregation-principle-isp)
    - [Dependency inversion principle (DIP)](#dependency-inversion-principle-dip)
- [GRASP](#grasp)
    - [Information expert](#information-expert)
    - [Creator](#creator)
    - [Controller](#controller)
    - [Low coupling](#low-coupling)
    - [High cohesion](#high-cohesion)
    - [Polymorphism](#polymorphism)
    - [Pure fabrication](#pure-fabrication)
    - [Indirection](#indirection)
    - [Protected variations](#protected-variations)
- [DRY](#dry)
- [KISS](#kiss)
- [YAGNI](#yagni)
- [Tell, don't ask](#tell-dont-ask)
- [Law of Demeter](#law-of-demeter)
- [Coupling](#coupling)
- [Cohesion](#cohesion)
- [Инварианты](#инварианты)
- [Рефакторинг](#рефакторинг)
    - [Нефункциональные характеристики программ](#нефункциональные-характеристики-программ)
- [Типизация в языках программирования](#типизация-в-языках-программирования)
- [Что почитать/посмотреть](#что-почитатьпосмотреть)

## Принципы ООП

### Инкапсуляция

Связывание данных и поведения в единый механизм. Обеспечивает сокрытие данных (приватные свойства + публичные методы). 

### Полиморфизм

Полиморфизм - это способность функции обрабатывать данные разных типов. Виды полиморфизма:

- полиморфизм подтипов (наследование и апкаст)
- параметрический (дженерики)
- специальный, "ad hoc" (перегрузка методов)

### Наследование

Увеличивает coupling. Как насчет композиции?

### Абстракция

Абстрагироваться - значит мысленно отвлекаться от тех или иных сторон, свойств или связей предметов и явлений с целью выделения существенных и закономерных их признаков. Мы упрощаем какую-то вещь с помощью абстракции, что позволяет нам легче справляться с ее сложностью. При этом абстракция не является чем-то, что должно помогать скрывать сложность.

### Обмен сообщениями

Взаимодействие между объектами осуществляется посредством отправки сообщений друг другу. Обмен сообщениями приводит к чрезвычайно позднему связыванию (extreme late binding).

## SOLID

Основной смысл - стремление уменьшить эффект каскадного изменения модулей.

### Single responsibility principle (SRP)

На каждый класс должна быть возложена одна единственная обязанность. Классы должны иметь одну и только одну причину для изменений.

### Open-closed principle (OCP)

Классы должны быть открыты для расширения, но закрыты для изменения. Т.е. классы могут менять свое поведение без изменения их исходного кода.

### Liskov substitution principle (LSP)

Все классы, реализующие один интерфейс должны быть полностью взаимозаменяемыми.

### Interface segregation principle (ISP)

Клиенты не должны зависеть от методов, которые они не используют. Слишком "толстые" интерфейсы необходимо разделять на более маленькие и специфические, чтобы клиенты маленьких интерфейсов знали только о методах, которые необходимы им в работе. В итоге, при изменении метода интерфейса не должны меняться клиенты, которые этот метод не используют.

### Dependency inversion principle (DIP)

Состоит из двух правил:

- модули высокого уровня не должны зависеть от модулей низкого уровня - оба должны зависеть от абстракций
- абстракции никогда не должны зависеть от деталей - детали должны зависеть от абстракций



## GRASP

GRASP - тот же SOLID, но другими словами. Если плохо воспринимается SOLID - можно посмотреть GRASP.

### Information expert

Ответственность должна быть назначена тому, кто владеет максимумом необходимой информации для исполнения - **информационному эксперту**.

> Например, имеется **Customer**, который имеет ссылки на все свои **Order**. Очевидно, **Customer** может взять на себя ответственность за рассчет общей стоимости всех **Order**, т.к. он владеет всей необходимой для этого действия информацией.

### Creator

Класс должен создавать экземпляры тех классов, которые он может содержать или агрегировать, записывать, использовать и инициализировать, имея нужные данные.

```php
final class Customer
{
    private array $orders;
    
    public function addOrder(array $products): void
    {
        $order = new Order($products); // Customer является создателем для Order.
        
        $ordersThatOrderedToday = array_filter($this->orders, static function (Order $order) => {
            return $order->isOrderedToday();
        });
        
        if (count($ordersThatOrderedToday) > 1) {
            throw new \DomainException('Нельзя создать более одного заказа в день.');
        }
        
        $this->orders[] = $order;
    }
}
```
### Controller

Отвечает за операции, запросы на которые приходят от пользователя. Не выполняет работу самостоятельно, а делегирует компетентным исполнителям.

Не является контроллером из MVC! Контроллеры в GRASP - это объекты управляющие обработкой бизнес-транзакций (Use case, Interactor, Handler и т.п.).

### Low coupling

Слабая связь - распределение ответственностей и данных, обеспечивающее взаимную независимость элементов. Элемент со "слабой" связью:

- имеет слабую зависимость от других элементов системы
- не зависит от внешних изменений (изменение одного элемента оказывает слабое влияние на другие элементы)
- прост для повторного использования

> См. также "shotgun surgery".

### High cohesion

По сути это single responsibility принцип. Это мера того, насколько сильно связаны все обязанности элемента. Другими словами, в какой степени части внутри элемента принадлежат друг другу.

### Polymorphism

См. [полиморфизм](#полиморфизм).

### Pure fabrication

Существует понятие модели программирования по предметной области, согласно которой, каждой сущности из предметной области соответствует один или более классов программной среды. При этом, обязанности взаимодействия сущностей, как правило накладываются на них самих. Такой подход имеет очевидный недостаток - высокая связность модулей системы. Шаблон чистая выдумка позволяет решить данную проблему, путем введения в программную среду дополнительного класса (не отражающего реальной сущности из предметной области) и наделение его требуемыми обязанностями.

Например, обязанности по сохранению информации о клиентах судоходной компании можно назначить выдуманному классу **ClientsSaver**.

Применение этого шаблона позволяет дизайну системы соответствовать принципам low coupling и high cohesion.

Иногда действительно сложно понять, на что следует возложить ответственность. Вот почему в DDD существует концепция доменного сервиса. Доменные сервисы содержат логику, не связанную с одной конкретной сущностью.

### Indirection

> Нужно наполнить.

### Protected variations

> Нужно наполнить.

## DRY

Каждая часть знания должна иметь единственное, непротиворечивое и авторитетное представление в рамках системы. Вот отличная статья - https://verraes.net/2014/08/dry-is-about-knowledge.

## KISS

Принцип KISS утверждает, что большинство систем работают лучше всего, если они остаются простыми, а не усложняются. Поэтому в области проектирования простота должна быть одной из ключевых целей, следует избегать ненужной сложности.

## YAGNI

Отказ от добавления функциональности, в которой нет непосредственной надобности.

## Tell, don't ask

Нужно "говорить" объектам, что ты хочешь сделать, а не "спрашивать" состояние объекта и на основе этих данных принимать решение о действии над этим объектом. Объект в состоянии самостоятельно принять решение - его состояние принадлежит ему.

Такой подход положительно влияет на [инкапсуляцию](#инкапсуляция) и в каком-то смысле следует принципу [обмена сообщениями](#обмен-сообщениями). 

> In my experience, object-oriented code is easier to understand and maintain if it is written in a "Tell, Don't Ask" style. In this style, objects make decisions using only the information that they hold internally or that they receive as message parameters; they do not make decisions using information that is held by other objects. That is, objects tell each other what to do by sending commands to one another, they don't ask each other for information and then make decisions upon the results of those queries. The end result of this style is that it is easy to swap one object for another that can respond to the same commands but in a different way. This is because the behaviour of one object is not tied to the internal structure of the objects that it talks to. How does that apply to Mock Objects? If objects interact by sending each other commands, their public interfaces provide no methods that let you interrogate their state. The only way to observe the behaviour of an object is to see how it affects the state of its world by sending commands to other objects. And that's what Mock Objects let you do. The extreme opposite of the "Tell, Don't Ask" style is "train-wreck" code that contains lots of statements like "object.getPart().getSubpart().getAttribute()" and "object.getPart().getSubpart().setAttribute(x)". In coding style the implementation of one object is coupled to the structure of its neighbours and its neighbours' neighbours, and so it is difficult to replace its neighbour with one that is implemented differently. This style of code is hard to test with Mock Objects. You find yourself creating lots of mock objects that only exist to let the object under test reach the objects that it actually uses. That's a strong sign that the code needs refactoring: you can simplify the code by introducing new methods in the immediate neighbours of the object under test.

## Law of Demeter

Говоря упрощённо, каждый программный модуль:

- должен обладать ограниченным знанием о других модулях - знать о модулях, которые имеют непосредственное отношение к этому модулю
- должен взаимодействовать только с известными ему модулями "друзьями", не взаимодействовать с "незнакомцами"
- обращаться только к непосредственным "друзьям"

## Coupling

"Плохие" виды связанности - content coupling, common (или global) coupling, temporal coupling, тысячи их. Вот тут подробнее - https://en.wikipedia.org/wiki/Coupling_(computer_programming)#Types_of_coupling.

Виды:

### Content

### Common

### External

### Control

См. [Tell, don't ask](#tell-dont-ask)

### Stamp (data-structured)

Когда в модуль передается больше данных чем необходимо.

```php
final class SomeException extends \Exception
{
    public function __construct(Entity $entity) // Хотя достаточно передать только идентификатор.
    {
        parent::__construct(\sprintf('Something went wrong with entity #%s', $entity->id()->value));
    }
        
}
```

### Data

### 
Избавиться от связанности не получится, можно только выбрать варианты получше, например, data coupling или message coupling.

![image](https://user-images.githubusercontent.com/13543469/219868639-6e1bbd97-2462-43b6-a6b3-4ab3d9985611.png)

## Cohesion

https://en.wikipedia.org/wiki/Cohesion_(computer_science)#:~:text=In%20computer%20programming%2C%20cohesion%20refers,concept%20served%20by%20that%20class.

Виды:

- coincidental (worst)
- logical
- temporal
- procedural
- communicational/informational
- sequential
- functional (best)
- perfect cohesion (atomic)

## Инварианты

В коде они обычно выглядят так:

- транзакции - какие то изменения, которые либо прошли целиком, либо целиком откатились
- валидация перед сохранением - которая либо дала сохранить изменения, либо упала с ошибкой о некорректном состоянии
- в целом, любое исключение может оказаться следствием поддержки инварианта - обеспечение целостности
- функциональные программы часто инвариантны, т.к. не меняют состояние исходного объекта
- приватные поля объекта, которые может менять только сам объект, плюс каждый публичный метод меняет их из рабочего и согласованного состояния в рабочее и согласованное состояние

## Рефакторинг

При анализе большинства программ обнаруживается, что большая часть времени расходуется небольшой частью кода. Если в равной мере оптимизировать весь код, то окажется, что 90% оптимизации произведено впустую.

Вместо реализации гибких решений следует задаться вопросом - "насколько сложно будет с помощью рефакторинга преобразовать обычное решение в гибкое?". Если, как чаще всего случается, ответ будет "весьма несложно", то надо просто реализовать обычное решение.

### Нефункциональные характеристики программ

Эти характеристики очень интересуют пользователей, они заметны невооруженным глазом (точнее, эти характеристики не интересуют пользователей, пока не станут плохими):

- скорость ответа (response time, performance)
- отсутствие багов (reliability, usability)
- доступность (availability)
- устойчивость к отказам (resilience)

А эти интересуют владельца продукта и программистов, но они необходимы, чтобы поддерживать первые четыре на должном уровне, а также чтобы вовремя выпускать новые фичи:

- расширяемость (extendability)
- поддерживаемость (maintainability)
- среднее время починки (mean time to repair)
- стоимость разработки и поддержки (cost)
- тестируемость (testability)
- возможность аудита и отладки (auditability)

Именно в зоне нефункциональных характеристик находится точка пересечения интересов программистов, владельца продукта и пользователей. Когда просите рефакторинг - указывайте, какие именно нефункциональные характеристики в данный момент вас не устраивают. Ваши аргументы должны основываться на том, какие проблемы есть у проекта в данный момент или какие проблемы появятся в ближайшем будущем, если ситуацию не изменить (превентивный менеджмент рисков).

> Если в проекте очень сложный и запутанный код, то, скорее всего, хромает maintainability, скорость разработки и тестируемость. Если там старые библиотеки и фреймворки, то, скорее всего, у вас проблемы с расширяемостью. Если архитектура не предполагала большого количества запросов, а они ожидаются - это грядущие проблемы с scalability и performance. Если вы тратите половину рабочего времени на поиск причин багов - явно стоит улучшать auditability.

## Типизация в языках программирования

Существует два вида типизации - **статическая** (типы проверяются на этапе компиляции) и **динамическая** (типы проверяются в рантайме).

Типизация может быть **сильной** (строгой) и **слабой**. В разных языках программирования уровень строгости различается, например, в JavaScript слабая типизация, в PHP средняя, в Python ближе к сильной.

**Утиная типизация** - определение факта реализации определённого интерфейса объектом без явного указания или наследования этого интерфейса, а просто по реализации полного набора его методов.

## Что почитать/посмотреть

- [Функторы, аппликативные функторы и монады в картинках](https://habr.com/ru/post/183150)
- [Simple made easy](https://www.youtube.com/watch?v=SxdOUGdseq4)
- [Principle of least astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
