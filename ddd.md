# DDD

- [Entity](#entity)
- [Value object](#value-object)
- [Service](#service)
- [Цикл существования объектов модели](#цикл-существования-объектов-модели)
- [Aggregate](#aggregate)
- [Factory](#factory)
- [Repository](#repository)
- [Bounded context](#bounded-context)
- [Ассоциации между моделями](#ассоциации-между-моделями)
- [Виды доменов](#виды-доменов)
- 
Изоляция бизнес-логики в DDD очень важна, вся бизнес-логика должна быть сосредоточена в доменном уровне.

## Entity

Сущность — логически целостный объект, определяемый совокупностью индивидуальных черт. Главное свойство сущности — индивидуальность. Сущность должна иметь идентификатор.

Рассмотрим транзакции в банковских операциях. Два депозита на одну и ту же сумму в один и тот же день являются различными транзакциями, у них есть индивидуальность и поэтому они — сущности.

Бывают случаи, когда какая-то вещь при одних условиях является сущностью, при других же — нет.

## Value object

Объектом-значением называется объект, который представляет описательный аспект предметной области и не имеет индивидуального существования, собственной идентичности. Такие объекты создаются в программе для представления тех элементов проекта, о которых достаточно знать только, что они собой представляют, но не кем именно они являются.

## Service

Служба (сервис?) — операция, предлагаемая в модели в виде обособленного интерфейса, который, в отличии от сущностей или объектов-значений, не инкапсулирует никакого состояния. Службы применимы и на инфраструктурном уровне, и на уровне предметной области (домена), но нужно четко обозначать их различие.

Название службе дается по его функции, а не сути — это глагол, а не существительное.

Хорошая служба обладает тремя свойствами:

- выполняемая ею операция соответствует понятию модели, не являющемуся естественной частью сущности или объекта-значения
- интерфейс службы определен через другие элементы модели предметной области
- операция не имеет собственного состояния и сайд-эффектов

> Служба — это пример применения шаблона pure fabrication из GRASP.

## Цикл существования объектов модели

Основные проблемы:

- поддержание целостности объекта на этапе его существования
- предотвращение излишней сложности в управлении циклом существования объектов

Эти проблемы в DDD решаются с помощью таких паттернов:

- Aggregate
- Factory
- Repository

## Aggregate

Совокупность взаимосвязанных объектов, которые мы воспринимаем как единое целое с точки зрения изменения данных, называется **агрегатом**. У каждого агрегата есть **корневой объект** (aggregate root, далее AR) и есть **граница**.

Граница определяет, что находится внутри агрегата.

Корневой объект — это один конкретный объект-сущность, содержащийся в агрегате. Корневой объект — единственный член агрегата, на который могут ссылаться внешние объекты, в то время как объекты, заключенные внутри границы, могут ссылаться друг на друга как угодно.

Чтобы реализовать концепцию агрегата в виде практической программной конструкции, необходимо иметь набор правил, выполняющихся для любой транзакции:

- AR имеет глобальную идентичность и несет полную ответственность за проверку инвариантов
- некорневые объекты-сущности имеют локальную идентичность — они уникальны только в границах агрегата
- нигде за пределами агрегата не может постоянно храниться ссылка на что-либо внутри него, кроме его корневого объекта
- из базы данных мы можем получать только корневые объекты агрегатов, все остальные объекты разрешается извлекать только по цепочке связей
- объекты внутри агрегата могут хранить ссылки на корневые объекты других агрегатов
- операция удаления должна одновременно ликвидировать все, что находится в границах агрегата
- как только вносится изменение в любой объект внутри границ агрегата, следует сразу удовлетворить все инварианты этого агрегата

Группируйте сущности и объекты-значения в агрегаты и определяйте границы каждого из них. Выберите один объект-сущность и сделайте его корневым. Осуществляйте все обращения к объектам в границах агрегата только через его корневой объект. Разрешайте внешним объектам хранить ссылки только на корневой объект. Ссылки на внутренние объекты агрегата следует передавать только во временное пользование, на время одной операции. Поскольку доступ к объектам агрегата контролируется через корневой объект, неожиданные изменения внутренних объектов невозможны. В такой схеме разумно требовать удовлетворения всех инвариантов для объектов в агрегате и для всего агрегата в целом при любом изменении состояния.

## Factory

Если создание объекта или целого агрегата представляет большую сложность или открывает постороннему глазу слишком много внутренней структуры, то нужную инкапсуляцию обеспечивают фабрики.

Любая хорошая фабрика должна отвечать двум фундаментальным требованиям:

- Каждый метод создания объекта должен быть един и неделим, он должен гарантировать соблюдение всех инвариантов создаваемого объекта или агрегата, фабрика должна уметь создавать только объект целиком в корректном состоянии
- абстрагировать фабрику следует к желаемому типу, а не к конкретному классу

## Repository

Наличие хранилища снимает с клиента огромную тяжесть — теперь он может общаться с простым, скрывающим технические подробности интерфейсом, и запрашивать нужную ему информацию в терминах модели. Программа-клиент должна опираться на модель, а все операции хранения и обработки данных объектов должны быть переданы хранилищам.

Оставьте контроль транзакций клиенту. Хотя именно хранилище помещает данные в базу и извлекает их оттуда, оно, как правило, не должно контролировать их завершение (т.е. выполнять фиксацию транзакции). Конечно, есть искушение, например, зафиксировать транзакцию после сохранения данных, но у клиента наверняка есть собственный контекст для корректной инициализации и завершения отдельных рабочих операций. Контроль транзакций со стороны клиента значительно облегчается, если хранилище в это дело не вмешивается.

В проектах с большим количеством запросов можно попытаться построить не просто хранилище, а целую архитектурную среду, ассоциированную с ним, в которой можно было бы составлять более гибкие запросы. Одно из особенно удачных решений для обобщения хранилищ путем создания архитектурной среды состоит в том, чтобы строить запросы на основе спецификаций. Спецификация позволяет клиенту описывать, что именно ему нужно, и при этом не беспокоиться, как именно это делается. В процессе этого создается объект, который фактически и осуществляет нужный выбор.

## Bounded context

https://martinfowler.com/bliki/BoundedContext.html

## Ассоциации между моделями

- простые связи лучше сложных, по возможности стоит избегать many2many 
- у некоторых связей одно из направлений может быть более значимым, т.е. в каких-то случаях можно избежать двунаправленных связей, используя однонаправленные 
- самое сильное упрощение — вообще избавиться от связи, если она несущественна для работы или фундаментального понимания объектов модели

## Виды доменов

- **Core domain** - the most important subdomain, which is essential for the business. Without it the business would fail. If you ever need to pick the first solution to implement - start with the core domain.
- **Supporting subdomain** - subdomain, which is less valuable for business than Core domain. Without it business may be can even survive for some time. But it still is quite important (supports core domain), it also is specific for the domain and has to be developed. In this case, for some reason, we can't buy an existing software or component to solve the problem.
- **Generic subdomain** - subdomain which is less valuable for business than Core domain. It also is generic enough to allow buying it off the shelf (unlike supporting domain).

> Перевести на русский.