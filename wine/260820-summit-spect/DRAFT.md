
client name = summit
server = [ 
    internal (dc) - api-taitaxes.excise.go.th,
    cloud proxy - winefasttrack.excise.go.th,
    api endpoint - excise-wine-nodejs-api-staging.devthinkbit.com
]

clien can reach only `internal (dc)`

config
1. cloud proxy
```
server {
    ...
    listen 443 ssl;
    server_name winefasttrack-uat.excise.go.th;
    ...

    location /cloud/apiv2-Winesearch_Excise {
        proxy_ssl_server_name on;
        proxy_ssl_name excise-wine-nodejs-api-staging.devthinkbit.com;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host excise-wine-nodejs-api-staging.devthinkbit.com;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_cache_bypass $http_upgrade;
        client_max_body_size 50m;
        client_body_buffer_size 128k;
        proxy_connect_timeout 600;
        proxy_send_timeout 600;
        proxy_read_timeout 600;
        proxy_buffers 32 4k;

        proxy_pass https://excise-wine-nodejs-api-staging.devthinkbit.com/apiv2-Winesearch_Excise;
    }
    ...
}
```

2. internal (dc)
```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name api-taitaxes.excise.go.th;
    ...

    location /cloud/apiv2-Winesearch_Excise {
        proxy_ssl_server_name on;
        proxy_ssl_name excise-wine-nodejs-api.devthinkbit.com;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host excise-wine-nodejs-api.devthinkbit.com;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;

        proxy_pass https://winefasttrack.excise.go.th/cloud/apiv2-Winesearch_Excise;
    }
    ...
}
```

3. api > source 
/Users/macpro/Documents/Repository/excise-wine-api
