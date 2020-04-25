# Troddy2Ray

An integrated solution for Trojan + V2Ray + Caddy2 server-side environment.

## Goal

By setting up this solution, your sever turns into a proxy server providing **BOTH** V2Ray **AND** Trojan services.

The V2Ray server is default configured as *VMess + TLS + WebSocket*, with the WebSocket path `/v`.

## Prerequisites

- This project depends on *Docker* and *Docker Compose* for fast deployment. You'll have to install them before deployment.

- The ports of `443` and `80` must **NOT** be occupied on your machine.

- Install Docker:

``` shell
$ curl -fsSL https://get.docker.com -o get-docker.sh

$ sh get-docker.sh
```

- Install Docker Compose:

``` shell
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

$ sudo chmod +x /usr/local/bin/docker-compose
```

## How To Deploy

Clone this repo on your server, say, to the directory of `/trojan`:

``` shell
$ git clone https://github.com/0x1ooo/troddy2ray /trojan
```

Change directory to `/trojan` and edit the following config files:

- In `caddy/caddy.json`:
    - In line `15`, change `your_domain_name` to a domain that points to your server:
        ``` json
        "host": [
            "your_domain"
        ]
        ```
    - In line `61`, change `email` as your preferr for certificate application:
        ``` json
        "email": "your@email.com",
        ```
- In `trojan/config/config.json`, set the `password` field as well as replacing **ALL** `your_domain_name` to the domain you set in `caddy.json` (line `7` to `13`):
    ``` json
    "password": [
        "your_password"
    ],
    "ssl": {
        "cert": "/ssl/your_domain_name/your_domain_name.crt",
        "key": "/ssl/your_domain_name/your_domain_name.key"
    },
    ```
- In `v2configs/002-ladder.json`, set the V2Ray inbound clients as [described in V2Ray's documentation](https://www.v2ray.com/chapter_02/protocols/vmess.html#clientobject):
    ``` json
    "clients": [
        {
            "id": "e86a5c4e-b0e8-4087-9940-0c3b2614970f",
            "level": 0,
            "alterId": 64,
            "email": "your@email.com"
        }
    ]
    ```

> Feel free to modify Caddy, Trojan and V2Ray config files for your needs if you are familiar with them.

- Give `svc` the execute permission and start up the services. `svc` is just a wrapper of `docker-compose` with some extra features such as `reload`.

``` shell
$ chmod +x svc

$ ./svc up -d
```

- You can use this command to view the runtime logs:

``` shell
$ ./svc logs -f
```

## Architecture

The solution consists of 3 services:

- Trojan (container `trojan`) is like a gateway in the forefront that listens on the `443` port. It takes legal Trojan data for further processing, while forwards others to Caddy on its `80` port.

- Caddy2 (container `caddy`) has two major roles: a SSL/TLS certificate manger and a webserver. The config file provided in this project won't redirect HTTP to HTTPS, so anything goes in Caddy will either be responded by a 'Hello, World` static web page, or be reverse proxied to the V2Ray service.

- V2Ray (container `v2`) takes care of WebSocket connections and do what it ought to do 🙂️

## Dependencies

Beside of *Docker* and *Docker Compose* for a runtime environment, this solution utilizes [Trojan-Go](https://github.com/p4gefau1t/trojan-go) as the Trojan server.

## Acknowledgement

This project is inspired by [FaithPatrick's Trojan-Caddy docker compose](FaithPatrick/trojan-caddy-docker-compose).

