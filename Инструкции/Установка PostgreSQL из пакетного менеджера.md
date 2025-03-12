Инструкция на этом [сайте](https://www.postgresql.org/download/linux/debian/)
## Фикс ошибок
Создаем БД
```sql
CREATE DATABASE rroom_db
    WITH
    ENCODING='UTF8'
    LC_COLLATE='ru_RU.UTF-8'
    LC_CTYPE='ru_RU.UTF-8'
    owner alexey;
```
Если возникла ошибка `ERROR:  new collation (ru_RU.UTF-8) is incompatible`, то делаем вот так:
```sql
CREATE DATABASE rroom_db
WITH
    ENCODING = 'UTF8'
    LC_COLLATE = 'ru_RU.UTF-8'
    LC_CTYPE = 'ru_RU.UTF-8'
    TEMPLATE = template0
    OWNER = alexey;
```

Как установить локалию вручную:
	Еще если проблемы с ru_RU.UTF-8 и не помогает `TEMPLATE=template0`, можно попробовать в ручную поставить ru_RU.UTF-8 командой `sudo dpkg-reconfigure locales`, там надо будет из списка выбрать и пробел нажать, тогда с `TEMPLATE=template0` заработает.  
	А проверить что ru_RU.UTF-8 установилось успешно можно вот так `locale -a | grep ru_RU`.

После этого:
```bash
sudo update-locale
sudo systemctl restart postgresql
```

### Гайд на установку на Windows psql

Если кому-то нужен более подробный гайд по установка psql в Windows: [https://www.postgresqltutorial.com/postgresql-getting-started/install-postgresql/](https://www.postgresqltutorial.com/postgresql-getting-started/install-postgresql/)  
Только при установке нужно оставить галочки только у pgAdmin4 и Comand Line Tools(в целом можно как в гайде, просто PostgreSQL Server не нужен в Windows, он будет в WSL запущен).  
Далее добавляем в PATH новую строку: `C:\Program Files\PostgreSQL\16\bin` и в терминале выполняем следующую команду `psql -h localhost -U rroom -d rroom_db`. И все)  
У меня еще была проблема, что после установки PostgreSQL в WSL, он не запустился самостоятельно, необходимо выполнить команду `sudo service postgresql start`.