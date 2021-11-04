4.2. Использование Python для решения типовых DevOps задач
Есть скрипт:
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
Какое значение будет присвоено переменной c?
Как получить для переменной c значение 12?
Как получить для переменной c значение 3?
Ответ:
========================
1. будет ошибка, т.к. типы не соответсвуют для операции , int и str
2. привести a к строке:       c=str(a)+b
3. привести b к целому числу: c=a+int(b)
========================
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все измененные файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
Ответ:
лишняя логическая переменная is_change
и команда breake которая прерывает обработку при первом же найденном вхождении
+ изменил строку поиска так как у меня стоит русифицированный git
#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops3-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tизменено:   ', '')
        print(prepare_result)
#        break

 
Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.
Ответ:
добавил обработку аргумента (считаем что аргумент единственный и дает нам нужную команду)
#!/usr/bin/env python3

import os
import sys

cmd = sys.argv[1]
bash_command = ["cd "+cmd, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tизменено: ', '')
# добавил замену всех оставшихся пробелов в строке для удобства вывода
        prepare_result = prepare_result.replace(' ', '') 
        print(cmd+prepare_result)
#        break


Вывод:

21:37:45 tretyakov@pc(0):~/python$ ./dz2.py ~/devops-netology/
/home/alex/devops-netology/README.md
/home/alex/devops-netology/dz5/dz.txt
/home/alex/devops-netology/file4taguse
/home/alex/devops-netology/renamed_file.txt
21:37:56 tretyakov@pc(0):~/python$ 




ДОРАБОТКА
Добавлена обработка наличия параметров, при наличии берется текущий каталог, при отсутствии параметров берется текущий рабочий каталог,
при ошибке наличия GIT в каталоге цветная ошибка
#!/usr/bin/env python3

import os
import sys

cmd = os.getcwd()

if len(sys.argv)>=2:
    cmd = sys.argv[1]
bash_command = ["cd "+cmd, "git status 2>&1"]

print('\033[31m')
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print('\033[31m Каталог \033[1m '+cmd+'\033[0m\033[31m не является GIT репозиторием\033[0m')    
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tизменено: ', '')
# добавил замену всех оставшихся пробелов в строке для удобства вывода
        prepare_result = prepare_result.replace(' ', '') 
        print(cmd+prepare_result)
#        break
print('\033[0m')

Вывод:
22:20:40 tretyakov@pc(0):~/python$ ./dz2.py ~/devops-netology/

/home/alex/devops-netology/README.md
/home/alex/devops-netology/dz5/dz.txt
/home/alex/devops-netology/file4taguse
/home/alex/devops-netology/renamed_file.txt

22:22:24 tretyakov@pc(0):~/python$ ./dz2.py ~/devops-net/

/bin/sh: 1: cd: can't cd to /home/tretyakov/devops-net/

22:22:26 tretyakov@pc(0):~/python$ ./dz2.py

 Каталог /home/tretyakov/python не является GIT репозиторием

22:22:30 tretyakov@pc(0):~/python$ 

 
Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: drive.google.com, mail.google.com, google.com.
Ответ:
дополнил вывод временем, и инициализацией 1ой итерацией без проверки
##!/usr/bin/env python3

import socket as s
import time as t
import datetime as dt

# set variables 
i = 1
wait = 2 # интервал проверок в секундах
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print('*** start script ***')
print(srv)
print('********************')

while 1==1 : #отладочное число проверок 
  for host in srv:
    ip = s.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip)
      srv[host]=ip
# счетчик итераций для отладки, закомментировать для бесконечного цикла 3 строки
  i+=1 
  if i >= 50 : 
    break
  t.sleep(wait)

результат работы:
22:39:03 tretyakov@pc(0):~/python$ ./dz3.py
*** start script ***
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
********************
2021-10-15 22:39:20 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 173.194.73.194
2021-10-15 22:39:20 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 64.233.165.83
2021-10-15 22:39:20 [ERROR] google.com IP mistmatch: 0.0.0.0 173.194.221.139
2021-10-15 22:40:46 [ERROR] mail.google.com IP mistmatch: 64.233.165.83 64.233.165.19
2021-10-15 22:40:48 [ERROR] mail.google.com IP mistmatch: 64.233.165.19 64.233.165.83
2021-10-15 22:40:52 [ERROR] google.com IP mistmatch: 173.194.221.139 173.190.222.101
2021-10-15 22:40:54 [ERROR] google.com IP mistmatch: 173.194.222.101 173.190.222.100
2021-10-15 22:41:18 [ERROR] google.com IP mistmatch: 173.194.222.100 64.233.164.139
2021-10-15 22:41:20 [ERROR] google.com IP mistmatch: 64.233.164.139 64.233.164.138
2021-10-15 22:41:36 [ERROR] drive.google.com IP mistmatch: 173.194.73.194 108.177.14.194
2021-11-26 22:42:04 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 173.194.73.194
22:42:40 tretyakov@pc(0):~/python$
