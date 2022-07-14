![yamdb_workflow](https://github.com/genpoplevin/yamdb_final/actions/workflows/yamdb_workflow/badge.svg)

# yamdb_final

REST API для сервиса отзывов YaMDb с возможностью автоматически развернуть данный проект на удалённом сервере в docker-контейнере с помощью настроенного для автоматической проверки, сборки, развёртывания и отправки в telegram сообщения workflow.

### Описание

Сервис отзывов YaMDb. Благодаря этому сервису можно оставлять отзывы на произведения в различных категориях (например -книги, фильмы, музыка). Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и оценивают произведение по шкале от 1 до 10. Исходя из среднего значения оценое формируется рейтинг произведения. На одно произведение уникальный пользователь может оставить только один отзыв.


### Установка

Чтобы работать с проектом, необходимо сделать ответвление, fork. Это означает, что GitHub создаст вашу собственную, независимую копию репозитория. Эта копия будет находиться в вашем пространстве имён, и вы сможете вносить изменения в репозиторий, отправляя их на сервер командой push
Сделайте Fork репозитория https://github.com/genpoplevin/yamdb_final - — зайдите на его страницу и нажмите кнопку Fork:
![Без имени](https://user-images.githubusercontent.com/93812078/178969455-817324ce-60c3-487b-829d-ad43cce72aa5.jpg)

В вашем аккаунте на GitHub появится новый репозиторий — yamdb_final

Клонируйте репозиторий на локальную машину. Для этого в терминале выполните такие команды:
```
# Перейти в папку, созданную для проекта
cd .../Dev/
# Клонировать репозиторий
... Dev/$ git clone git@github.com:<ваш_username>/yamdb_final.git 
```
Создайте и активируйте виртуальное окружение, установите в него зависимости:
```
... Dev/$ cd yamdb_final
# Для MacOS и Linux
... Dev/yamdb_final$ python3 -m venv venv && . venv/bin/activate
# Для Windows
... Dev/yamdb_final$ python -m venv venv && . venv/Scripts/activate
(venv) ... Dev/yamdb_final$ pip install -r requirements.txt
```

В проекте настроен workflow - последовательность команд, которые должны выполняться после того, как будет сделан push в ветку master.
Скоро мы его запустим, а пока подготовим удалённый сервер...

Войдите на свой удаленный сервер в облаке

Остановите службу nginx
```
 sudo systemctl stop nginx
```

Установите docker
```
sudo apt install docker.io
```

 

Выполняем миграции: 
```bash 
docker-compose exec web python manage.py makemigrations reviews 
``` 
```bash 
docker-compose exec web python manage.py migrate --run-syncdb
``` 

Создаем суперпользователя: 
```bash 
docker-compose exec web python manage.py createsuperuser 
``` 

Србираем статику: 
```bash 
docker-compose exec web python manage.py collectstatic --no-input 
``` 




### Примеры запросов 
Полный перечень примеров запросов доступны по адресу: http://<ваш-URL>/redoc/
