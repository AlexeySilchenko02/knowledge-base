## 1. Установка файла Python

Переходим на [официальный сайт](https://www.python.org/) и скачиваем последнюю версию Python, нам нужен "[Gzipped source tarball](https://www.python.org/ftp/python/3.13.2/Python-3.13.2.tgz)".

Установка в Debian:
```bash
# Установить программу для скачивания из сети
sudo apt install wget -y

# Установка архива
wget https://www.python.org/ftp/python/3.13.2/Python-3.13.2.tgz

# Распаковка архива
tar -xzvf Python-3.13.2.tgz
```

Следующее мы можем почитать как конфигурировать Python выполнив вот такой запрос: `python build from source site:python.org` 
[Сам сайт с конфигурациями.](http://docs.python.org/3/using/configure.html)

## 2. Установка зависимостей

Выполняем вот такой запрос `python 3.13 build debian bookworm` и ищем вот такой [сайт](https://fostips.com/install-python-3-10-debian-11/).

Установка зависимостей:
```bash
# Обновление пакетов
sudo apt update

# Установка зависимостей
sudo apt install wget build-essential libreadline-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev
```

## 3. Конфигурация Python

`--prefix="$HOME/.python3.13"` - директория куда будет сконфигурирован Python.

```bash
cd ~/Python-3.13.2

./configure --enable-optimizations --prefix="$HOME/.python3.13"
```

Далее смотрим кол-во процессорных ядер:
```bash
lscpu

#CPU(s):16
```

И выполняем сборку, где `16` это кол-во процессорных ядер:
```bash
sudo make -j16 && sudo make altinstall
```

Добавляем директорию в `$PATH`:
```bash
echo "export PATH=\$PATH:$HOME/.python3.13/bin" >> ~/.zshrc
```