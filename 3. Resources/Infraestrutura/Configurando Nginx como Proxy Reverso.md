---
title: Configurando Nginx como Proxy Reverso
---

Para fins de exemplo, configuraremos os subdiretórios `/app` e `/keycloak` para uma aplicação Spring Boot rodando em `localhost:5000/app` e uma instância Keycloak rodando em `localhost:8080/keycloak`, respectivamente, utilizando Nginx. Neste exemplo, estaremos utilizando o domínio `teofilosalgado.io`.

Primeiro, configuraremos as aplicações para receberem requisições no contexto (subdiretório) em questão. 

No caso, para uma aplicação Spring Boot, adicionamos o seguinte conteúdo ao arquivo `application.yaml`:

```yaml
server:
  port: 5000
  servlet:
    contextPath: /app
```

Para o Keycloak, definimos a variável de ambiente
`KC_HOSTNAME` no formato `https://[domínio]/[subdiretório]`. Neste exemplo, utilizaremos o valor `https://teofilosalgado.io/keycloak`.

Depois, criaremos constantes de proxy para o Nginx preenchendo o arquivo `conf.d/proxy.conf` com o seguinte conteúdo:

```nginx
proxy_set_header Host               $host;
proxy_set_header X-Real-IP          $remote_addr;
proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto  $scheme;
proxy_set_header X-Forwarded-Host   $http_host;
proxy_set_header X-Forwarded-Server $host;
proxy_set_header X-Real-IP          $remote_addr;
```

Finalmente, configuraremos o Nginx preenchendo o arquivo `nginx.conf` com a seguinte configuração:

```nginx
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    
    server {
        listen  80 default_server;
     server_name _;

     location / {
         return 301 https://$host$request_uri;
     }
    }
    
    server {
        listen       443 ssl;
        server_name  teofilosalgado.io;

        ssl_certificate      cert.pem;
        ssl_certificate_key  cert.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location /app/ {
            include conf.d/proxy.conf;
            proxy_pass http://localhost:5000/app;
        }
        
        location /keycloak/ {
            include conf.d/proxy.conf;
            proxy_pass http://localhost:8080/keycloak;
        }
    }
}
```
