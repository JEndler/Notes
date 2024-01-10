# Setting up Obsidian Livesync

Obsidian Livesync is a self-hostable plugin to sync multiple obsidian vaults on different machines.

This is a small walkthrough of the steps to host this tool :)

For reference, im using a Server hosted by hetzner running Ubuntu 22.04, with Docker 20.10.12

First, since obsidian-livesync uses CouchDB as a backend, i followed the steps outlined here:

https://docs.couchdb.org/en/stable/install/unix.html#installation-using-the-apache-couchdb-convenience-binary-packages

So i simply have to run this to install the package:

```bash
sudo apt update && sudo apt install -y curl apt-transport-https gnupg
curl https://couchdb.apache.org/repo/keys.asc | gpg --dearmor | sudo tee /usr/share/keyrings/couchdb-archive-keyring.gpg >/dev/null 2>&1
source /etc/os-release
echo "deb [signed-by=/usr/share/keyrings/couchdb-archive-keyring.gpg] https://apache.jfrog.io/artifactory/couchdb-deb/ ${VERSION_CODENAME} main" \
    | sudo tee /etc/apt/sources.list.d/couchdb.list >/dev/null

sudo apt update
sudo apt install -y couchdb
```

This will also automatically lead you through the database creation, including creating an admin user + password.

Alternatively, one could also use a docker-image to run couchdb, like this:

```bash
$ docker run --rm -it -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=password -v /home/jakob/couchdb/local.ini:/opt/couchdb/etc/local.ini -p 5984:5984 couchdb
```

The local.ini file for obsidian-livesync should look like this:

```
[couchdb]
single_node=true

[chttpd]
require_valid_user = true

[chttpd_auth]
require_valid_user = true
authentication_redirect = /_utils/session.html

[httpd]
WWW-Authenticate = Basic realm="couchdb"
enable_cors = true

[cors]
origins = app://obsidian.md,capacitor://localhost,http://localhost
credentials = true
headers = accept, authorization, content-type, origin, referer
methods = GET, PUT, POST, HEAD, DELETE
max_age = 3600
```

Now that CouchDB is running, setting up the livesync-plugin should work :)

However, if you have a firewall in place on your server, you should open the port couchdb is running on.

In my case, this is done like this:

```bash
sudo ufw allow 5984
```

## Setting up nginx

If you run a reverse proxy like nginx on your server, you’ll want to configure nginx for a subdomain that serves your couchdb instance.

I chose *db.endler.tech* for this.

> You’ll have to setup your dns registry to work for that. Just add an A record with the desired subdomain to point to your server.

Here’s the config i used:

```nginx
server {
    # listen on port 80 (http)
    listen 80;
    server_name db.endler.tech;

    location / {
	return 301 https://$host$request_uri;
    }	
}
server {
    listen 443 ssl;
    server_name db.endler.tech;

    # location of the SSL certificate
    ssl_certificate /etc/letsencrypt/live/db.endler.tech/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/db.endler.tech/privkey.pem;


    # write access and error logs to /var/log
    access_log /var/log/couchdb_access.log;
    error_log /var/log/couchdb_error.log;

    location / {
        # forward application requests to the gunicorn server
        proxy_pass http://localhost:5984;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

You will also need a valid ssl certificate, this could be done via *Certbot*, like this:
(You likely need to disable nginx while doing this.)

```bash
sudo certbot certonly --standalone --preferred-challenges http -d db.endler.tech
```

### Setting up the Plugin.

Now, simply follow the steps outlined here:

https://github.com/vrtmrz/obsidian-livesync/blob/main/docs/quick_setup.md

