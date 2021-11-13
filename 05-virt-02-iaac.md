### Задача 1
Опишите своими словами основные преимущества применения на практике IaaC паттернов.
Какой из принципов IaaC является основополагающим?
```
Быстрая развертка инфраструктуры - кто пользуется такими виртуалками будет иметь одинаковые настройки. Не получится такого что у кого-то функциональность разработанного приложения работает, а у другого сотрудника не работает.

```
### Задача 2
Чем Ansible выгодно отличается от других систем управление конфигурациями?
Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?
```

В Ansible низкий порог вхождения. Не требуется установка агента.
В методе push может возникнуть ситуация когда сервер какое-то время будет сидеть 
и ждать когда же ему пришлют конфигурацию.

```
### Задача 3
Установить на личный компьютер:
VirtualBox
Vagrant
Ansible
Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.
```
tretyakov@ubuntu:~/vagrant$ vagrant --version
Vagrant 2.2.19
tretyakov@ubuntu:~/vagrant$ vboxmanage --version
6.1.26_Ubuntur145957
tretyakov@ubuntu:~/vagrant$ ansible --version
ansible [core 2.11.6] 
  config file = /home/tretyakov/vagrant/ansible.cfg
  configured module search path = ['/home/tretyakov/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/tretyakov/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.9.7 (default, Sep 10 2021, 14:59:43) [GCC 11.2.0]
  jinja version = 2.11.3
  libyaml = True

```
