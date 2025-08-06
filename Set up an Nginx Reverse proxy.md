Install nginx web server
```bash
sudo apt install nginx
```

Access the Raspberry Pi's domain name or IP. It should return something about nginx being ready.

Create an nginx virtual host configuration file
```bash
nano /etc/nginx/conf.d/app.conf
```

Add the following configuration:

```conf
server {
    listen 80;
    server_name <local-domain.local>;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Save and close the file, then edit the main Nginx configuration

```bash
nano /etc/nginx/nginx.conf
```

Add the following lines below the `http{` line:

```
server_names_hash_bucket_size 64;
client_max_body_size 20M;
```

Save the file, then run the nginx's config file validator to ensure that there are no syntax errors:

```bash
sudo nginx -c
```

If there are no syntax errors, restart the nginx service to apply the changes

```
systemctl restart nginx
```

Verify if the setup is working by accessing the ip/domain name with the default port (80).