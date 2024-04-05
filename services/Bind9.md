Bind9 DNS Server

Deployment: [Docker](https://hub.docker.com/r/ubuntu/bind9) / [Systemd service](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-20-04) (official)

У тебя есть:
1. Конфиги `named.conf`, `named.options.conf`
2. Конфигурации зон `*.zone`

Запускать `named.service` с конфигой `named.conf`

DNS сервер должен отвечать на прямой DNS запрос и на запрос с главного маршрутизатора (разрещить все внутренние сети)

```bash
dig @192.168.100.54 test.leha-vnuk.ru
```

Флаг `+trace` для просмотра цепочки DNS запросов