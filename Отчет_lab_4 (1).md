## Лабораторная работа №4

Для решения понадобится установить Docker. Можно это делать как в виртуальной машине с установленным Linux, так и через WSL (главное на линукс подобной системе). Я использовал виртуальную машину. Для установки в терминале прописал команду, которая написана в документации Docker (https://docs.docker.com/engine/install/ubuntu/)
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Задание:  
Запустить в контейнере приложение “aafire”. Обратите внимание, что оно бесконечное и контейнер не будет автоматически отключаться.  
Приложить скриншот в процессе работы контейнера.  

Далее в рамках лабораторной работы необходимо самостоятельно настроить сеть между двумя контейнерами, также как в предыдущей работе вы настраивали связь между двумя виртуальными машинами.  

Для проверки сети между контейнерами вам потребуется утилита ping. Поскольку контейнеры очень маленькие и в них нет ничего лишнего (по сравнению с виртуальными машинами) - ping там не установлен. В вашем образе нужно будет установить пакет с этой утилитой, помимо aafire.  

Далее запустите два контейнера с aafire и оставьте их в работающем состоянии.  
Откройте ещё одно окно терминала и создайте сеть при помощи команды 
```
docker network create myNetwork
```
После этого нужно будет подключить контейнеры к вашей сети. Названия контейнеров можно увидеть при выводе списка действующих контейнеров у вас на машине.
```
docker network connect myNetwork mycontainer1
docker network connect myNetwork mycontainer2
```
Теперь при помощи следующей команды вы можете увидеть настройки созданной вами сети.
```
docker network inspect myNetwork
```
Далее вам нужно самостоятельно протестировать соединение между контейнерами утилитой ping и приложить скриншот.

## Выполнение задания

Создан Dockerfile с базовым образом Ubuntu.
В Dockerfile добавлены команды для установки необходимых пакетов:
  - `aafire`: приложение для визуализации.
  - `iputils-ping`: утилита для проверки сетевого соединения.

Содержимое файла `Dockerfile`:
```dockerfile
FROM ubuntu:latest

# Установка необходимых пакетов
RUN apt-get update && \
    apt-get install -y libaa-bin iputils-ping && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Запуск приложения aafire
CMD ["aafire"]
```

`apt-get clean  && rm -rf /var/lib/apt/lists/*` использовались для очистки память, потому что приложение заполняло много места

Сборка контейнера:
```bash
docker build -t aafire_image:latest .
```

Затем запущены 2 терминала:
```bash
docker run -it aafire_image
docker run -it aafire_image
```

![Снимок экрана 2024-12-06 164022](https://github.com/user-attachments/assets/a1f9e169-0242-4230-8183-9e73c48f4ec3)

Для удобства переименовываны контейнеры
```bash
docker rename my_container_name_1 mycontainer1
docker rename my_container_name_2 mycontainer2
```

Открыто ещё одно окно терминала и создана сеть при помощи команды 
```
docker network create myNetwork
```
После этого контейнеры подключены к сети.
```
docker network connect myNetwork mycontainer1
docker network connect myNetwork mycontainer2
```
Теперь при помощи следующей команды можно увидеть настройки созданной вами сети.
```
docker network inspect myNetwork
```










