Домашнее задание Пользователи и группы. Авторизация и аутентификация_РАМ

После развертывания машины было выполнено добавление нужных пользователей и установка их паролей:

root@pam:~# sudo useradd otus

root@pam:~# sudo useradd otusadm

root@pam:~# passwd otus

root@pam:~# passwd otusadm

Далее создал группу admin и добавил их в нее:

root@pam:~# groupadd -f admin

root@pam:~# usermod otusadm -a -G admin

root@pam:~# usermod vagrant -a -G admin

root@pam:~# usermod root -a -G admin

Сразу залогиниться по ssh  не получилось, не принимался метод аутентификации:

otus@192.168.57.10: Permission denied (publickey).

Проблему решил изменением настройки на виртуальном машине, установив в файле /etc/ssh/sshd_config.d/60-cloudimg-settings.conf 

значение PasswordAuthentication no -> yes и перезапустив сервис sshd

После этого смог зайти по ssh на ВМ 

Далее, по методичке был создан файл /usr/local/bin/login.sh

Ему были даны права на запуск:

chmod +x /usr/local/bin/login.sh

Файл был добавлен в конфиг sshd - /etc/pam.d/sshd

auth required pam_exec.so debug /usr/local/bin/login.sh

После этого я попытался выполнить команду 

root@pam:~# sudo date 082712302022.00

Но, не смотря на ее выполнение, дата восстанавливалась на текущую. Пришлось отключить синхроанизацию времени:

timedatectl set-ntp false

После этого, дата стала меняться - установилось Воскресенье 27.08.2022

Пользователя otus пускать перестало:

user@pc ~> ssh otus@192.168.57.10

otus@192.168.57.10's password: 

Permission denied, please try again.

В тоже время - пользователь otuadm входил нормально:

user@pc ~> ssh otusadm@192.168.57.10

otusadm@192.168.57.10's password: 

Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-100-generic x86_64)

Все, ограничение доступа для простых пользовтелей в выходные успешно настроено!