### 1 ###
1) Запись осуществляется на диск, поэтому критичной метрикой является свободное место на диске -- **FS**: следствием исчерания этого ресурса будет отказ всей системы, она больше не сможет поставлять ценность, поэтому для бизнеса очень важно предсказать наступление этого момента, чтобы принять превентивные меры -- уменшить время хранения данных, расширить хранилище и т.д
2) В случае высокой нагрузки возможна ситуация, при которой даже при наличии свободного места на диске будут наблюдаться проблемы с записью, сформируется очередь -- для своевременного предотвращения подобных ситуаций необходимо отслеживать метрику **IOPS** -- число операций с диском в секунду
3) Вычисления загружают ЦПУ, поэтому необходимо следить за утилизацией этого ресурса, используя метрику **CPU LA**. Как и в случае с местом на диске внезапное исчерпание ресурса ЦПУ приведет к системному сбою, поэтому важно заранее выявлять тенденции по возрастанию средней нагрузки на ЦПУ, чтобы принять превентивные меры по увеличению ресурса и/или оптимизации его использования
4) Взаимодействие с платформой для вычислений осуществляется с помощью http-сервера, поэтому важно в режиме реального времени отслеживать эффективность его работы. Для этого можно использовать две основные метрики -- **время отклика** и **уровень ошибок**. В случае нормальной работы на уровне использования ЦПУ и записи на диск все равно возможна ситуация (из-за проблем на уровне кода приложения, например), при которой значительное для бизнеса число пользователей столкнется с проблемами в обработке их запросов. Важно своевременно узнавать о снижении качества работы пользовательского интерфейса, чтобы передать информацию разработчикам, если причина неполадок находится на стороне кода приложения, или оптимизировать работу http-сервера на инфраструктурном уровне.
5) Разные компоненты системы взаимодействуют посредством сети, поэтому важно, чтобы на этом уровне не возникало бутылочных горлышек и система сохраняла целостность, поддерживала слаженную работу всех частей. Чтобы вовремя заметить проблемы на этом уровне и принять меры, нужно отслеживать сетевые метрики. Например, **NetTraffic** -- пропускную способность сетевого трафика.

### 2 ###
Для упрощения взаимодействия специалистов по инфраструктуре с менеджером продукта можно использовать SRE-подход и строго сформулировать SLA: например, по предельно допустимому суммарному времени недоступности системы в год, по доле успешно обрабатываемых системой HTTP-запросов. На основе SLA можно определить SLO и каждый день / каждую неделю рассчитывать ключевые SLI, которые будут передаваться менеджеру (можно автоматизировать сбор этих метрик и рассылку содержащей их сводки).

### 3 ### 
В случае нехватки у бизнеса средств на построение полноценной системы сбора логов, подразумевающей их хранение и разворачивание сервиса поиска, можно реализовать минимальную систему перехватки ошибок и, как вариант, построить на ее основе систему алертов, используя только эту систему и уже имеющиеся в инструментарии компании приложения для коммуникации (электронную почту, корпоративный мессенджер). В таком случае разработчики смогут в режиме реального времени получать информацию о сбоях.

### 4 ###
Возможные коды ответа не исчерпываются 4xx, 5xx и 2xx. Для уточнения формулы в нее также можно включить ответы с 3xx статусами: (summ_3xx_requests + summ_2xx_requests) / (summ_all_requests)

## 5 ##
Их плюсов push СМ можно выделить:
1) Гибкость настройки отправки информации по метрикам в СМ -- в каждой из исследуемых систем можно настроить свой механизм сбора и отправки метрик, специализируя такие параметры, как состав используемых метрик, насыщенность данных, частота отправки в СМ, репликация / шардинг данных мониторинга в разные СМ / разные копии СМ -- т.е можно, например, настроить подготовку полной и краткой сводки для одой из исследуемых систем и отправлять полную сводку в одну СМ, а краткую в другую
2) push-системы СМ зачастую используют UDP, который является менее затратным способом передачи данных (однако это может быть плюсом, только если мы дополнительными средствами обеспечиваем гарантию отсутствия значительных потерь данных или если потенциальные потери не являются критичными)

Единая точка настройки сбора и обработки данных в pull-СМ позволяет:
1) Проще контролировать весь процесс сбора метрик (контролировать подлинность данных)
2) Быстрее разворачивать и системно изменять (отлаживать) процесс мониторинга

## 6 ##

1. pull-СМ:
Prometheus (сбор метрик осуществляется активно компонентом Exporter)

2. push-СМ:
TICK (хост-машины передают данные в компонент Telegraph)


3. Гибридные СМ:
Zabbix (push/pull дуализм проявляется в возможности настроить пассивные и активные проверки)


