# nginx-ChatGPTAPI-proxy
使用nginx代理ChatGPT API接口，从而实现国内直接访问API

## 自行下载nginx

可直接下载，也可用docker

## nginx配置

```nginx
server
{
        listen 80;
         server_name 你的域名或ip;
         charset utf-8;
          # 强制https
        rewrite ^(.*)$ https://${server_name}$1 permanent;

}
server
    {
        listen 443 ssl;
        server_name 你的域名或ip;
         charset utf-8;
        #error_page   404   /404.html;
        #请填写证书文件的相对路径或绝对路径
        ssl_certificate 你的域名证书文件.crt;
        #请填写私钥文件的相对路径或绝对路径
        ssl_certificate_key 你的域名证书文件.key;
        #请按照以下协议配置
        ssl_protocols TLSv1.2 TLSv1.3;

        #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;

        location /v1/ {
            chunked_transfer_encoding off;
            proxy_cache off;
            proxy_buffering off;
            proxy_redirect off;
            proxy_ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            proxy_ssl_server_name on;
            proxy_http_version 1.1;
             proxy_set_header Host api.openai.com;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Real-Port $remote_port;
            proxy_set_header Connection '';
           proxy_pass https://api.openai.com/v1/;

        }


    }

```

