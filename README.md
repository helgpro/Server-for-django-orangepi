# Server-for-django-orangepi
## Настройка OS сервера + python + django

ORANGE PI  <br/>
* a)Скачать Debian для Orange
* b)Отформатировать флэшку sd cart formatern
* c)Записать через win32 image  образ
* d)Растянуть место <br/> по умолчанию режит место
-Берем флэху и gparted увиличиваем раздел сохраняем настройки ждем 

2) настройка сети в nano /etc/network/interface <br/>
	Прописать </br>
        source /etc/network/interfaces.d/* это я закоментировал
            allow-hotplug eth0 #автоматически выполнять перезапуск интерфейса при его па


       iface eth0 inet static 
	
       address 62.84.121.14

       netmask 255.255.255.128 
	
     	 gateway 62.84.121.64 213.145.129.19
	
	     dns-nameservers 62.84.121.64 213.145.129.19 8.8.8.8
	
	     auto eth0 
	
####  в консоле~#  route add default gw 62.84.121.64
    ~# nano /etc/resolv.conf вписать 
    
    nameserver 62.84.121.64 

    nameserver 213.145.129.19 

> перезагрузить	    ~# /etc/init.d/networking restart

#### пароль для рута 
> ~$ sudo passwd root
> 
>Enter new UNIX password:
>
>Retype new UNIX password:
>
>passwd: password updated successfully


#### Создать нового пользователя 
adduser helg

'''Допустим у вас уже есть какой-то пользователь john, которому вы хотите дать root права:
$ grep john /etc/passwd
john : x : 1001 : 1001 : : /home/alice : /bin/sh
Для этого необходимо открыть файл /etc/passwd, найти этого пользователя и изменить его UID и GID на 0:
$ grep john /etc/passwd
john : х : 0 : 0 : : /home/john:/bin/sh
'''
#### Добавить Пользователя в Группу Root
Если вы только хотите добавить пользователя john в группу root, без предоставления ему всех привилегий пользователя root, выполните следующую команду:

    $ sudo usermod -a -G root helg
    
####  Настройка сервера SSH
> apt-get install ssh
> service ssh stop
> service ssh start
###### В настройкаках убрать подключения рута и вход по паролю
> sudo vim /etc/ssh/sshd_config
     
     AllowUsers helg user2 user3
     PermitRootLogin no
     PasswordAuthentication no


#### Restart SSH server:
##### Рабоч вариан перезагр
> /etc/init.d/ssh start   or stop
##### Еще варианты
> sudo service ssh restart
> service sshd restart
> /etc/init.d/sshd stop
> /etc/init.d/sshd start
> systemctl restart ssh.service
> systemctl restart sshd.service
> systemctl restart ssh
> systemctl restart sshd
> sudo passwd www

'''Второй параметр разрешает или запрещает вход по SSH под суперпользователем. Запрещаем вход суперпользователю.
PermitRootLogin no
AllowUsers helg
В нашем примере разрешение есть только у пользователя helg. Значениями этого параметра могут выступать имена пользователей, отделённые друг от друга пробелами. Нельзя использовать в качестве значений UID (User ID). Можно использовать запись пользователя в виде user@host, например helg@echidna. Это означает, что доступ разрешён пользователю helg с компьютера echidna. Причём пользователь и компьютер проверяются отдельно, это позволяет разграничить доступ определенных пользователей с определенных компьютеров. Существует обратный параметр – DenyUsers, который запрещает доступ пользователям, перечисленным в значении этого параметра. Кроме параметров связанных с доступом отдельных пользователей существуют соответствующие параметры для групп: AllowGroups и DenyGroups.
Следующий параметр отвечает за аутентификацию по паролю. Сейчас используется аутентификация по ключу хоста - просто раскомментируем строку.
PasswordAuthentication yes'''

### Установка питона 3.8.0 на дебиан

Скачиваем зависимости

    sudo apt-get install -y  tree redis-server nginx zlib1g-dev libbz2-dev libreadline-dev llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev liblzma-dev python3-dev python-imaging python3-lxml libxslt-dev python-libxml2 python-libxslt1 libffi-dev libssl-dev python-dev gnumeric libsqlite3-dev libpq-dev libxml2-dev libxslt1-dev libjpeg-dev libfreetype6-dev libcurl4-openssl-dev supervisor
    
    apt install libffi-dev libbz2-dev liblzma-dev libsqlite3-dev libncurses5-dev libgdbm-dev zlib1g-dev libreadline-dev libssl-dev tk-dev build-essential libncursesw5-dev libc6-dev openssl git
  
Скачиваем Python в необходимую директорию смотрим последнию версию на оф источнике  
    
    wget https://www.python.org/ftp/python/3.7.3/Python-3.9.5.tgz ; \ скачиваем архив
    tar xvf Python-3.7.* ; \ распаковываем
    cd Python-3.7.3 ; \ заходим в распакованное
    mkdir ~/home/.python ; \ создаем хитрую папку где булут храниться все зависемости к 3иму питону
    ./configure --enable-optimizations --prefix=/home/.python ; \ куда установить собирает все это дело
    ./configure --prefix=$HOME/.local --enable-optimizations;\ для ORANGE
    make -j8 ; \ число потоков от ядра компа
    sudo make install \ ставим в выбранную директорию

**PS  на orangepi ядро так се по этому make -j -l 4** 

### Установка pip

Автоматом из папки с Питоном откуда собирали пакет /home/helg/Python3.8.0

    sudo /home/helg/.python/bin/python3.8 -m pip install -U pip

после удоляем архив питона если необходимо

    cd ~
    sudo  rm –rf Python-3.8.0.tgz Python-3.8.0
Проверяем работу питона 

    ~/.python/bin/python3.8
    Exit() выход из питона
''' WARN! По умолчанию эта установка Python уже устанавливает pip. Так что вы можете пропустить эту главу.
Если вы хотите установить его вручную, вам необходимо выполнить следующие действия.
Иду домой:
$ cd ~
Скачивание необходимого скрипта:
$ wget https://bootstrap.pypa.io/get-pip.py
Запуск этого скрипта с использованием нашего нового Python 3.8: 
$ sudo python3.6 get-pip.py '''

### Пробрасываем 3-йПитон
Сейчас питон вызываеться только так /home/.python/bin/python3.9
пробрасываем пути

    nano /etc/bash.bashrc
    либо nano ~/.bashrc 

в конце добавить

    export PATH=$PATH:/home/.python/bin    //этот вариант
Чтобы по умолчанию вызывался питон3.8 из командной строки ~# python  

>В ~# nano /etc/bash.bashrc
>Добавляем альяс

    alias python=home/helg/.python/bin/python3.9
    alias pip=home/helg/.python/bin/pip

Перегружаем как в root-е так и везде

    source .bashrc
    source /etc/bash.bashrc //этот вариант
теперь pythn3.9 работает
    
 ### установка виртуального окружения 

    ~#mkdir code в этой паке создаються проекты где у каждого проекта свое виртуальное окружения
    ~# cd code
    ~# mkdir project1
    ~# cd project1

Project1~#python3.7 -m venv env
где env это создания каталога вирт окруженя

> активация 	.  env/bin/activate       ---- посде точки пробел    
  
    source env/bin/activate

> дезактивация deactivate 

апгрэйд pip
~# pip install –U pip


### Установка django

    ~# pip install django

Покажет что встало 

    ~# pip freeze > requirements.txt
    ~# nano requirements.txt покажет что встало

>Установка проекта

    django-admin startproject project1
    cd project1
    pip install ipython

На команду <br/>    
> python ./manege.py shell    начал ругаться что нет sqlite сервака ModuleNotFoundError: No module named '_sqlite3' 
> apt-get install sqlite-devel libsqlite3-dev

из папки с распокованым питоном Pathon3.8.3 
./configure --enable-loadable-sqlite-extensions
Ругался на configure слител фаил из распоковонного питона

    make –j –l 2
    pip install --upgrade pip 
    pip install pysqlite3

> P.S. добавил библиотеки и собрап питон поновой все встало как надо

### StartApp django

    python ./manage.py startapp ferst

В project1 nano setings.py добавляем приложение тема джанги
Выходим снова в project1/gpio 
Запускаем сервак   ~# ./manage.py runserver 62.84.121.14:8000 
 тоже работает 0.0.0.0:8000
В браузере заходим выводит nginx если порт 8000 то джанго
В gpio/gpio settings.py Allowhost прописать ip-ик [’62.84.121.14’]
Перезапускаем и ракета летит!!!
pip install psycopg2

### Установка Gunicorn

    ~#pip install gunicorn
    ~# pip freeze
    ~# cd /home/helg/code/project1/gpio
    ~# nano gunicorn_config.py
      
      command = '/home/helg/code/project1/env/bin/gunicorn'
      pythonpath = '/home/helg/code/project1/gpio'
      bind = '62.84.121.14:8001'
      workers = 5
      user = 'helg'
      limit_request_fields = 32000
      limit_request_field_size = 0
      raw_env = 'DJANGO_SETTINGS_MODULE=gpio.settings'

      ~# cd /home/helg/code/project1
      ~# mkdir bin
       ~# nano /home/helg/code/project1/bin/start_gunicorn.sh

	#!/bin/bash
	source /home/helg/code/project1/env/bin/activate
	source /home/helg/code/project1/env/bin/postactivate
	exec gunicorn  -c "/home/helg/code/project1/gpio/gunicorn_config.py" gpio.wsgi

добавим прав на выполнение

    ~# chmod +x /home/helg/code/project1/bin/start_gunicorn.sh

Запускаем gunicorn

    code/project1#  . ./bin/start_gunicorn.sh

Добавляем прокси в nginx

    nano /etc/nginx/site-enable defoult

    server {
            listen 80 default_server;
            listen [::]:80 default_server;

            root /var/www/html;


            index index.html index.htm index.nginx-debian.html;

            server_name _;

    #       location / {
    #
    #               try_files $uri $uri/ =404;
    #       }


            location / {
                    proxy_pass http://127.0.0.1:8001;
                    proxy_set_header X-Forwarded-Host $server_name;
                    proxy_set_header X-Real-IP $remote_addr;
                    add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM N$
                    add_header Access-Control-Allow-Origin *;

            }


    }

> Запускаем в браузере 62.84.121.14:801
>Должно выдать 502 ошибку если запустить в bin start_gunicorn
>То ракета летит

### Supervision чтоб гуник запускался в автомате 

В папке создать фаил  /etc/supervisor/conf.d/project1.conf
Вставить 
    
    [program:www_gunicorn]
    command=/home/helg/code/project1/bin/start_gunicorn.sh
    user=helg
    process_name=%(program_name)s
    numprocs=1
    autostart=1
    autorestart=1
    redirect_stderr=true

можно смотреть логи в /var/log/supervisor/ как что запустилось

Теперь ставим postgres
Удалось поставить только 9.6.19 версию были конфликты с архетектурой проца

>Ставил apt-get install postgressql Mysql-server также невставал понт с mariadb нехочет стартовать статус 227 пока не решил проблему

    su  postgres
    export PATH=$PATH:/usr/lib/postgresql/9.6/bin
    createdb --encoding UNICODE dbms_db --username postgres
    exit
#### Создание нового юзера 
Создание новой бд
Даем все привилегии данному юсеру

    postgres=# ...
    create user dbms with password 'some_password';
    ALTER USER dbms CREATEDB;
    grant all privileges on database dbms_db to dbms;
    \c dbms_db
    GRANT ALL ON ALL TABLES IN SCHEMA public to dbms;
    GRANT ALL ON ALL SEQUENCES IN SCHEMA public to dbms;
    GRANT ALL ON ALL FUNCTIONS IN SCHEMA public to dbms;
    CREATE EXTENSION pg_trgm;
    ALTER EXTENSION pg_trgm SET SCHEMA public;
    UPDATE pg_opclass SET opcdefault = true WHERE opcname='gin_trgm_ops';
    \q
    exit
 
> gin_trgm_ops  - Это расширение для триграмного поиска

#### Для входа без пароля

     nano ~/.pgpass
      localhost:5432:dbms_db:dbms:ziga
    chmod 600 ~/.pgpass
    psql -h localhost -U dbms dbms_db

Подключаем postgres к Djanjo

    ~# cd code/project1/env
    ~# . env/bin/activate
    ~# pip install psycopg2

    ~# nano  gpio/gpio/settings.py
    . . .

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2',
            'NAME': 'dbms',
            'USER': 'dbms_db',
            'PASSWORD': 'ziga',
            'HOST': 'localhost',
            'PORT': '5432',
        }
    }

    . . .
    ~# python manage.py makemigrations
    ~# python manage.py migrate


wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - ; \
RELEASE=$(lsb_release -cs) ; \
echo "deb http://apt.postgresql.org/pub/repos/apt/ ${RELEASE}"-pgdg main | sudo tee  /etc/apt/sources.list.d/pgdg.list ; \
apt update ; \
apt -y install postgresql-11 ; \

#### Меняем пароль на юзера postgres
   
    ~# passwd postgres
Пароль ziga 
Зайдем под юзером postgres 
  
    su - postgres


Настройка локалей  без них ругаеться postgres на создание БД

    sudo localedef ru_RU.UTF-8 -i ru_RU -fUTF-8 ; \
    export LANGUAGE=ru_RU.UTF-8 ; \
    export LANG=ru_RU.UTF-8 ; \
    export LC_ALL=ru_RU.UTF-8 ; \
    sudo locale-gen ru_RU.UTF-8 ; \
    sudo dpkg-reconfigure locales

В файле       ~# nano /etc/profile

    localedef ru_RU.UTF-8 -i ru_RU -fUTF-8 ; \
    export LANGUAGE=ru_RU.UTF-8 ; \
    export LANG=ru_RU.UTF-8 ; \
    export LC_ALL=ru_RU.UTF-8 ; \
    locale-gen ru_RU.UTF-8 ; \
    dpkg-reconfigure locales

    export LC_ALL="en_US.UTF-8"
    export LC_CTYPE="en_US.UTF-8"
    sudo dpkg-reconfigure locales

Экспортируем директорию 

    export PATH=$PATH:/usr/lib/postgresql/11/bin
и создадим деректорию  dbms_db она же база данных 

    createdb --encoding UNICODE dbms_db --username postgres

Создаем юзера sudo -u postgres psql
Создаем пароль для этого юсера 
create user dbms with password 'ziga';

exit

#### Была проблема с upgrade apt-get проблема с redise-server

    apt-get purge redis-server удалил

Исправляем ошибку так
Заходим в директорию /var/lib/dpkg/info (это делается с правами root) и находим в ней все файлы с именем обрабатываемого пакета, из-за которого выдается ошибка и переименовываем все файлы с его именем (например, было paket.info стало ~paket.info)…
Далее показано как перейти в каталог и удалить нужные пакет (слово paket замените на имя вашего пакета).

    cd /var/lib/dpkg/info
     sudo rm paket.*
Запускаем команду исправления:

    sudo apt-get install -f && sudo dpkg --configure -a
Затем устанавливаем пакет по новой (если конечно он еще нужен). Проверяем, создались ли новые файлы в папке /var/lib/dpkg/info, а если нет — то переименовываем наши файлы обратно (убираем «~»).
Все вроде…
> PS: если не получилось, пробуем еще так:

    sudo DEBCONF_DEBUG=developer apt-get install -f
Завершаем всё обновлением всего

    sudo apt-get update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade
да и в завершении всего, можно радостно перегрузить систему:
    
    sudo reboot
    ~# nano project1/settings.py
В setting install apps добавить приложение 


### OrangePi PC работаем с GPIO, UART и i2c
Для работы с GPIO в OrangePi не подойдут библиотеки и инструменты предназначенные для RaspberryPi, однако мне удалось найти библиотеку WiringOP основанную на библиотеке WiringPi.

Установка WiringOP

    git clone https://github.com/zhaolei/WiringOP.git -b h3
    cd WiringOP
    chmod +x ./build
    sudo ./build

после установки проверяем работоспособность

    gpio readall
покажет все порты, назначения и состояния

### Django дополнение
Создасться главая директория сайта

    $ django-admin startproject gpio
 Создание приложений

    $ python manage.py startapp main
В приложении main в views.py
    
    from django.http import HttpResponse
    def index(request):
        return HttpResponse("Hello, world. You're at the mains index.")

Добавить файл urls.py в ту же деректорию с приложением main/ в файле:

    from django.urls import path
    from . import views
    urlpatterns = [
       path('', views.index, name='index'),  # views.index это экшин
    ]
В основной урл который в каталоге с названием сайта у меня gpio
Добавить include и путь к урлу приложения

    from django.contrib import admin
    from django.urls import path
    from django.urls import include
    urlpatterns = [
       path('admin/', admin.site.urls),
        path('main/', include('main.urls')),                  # тут main это контроллер в урле include urls.py в /main
    ]
Я прописал  path(‘’,include(main.urls)),
Подключаем шаблон
В папке с приложением создаем две папки main/templates/main и в последней создать фаил 
	Index.html     c кодом html
	> Если шаблон наследуется то прописываем 
	
    {% extends 'base.html' %}       наследование с базового шаблона
	  {% extends 'blog/base_blog.html' %} наследование с главного шаблоа приложения
Логика вывода 
Блоки в которых выводиться логика должны называться одинаково как в базовом шаблоне так и наследуемые

    {%block title%}        
    {%endblock%}
     {% block content %}
      <h2 index blog/templates/ blog/ index.html </h1>
      <p> {{name}}</p>     вывод одной переменной

    {%for n in names%}   цикл вывода списка либо словаря

        <p>{{n}}

    {%endfor%}

    {% endblock %}

### Управление пинами 
В шаблоне прописываем форму с кнопками не обращаясь и не создовая фаил forms.py
    
    <hr>
         <form action="" method="post">
          {% csrf_token %}
          <input type="submit" name="on" value=" On led ">
          <input type="submit" name="of" value=" Of led">
      </form>
В катологе main/views.py ипортируем библиотеку os и принимаем методом POST наши кнопки

    import os
    from django.shortcuts import render
    from django.http import HttpResponse
    #from .forms import KnopkaForm
    def index(request):
            if request.method == 'POST' and 'on' in request.POST:
                    os.system("/home/helg/gpioini.sh")
    #                os.system("echo 20> /sys/class/gpio/export")                      # без файла
    #                os.system("echo out > /sys/class/gpio/gpio20/direction")
    #                os.system("echo 1 > /sys/class/gpio/gpio20/value")
            elif request.method == 'POST' and 'of' in request.POST:
                    os.system("/home/helg/gpioini2.sh")
            else:
                    pass
            return render(request, 'main/index.html')

Создаем в корне домашней директории два файла c bash скриптами для управлениями пинами  gpioini.sh gpioini2.sh

    #!/bin/bash

    ## gpio20 PA0 pin 37
    echo "20" > /sys/class/gpio/export
    echo "out" > /sys/class/gpio/gpio20/direction
    echo "1" > /sys/class/gpio/gpio20/value         
один включает пин второй выключает
последняя строка второго файла по PA пинам смотреть доку так 37 пин = PA20

    echo "0" > /sys/class/gpio/gpio20/value         

В папке с приложением main/view.py

    from django.shortcuts import render
    def index(request):
        return render(request, 'blog/index.html')
Базовый шаблон 
В корне создаем папку templates/ в ней будет базовый шаблон base.html
Что бы джанго понимал что есть базовый шаблон в файле settings.py 
Прописываем путь к папке tevplates

    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [BASE_DIR,'templates'],
    …….
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>
    {%block title%}
    Сайт Django
    {%endblock%
    }</title>
      <link rel="stylesheet" href="static/css/bootstrap.min.css">
    </head>
    <body>
    <ul class="sidebar-nav">
      <div class="jumbotron">
        <div class="container">
            <a href="/" class="btn btn-success btn-lg">Главная страница</a>
            <a href="/catalog" class="btn btn-primary btn-lg">Каталог</a>   
            <a href="/contact" class="btn btn-success btn-lg">Контакты</a>
        </div>
          {% block content %}
        pusto
          {% endblock %}
      </div>
      <div class="container">
        <hr>
        &copy; Все права защищены 2018
      </div>
    </body>
    </html>
Так же  можно прописать путь к статической папке где лежат bootstrap css итд в setting.py

    TATIC_URL = '/static/'
    STATICFILES_DIRS = [
        BASE_DIR / "static",
        '/var/www/static/',
    ]


###Модели

    from django.db import models
    # Create your models here.
    class Post(models.Model):
        title = models.CharField(max_length=150,db_index=True)
        slug = models.SlugField(max_length=150,unique=True)
        body = models.TextField(blank=True, db_index=True)
        date_pub = models.DateTimeField(auto_now_add=True)

        def __str_(self):                                                                                 # метод str переопределяет название класа с jbject
            return '{}'.format(self.title) # {} format strok                       # Форматирование строк {} = место куда подставл данные
Реверс пути
Фильтры для шаблонов   |trancatewords

   

