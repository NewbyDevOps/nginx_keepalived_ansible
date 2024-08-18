1. круто что разобрался с sed. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/second_review/roles/nginx_deploy/tasks/main.yml#L10-L13 но лучше сделать полностью подкладывание конфигов, вместо использования shell модуля. он не иденпотентен ---?

2. шлюз может быть другим в другой месте https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/second_review/roles/keepalived_deploy/tasks/main.yml#L37 +++

3. ты не правильно понял как работают переменные в ансибле. они не должны ссылаться друг на друга, они имеют разные приоритеты https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/second_review/inventory/group_vars/all.yml +++

4. я просил несколько иную логику этих переменных

https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/second_review/roles/keepalived_deploy/defaults/main.yml#L13

https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/second_review/roles/keepalived_deploy/defaults/main.yml#L16

есть например 3 ноды с ip дресами 172.16.3.{151..153} +++

MASTER должен быть 151, остальные BACKUP +++

приоритет должен быть завязан на ip, но чем "больший" последний октет, тем позже он примет VIP. ip с ansible_host не всегда брать актуально. представим ситуацию что на ерверах по 2 интерфейса, (что в целом в настройках keepalived у тебя реализовано) один интерфейс у тебя для работы системы проде(и ты доступа к серверам по этому интерфейсу не имеешь), второй для менеджмента. +++

настраиваешь ты по мменеджмент интерфейсу, работает это по дата интерфейсу. соответственно брать ip надо из дата интерфейса. покопайся в ансибл фактах, там много интересного. +++

ПС: второй интерфейс можно и не настраивать, главное выведи в переменную название интерфейса  и с него и забирай айпишник. +++