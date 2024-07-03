1. https://github.com/NewbyDevOps/nginx_keepalived_ansible/tree/first_review роль лучше назвать keepalived, она не только для nginx может использоваться, мы в переменных должны иметь возможность менять скрипт того что будем чекать для назначения VIP. +++

2. установку и настройку nginx надо будет оттуда выпилить, это может настраиваться отдельной ролью +++

3. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/tasks/default_settings.yml вот это опасно очень, можно так обновиться, что разломаешь то что работало. убери. +++

4. разбивать на файлы ради одной таски бессмысленно, разбивка на файлы обычно делается, когда очень сложный механизм и его логически распихивают по разным файлам для упрощения анализа того что делает роль +++

5. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/tasks/configure_firewalld.yml#L23-L36 то после перезагрузки сервера слетит, сделай файл /etc/sysctl.d/keepalived.conf и в него пихани настроки ядра, при перезагрузке он перечитает конфиг и применит настройки. а так ребут превращает сервис в тыкву, не дело) --- ???

6.
https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/tasks/configure_keepalived.yml#L11 думаю тут больше презент симпл сгодится +++

7. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/tasks/configure_keepalived.yml#L15 тут просто имя файла из папки templates пишется, иначе плейбук должен лежать только в одном месте, а в другой папке он не сработает. +++

8. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/templates/keepalived.j2#L7-L11 нужно чтобы это задавалось переменной +++

9. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/templates/keepalived.j2#L14-L26 это тоже должно быть универсальным и задаваться в переменных +++

10. а это зачем? https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/templates/keepalived.j2#L27-L30 +++

11. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/handlers/main.yml не вижу reload для keepalived, что будет, если конфиг изменится в процессе экссплуатации? правильно keepalived не узнает что конфиг изменился и продолжит работать, как работал. +++

12. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/defaults/main.yml#L8 все неплохо, но тут есть тонкость, о которой ты не знаешь. сортировка списка хостов может измениться в зависимости от установленных зависимостей в системе. завяжись на последний октет ip, он не меняется, как правило. +++

13. https://github.com/NewbyDevOps/nginx_keepalived_ansible/blob/first_review/roles/ha_nginx_keepalived/templates/keepalived.j2#L18 priority у тебя кстати будет одинаковым на всех серверах, что плохо. опять же лучше завяжись на последний октет ip при проставлении приоритета. +++

14. про последние 2 сразу дам подсказку где можно взять очень много инфы о хостах:
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html