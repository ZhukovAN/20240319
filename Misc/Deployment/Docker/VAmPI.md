``` bash
# Клонируем исходный код микросервиса
git clone https://github.com/erev0s/VAmPI.git
# Редактируем файл VAmPI/openapi_specs/openapi3.yml, удалив из него endpoint /createdb, поскольку нам не нужно обращаться к нему в ходе сканирования посредством BlackBox
...
# Собираем и запускаем образ
docker build --tag ptdemo/vampi:latest .
docker run --detach \
  --publish 8081:5000 \
  --restart=unless-stopped \
  --name vampi \
  --network ptdemo-bridge \
  ptdemo/vampi:latest

```