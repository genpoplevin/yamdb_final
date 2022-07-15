![yamdb_workflow](https://github.com/genpoplevin/yamdb_final/workflows/yamdb_workflow/badge.svg)
# yamdb_final
REST API для сервиса отзывов YaMDb с возможностью автоматически развернуть данный проект на удалённом сервере в docker-контейнере с помощью настроенного для автоматической проверки, сборки, развёртывания и отправки в telegram сообщения workflow.

### Описание

Сервис отзывов YaMDb. Благодаря этому сервису можно оставлять отзывы на произведения в различных категориях (например -книги, фильмы, музыка). Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и оценивают произведение по шкале от 1 до 10. Исходя из среднего значения оценое формируется рейтинг произведения. На одно произведение уникальный пользователь может оставить только один отзыв.


### Установка на локальном компьютере
Эти инструкции помогут вам создать копию проекта и запустить ее на локальном компьютере для целей разработки и тестирования.

## Установка Docker
Установите Docker, используя инструкции с официального сайта:
- для [Windows и MacOS](https://www.docker.com/products/docker-desktop)
- для [Linux](https://docs.docker.com/engine/install/ubuntu/). Отдельно потребуется установть [Docker Compose](https://docs.docker.com/compose/install/)

# Соберите приложение:

Клонируйте репозиторий с проектом на свой компьютер.
В терминале из рабочей директории выполните команду:
```
git clone https://github.com/genpoplevin/yamdb_final.git
```
# Перейти в папку yamdb_final/
```
cd yamdb_final
```

# Cоздать и активировать виртуальное окружение:

```
python -m venv venv
```

```
source venv/Scripts/activate
```

Установить зависимости из файла requirements.txt:
```
pip install -r requirements.txt
```

Из папки yamdb_final/infra в терминале выполните команду:
docker-compose up -d --build

Выполните миграции:
docker-compose exec web python manage.py migrate

Загрузите статику:
docker-compose exec web python manage.py collectstatic --no-input

Заполните базу тестовыми данными:
docker-compose exec web python manage.py loaddata fixtures.json
```
Теперь приложение будет доступно в браузере по адресу localhost/admin/
Логин: 79530088804@yandex.ru
пароль: admin


### Деплой на удалённый сервер

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

Установите docker-compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Скопируйте файлы docker-compose.yaml и nginx/default.conf из вашего проекта на сервер в home/<ваш_username>/docker-compose.yaml и home/<ваш_username>/nginx/default.conf соответственно

Добавьте в Secrets GitHub Actions:
HOST - ip вашего удалённого сервера
USER - имя пользователя
SSH_KEY
PASSPHRASE - пароль
DB_ENGINE - можно использовать значение 'django.db.backends.postgresql'
POSTGRES_USER - можно использовать значение postgres 
POSTGRES_PASSWORD  - можно использовать значение postgres
DB_HOST - можно использовать значение db
DB_PORT - можно использовать значение 5432
DOCKER_PASSWORD - пароль от dockerhub
DOCKER_USERNAME - имя пользователя dockerhub
TELEGRAM_TO - id вашего телеграм аккаунта
TELEGRAM_TOKEN - токен телеграм-бота (в workflow настроена отправка ботом сообщения об успешном деплое проекта на удалённый сервер)

Запуск workflow
Чтобы workflow запустился, нужно сделать push в мастер ветку репозитория:
Подготовьте локальный репозиторий к первому коммиту, сделайте коммит и запушьте его в удалённый репозиторий:
```
(venv) ... Dev/yamdb_final$ git add .
(venv) ... Dev/yamdb_final$ git commit -m 'ваш_комментарий'
(venv) ... Dev/yamdb_final$ git push
```

Файлы проекта запушатся в Git, в ветку master. После этого должен был сработать workflow, а телеграм-бот пришлёт сообщение об успешном деплое проекта.


На удалённом сервере выполняем миграции: 
``` 
sudo docker-compose exec web python manage.py makemigrations reviews 
``` 
``` 
sudo docker-compose exec web python manage.py migrate --run-syncdb
``` 

Создаем суперпользователя: 
``` 
sudo docker-compose exec web python manage.py createsuperuser 
``` 

Србираем статику: 
``` 
sudo docker-compose exec web python manage.py collectstatic --no-input 
``` 


## Аутентификация

Выполните POST-запрос *localhost/api/v1/auth/token/* передав поля username и confirmation_code(см. пункт 9 и 10).

API вернет JWT-токен в формате:

    {
        "token": "ХХХХХXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    }
    
token - наш токен, который необходимо передать в заголовке Authorization: Bearer <токен> при отправке запросов

Теперь пользователь считается авторизованным и может полноценно использовать текущий проект по отзывам произведений.

## Примеры запросов

### Регистрация пользователей и выдача токенов

Регистрация нового пользователя

Получить код подтверждения на переданный email.
Права доступа: Доступно без токена.
Использовать имя 'me' в качестве username запрещено.
Поля email и username должны быть уникальными.
```
POST http://127.0.0.1:8000/api/v1/auth/signup/
```
Пример запроса:
```
{
"email": "string",
"username": "string"
}
```
Пример ответа:
```
{
"email": "string",
"username": "string"
}
```
Получение JWT-токена

Получение JWT-токена в обмен на username и confirmation code.
Права доступа: Доступно без токена.
```
POST http://127.0.0.1:8000/api/v1/auth/token/
```
Пример запроса:
```
{
"username": "string",
"confirmation_code": "string"
}
```
Пример ответа:
```
{
  "token": "string"
}
```
### Категории

Получение списка всех категорий

Получить список всех категорий
Права доступа: Доступно без токена
```
GET http://127.0.0.1:8000/api/v1/categories/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "name": "string",
        "slug": "string"
      }
    ]
  }
]
```
Добавление новой категории

Создать категорию.
Права доступа: Администратор.
Поле slug каждой категории должно быть уникальным.
```
POST http://127.0.0.1:8000/api/v1/categories/
```
Пример запроса:
```
{
  "name": "string",
  "slug": "string"
}
```
Пример ответа:
```
{
  "name": "string",
  "slug": "string"
}
```
Удаление категории

Удалить категорию.
Права доступа: Администратор.
```
DELETE http://127.0.0.1:8000/api/v1/categories/{slug}/
```

### Жанры
Получение списка всех жанров

Получить список всех жанров.
Права доступа: Доступно без токена
```
GET http://127.0.0.1:8000/api/v1/genres/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "name": "string",
        "slug": "string"
      }
    ]
  }
]
```
Добавление жанра

Добавить жанр.
Права доступа: Администратор.
Поле slug каждого жанра должно быть уникальным.
```
POST http://127.0.0.1:8000/api/v1/genres/
```
Пример запроса:
```
{
  "name": "string",
  "slug": "string"
}
```
Удаление жанра

Удалить жанр.
Права доступа: Администратор.
```
DELETE http://127.0.0.1:8000/api/v1/genres/{slug}/
```

### Произведения (Titles)
Получение списка всех произведений

Получить список всех объектов.
Права доступа: Доступно без токена
```
GET http://127.0.0.1:8000/api/v1/titles/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "name": "string",
        "year": 0,
        "rating": 0,
        "description": "string",
        "genre": [
          {
            "name": "string",
            "slug": "string"
          }
        ],
        "category": {
          "name": "string",
          "slug": "string"
        }
      }
    ]
  }
]
```
Добавление произведения

Добавить новое произведение.
Права доступа: Администратор.
Нельзя добавлять произведения, которые еще не вышли (год выпуска не может быть больше текущего).
При добавлении нового произведения требуется указать уже существующие категорию и жанр.
```
POST http://127.0.0.1:8000/api/v1/titles/
```
Пример запроса:
```
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```
Пример ответа:
```
{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}
```
Получение информации о произведении

Информация о произведении
Права доступа: Доступно без токена
```
GET http://127.0.0.1:8000/api/v1/titles/{titles_id}/
```
Пример ответа:
```
{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}
```
Частичное обновление информации о произведении

Обновить информацию о произведении
Права доступа: Администратор
```
PATCH http://127.0.0.1:8000/api/v1/titles/{titles_id}/
```
Пример запроса:
```
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```
Пример ответа:
```
{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}
```
Удаление произведения

Удалить произведение.
Права доступа: Администратор.
```
http://127.0.0.1:8000/api/v1/titles/{titles_id}/
```

### Отзывы (Reviews)
Получение списка всех отзывов

Получить список всех отзывов.
Права доступа: Доступно без токена.
```
GET http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "text": "string",
        "author": "string",
        "score": 1,
        "pub_date": "2019-08-24T14:15:22Z"
      }
    ]
  }
]
```
Добавление нового отзыва

Добавить новый отзыв. Пользователь может оставить только один отзыв на произведение.
Права доступа: Аутентифицированные пользователи.
```
POST http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/
```
Пример запроса:
```
{
  "text": "string",
  "score": 1
}
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Полуение отзыва по id

Получить отзыв по id для указанного произведения.
Права доступа: Доступно без токена.
```
GET http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Частичное обновление отзыва по id

Частично обновить отзыв по id.
Права доступа: Автор отзыва, модератор или администратор.
```
PATCH http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/
```
Пример запроса:
```
{
  "text": "string",
  "score": 1
}
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Удаление отзыва по id

Удалить отзыв по id
Права доступа: Автор отзыва, модератор или администратор.
```
DELETE http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/
```

### Комментарии к отзывам (Comments)
Получение списка всех комментариев к отзыву

Получить список всех комментариев к отзыву по id
Права доступа: Доступно без токена.
```
GET http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "text": "string",
        "author": "string",
        "pub_date": "2019-08-24T14:15:22Z"
      }
    ]
  }
]
```
Добавление комментария к отзыву

Добавить новый комментарий для отзыва.
Права доступа: Аутентифицированные пользователи.
```
POST http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/
```
Пример запроса:
```
{
  "text": "string"
}
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Получение комментария к отзыву

Получить комментарий для отзыва по id.
Права доступа: Доступно без токена.
```
GET http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Частичное обновление комментария к отзыву

Частично обновить комментарий к отзыву по id.
Права доступа: Автор комментария, модератор или администратор.
```
PATCH http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
```
Пример запроса:
```
{
  "text": "string"
}
```
Пример ответа:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Удаление комментария к отзыву

Удалить комментарий к отзыву по id.
Права доступа: Автор комментария, модератор или администратор.
```
DELETE http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/{review_id}/comments/{comment_id}/
```

### Пользователи (Users)
Получение списка всех пользователей

Получить список всех пользователей.
Права доступа: Администратор
```
GET http://127.0.0.1:8000/api/v1/users/
```
Пример ответа:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "username": "string",
        "email": "user@example.com",
        "first_name": "string",
        "last_name": "string",
        "bio": "string",
        "role": "user"
      }
    ]
  }
]
```
Добавление пользователя

Добавить нового пользователя.
Права доступа: Администратор
Поля email и username должны быть уникальными
```
POST http://127.0.0.1:8000/api/v1/users/
```
Пример запроса:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Пример ответа:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Получение пользователя по username

Получить пользователя по username.
Права доступа: Администратор
```
GET http://127.0.0.1:8000/api/v1/users/{username}/
```
Пример запроса:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Изменение данных пользователя по username

Изменить данные пользователя по username.
Права доступа: Администратор.
Поля email и username должны быть уникальными.
```
PATCH http://127.0.0.1:8000/api/v1/users/{username}/
```
Пример запроса:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Пример ответа:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Удаление пользователя по username

Удалить пользователя по username.
Права доступа: Администратор.
```
DELETE http://127.0.0.1:8000/api/v1/users/{username}/
```
Получение данных своей учетной записи

Получить данные своей учетной записи
Права доступа: Любой авторизованный пользователь
```
GET http://127.0.0.1:8000/api/v1/users/me/
```
Пример ответа:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
Изменение данных своей учетной записи

Изменить данные своей учетной записи
Права доступа: Любой авторизованный пользователь
Поля email и username должны быть уникальными.
```
PATCH http://127.0.0.1:8000/api/v1/users/me/
```
Пример запроса:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string"
}
```
Пример ответа:
```
{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```
