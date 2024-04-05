
Deployment: [Docker](https://hub.docker.com/_/nginx) / Service не важно

Установлено должно быть на каждую виртуалку где есть какие-то сервисы

[Русские сертификаты](https://www.sberbank.com/ru/certificates)

Сертификаты положить в `/etc/nginx/ssl`
Конфиги положить в `/etc/nginx/conf.d`
Тест конфигураций nginx: `nginx -t`
Перезапуск nginx: `nginx -s reload`
Чекнуть статус: `service nginx status`

Нужно:
- [Достать какой-нибудь доверенный российский сертификат](https://interface31.ru/tech_it/2022/10/ustanovka-rossiyskih-kornevyh-sertifikatov-v-debian-i-ubuntu.html) (который есть в яндекс браузере)
- От него подписать wildcard сертификат на внутренние сервисы на `domain.ru` и `*.domain.ru`
- Подложить этот корневой сертификат на все виртуальные машины
- Если сертификат минцифры или любой другой уже есть в яндекс браузере, на компы пользователей его раскладывать не надо

Пример конфиги
```conf
upstream idp {
  server localhost:8484;
}
server {
  server_name idp.djidjya.ru;
  location / {
    proxy_pass https://idp;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_buffers 4 16k;
  }
  
  listen 443 ssl; 
  ssl_certificate /infra/certificates/djidjya.ru/fullchain.pem; 
  ssl_certificate_key /infra/certificates/djidjya.ru/privkey.pem; 
  include /infra/certificates/options-ssl-nginx.conf; 
  ssl_dhparam /infra/certificates/ssl-dhparams.pem; 
}
server {
  if ($host = idp.djidjya.ru) {
    return 301 https://$host$request_uri;
  }
  server_name idp.djidjya.ru;
  listen 80;
  return 404;
}
```