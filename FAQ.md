---
title: FAQ
description: 
published: true
date: 2025-12-27T09:24:40.739Z
tags: 
editor: markdown
dateCreated: 2025-12-05T16:59:15.440Z
---

## Wiki-FAQ

### Почему эта wiki?

На самом деле выбор был небольшой, из-за того что при выборе движка хотелось соблюсти несколько критериев:

  - Запускаться должна на минимальном хостинге по цене плескавицы в месяц
  - Иметь интеграцию с git для хранения архива
  - Иметь встроенную авторизацию + коробочное решение для авторизации с GitHub/Google и прочие OAuth.
  - Хоть сколько-то рабочий поиск
  - WYSIWYG редактор для снижения порога входа
  - Markdown для опытных


### Хочу поднять такую же, но у себя на компе/где-то ещё

В принципе это не сложно, можно почитать документацию: https://docs.requarks.io/install

Но можно скипнуть лонгрид и сделать следующее:

1. Создайте директорию wiki
2. Внутри создайте файл `docker-compose.yml`, со следующим содержимым (стоит заменить пароли конечно):

```yml
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: wiki
      POSTGRES_PASSWORD: wiki_password
      POSTGRES_DB: wiki
    volumes:
      - ./data/pg:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U wiki"]
      interval: 5s
      timeout: 5s
      retries: 5
  wiki:
    image: ghcr.io/requarks/wiki:2
    volumes:
      - ./data/wiki:/wiki/data
    environment:
      DB_TYPE: postgres
      DB_PORT: 5432
      DB_HOST: postgres
      DB_NAME: wiki
      DB_USER: wiki
      DB_PASS: wiki_password
    ports:
      - 3000:3000
    depends_on:
      - postgres
```

3. Запустите через `docker-compose up -d`, откройте в браузере: http://localhost:3000
4. Создайте первого пользователя и залогиньтесь под ним

![scr-20251227-jjvm-2.png](/faq/scr-20251227-jjvm-2.png =250x)

5. Настройте синк (pull only):
	* Идем в Administration => Storage => Git
  * Заполняем поля формочки:
  	* Authentication Type - `basic`
    * Repository URI - `https://github.com/ixpict/diyserbia-wiki.git`
    * branch - `main`
  	* Sync direction - `Pull from target`
  * Сохраняем кнопкой вверху Apply, активируем и в самом низу страницы нажимаем `Import Everything` -> `Run`
6. PROFIT, увы разделы и прочие настройки вам прийдется нарисовать самостоятельно


### Ограничения на размер файлов

Поскольку хостинг стоит как 1 плескавица в месяц - разумным ограничением выставили до 5MB на файл. Если вам требуется больше - можем обсудить

### Есть ли уведомления об изменениях?

Напрямую нет, но если вы умеете пользоваться github - можете подписаться на репозиторий: https://github.com/ixpict/diyserbia-wiki

### Почему редактировать можно только для залогиненных?

Борьба с ботами. Открывать эту вики на паблик

### Как добавить раздел в навигацию?

Напишите админам в чате и/или https://t.me/ixpict. Мы открыты для любых предложений и если вам интересно вести какой-либо раздел - только за.