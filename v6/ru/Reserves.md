---
title: Резервы
layout: default
---
# Резервы #

Для планирования мероприятий предусмотрен функционал бронирования столов в залах ресторана. В зависимости от мероприятия заранее можно спланировать время и место его проведения, список гостей, меню. По степени подготовки мероприятия резервы делятся на две категории: бронирование столов и банкеты.

С помощью API можно реализовать резервирование столов через веб-сайт или мобильное приложение, предоставив клиенту возможность самостоятельно выбрать предпочтительное место в зале (например, у окна, подальше от сцены или рядом с розеткой).

## Бронирование столов ##

Данный тип резервов применяется главным образом для того, чтобы гарантировать наличие свободного стола на вечер для небольшой группы гостей. В API резервы представлены типом `IReserve`, включающим в себя следующую информацию:

- Клиент (`IClient`) — элемент справочника клиентов, хранит имя человека, его контактные данные. Позволяет связаться при необходимости уточнить детали.
- Ожидаемые время начала и продолжительность мероприятия (`EstimatedStartTime`, `Duration`). Эти сведения позволяют эффективно управлять заполнением залов ресторана, избегая ситуаций, когда гости приходят к назначенному времени, а их стол занят, или, напротив, гости не пришли, а их стол простаивает, помогает рассаживать гостей, пришедших без предупреждения. Также система может заблаговременно напоминать официантам о необходимости готовить стол к приходу гостей (`ShouldRemind`).
- Стол (`ITable`) — определяет резервируемый стол. В интерфейсе iikoFront стол будет отмечен как занятый, дополнительно можно распечатать и разместить на столе специальную табличку о резерве (см. `PrintTableReservedCheque`). Резервирование нескольких столов разом (для большой группы гостей) не поддерживается, придётся выбрать один стол.
- Ожидаемое количество гостей (`GuestsCount`), соответствует свойству `EstimatedGuestsCount` заказа. Эта информация подскажет, на сколько персон сервировать стол, сколько поставить стульев и т. п. Не путать с количеством виртуальных гостей (`OrderItemGuest`), которое определяет официант по своему усмотрению.
- Произвольный комментарий (`Comment`). Здесь могут быть указаны, например, особые пожелания клиента.
- Время начала (`GuestsComingTime`) — фактическое время прихода гостей, устанавливается в момент закрытия резерва или в момент начала банкета.    

Жизненный цикл резерва прост — он закрывается либо с созданием заказа на соответствующем столе (`ActivateReserve`), если клиент пришёл и запланированное мероприятие началось, либо с указанием причины отмены (`CancelReserve`), если клиент попросил снять резерв или не пришёл к назначенному времени. Кроме того, резерв может быть преобразован в банкет путём присоединения к нему заказа (`BindReserveToOrder`).  

## Банкеты ##

Банкет — это связка резерва и заказа (`IReserve` и `IOrder`). Совместное их использование позволяет дополнительно к возможностям резерва заранее согласовать с клиентом меню и внести предоплату. Обычно это используется при проведении больших мероприятий, чтобы ресторан мог не только зарезервировать стол (а чаще целый банкетный зал), но и закупить на кухню продукты к предстоящему событию, украсить должным образом помещения и прочее.

Банкет может быть создан с нуля, на базе существующего заказа (`CreateBanquet`), либо путём соединения созданных ранее заказа и резерва (`BindReserveToOrder`), при этом необходимо, чтобы заказ и резерв находились на одном и том же столе, а ожидаемое количество гостей в них совпадало.

Поскольку банкет представляет собой резерв, связанный с заказом, он закрывается не в момент прихода гостей, а при закрытии заказа, соответственно, его жизненный цикл помимо статусов `New` и `Closed` содержит дополнительный промежуточный статус — `Started`.

## Удаление ##
Как и заказы, закрытые резервы удаляются при закрытии соответствующей кассовой смены.

Открытые резервы и связанные с ними банкетные заказы теоретически должны существовать до тех пор, пока не будут закрыты, но в силу особенностей механизма их хранения это не всегда так. Представленная ниже информация не относится к API, это деталь реализации, которая может быть изменена в любой момент, но знание которой может помочь избежать неприятных неожиданностей. Резервы и заказы хранятся по отдельности и при установке новых версий iikoFront обновляются по-разному: копии резервов хранятся на сервере iikoRms, откуда iikoFront и загружает их при первом запуске новой версии, в то же время заказы хранятся и обновляются непосредственно на терминалах iikoFront. Это может приводить к несогласованности:

* Произвели обновление iikoFront. В первое время (обычно непосредственно после первого запуска новой версии) могут отсутствовать резервы (`IReserve`), соответственно, информации по забронированным столам в этот момент не будет совсем, а для банкетов будут доступны только заказы. Недостающие резервы станут доступны в течение короткого времени (обычно несколько секунд или минут), при этом будут разосланы стандартные уведомления (`ReserveChanged`), идентификаторы резервов останутся неизменными. Ограничение не затрагивает создание новых резервов.
* Потеряли хранившиеся на терминале iikoFront данные (например, вышел из строя жёсткий диск). В отличие от предыдущего пункта, где данные находятся в несогласованном состоянии недолго и восстанавливаются автоматически, данный случай является нештатным и может потребовать обращения к техподдержке для восстановления утерянных данных. Хорошо, если специалистам удастся спасти данные (извлечь их из повреждённого диска, собрать с других терминалов iikoFront), в противном случае терминал iikoFront загрузит резервы с сервера iikoRms, но связанных с ними заказов не будет. Резерв, заказ которого утерян, считается повреждённым. Его можно восстановить с помощью `RecoverCorruptedReserve`, но следует понимать, что при этом всего лишь будет создан новый пустой заказ вместо утерянного.