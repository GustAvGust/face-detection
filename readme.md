Максимов Константин 11-901, ИТИС 2022, Cloud Computing

Задание 2. Обработка фотографий с лицами людей

Настройка инфраструктуры через веб-приложение Yandex Cloud

1. Создайте приватный бакет itis-2022-2023-vvot16-photos в сервисе Object Storage.
1. Создайте приватный бакет itis-2022-2023-vvot16-faces в сервисе Object Storage.
2. Создайте сервисный аккаунт с ролью storage.admin и статический ключ для него.
3. Создайте сервисный аккаунт с ролью viewer editor (`vvot-16-sa-tasks`) и статический ключ для него.

4. В сервисе Message Queue создайте очередь vvot16-tasks
- Тип: стандартная

5. В сервисе Cloud Functions создайте публичную облачную функцию vvot16-face-detection (среда выполнения Python), используя код из файла face-detection/index.py.
Далее в редакторе функции:
6. Создайте файл requirements.txt, используя содержимое файла vvot16-face-detection/requirements.txt.
7. Создайте сервисный аккаунт с ролью ai.vision.user и привяжите его к функции в редакторе.
8. Добавьте следующие переменный окружения:
- AWS_ACCESS_KEY_ID = key_id (2)
- AWS_SECRET_ACCESS_KEY = key (2)
- SQS_AWS_ACCESS_KEY_ID = key_id (3)
- SQS_AWS_SECRET_KEY = key (3)
- QUEUE_URL = url for vvot16-tasks


9. Создайте сервисный аккаунт с ролью serverless.functions.invoker (`vvot16-function-invoker`)
10. В сервисе Cloud Functions/Triggers создайте триггер vvot16-photo-trigger.
- Базовые параметры
    - Тип: Object Storage
    - Запускаемый ресурс: Функция
- Настройки Object Storage:
    - Бакет: itis-2022-2023-vvot00-photos
    - Типы событий: Создание объекта
    - Суффикс ключа объекта: jpg
- Настройки функции:
    - Функция: vvot16-face-detection
    - Тег версии функции: $latest
    - Сервисный аккаунт: сервисный аккаунт, созданный на шаге 7.

11. В сервисе Managed service for YDB создайте базу данных `vvot00-db-photo-face`.
    - Тип Serverless
    -
        create table photo_faces(
            id Int64,
            name String,
            photo_key String,
            face_key String,
            user_chat_id Int64,
            PRIMARY KEY (id)
        );


11. Сервисный аккаунт sa-vvot16-face-cutter container-registry.images.puller editor
11. В сервисе Serverless Containers создайте контейнер vvot16-face-cut
  - В сервисе Container Registry создайте реестр
  - Создайте образ на основе vvot16-face-cut/controller.py
  - yc container registry configure-docker
  - docker buildx build --platform linux/amd64 . -t test-python
  - docker tag test-python:latest cr.yandex/crpivr6i50ljcg7u6hm6/test-python:latest
  - docker push cr.yandex/crpivr6i50ljcg7u6hm6/test-python:latest
  - выберите образ в редакторе контейнера
  - Добавьте переменные окружения
    - PHOTO_BUCKET_ID - бакет с оригинальными фотографиями
    - FACES_BUCKET_ID - бакет для фотографий с найденными лицами
    - AWS_ACCESS_KEY_ID = key_id (2)
    - AWS_SECRET_ACCESS_KEY = key (2)
    - DB_ENDPOINT
    - DB_PATH
    - API_GATEWAY - url api gateway

12. В сервисе Cloud Functions/Triggers создайте триггер vvot16-task-trigger.
- Базовые параметры
    - Тип: Message Queue
    - Запускаемый ресурс: Контейнер
- Настройки сообщений Message Queue:
    - Очередь сообщений: vvot16-tasks
    - Сервисный аккаунт: сервисный аккаунт c ролью editor. (sa-vvot16-task-tr)
- Настройки контейнера:
    - Контейнер (созданный ранее)
    - Сервисный аккаунт с ролью serverless.containers.invoker (sa-vvot16-faces-cut-invoker)

13.
- sa (`serverless.containers.invoker editor`)
- Создайте и сконфигурируйте API-шлюз.
    - Используйте конфигурацию из task2/face_detection/vvot16-boot/api_gateway.yml
13. boot
- TELEGRAM_BOT_TOKEN
- DB_ENDPOINT
- DB_PATH

TG_BOT_TOKEN=#{TELEGRAM_BOT_TOKEN}
YCF_URL=#{function_url}
TG_URL="https://api.telegram.org/bot${TG_BOT_TOKEN}/setWebhook?url=${YCF_URL}"
curl -s ${TG_URL}

14. Создайте TG бота с помощью bot father. Запомните токен (TG_BOT_TOKEN)
Далее в консоли (привязываем бота к функции)
```
TG_BOT_TOKEN=#{TELEGRAM_BOT_TOKEN}
YCF_URL=#{function_url}
TG_URL="https://api.telegram.org/bot${TG_BOT_TOKEN}/setWebhook?url=${YCF_URL}"
curl -s ${TG_URL}
```
