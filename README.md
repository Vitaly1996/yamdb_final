### YaMDb API
### Описание
Проект представляет собой API для проекта api-yamdb.
Проект YaMDb собирает отзывы (Review) пользователей на произведения (Titles). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». 
В каждой категории есть произведения: книги, фильмы или музыка. Произведению может быть присвоен жанр (Genre) из списка предустановленных. Новые жанры может создавать только администратор. Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

![example workflow](https://github.com/Vitaly1996/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

### Технологии
- Python 3.7
- Django 2.2
- Django Rest Framework 3.12.14
- Simple-JWT 5.2.0
- Docker 20.10.18


### Особенности
- применены вьюсеты;
- для аутентификации использованы JWT-токены;
- у неаутентифицированных пользователей доступ к API только на чтение;
- аутентифицированным пользователям разрешено изменение и удаление своего контента, в остальных случаях доступ предоставляется только для чтения;
- для загрузки проекта применен Docker, подготовлены файлы для развертывания инфраструктуры;
- настроены Continuous Integration и Continuous Deployment;

### Установка
- склонировать репозиторий
```sh
git clone github.com/Vitaly1996/yatube.git
```
- в директории infra_sp2/infra/ создаем файл .env и записываем в него следующие переменные окружения:
  DB_ENGINE=django.db.backends.postgresql
  DB_NAME=<имя базы данных>
  POSTGRES_USER=<ваш_логин для подключения к базе данных>
  POSTGRES_PASSWORD=<ваш_пароль для подключения к базе данных>
  DB_HOST=<название сервиса(контейнера)>
  DB_PORT=<порт для подключения к базе данных>

- в директории infra_sp2/infra/ выполняем команду для сбора контейнров 
```sh
docker-compose up -d --build
```

- внутри собранных котейнеров создаем и выполняем миграции, создаем суперпользователя и собираем статику
```sh
docker-compose exec web python manage.py makemigrations users
docker-compose exec web python manage.py migrate
docker-compose exec web python manage.py createsuperuser
docker-compose exec web python manage.py collectstatic --no-input
```

- проект доступен по адресу http://<IP вашей машины>/
### Получение персонального токена
Для доступа к API необходимо получить токен: 
- нужно выполнить POST-запрос ```localhost:8000/api/v1/auth/signup/``` передав поля username и email.
API отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email;

- пользователь отправляет POST-запрос с параметрами username и confirmation_code на  
эндпоинт ```/api/v1/auth/token/```,в ответе на запрос ему приходит token (JWT-токен);

- после регистрации и получения токена пользователь может отправить PATCH-запрос 
на эндпоинт ```/api/v1/users/me/``` и заполнить поля в своём профайле.

- затем, отправляя токен с каждым запросом, можно будет обращаться к методам, например: 
```sh
/api/v1/titles/ (GET, POST, PATCH, DELETE)    
/api/v1/genre/ (GET, POST, DELETE)    
/api/v1/categories/ (GET, POST, DELETE)    
```

- при отправке запроса необходимо передать токен в заголовке Authorization: Bearer <токен>.

### Примеры запросов
  - POST   http://89.223.68.3/api/v1/titles/   

    {
      "email": "string",
      "username": "string"
    }

  - POST   http://89.223.68.3/api/v1/genres/
  
    {
      "name": "string",
      "slug": "string"
    }

  - DELETE http://89.223.68.3/api/v1/titles/{title_id}/reviews/{review_id}/ 
  
  - GET    http://89.223.68.3/api/v1/titles/{title_id}/reviews/{review_id}/    
    {
      "id": 0,    
      "text": "string",    
      "author": "string",    
      "score": 1,    
      "pub_date": "2019-08-24T14:15:22Z"
    }     
    
  - POST   http://89.223.68.3/api/v1/titles/{title_id}/reviews/
    
    {
      "text": "string",
      "score": 1
    }
    
Полный список эднпоинтов при развернутом проекте приведен по адресу: .../redoc/

Над проектом работали:[Лев Подъельников](https://github.com/podlev)|[Виталий Сергеев](https://github.com/Vitaly1996)|[Ксения Зверева](https://github.com/Ksenia175)

