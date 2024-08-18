1. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/tasks/main.yml#L33 забыл убрать айпишник

2. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/defaults/main.yml#L12C24-L12C38 что за переменная ansible_enp0s8? А если у нас не будет такого интерфейса на сервере? вот ты же объявил на каком интерфейсе он будет работать, вот эту переменную и заиспользуй) если прям совсем не понятно, то лучше спроси, а то мы ходим вокруг да около)

3. почему 252 ? https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/third_review/roles/keepalived_deploy/defaults/main.yml#L15