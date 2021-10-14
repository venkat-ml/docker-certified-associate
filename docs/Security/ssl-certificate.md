

```bash
$ systemctl status docker
$ yum install epel-release
$ sudo yum install certbot python2-certbot-apache
```

```bash
sudo certbot certonly --standalone --preferred-challenges http --non-interactive --staple-ocsp --agree-tos -m venkat_lanka@outlook.com -d servername

sudo su 

cd /etc/letsencrypt/live
cp private.pem domain.key
cat cert.pen chain.pen > domain.crt
chmod 777 domain.*

sudo mkdir -p /mnt/docker-registry
sudo chown cloud_user:cloud_user /mnt/docker-registry
sudo docker run --entrypoint htpasswd registry:latest -Bbb testuser testpass > mnt/docker-registry/passfile

sudo docker run -d -p 443:5000 --restart=always --name registry -v /etc/letsencrypt/live/myservername:/certs:Z -v /mnt/docker-registry:/var/lib/registry:Z -e REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt -e REGISTRY_HTTP_TLS_KEY: /certs/domain.key -e REGISTRY_AUTH: htpasswd
-e "REGISTRY_AUTH_HTPASSWD_PATH: /var/lib/registry/passfile" -e "REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm" registry:latest
```

Test the repos using curl
```bash
curl https://testuser:testpass@myservername:443/v2/_catalog
```

Create private image and put it into repository

```
```

