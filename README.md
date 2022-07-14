![Yamdb Workflow Status](https://github.com/genpoplevin/yamdb_final/actions/workflows/yamdb_workflow/badge.svg)

# yamdb_final

REST API для сервиса отзывов YaMDb с возможностью развернуть данный проект на удалённом сервере в Docker-контейнере.

### Описание

Сервис отзывов YaMDb. Благодаря этому сервису можно оставлять отзывы на произведения в различных категориях (например -книги, фильмы, музыка). Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и оценивают произведение по шкале от 1 до 10. Исходя из среднего значения оценое формируется рейтинг произведения. На одно произведение уникальный пользователь может оставить только один отзыв.


### Установка

Сделайте Fork репозитория https://github.com/genpoplevin/yamdb_final

Клонируйте репозиторий и перейдите в него: 
``` 
git clone https://github.com/genpoplevin/yamdb_final.git
cd yamdb_final 
cd api_yamdb 
``` 
![image](https://user-images.githubusercontent.com/93812078/178964359-757ff9cc-f6fe-4289-abc3-88f1acbb5718.png)

 
Создаем и активируем виртуальное окружение: 
```
python -m venv venv 
source /venv/Scripts/activate
python -m pip install --upgrade pip 
``` 
 
Ставим зависимости из requirements.txt: 
``` 
pip install -r requirements.txt 
``` 

Войдите на свой удаленный сервер в облаке

Остановите службу nginx
```
 sudo systemctl stop nginx
```

Установите docker
```
sudo apt install docker.io
```

Переходим в папку с файлом docker-compose.yaml: 
``` 
cd infra 
``` 
 
Поднимаем контейнеры (infra_db_1, infra_web_1, infra_nginx_1): 
```bash 
docker-compose up -d --build 
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
