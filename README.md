The `nginx-conf/` folder is for the nginx config used by the nginx container in the stack. It is not the outer proxy config.

Example proxy config:
```nginx
server {
  listen 80;
  listen [::]:80;

  server_name example.com;

  location / {
    return 301 https://$host$request_uri;
  }
}

server {
  error_log /var/log/nginx/example.com/error.log;

  listen 443 ssl;
  listen [::]:443 ssl;
  http2 on;

  server_name example.com;

  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header REMOTE_ADDR $remote_addr;
    proxy_set_header X-Forwarded-Proto https;
    proxy_pass http://127.0.0.1:9876;
    client_max_body_size 100M;
  }
}
```
