# Шпаргалка по Linux

## Терминал

В Ubuntu придется часто использовать терминал. Его можно вызвать сочетанием клавиш `Ctr+Alt+T`. Важно запомнить, что для большинства команд можно вызвать краткую справку:

```
<command> -h
```

Или если не работает первая:

```
<command> --help
```

Некоторые имеют подробный манул:

```
man <command>
```

Сочетания клавиш для операций копировать, вставить, вырезать работают только с зажатым `Shift`, например, `Shift+Ctrl+V`.

После запуска терминала мы по-умолчанию находимся в нашем домашнем каталоге.  Он обозначается как ~. В терминале тильда в начале пути заменяется на `/home/$USER`. Файл `~/.bashrc` сожержит команды, выполняемые интепретатором баша после запуска (например, при старте терминала). Имена исполняемых фалов, размещенных в каталогах `/usr/bin` или `/usr/local/bin`, доступны как команды.

`Ctrl+D` - вставить символ EOF закрыть (закрывает терминал).

Если нужно выполнить одну единственную команду, то можно нажать Alt+F2 и ввести ее в поле.

## sudo

sudo - это утилита, позволяющая выполнить команды с правами суперпользователя root. Перед именами команд, которые работают с файловой системой вне домашнего каталога, а так же для остановки root-процессов, нужно писать sudo. sudo используется для консольных приложений, в старых версиях убунту была утилита gksu которая позволяла запускать из под рута графические приложения (показывала окно ввода пароля перед запуском). Из Ubuntu 18.04 убрали ее, вместо нее предлагается использовать что-то типа `gedit admin:///etc/hosts`.

Если надоело `sudo` вводить можно авторизоваться под рутом:

```
sergey@sergey-pc:~$ sudo -i
[sudo] password for sergey: 
root@sergey-pc:~# 
```

Либо можно использовать `sudo -s` или `sudo su`.

##  PATH

Чтобы использовать в качестве команд исполняемые файлы из других каталогов, нужно добавить их в переменную PATH. В переменной **PATH** хранятся пути, разделенные двоеточиями. Для этого в `~/.bashrc` нужно добавить строку:

```bash
export PATH="/path/to/executable:$PATH"
```

Это важно: если мы вводим команду `python`, то интерпретатор bash начинает сканирование списка каталогов в переменной PATH. Поиск продолжается пока не будет найден исполняемый файл в каком-то из каталогов, именно поэтому старое значение PATH добавляется в конец.

## Перенаправление вывода

### Файловые дескрипторы

Дескриптор файла - это число, которое ассоциируется с открытым файлом (либо с соединением, так как в Linux все файлы).

| Значение <img width="450"> | Описание <img width="450"> |
| -- | -- |
| 0 | stdin - входные данные для сценария (если поддерживает) |
| 1 | stdout - стандартный вывод |
| 2 | stderr - ошибки |

#### Примеры

```bash
# Передаем данные в stdin сценария
echo "foo" | somecommand
# Либо так
somecommand <<< "foo"
# Вывод ошибки в stderr
echo "An error has occurred" >&2
# Игнорирование ошибок. `/dev/null` - как-бы файл в который записываем все, что не хотим видеть в консоли.
somecommand 2> /dev/null
# А можно их записывать в error log. `>>` запись в режиме добавления
somecommand 2>> /path/to/error.log
```

### Ссылки

* [I/O Redirection](https://www.tldp.org/LDP/abs/html/io-redirection.html)

## Переменные

| Имя <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `$#` | Количество аргументов |
| `$@` | Список аргументов |
| `$0` | Имя исполняемого файла |
| `${1-N}` | Значение аргумента для исполняемого файла либо функции |
| `$_` | Последний аргумент предыдущей команды |
| `$USER` | Текущий пользователь |
| `$HOME` | Домашний каталог |
| `$PWD` | Рабочий каталог (для скрипта тот же что и для сессии терминала) аналог вызова `pwd` |
| `$RANDOM` | Случайное число |

### Примеры

#### $_

```bash
$ echo test
test
$ echo $_
test
```

## Полезные команды

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `!!` | Выполнить предыдущюю команду |
| `cd -` | Перейти в предыдущий каталог |
| `xdg-open <filename>` | Открыть файд в программе по-умолчанию | 
| `sudo reboot` | Перезагрузка компьютера |
| `sudo poweroff` | Выключение компьютера |
| `yes \| <command>` | Ввод и отправка Y при выполнении команды |
| `time <command>` | Время выполнения команды |
| `strace <command>` | Вывести список системных вызовов, которые совершает команда |
| `ldd <command>` | Список зависимостей команды |
| `cal` | Календарь |
| `watch <command>` | Запуск команды через определенный промежуток времени (2 секунды по-умолчанию) |
| `loginctl lock-session` | Заблокировать экран |
| `loginctl lock-session` | Разблокировать экран |
| `type/wicth COMMAND` | Напечатает путь до обработчика команды |
| `cat /etc/shells` | Список доступных командных оболочек |
| `chsh -s /bin/zsh` | Изменить командную оболочку |
| `./upwork_statistics.py \| sort -k 2 -rn` | Отсортировать выхлоп работы скрипта по второй колонке |
| `ls \| peco` | Выбираем стрелочками нужное |
| `basename "/"$(ps -f -p $(cat /proc/$(echo $$)/stat \| cut -d \  -f 4) \| tail -1 \| sed 's/^.* //')`
 | Узнать оболочку терминала |
 | `dmesg \| less` | Посмотреть сообщения ядра |

```bash
# move all media files in subdirectories into the current directory
find ./ \( -name '*.JPG' -or -name '*.jpg' -or -name '*.MOV' -or -name '*.mp4' \) -exec mv '{}' ./ \;

# tar.gz a director
tar czvf wordpress-orig.tar.gz /var/www/

# add user to linux
adduser david

# add existing user to existing group
usermod -g www david
```

### Сессия пользователя

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `gnome-session-quit` | Выйти из системы |

### Обработка ошибок

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `set -e` | Отключить выход из сценария при ошибке. |
| `set +e` | Включить выход из сценария при ошибке. |

### Операции над строками

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `expr length $str` | Длина строки |
| `${var#*SubStr}`  | will drop begin of string up to first occur of `SubStr` |
| `${var##*SubStr}` | will drop begin of string up to last occur of `SubStr` |
| `${var%SubStr*}`  | will drop part of string from last occur of `SubStr` to the end |
| `${var%%SubStr*}` | will drop part of string from first occur of `SubStr` to the end |
| `echo ${first_string/Suzi/$second_string}` | Замена подстроки в строке |
| `${str^^}` | Перевод строки в верхний регистр в Bash 4.0 |
| `echo $str \| tr a-z A-Z` | Тоже в более ранних версиях |
| `echo "${str,,}"` | Перевод строки в нижний регистр в Bash 4.0 |
| `echo $str \| tr '[:upper:]' '[:lower:]'` | Тоже в более ранних версиях |
| `base64 <<< "Привет, мир!"` | Закодировать строку в base64 |
| `echo <base64> \| base64 --decode` | Обратная операция | 
| `dd if=/dev/urandom \| hexdump -C \| less` | Просмотр данных в HEX |

### Информация о системе

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `cat /etc/*-release` | Информация о дистрибутиве |
| `lsb_release -cs` | Кодовое имя дистрибутива |
| `lscpu` и `sudo lshw -class processor` | Информация о процессоре |
| `cat /proc/cpuinfo  \| grep name  \| uniq` | Модель процессора |
| `arch` или `uname -m` | Архитектура процессора |
| `nproc` или `cat /proc/cpuinfo  \| grep process  \| wc -l` | Количество ядер процессора |
| `sudo lshw -C display` | Информация о видеокарте |
| `free -m` | Доступная и используемая оперативная память |
| `cat /proc/meminfo` | Подробная информация по оперативной и виртуальной памяти |
| `sudo dmidecode --type 17` | Тип и размер оперативной памяти |
| `sudo fdisk -l \| grep "Disk /dev/sd"` | Список дисков |
| `cat /proc/uptime` | Сек., время работы CPU и время простоя в сумме для каждого ядра |
| `sudo lshw -C display` | Информация о видеоадаптерах |
| `sudo dmidecode -t baseboard` | Модель материнской платы | 
| ` sudo smartctl -A /dev/nvme0` | Проверить состояние SSD |
| `df -h --total` | Информация об используемом месте на различных носителях |

### Арифметическии операции

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `expr 2 + 2` или `echo $((2+2))` | Выполняет выражение и возвращает результат |

### Процессы

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `top` | Мониторинг процессов |
| `top -p PID` | Следим за расходом рксурсов одним процессом |
| `pstree` | Дерево процессов |
| `ps aux` | Список всех процессов |
| `ps aux  \| grep python` | Список всех процессов python |
| `kill <PID>` | Остановить процесс по идентификатору |
| `killall firefox` | Остановить все процессы с именем firefox |
| `killall -r gunicorn` | Остановить все процессы соотв регулярке |
| `exec -a <MyProcessName> <Command>` | Запуск процесса под именем |
| `pkill -f <MyProcessName>` | Остановка процесса с именем |

### Файловая система

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `exec 3>out.txt` | Открыть файл в режиме чтения и записи и получить дескриптор файла |
| `echo "Some line" >&3` | Записать строку в файл, используя дескриптор файла |
| `cat <&3` | Прочитать данные из дескриптора |
| `3>&-` | Закрыть дескриптор файла |
| `> <filename>` | Очистить файл | 
| `file <filename>` | Тип содержимого файла |
| `sudo -- sh -c "printf '127.0.0.1\tholocaust\n' >> /etc/hosts"` | Добавить строку в файл с рут-привелегиями |
| `-b filename` | Block special file |
| `-c filename` | Special character file |
| `-d directoryname` | Check for directory Existence |
| `-e filename` | Check for file existence, regardless of type (node, directory, socket, etc.) |
| `-f filename` | Check for regular file existence not a directory |
| `-G filename` | Check if file exists and is owned by effective group ID |
| `-G filename set-group-id` | True if file exists and is set-group-id |
| `-k filename` | Sticky bit |
| `-L filename` | Symbolic link |
| `-O filename` | True if file exists and is owned by the effective user id |
| `-r filename` | Check if file is a readable |
| `-S filename` | Check if file is socket |
| `-s filename` | Check if file is nonzero size |
| `-u filename` | Check if file set-user-id bit is set |
| `-w filename` | Check if file is writable |
| `-x filename` | Check if file is executable |
| `cat` | чтение из основного потока до &lt;EOF&gt; |
| `cat <path/to/file>` | просмотр содержимого файлов |
| `cd <path>` | навигация по каталогам |
| `cp <src> <dst>` | копирование файлов/каталогов |
| `mv <src> <dst>` | перемещение файлов/каталогов |
| `mkdir <name>` | создание каталогов |
| `nano <path/to/file>` | редактировать файд |
| `rm <path/to/file>` | удаление файлов/каталогов |
| `rmdir <path/to/catalog>` | удалить пустой каталог |
| `rm -rf <path/to/catalog>` | Удалить каталог со всем его содержимым |
| `rm *.txt` | Удалить все файлы с расширением `.txt` |
| `ln -s <src> <dst>` | Создание мягкой ссылки |
| `df -h` | Информация о файловой системе |
| `sudo fdisk -l` | Инофрмация о "дисках" |
| `tar -xvf <archive>` | Извлечь файлы из архива в текущий каталог |
| `tar -xvf <archive> -C /path/to/extract` | Извлечь файлы из архива в другой каталог |
| `tar -zcvf hydra.tgz --exclude=hydra/.venv hydra` | Заархивировать папку, игнорируя содержимое .venv |
| `unzip file.zip -d /path/to/directory` | Извлечь содержимое zip-архива в каталог |
| `curl -s http://exmaple.org/file.zip  \| tar -xvf -` | Скачать файл и разархивировать |
| `readlink -f file.ext` | Получить полный путь до файла |
| `find . -type f` | Вывести список файлов в директории рекурсивно | 
| `umount /dev/sd[XN]` | Размонтировть устройство (например, перед форматированием) |
| `mkfs /dev/sda1` | Отформатировать раздел |
| `sudo dd if=/dev/sda of=/dev/sdb conv=notrunc,noerror` | Копировать жесткий диск | 
| `script_dir=$(dirname $0)` | Текущий каталог для скрипта |
| `grep -Pri <текст_для_поиска> <путь_к_директории>` | Поиск файлов по содержимому |
| `zless`, `zcat`, `zgrep` | Аналоги команд с похожими названиями, но работающие с архивами |
| `$ find . -name '*.pdf' \| cpio -pdm  ~/Documents/Books` | Копировать файлы с опр. расширением в нужный каталог |
| `df -k .` | Размер и свободное место текущего раздела |
| `df -k /dev/mapper/ubuntu--vg-root` | |
| `mount` | Все точки монтирования |
| `findmnt --target /tmp` | Покажет раздел на котором находится каталог |
| `du -h <dir>` или `du -hcs <dir>`| Размер каталога |
 
### Установка и удаление пакетов

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `sudo apt-get update` | Обновить список пакетов  |
| `sudo apt-get upgrade` | Обновить все пакеты  |
| `sudo apt-get install <package> -y` | Установить пакет без подтверждления |
| `sudo apt-get remove <package>` | Удалить пакет |
| `sudo apt-get autoremove` | Удалить неиспользуемые пакеты |
| `sudo apt-get clean` | Удалить загруженне архивы |
| `apt list --installed` | Список установленныхпакетов |
| `sudo dpkg -i <.deb package>` | Установить либо обновить deb-пакет |
| `sudo apt-get install -f` | При ошибке предудыщей выполните эту и повторите |
| `sudo dpkg -p <package>` | Удалить deb-пакет с сохранением конфигураций |
| `sudo dpkg -P <package>` | Удалить deb-пакет с конфигурациями |
| `dpkg -l  \| less` | Просмотр списка установленных deb-пакетов |
| `./configure && make && sudo make install` | Установить из исходников |

Помимо `apt-get` на Ubuntu доступен пакетный менеджер `snap`. Он более продвинутый. Все доступные пакеты для установки с помощью него можно увидеть на сайте [snapcraft.io](http://snapcraft.io).

Also: **GDebi Package Installer** - программа для установки `.deb` пакетов.

### Сервисы

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `service --status-all` | Список сервисов |
| `systemctl list-unit-files --type=service  \| grep enabled` | Список включенных сервисов |
| `journalctl -xe` | Журнал ошибок сервисов |  

### Сеть

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `netstat -lt` | Список всех серверов, запущенных на хосте |
| `printf 'GET / HTTP/1.0\r\nHost: google.com\r\n\r\n' \| nc google.com 80` | Отправить сырой TCP-запрос |
| `curl -d <json> -H "Content-Type: application/json" -X POST <url>`| Отправить JSON-апрос через POST |
| `exec {дескриптор-файла}<>/dev/{протокол}/{адрес-узла}/{номер-порта}` | Открыть TCP/UDP соединение (дескриптор - целое число больше 2) |
| `wget url -O - \| sh` | Выполнить удаленный скрипт |
| `ssh -D 1080 -N user@server` | Создать socks-туннель (localhost:1080) |

Выкачать весь сайт:

```bash
wget --mirror            \
     --convert-links     \
     --html-extension    \
     --wait=2            \
     -o log              \
     http://howisoldmybusiness.com
```

### nginx

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `sudo systemctl start nginx` | Запустить nginx |
| `sudo systemctl stop nginx` | Остановить nginx |
| `sudo systemctl restart nginx` | Перезапустить nginx |
| `sudo systemctl enable nginx` | Включить автозапуск nginx |
| `sudo systemctl disable nginx` | Выключить автозапуск nginx |
| `sudo nginx -t` | Проверить конфигурацию на ошибки |
| `sudo systemctl reload nginx` | Перечитать конфигурационные файлы |
| `systemctl status nginx` | Статус nginx |
| `tail -f /var/log/nginx/access.log` | Выводить добавлямые в лог строки |
| `less /var/log/nginx/access.log` | Просмотр лога с возможностью прокрутки стрелками |

### Postgres

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `psql databasename < data_base_dump` | Импорт дампа |
| `pg_dump -d mydb -f schema.sql` | Сделать дамп бд |

### psql (интерактивный режим)

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `/q` | Выход | 
| `/d <tablename>` | Просмотр информации о колонках таблиц |

### MySQL

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `mysql -u root -p` | Запуск с вводом пароля |
| `mysqladmin -u root -p'qwerty' password ''` | Удаляем пароль чтобы его больше не вводить |
| `mysql -u root -e 'show databases;'` | Выполнение запроса |

### Python

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `python -m venv virtualenvironment` | Создать виртуальное окружение |
| `. virtualenvironment/bin/activate` | Активировать виртуальное окружение |
| `deactivate` | Дективировать виртуальное окружение |
| `pip install requests` | Установить модуль |
| `pip install git+git://github.com/pyeve/eve.git` | Установить модуль через git |
| `pip freeze > requirements.txt` | Сохранить список установленных модулей с их версиями в файл |
| `pip install -r requirements.txt` | Установить модули из файла |
| `pip freeze  \| xargs pip uninstall -y` | Удалить все установленные через pip модули |
| `pip freeze --local \| grep -v ‘^\-e’ \| cut -d = -f 1 \| xargs -n1 pip install -U` | Обновить все модули |
| `idle` или `python -m idlelib` | Запустить idle |
| `idle <filename>` | Открыть файл в idle |

### pyenv

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `pyenv install 3.6.6` | Установить определенную версию |
| `pyenv global 3.6.6` | Сделать версией по-умолчанию для команды |
| `pyenv local 2.7.15` | Сделать версией по-умолчанию для текущего каталога и всех вложенных |
| `pyenv shell 2.7.15` | |
| `pyenv versions` | Доступные версии |

### nvm

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `nvm install 8.11.4` | Установить определенную версию Node.js |
| `nvm install node` | Установить последнюю версию |
| `nvm uinstall <ver>` | Удалить версию |
| `nvm alias default 8.11.4` | Сделать версией по-умолчанию |
| `nvm alias default node` | Сделать последнюю установленную версию версией по-умолчанию |
| `nvm ls` | Список установленных версий |

### npm

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `npm init` | создать новый проект |
| `npm -g i vue` | установить модуль глобально |
| `npm i express --save` | установить модуль и добавить зависимости в package.json |

### Vue

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `npm i -g @vue-cli` | Установить vue-cli |
| `vue create <projectName>` | Создать новый проект (пробел чтобы отметить пункт меню) |
| `vue add pug -f` | Установка нового модуля с игнорированием `Error: Plugin ... does not have a generator` |
| `npm run serve` | Запустить отладочный сервер |
| `npm run build` | Запустить сборку проекта |

### Git

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `git config --global user.email email@example.com` | Указать email пользователя |
| `git config --global user.name "John Doe"` | Указать имя пользователя |
| `git config --global credential.helper "cache --timeout=2592000"` | Хранить введенные логин и пароль в течении 30 дней |
| `git clone https://github.com/requests.git` | Создать локальную  копию репозитория |
| `git pull` | Обновить локальный репозиторий с сервера |
| `git reset --hard` | Please commit your changes or stash them before you merge |
| `git add .` | Добавить все файлы из текущего и вложенных каталогов |
| `git reset` | Отменить предыдущую операцию |
| `git restet <file>` | отмена добавления файла |
| `git commit -m "message"` | Коммитим измения с целью их публикации|
| `git push` | Загружаем измененнные файлы на сервер |
| `git checkout <branch>` | Меняем ветку |
| `git checkout -b <branch>` | Создаем новую ветку и переключаемся на нее |
| `git rm -r --cached` | Удалить каталог из отслеживания|
| `git subtree push --prefix <path/to/folder> <remote-repo> <branch>` | Push отдельной папки |
| `git log --all --grep=<pattern>` | Поиск коммита по названию |
| `git log -S<text>` | Поиск коммита по содержимому |
| `git log -n 10` | вывести 10 последних коммитов |
| `git stash` | Сохранить и откатить измения (многие действия не возможно сделать без пуша) |
| `git stash apply` | Восcтанавливаем откаченные изменения |
| `git show HASH:path/to/file` | посмотреть старую версию файла |

### Git Flow

Git Flow - это обертка над git.

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `git flow init` | Сначала нужно инициализировать git flow |
| `git flow feature start DEV-97` | Начать новую фичу |
| `git checkout develop && git pull origin` | Если предыдущая команда возвращает ошибку: `Branches 'develop' and 'origin/develop' have diverged...` |
| `git flow feature publish` | Опубликовать фичу (перед этим нужно добавить изменения и сделать коммит) |
 
### Docker

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `docker build -t web .` | Создать образ из Dockerfile в текущем каталоге |
| `docker run -d -p <hostPort>:<containerPort> web` | Запустить образ в новом контейнере с проброской порта |
| `docker exec -it <first_symbols_of_hash_or_container_name> sh` | Подключиться к контейнеру |
| `docker ps -a` | Список запущенных контейнеров |
| `docker stop <HASH_OR_NAME>` | Остановить запущенный контейнер |

```bash
$ docker-compose up -d         # start containers in background
$ docker-compose kill          # stop containers
$ docker-compose up -d --build # force rebuild of Dockerfiles
$ docker-compose rm            # remove stopped containers
$ docker ps                    # see list of running containers
$ docker exec -ti [NAME] bash # ssh to the container

# list all images
docker images

# tag and publishing
docker tag <id> buonzz/name:version
docker login
docker push buonzz/name

# delete image
docker rmi -f <id>

# run an image
docker run yourusername/docker-whale

# view logs
docker logs [OPTIONS] CONTAINER

# set the port to expose in host
docker run -p 3000 my_image

# start new container interactively
docker container run -it
```

Деплой на удаленном сервере:

```bash
$ docker-compose build
$ docker-compose up -d
```

### Прочее

| Команда <img width="450"> | Описание <img width="450"> |
| -- | -- |
| `cloc . --exclude-dir=.vscode,venv` | Подсчет строк исходного кода |
| `echo 'one two three' \| xargs mkdir` | xargs читает входной поток и передает его в качестве аргуиентов др. команде |
| `code <path>` | Открыть файл или каталог в VS Code |

## Рецепты

### Bash - это недоязык

В котором все строки, даже небо, даже Аллах. Отсюда, низкая скорость работы и куча неочевидных ошибок пр  написании кода.

### Всегда заключайте переменные в двойных кавычки

Дело в том, что при вызове:

```bash
foo $bar
```
Моет произойти не то, что ожидается, например:

```bash
bar="bar baz"
```
В foo будут сл. аргументы:

```bash
echo $1 # выведет "bar"
echo $2 # выведет "baz"
```

### Текущий каталог скрипта

Текущий рабочмий каталог привязан к терминалу. Чтобы работать с путями относительно каталога в котором пасположен скрипт используем сл. сниппеты:

```bash
# Делаем рабочим
cd `dirname $0`
# Делаем рабочим и запоминаем в переменную
script_dir=$(cd `dirname $0` | pwd)
```

### Pyenv

Pyenv - менеджер версий Python, позволяет использовать определенную версию для каждого проекта. Прост в использовании.

Установка:

```bash
$ curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```

Перед установкой стоит установить все зависимости, из установка описана [тут](https://github.com/pyenv/pyenv/wiki/common-build-problems).

Ссылки:

* [Cтраница проекта](https://github.com/pyenv/pyenv)
* [Скрипт для установки](https://github.com/pyenv/pyenv-installer)

### IPython и BPython и пр.

> IPython (англ. Interactive Python) — интерактивная оболочка для языка программирования Python, которая предоставляет расширенную интроспекцию, дополнительный командный синтаксис, подсветку кода и автоматическое дополнение. Является компонентом пакетов программ SciPy и Anaconda.

Установка:

```bash
$ pip install ipython
```

Запуск:

```bash
$ python -m IPython
```

`Tab` - автодополнение. `Ctrl + Space` - это режим выделения. Что он делает не знаю.

BPython практически полный аналог IPython. Так же существует Ptpython. Все эти модули созданы для работы в консоли. Что из них выбрать каждый решит сам.

### Spyder

Spyder - это IDE для Python, написанная на Python.

Установка:

```bash
$ sudo apt-get install spyder3
```

Либо можно установить как модуль:

```bash
$  pip install spyder
```

Запуск выглядит так:

```bash
$ python -m spyder.app.start
```

### tldr

Как man только ничего лишнего.

### USB

Если устройство примонтировано:

```bash
$ umount /dev/sdb
```

Если разметка карты испорчена:

```bash
$ sudo dd if=/dev/zero of=/dev/sdb bs=512 count=1
```

Забиваем null-байтами первые 512 байт, где хранится информация о разметке.

Далее нужно создать файловую систему, раздел (sdb1) и отформатировать диск (sdb1).

Запись образа на диск:

```bash
sudo dd if=~/Downloads/Win10.iso of=/dev/sdb bs=8M
```

```bash
umount /dev/sdb1
sudo dd if=~/Downloads/manjaro-xfce-17.1.12-stable-x86_64.iso of=/dev/sdb bs=1M status=progress
```

### Установка PostgresSQL ###

Устанавливаем Postgres:

```bash
$ sudo apt update
$ sudo apt install postgresql postgresql-contrib
```

Запускаем сервер:

```bash
$ sudo systemctl start postgresql
```

Создаем пользователя чье имя соответствует вашему:

```bash
$ sudo -u postgres createuser --superuser $USER
```

Нужно так же создать одноименную БД:

```bash
$ sudo -u postgres createdb $USER
```

Устанавливаем пароль для юзера sergey:

```bash
$ sudo -u sergey psql
psql (10.4 (Ubuntu 10.4-0ubuntu0.18.04))
Type "help" for help.

sergey=# \password
Enter new password: 
Enter it again: 
sergey=#
```

Команды в интерактивной консоли начинаются с "\", т.е. надо писать "\help".

### Как установить pgAdmin4

Сначала  небольшое введение. В Ubuntu при стандартной установке доступны старые версии интрепретатора Python. Чтобы использовать для команды `python` другую версию интепретатора, я рекомендую установить менеджер версий [pyenv](https://github.com/pyenv/pyenv). 

```bash
$ python --version
Python 3.6.5
```

pgAdmin 4 не работает с Python 3.7.0: падает, встретив async как имя аргумента.
 
Если у вас не установлен pyenv, то вызов `python` везде замените на `python3`.

Создаем и активируем виртуальное окружение:

```bash
$ python -m venv pgadmin4
$ . pgadmin4/bin/activate
```

Качаем wheel:

```bash
$ wget https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v3.1/pip/pgadmin4-3.1-py2.py3-none-any.whl
$ pip install pgadmin4-3.1-py2.py3-none-any.whl
```
Создадим локальные настройки:

```bash
$ cd /pgadmin4/lib/python3.6/site-packages/pgadmin4
$ nano config_local.py
```

Содержимое файла `config_local.py`:

```python
import os
SERVER_MODE = False
DATA_DIR = os.path.realpath(os.path.expanduser('~/.pgadmin/'))
LOG_FILE = os.path.join(DATA_DIR, 'pgadmin4.log')
SQLITE_PATH = os.path.join(DATA_DIR, 'pgadmin4.db')
SESSION_DB_PATH = os.path.join(DATA_DIR, 'sessions')
STORAGE_DIR = os.path.join(DATA_DIR, 'storage')
```

Создадим сервис. Сервис не привязан к терминалу. Его можно добавить в автозагрузку, остановить и т.д.

```bash
$ gedit admin:///etc/systemd/system/pgadmin4.service
```

Пример файла:

```ini
[Unit]
Description=pgAdmin 4

[Service]
Type=simple
ExecStart=/home/sergey/dev/pgadmin4/bin/python /home/sergey/dev/pgadmin4/lib/python3.6/site-packages/pgadmin4/pgAdmin4.py

[Install]
WantedBy=multi-user.target
```

Запуск:

```bash
$ sudo systemctl start pgadmin4
```

### pip freeze ignore

Проблема у меня возникла с модулем pylint, для установки которого требуется gcc. Приложения я запускаю через Docker. Используемые образы не имеют пакета gcc, его приходится ставить, что влияет как на размер образа так и на время сборки.

Для решения этой проблемы:

1) Создаем виртуальное окружение и устанавливаем модули, которые нужно игнорировать;
2) Выполняем `pip freeze > ~/dev/ignore-requirements.txt`;
3) Добавляем в `.bashrc` строку `alias pipfreezeignore='pip freeze | grep -vFxf ~/dev/ignore-requirements.txt'`.

Использование:

```bash
$ pipfreezeignore > requirements.txt
```

### Что делать если перестал работать pyenv

Возможно, в текущем каталоге либо в родительских имеется файл `.python-version`. Его нужно удалить.

### Как создать свой сервис

В папке `/etc/systemd/system` создаем файл `<service-name>.service`.

Пример:

```ini
[Unit]
Description=<description>

[Service]
Type=simple
ExecStart=<command>

[Install]
WantedBy=multi-user.target
```

Нужно прописывать полный путь до команды (~ нельзя использовать).

### Docker

#### Немного о синтаксисе

Запомнить:

* Dockerfile может иметь сколько угодно инструкций FROM.

#### Алиасы для команд

Добавить в `~/.bashrc` следующие строки:

```bash
alias docker-stop-all='docker stop $(docker ps -qa)'
alias docker-rm-all='docker rm -f $(docker ps -qa)'
alias docker-rmi-all='docker rmi $(docker images -q)'
alias docker-clean='docker-stop-all; docker-rm-all; docker-rmi-all'
```

#### Как из контейнера подключиться к базе на хосте

##### Настройка сети

Самое простое решение - это `docker run --network host...` либо в `docker-compose.yml` добавить:

```yaml
version: '3'

services:
  myservice:
    // ...
    network_mode: host
```

По целому ряду причин эти решения могут нам не подходить.

##### Монтирование пути

Можно запустить базу данных в контейнере и смонтировать путь до файлов БД с помощью volume [описание тут](https://docs.docker.com/engine/examples/postgresql_service/).

##### Указание вместо localhost локального ip-адреса

Можно указать свой локальный айпи-адрес в качестве хоста базы, но перед этим нужно настроить сервера баз данных, так чтобы они слушали хост локальный айпи либо `0.0.0.0`.

Итак, для начала нам нужно узнать свой локальный ip-адрес:

```bash
$ hostname -I | cut -d ' ' -f1
192.168.0.82
```

Этот айпи мы будем указывать в своих скриптах в качестве хоста. Сервера баз данных должны слушать этот адрес (универсальное решение в качестве хоста указать 0.0.0.0).

###### Postrgres

```bash
$ sudo nano /etc/postgresql/10/main/postgresql.conf
```

Ищем "listen_adresses":

```conf
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

listen_addresses = 'localhost,192.168.0.82'
# либо можно слушать все адреса
# listen_addresses = '*'
```

В конец `/etc/postgresql/10/main/hba_conf.conf` нужно добавить строку:

```conf
host    all             all             172.17.0.0/8            md5
```

Это позволит избежать ошибок вида:

```
asyncpg.exceptions.InvalidAuthorizationSpecificationError: no pg_hba.conf entry for host "172.17.0.x"
```

Перезапускаем Postgres:

```bash
$ sudo sytemctl restart postgresql
```

###### MySQL

Нужно в файле `/etc/my.cnf` или `/etc/mysql/my.cnf` изменить параметр **bind-address**:

```conf
[mysqld]
bind-address = 0.0.0.0
```

Мне этого делать не потребовалось `netstat -lt` показал что мускул и так слушает все хосты.

###### Redis

```bash
$ sudo nano /etc/redis/6379.conf
```

Находим и комментриуем `bind 127.0.0.1`:

```conf
# IF YOU ARE SURE YOU WANT YOUR INSTANCE TO LISTEN TO ALL THE INTERFACES
# JUST COMMENT THE FOLLOWING LINE.
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# bind 127.0.0.1
```
Перезапускаем сервис:

```bash
$ sudo systemctl restart redis_6379
```
### Создание ярлыков:

```bash
$ sudo apt-get install --no-install-recommends gnome-panel
$ gnome-desktop-item-edit --create-new ~/Desktop
```
### /sbin, /bin, /usr/bin и /usr/local/bin

> `/sbin` , as distinct from `/bin` , is for system management programs (not normally used by ordinary users) needed before `/usr` is mounted. `/usr/bin` is for distribution-managed normal user programs. ... `/usr/local/bin` is for normal user programs not managed by the distribution package manager, e.g. locally compiled packages.

### Как в Node.js использовать import и export

Для начала создадим новый проект:

```bash
npm init
```

Установим зависимости:

```bash
npm i --save-dev @babel/core @babel/cli @babel/node @babel/preset-env nodemon
```

Теперь создадим в корне проекта файл `.babelrc`:

```json
{
  "presets": ["@babel/preset-env"]
}
```

Отредактируем `package.json`:

```json
  "scripts": {
    // ...
    "start": "nodemon --exec babel-node src/server.js",
    "build": "babel src --out-dir dist",
    "serve": "node dist/server.js"
  },
```

В папке `src` будут наши исходники. Пример `src/server.js`:

```js
import express from 'express'

const app = express()

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(3000)
```

Запуск проекта в режиме отладки:

```bash
node start
```

Сборка проекта:

```bash
npm run-script build
```

Запуск в production:

```bash
node serve
```

## Перенос каталога /home на другой раздел

Для начала нужно узнать UUID раздела, а затем добавить в `/etc/fstab` строку наподобие:

```
UUID=003fad47-a30b-45b0-acce-5e69a0de2011       /home   ext4    defaults        0       2
```

## Установка Oh My ZSH!

Устпановим необходимые пакеты:

```bash
apt-get install zsh
apt-get install git-core
```

Теперь нужно установить Oh My ZSH!:

```bash
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
```

Меняем командую оболочку:

```bash
chsh -s `which zsh`
```
Перезагружаемся:

```bash
sudo shutdown -r 0
```

При использовании темы agnoster шрифт в терминале нжно сменить, например, на Souce Code Pro.

```bash
#!/usr/bin/env bash
cd Downloads
wget https://github.com/adobe-fonts/source-code-pro/archive/2.030R-ro/1.050R-it.zip
if [ ! -d "~/.fonts" ] ; then
    mkdir ~/.fonts
fi
unzip 1.050R-it.zip
cp source-code-pro-*-it/OTF/*.otf ~/.fonts/
rm -rf source-code-pro*
rm 1.050R-it.zip
cd ~/
fc-cache -f -v
```

Модифицируем `settings.json`:

```json
{
  // ...
  "terminal.integrated.fontFamily": "Source Code Pro",
  // ...
}
```

Для zsh есть подсветка синтаксиса https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md

## Управление пользователями

Добавление

```bash
$ sudo adduser <username>
$ sudo usermod -aG sudo <username>
```

Удаление

```
$ sudo deluser <username>
$ sudo nano /etc/sudoers  # отсюда тоже может потребоваться удаление юзера
```

## Установка дополнительных дистрибутивов

Создаем раздел, задаем точку входа "/", помечаем как boot
Чтобы загрузчик grub увидел новый дистрибутив выполняем:

```bash
$ sudo update-grub
```

## Монтирование раздела

```bash
$ sudo mkdir /mnt/storage
$ sudo mount /dev/sda2 /mnt/storage
```

Чтобы посмотреть доступные разделы:

```bash
$ sudo fdisk -l
```

## Backup

Создание:

```bash
$ sudo tar -cvpzf /path/to/backups/root.tar.gz --exclude=/dev --exclude=/mnt --exclude=/tmp --one-file-system /
$ sudo tar -cvpzf /path/to/backups/home.tar.gz --one-file-system /home/
```

Восстановление бэкапа будет выглядеть так:

```bash
$ sudo tar -xvpzf /mnt/storage/Backups/root.tar.gz -C /dev/vg1/lv1 --numeric-owner
```

## LVM

Иерархия LVM: физический раздел -> группа разделов -> логический раздел.

Через GParted создаем раздел с разметкой "lvm2 pv".

Далее создаем группу разделов:

```bash
ubuntu@ubuntu:~$ sudo vgcreate vg1 /dev/nvme0n1p5
  Volume group "vg1" successfully created
```

Убедимся, что группа создана:

```bash
ubuntu@ubuntu:~$ sudo pvscan
  PV /dev/nvme0n1p5   VG vg1             lvm2 [82.28 GiB / 82.28 GiB free]
  Total: 1 [82.28 GiB] / in use: 1 [82.28 GiB] / in no VG: 0 [0   ]
```

Теперь в группе создадим два логических раздела под корень и каталог home:

```bash
ubuntu@ubuntu:~$ sudo lvcreate -n lv1 -L 20G vg1
  Logical volume "lv1" created.
ubuntu@ubuntu:~$ sudo lvcreate -n lv2 -L 30G vg1
  Logical volume "lv2" created.
```
Убедимся в том, что все сделано правильно:

```bash
ubuntu@ubuntu:~$ sudo pvscan
  PV /dev/nvme0n1p5   VG vg1             lvm2 [82.28 GiB / 32.28 GiB free]
  Total: 1 [82.28 GiB] / in use: 1 [82.28 GiB] / in no VG: 0 [0   ]
```

Теперь на каждом логическом разделе создадим файловую систему:

```bash
ubuntu@ubuntu:~$ sudo mkfs.ext4 /dev/vg1/lv1
mke2fs 1.44.1 (24-Mar-2018)
Discarding device blocks: done                            
Creating filesystem with 5242880 4k blocks and 1310720 inodes
Filesystem UUID: 780e791b-488b-430e-a1b8-1b304ad249c8
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   

ubuntu@ubuntu:~$ sudo mkfs.ext4 /dev/vg1/lv2
mke2fs 1.44.1 (24-Mar-2018)
Discarding device blocks: done                            
Creating filesystem with 7864320 4k blocks and 1966080 inodes
Filesystem UUID: 5633959e-5f43-40f3-8078-7fa870e422a8
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   
```

Для управления LVM можно установить пакет kvpm.

## Восстановление Boot

Для этого нам потребуются загрузочная флешка и наличие Интернета. Грузимся, открываем терминал и выполняем:

```bash
$ sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update && sudo apt-get install boot-repair -y && boot-repair
```

Далее следуем инструкциям.

Нужно ли править /etc/fstab перед этим?

## Дамп BIOS видекарты

```bash
echo 1 > /sys/devices/pci0000:00/0000:00:02.0/rom
cat /sys/devices/pci0000:00/0000:00:02.0/rom > vbios.dump
echo 0 > /sys/devices/pci0000:00/0000:00:02.0/rom
```
