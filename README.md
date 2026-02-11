# Лабораторная работа номер 3

## Студент: Кроитор Александр

## Группа: IA2403

## Преподаватель: M. Croitor

## Дата: 07-02-2026

### Предисловие:

Я не могу использовать Docker Desktop, так как я на Linux (NixOS), поэтому я буду использовать docker/podman (podman - not daemon docker containers https://podman.io/)

Чтобы сделать тестовый Dockerfile рабочим с podman, я явно укажу источник image

```dockerfile
FROM docker.io/library/debian:latest
```

Запуск:

```bash
docker build -t containers02 .
```

С помощью команды time вычисляем время запуска контейнера

```bash
time podman build -t containers03 .
STEP 1/3: FROM docker.io/library/debian:latest
Trying to pull docker.io/library/debian:latest...
Getting image source signatures
Copying blob ef235bf1a09a skipped: already exists
Copying config 404d07a111 done   |
Writing manifest to image destination
STEP 2/3: COPY ./site/ /var/www/html/
--> 020599a48d99
STEP 3/3: CMD ["sh", "-c", "echo hello from $HOSTNAME"]
COMMIT containers02
--> 1fc963b3783b
Successfully tagged localhost/containers02:latest
1fc963b3783b7f02e744691354663c58f3fb9ddab850cb337e2f576bc7be7056

________________________________________________________
Executed in    2.55 secs      fish           external
   usr time  521.94 millis    1.00 millis  520.95 millis
   sys time  217.20 millis    1.09 millis  216.11 millis
```

Далее

```bash
 podman run --name containers03 containers03
hello from 2bcb3dced3db
```

При запуске контейнера выводится результат CMD.

Удаляем контейнер:

```bash
podman rm containers03
containers03
```

А после заходим в него через bash:

```bash
podman run -ti --name containers03 containers03 bash
```

-ti в данном случае означает tty (псевдотерминал) + i (interactive), сохраняя CMD открытым.

При запуске видно, что нам доступны базовые команды из sh без дистрибутивных команд:

```bash
root@6ac45fc801ba:/# ls
bash: /usr/bin/ls: No such file or directory
root@6ac45fc801ba:/# rm -rf /
bash: /usr/bin/rm: No such file or directory
root@6ac45fc801ba:/# apt install
bash: apt: command not found
root@6ac45fc801ba:/#
```

Я удалил все файлы внутри контейнера, никак не повредив файлы снаружи - сила контейнеризации:

```bash
root@6ac45fc801ba:/# ls
bash: /usr/bin/ls: No such file or directory
root@6ac45fc801ba:/#
```

В /var/www/html загрузился наш index.html, так как мы скопировали его в эту папку через:

```dockefile
COPY ./site/ /var/www/html/
```

```bash
root@9e5a980b3049:/var/www/html# ls -l
total 4
-rw-r--r-- 1 root root 2292 Feb  7 03:38 index.html
root@9e5a980b3049:/var/www/html# cat index.html
```

```html
<!doctype html>
<html lang="ru">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Сравнение Python и Lua</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        background-color: #f5f5f5;
        color: #333;
        margin: 20px;
      }

      h1 {
        text-align: center;
        color: #2c3e50;
      }

      table {
        width: 80%;
        margin: 20px auto;
        border-collapse: collapse;
        background-color: #fff;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      }

      th,
      td {
        padding: 12px 15px;
        border: 1px solid #ddd;
        text-align: left;
      }

      th {
        background-color: #3498db;
        color: #fff;
      }

      tr:nth-child(even) {
        background-color: #f9f9f9;
      }

      tr:hover {
        background-color: #f1f1f1;
      }
    </style>
  </head>

  <body>
    <h1>Сравнение языков программирования: Python vs Lua</h1>
    <table>
      <thead>
        <tr>
          <th>Критерий</th>
          <th>Python</th>
          <th>Lua</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Типизация</td>
          <td>Динамическая</td>
          <td>Динамическая</td>
        </tr>
        <tr>
          <td>Простота синтаксиса</td>
          <td>Очень простая</td>
          <td>Простая, но чуть более минималистичная</td>
        </tr>
        <tr>
          <td>Скорость выполнения</td>
          <td>Средняя</td>
          <td>Высокая (легковесный интерпретатор)</td>
        </tr>
        <tr>
          <td>Область применения</td>
          <td>Веб, наука, AI, автоматизация</td>
          <td>Игры, встроенные скрипты, расширения</td>
        </tr>
        <tr>
          <td>Поддержка библиотек</td>
          <td>Огромная экосистема (PyPI)</td>
          <td>Меньше, но есть LuaRocks</td>
        </tr>
      </tbody>
    </table>
  </body>
</html>
```

```bash
root@9e5a980b3049:/var/www/html#
```
