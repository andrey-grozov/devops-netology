# Домашние задания
## Домашнее задание к занятию «2.1. Системы контроля версий.»
#### devops-netology
    ``first line``
    second line
    another line
#### В файле README.md опишите своими словами какие файлы будут проигнорированы в будущем благодаря добавленному 
    в .gitignore будут проигнорированы файлы или каталоги, которые мы укажем по опредедленным шаблонам.
    Игнорируется указанный файл -                     crash.log
    Игнорировать все файлы в каталоге terraform/ -    terraform/
    Игнорировать все .txt файлы в каталоге log/ -     log/**/*.log
    Исключить все файлы с расширением .a??. где ?? 2 любых символа -     *.t??
    Отслеживать файлы, которые попадают под исключение выше -            !test.tmp

## Домашнее задание к занятию «2.4. Инструменты Git»
#### 1. Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea. 
    git show aefea 
    commit aefead2207ef7e2aa5dc81a34aedf0cad4c32545
    Update CHANGELOG.md

#### 2. Какому тегу соответствует коммит 85024d3?
    git show 85024d3
    Коммит 85024d3 соответствует тегу tag: v0.12.23

#### 3. Сколько родителей у коммита b8d720? Напишите их хеши. 
    git show b8d720
    commit b8d720f8340221f2146e4e4870bf2ee0bc48f2d5
    Merge: 56cd7859e 9ea88f22f
    2 родителя - Merge: 56cd7859e 9ea88f22f

#### 4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
    git log --pretty=oneline v0.12.24...v0.12.23
    33ff1c03bb960b332be3af2e333462dde88b279e (HEAD, tag: v0.12.24) v0.12.24
    b14b74c4939dcab573326f4e3ee2a62e23e12f89 [Website] vmc provider links
    3f235065b9347a758efadc92295b540ee0a5e26e Update CHANGELOG.md
    6ae64e247b332925b872447e9ce869657281c2bf registry: Fix panic when server is unreachable
    5c619ca1baf2e21a155fcdb4c264cc9e24a2a353 website: Remove links to the getting started guide's old location
    06275647e2b53d97d4f0a19a0fec11f6d69820b5 Update CHANGELOG.md
    d5f9411f5108260320064349b757f55c09bc4b80 command: Fix bug when using terraform login on Windows
    4b6d06cc5dcb78af637bbb19c198faff37a066ed Update CHANGELOG.md
    dd01a35078f040ca984cdd349f18d0b67e486c35 Update CHANGELOG.md
    225466bc3e5f35baa5d07197bbc079345b77525e Cleanup after v0.12.23 release

#### 5.Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).
    git log -S'func providerSource' --oneline
    git show 8c928e835
    8c928e835 main: Consult local directories as potential mirrors of providers

#### 6.Найдите все коммиты в которых была изменена функция globalPluginDirs.
    git log -SglobalPluginDirs --oneline
    35a058fb3 main: configure credentials from the CLI config file
    c0b176109 prevent log output during init
    8364383c3 Push plugin discovery down into command package

#### 7.Кто автор функции synchronizedWriters?
    git log -S'synchronizedWriters' --oneline
    git show 5ac311e2a
    git checkout 5ac311e2a
    git blame synchronized_writers.go
    (Martin Atkins 2017-05-03 16:25:41 -0700 15)

## Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"
#### 8.Ознакомиться с разделами man bash, почитать о настройках самого bash:
    Какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
    длина журнала задается переменной HISTSIZE, описывается в manual разделе переменных командного интерпретатора.

    Что делает директива ignoreboth в bash?
    использует обе опции ‘ignorespace’ и ‘ignoredups’ для контролирования способа сохранения истории команд
    ‘ignorespace’ - не сохранять строки начинающиеся с символа <пробел>
    ‘ignoredups’ -  не сохранять строки, совпадающие с последней выполненной командой

#### 9.В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?
    {} используются для создания списков, выполняющихся в среде текущего командного интерпретатора.и описываются в строке  { list; }

#### 10.Основываясь на предыдущем вопросе, как создать однократным вызовом touch 100000 файлов? А получилось ли создать 300000?
    touch test{1..100000} команда отработала нормально
    touch test1{1..300000} команда отработала с ошибкой 
    -bash: /usr/bin/touch: Argument list too long

#### 11.В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]
    конструкция [[ -d /tmp ]] проверяет наличие указанного каталога, в случае успеха возвращает истину, иначе ложь ( В нашем случае истину).

#### 12.Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:
    bash is /tmp/new_path_directory/bash
    bash is /usr/local/bin/bash
    bash is /bin/bash
    В данном случае Необходимо создать каталог new_path_directory, скопировать в него bash и добавить к переменной PATH этот каталог PATH=/tmp/new_path_directory/:$PATH
    vagrant@vagrant:/tmp$ mkdir new_path_directory
    vagrant@vagrant:/tmp$ cp -p /bin/bash /tmp/new_path_directory/bash
    vagrant@vagrant:/tmp$ PATH=/tmp/new_path_directory:$PATH
    vagrant@vagrant:/tmp/new_path_directory$ type -a bash
    bash is /tmp/new_path_directory/bash
    bash is /usr/bin/bash
    bash is /bin/bash

#### 13.Чем отличается планирование команд с помощью batch и at?
    batch планирует задания и выполняет их если позволяет уровень загрузки системы (по умолчанию 1.5). Если средняя загрузка системы выше указанной, задания будут ждать в очереди.
    at используется для назначения и выполнения задания без учета средней загрузки системы.

## Домашнее задание к занятию "3.1. Работа в терминале, лекция 2"

#### 1.Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
    Команда CD является внутренней командой, встроенной в оболочку. Возможно сделано для того чтобы она везде работала одинаково и пользователям нельзя было переопределять ее функционал.

#### 2.Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.
    альтернативная команда grep <some_string> <some_file> -с выведет количество строк, содержащих <some_string>. 

#### 3.Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?
    процесс с PID 1 systemd является родителем для всех процессов.

#### 4.Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?
    ls /root 2>/dev/pts/1
#### 5.Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.
    vagrant@vagrant:~$ cat <list.in >list.out

#### 6.Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?
    Вывести данные из PTY в TTY можно cat /dev/pts/1 > /dev/tty1. Вводимые данные можно наблюдать.

#### 7.Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?
    bash 5>&1 - перенаправит stdout в файловый дискриптор 5. 
    echo netology > /proc/$$/fd/5 - выведет строку netology, т.к. переопределен стандартный вывод на файловый дискриптор 5. 

#### 8.Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.
    vagrant@vagrant:~$ bash 6>&1
    vagrant@vagrant:~$ ls root >&6 2>&1 | grep 'such'
    ls: cannot access 'root': No such file or directory

#### 9.Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?
    Нужно запустить команду env.

#### 10.Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe.
    /proc/<PID>/cmdline - файл содержит полную командную строку запуска процесса, кроме зомби процессов.
    /proc/<PID>/exe - файл является символьной ссылкой, содержащей фактическое полное имя выполняемого файла.

#### 11.Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.
    Наиболее старшая версия инструкций - sse4_2.

#### 12.При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:
    vagrant@netology1:~$ ssh localhost 'tty'
    not a tty
    
    Почитайте, почему так происходит, и как изменить поведение.
    Происходит потому что по умолчанию TTY не выделяется для удаленного сеанса, изменить поведение можно используя ssh -tt localhost 'tty'

#### 13.Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.
    vagrant@vagrant:~$ ps -a
    PID TTY          TIME CMD
    1182 pts/0    00:00:00 sudo
    1183 pts/0    00:00:00 su
    1184 pts/0    00:00:00 bash
    1193 pts/1    00:00:00 screen
    1233 pts/0    00:00:00 top
    1234 pts/2    00:00:00 ps
    vagrant@vagrant:~$ sudo reptyr 1233

#### 14.sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.
    Команда tee нужна для записи вывода любой команды в один или несколько файлов.
    Rоманда с sudo tee будет работать будет потому что она перенаправляет потоки и запускается от sudo в отличии от шела.

## Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

#### 1.Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. Вам нужно найти тот единственный, который относится именно к cd.

    chdir("/tmp")

#### 2.Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:

    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64

    Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.

    База данных находится в /usr/share/misc/magic.mgc
    
#### 3.Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

    vagrant@vagrant:~/big$ cat /dev/urandom >> logfile
    vagrant@vagrant:~/big$ rm logfile
    vagrant@vagrant:~/big$ lsof | grep cat
    lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
      Output information may be incomplete.
    cat       2747                        vagrant  cwd       DIR              253,0       4096     131098 /home/vagrant/big
    cat       2747                        vagrant  rtd       DIR              253,0       4096          2 /
    cat       2747                        vagrant  txt       REG              253,0      43416    1310756 /usr/bin/cat
    cat       2747                        vagrant  mem       REG              253,0    5699248    1321336 /usr/lib/locale/locale-archive
    cat       2747                        vagrant  mem       REG              253,0    2029224    1313864 /usr/lib/x86_64-linux-gnu/libc-2.31.so
    cat       2747                        vagrant  mem       REG              253,0     191472    1313821 /usr/lib/x86_64-linux-gnu/ld-2.31.so
    cat       2747                        vagrant    0u      CHR              136,1        0t0          4 /dev/pts/1
    cat       2747                        vagrant    1w      REG              253,0 1750372352     131101 /home/vagrant/big/logfile (deleted)
    cat       2747                        vagrant    2u      CHR              136,1        0t0          4 /dev/pts/1
    cat       2747                        vagrant    3r      CHR                1,9        0t0         11 /dev/urandom
    cat       2747                        vagrant    9u      CHR              136,1        0t0          4 /dev/pts/1
    vagrant@vagrant:~/big$ cat /dev/null > /proc/2747/fd/1
    
#### 4.Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

    Зомби процессы не занимают ресурсы в ОС (CPU, RAM, IO). 
    Они блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом. 
    Т.е. приводит к «утечке ресурсов» в виде накопления записей в таблице процессов.    

#### 5.В iovisor BCC есть утилита opensnoop:

    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc

    На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке.

    PID    COMM               FD ERR PATH
    796    vminfo              5   0 /var/run/utmp
    585    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
    585    dbus-daemon        16   0 /usr/share/dbus-1/system-services
    585    dbus-daemon        -1   2 /lib/dbus-1/system-services
    585    dbus-daemon        16   0 /var/lib/snapd/dbus-1/system-services/
#### 6.Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.
    
    Используется системный вызов uname - получает название ядра и информацию о нем. 
    Цитата из man об альтернативном местоположении информации - Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

#### 7.Чем отличается последовательность команд через ; и через && в bash? Например:

    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#

    ; — оператор последовательного выполнения нескольких команд. Каждая последующая команда начинает выполняться только после завершения предыдущей (неважно, успешного или нет);
    && — оператор выполнения команды только после успешного выполнения предыдущей;

    Есть ли смысл использовать в bash &&, если применить set -e?
    -e немедленный выход при выполнении команды с ненулевым результатом,
    Смысл использовать && есть, т.к. если будет нулевой результат выполнения команды до &&, то выполнение продолжится.
 
#### 8.Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?
    
    -e скрипт немедленно завершит работу, если любая команда выйдет с ошибкой.
    -u проверяет инициализацию переменных в скрипте. Если переменной не будет, скрипт немедленно завершиться.  
    -x bash печатает в стандартный вывод все команды перед их исполнением.
    -o чтобы убедиться, что все команды в пайпах завершились успешно.

#### 9.Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

    Наиболее часто встречающийся стстус - S    interruptible sleep. 
    Дополнительные символы к основной букве выводят расширенную информацию о процессе (приоритет процесса, имеет заблокированные страницы в памяти и др.) 

## Домашнее задание к занятию "3.4. Операционные системы, лекция 2"
#### 1.Node Exporter
    unit-файл
    vagrant@vagrant:~$ cat /etc/systemd/system/node_exporter.service
    [Unit]
    Description=Node Exporter
    After=network-online.target
    
    [Service]
    User=node_exporter
    Group=node_exporter
    Type=simple
    EnvironmentFile=-/etc/default/node_exporter
    ExecStart=/usr/local/bin/node_exporter $EXTRA_OPTS
    
    [Install]
    WantedBy=multi-user.target    
    
    файл с параметрами
    vagrant@vagrant:~$ cat /etc/default/node_exporter
    EXTRA_OPTS="--collector.disable-defaults --collector.cpu --collector.meminfo --collector.filesystem --collector.netdev"

    systemctl daemon-reload
    systemctl enable node_exporter
    systemctl start node_exporter
    systemctl status node_exporter
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.894Z caller=node_exporter.go:179 msg="Build context" build_context="(go=go1.15.8, user>Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=filesystem_common.go:74 collector=filesystem msg="Parsed flag --collector.>Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=filesystem_common.go:76 collector=filesystem msg="Parsed flag --collector.>Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=node_exporter.go:106 msg="Enabled collectors"
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=node_exporter.go:113 collector=cpu
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=node_exporter.go:113 collector=filesystem
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.895Z caller=node_exporter.go:113 collector=meminfo
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.896Z caller=node_exporter.go:113 collector=netdev
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.896Z caller=node_exporter.go:195 msg="Listening on" address=:9100
    Jun 25 02:27:05 vagrant node_exporter[1692]: level=info ts=2021-06-25T02:27:05.896Z caller=tls_config.go:191 msg="TLS is disabled." http2=false

#### 2.Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
    --collector.cpu 
    --collector.meminfo 
    --collector.filesystem 
    --collector.netdev

#### 4.Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
    Да можно, есть много сообщений про систему виртуализации, например:
    Booting paravirtualized kernel on KVM
    Detected virtualization oracle.

#### 5.Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
    Значение по умолчанияю для максимального значения дескрипторов fs.nr_open = 1024*1024 (1048576)
    Ulimit органичивает ресурсы на текущую сессию - open files (-n) 1024.
#### 6.Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
    vagrant@vagrant:~$ sudo unshare -f --pid --mount-proc sleep 1h
    vagrant@vagrant:~$ ps aux | grep sleep
    root        2539  0.0  0.0   8076   528 pts/0    S    12:37   0:00 sleep 1h
    
    vagrant@vagrant:~$ sudo nsenter --target 2539 --pid --mount
    root@vagrant:/# ps aux
    USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root           1  0.0  0.0   8076   528 pts/0    S+   12:37   0:00 sleep 1h
    root           2  0.0  0.3   9836  4012 pts/1    S    12:53   0:00 -bash
    root          11  0.0  0.3  11492  3396 pts/1    R+   12:53   0:00 ps aux
#### 7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
    :(){ :|:& };: = это форк-бомба, функция, которая порождает себя n-раз, до исчерпания ресурссов системы.
    
    Механизм автоматической стабилизации - fork rejected by pids controller in /user.slice/user-1000.slice/session-3.scope
    Ограничение максимального числа задач.
    По умолчанию TaskMax равен 33% (от ограничений user.slice и зависит от размера оперативной памяти выделенной ОС), его можно увеличить в файле /usr/lib/systemd/system/user-.slice.d/10-defaults.conf
    vagrant@vagrant:~$ systemctl status
    ● vagrant
        State: running
         Jobs: 0 queued
       Failed: 0 units
        Since: Sat 2021-06-26 04:40:28 UTC; 1h 12min ago
       CGroup: /
               ├─user.slice
               │ └─user-1000.slice