## Создание пользователя

Создаем пользователя:
```bash
sudo adduser имя_пользователя
```

Добавляем его в sudoers:
```bash
sudo usermod -aG sudo имя_пользователя
```

## Настройка сервера

Обновление пакетов:
```bash
sudo apt -y update && sudo apt -y upgrade
```

Установка python:
```bash
sudo apt install python3 python3-pip -y
```

Установка виртуального окружения:
```bash
sudo apt install python3-venv -y
```

## Деплой

Создание виртуального окружения:
```bash
python3 -m venv venv
```

Активация окружения:
```bash
source venv/bin/activate
```

Устанавливаем gunicorn:
```python
pip install gunicorn
```

Создаем службу для автоматического запуска gunicorn:
```bash
sudo micro /etc/systemd/system/gunicorn.service
```

Содержимое(текущее):
```bash
[Unit]
Description=Gunicorn instance to serve Flask app
After=network.target

[Service]
User=worker
Group=www-data
WorkingDirectory=/home/worker/data_statistics
Environment="PATH=/home/worker/data_statistics/venv/bin"
ExecStart=/home/worker/data_statistics/venv/bin/gunicorn --workers 3 --bind unix:app.sock -m 007 app:app
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Пример с GPT:
```bash
[Unit]
Description=Gunicorn instance to serve Flask app
After=network.target

[Service]
User=worker
Group=www-data
WorkingDirectory=/home/worker/data_statistics
ExecStart=/home/worker/data_statistics/venv/bin/gunicorn --workers 3 --bind unix:/home/worker/data_statistics/app.sock app:app

[Install]
WantedBy=multi-user.target
```

Запуск сервиса:
```bash
sudo systemctl daemon-reload
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

### Установка nginx
```bash
sudo apt install nginx
```

Создаем конфигурацию:
```bash
sudo micro /etc/nginx/sites-available/data_statistics
```

Текущая конфигурация:
```bash
server {
    listen 80;
    server_name mining-stat.ru www.mining-stat.ru;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/worker/data_statistics/app.sock;
    }
}
```

Предложение GPT:
```bash
server {
    listen 80;
    server_name mining-stat.ru www.mining-stat.ru;

    location / {
        proxy_pass http://unix:/home/worker/data_statistics/app.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static/ {
        alias /home/worker/data_statistics/static/;
    }

    location /favicon.ico {
        alias /home/worker/data_statistics/static/favicon.ico;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

Создаем ссылку ln:
```bash
sudo ln -s /etc/nginx/sites-available/data_statistics /etc/nginx/sites-enabled
```

Проверяем конфигурация nginx:
```bash
sudo nginx -t
```

Перезапускаем nginx:
```bash
sudo systemctl restart nginx
```

Фикс возникшей проблемы:
```bash
sudo chmod o+x /home/worker
```

### Запуск бота:
```bash
sudo micro /etc/systemd/system/telegram_bot.service
```

Содержимое:
```bash
[Unit]
Description=Telegram Bot
After=network.target

[Service]
User=worker
WorkingDirectory=/home/worker/data_statistics
Environment="PATH=/home/worker/data_statistics/venv/bin"
ExecStart=/home/worker/data_statistics/venv/bin/python bot.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Запуск сервиса:
```bash
sudo systemctl daemon-reload

sudo systemctl start telegram_bot.service 

sudo systemctl enable telegram_bot.service
```

## Очистка логов

Посмотреть, что занимает больше всего памяти из логов:
```bash
du -h /var/log | sort -rh | head -10
```

Удаление логов nginx:
```bash
sudo rm -f /var/log/nginx/*.log
```

Удаление логов gunicorn
```bash
sudo rm -f /var/log/gunicorn/*.log
```

Также можно установить ограничение для системных логов:
```bash
sudo journalctl --vacuum-size=50M
```

```bash
sudo journalctl --vacuum-time=7d
```

Перезагрузка службы `systemd-journald` для применения изменений:
```bash
sudo systemctl restart systemd-journald
```

Еще полезные команды(для очищения места):
```shell
sudo apt clean
```

## SSL-сертификат
[Ютуб](https://www.youtube.com/watch?v=1wnOw1vwPEo)