1. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/tasks/main.yml#L33 забыл убрать айпишник

2. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/defaults/main.yml#L12C24-L12C38 что за переменная ansible_enp0s8? А если у нас не будет такого интерфейса на сервере? вот ты же объявил на каком интерфейсе он будет работать, вот эту переменную и заиспользуй) если прям совсем не понятно, то лучше спроси, а то мы ходим вокруг да около)

3. почему 252 ? https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/defaults/main.yml#L15

### Сложное задание
По поводу задания где нужно сделать разные ip для разных скриптов идея такая...
По сути, для каждого скрипта нужно напсать свой инстанс и прописать туда свой vrrp vip. Тогда каждый VRRP инстанс будет отвечать за свой виртуальный ip и использоваться разными сервисами. Но если пилить разные инстансы, а их может быть много, то файл конфигурации будет сильно раздуваться и руками всё это править не круто. Поэтому можно написать один шаблон-инстанс, а переменными задать параметры для разных приложений и обходить все блоки переменных по очереди и передавать их в шаблон-инстанс. Тогда на выходе получим готовый файл конфигурации для всех сервисов.
Думал сначала сделать переменные примерно так:

```
defaults/main.yml

FOR_NGINX
keepalived_script: '"pidof nginx"'
keepalived_interval: 2     
keepalived_weight: 3

keepalived_state: "{{ (ansible_enp0s8.ipv4.address.split('.')[-1] == '2') | ternary('MASTER', 'BACKUP') }}"
keepalived_interface: "enp0s8"
keepalived_router_id: 151
keepalived_priority: "{{ 252 -  ansible_enp0s8.ipv4.address.split('.')[-1] | int }}"

keepalived_vip: 192.168.10.50/24
keepalived_broadcast_ip: 224.0.0.18

keepalived_track_script: "check_nginx"

FOR_POSTGRESQL
keepalived_script: '"pidof postgresql"'
keepalived_interval: 2     
keepalived_weight: 3

keepalived_state: "{{ (ansible_enp0s8.ipv4.address.split('.')[-1] == '2') | ternary('MASTER', 'BACKUP') }}"
keepalived_interface: "enp0s8"
keepalived_router_id: 152
keepalived_priority: "{{ 252 -  ansible_enp0s8.ipv4.address.split('.')[-1] | int }}"

keepalived_vip: 192.168.10.51/24
keepalived_broadcast_ip: 224.0.0.18

keepalived_track_script: "check_postgresql"
```

Но понял, что есть много повторяющегося кода и думаю можно как то ещё уменьшить его количество за счёт общих переменных.
Затем в файле keepalived.j2 пишем шаблон-инстанс и с помощью цикла обходим все блоки переменных из файла defaults/main.yml:

Примерно так:

```
{% for i in default/main.yml %}

vrrp_instance VI_{{ i.name }} {
state {{ keepalived_state }}
interface {{ i.keepalived_interface }}
virtual_router_id {{ i.keepalived_router_id }}
priority {{ i.keepalived_priority }}
virtual_ipaddress {
    {{ i.keepalived_vip }}
  }
  track_script {
    {{ i.keepalived_track_script }}
  }
}

{% endfor %}
```
Уверен на 120%, что код в таком виде не заработает. Но надеюсь, что сама концепция верна ))