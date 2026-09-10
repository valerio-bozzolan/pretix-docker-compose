# Pretix standalone Production Environment with Docker Compose

A Docker Compose to use Pretix, standalone, together with
PostgreSQL and Redis.

This Docker Compose assumes you already have a frontend webserver taking care
of the SSL certificates (for example, Apache or nginx, already running on port 80 and 443).

## Installation

```
git clone https://github.com/valerio-bozzolan/pretix-docker-compose.git
```

## Configuration

First, copy the default Docker environment example file:

```
cp .env.example .env
```

Edit the resulting Docker env file (`.env`) and generate a new strong password for `POSTGRES_PASSWORD=...`.

Then, copy the default Pretix configuration file:

```
cp ./data/pretix/etc/pretix.cfg.template ./data/pretix/etc/pretix.cfg
```

Edit the resulting Pretix confuguration file (`./data/pretix/etc/pretix.cfg`) and:

- Set the same password in `password=...`
- Set the expected URL under `url=`, like `url=https://pretix.example.com`
- Set outgoing email credentials

## Up

Startup Pretix:

```
docker compose up
```

The web application will be available here:

http://localhost:8000

To change this port, edit the file `.env` and personalize the option `PRETIX_LISTEN`.

## Cron

Pretix has a job which should be executed at least every hour. Manual execution:

```
docker compose run --rm pretix cron
```

For automatic execution, just define a crontab entry:

```
sudo crontab -e
0 */10 * * * cd /path/to/pretix-docker-compose && docker compose run --rm pretix cron > /dev/null
```

## Frontend webserver

Pretix is designed to work plaintext, in HTTP. To serve HTTPs you need a frontend webserver, like Apache HTTPd or nginx.

The DNS record `pretix.example.com` should indeed point to that frontend webserver.

Assuming you already have an Apache HTTPd webserver running, the goal is to create a new virtualhost, serving https, to proxy all requests to the running Pretix.

Assuming you want Apache in Debian, to install it:

```
sudo apt update
sudo apt install apache2
```

Then save this configuration file as `/etc/apache2/sites-available/pretix.conf`, replacing `pretix.example.com` with your domain:

```
<VirtualHost *:443>
        ServerName pretix.example.com

        DocumentRoot /var/www/html

        ProxyPreserveHost on

        RemoteIPHeader X-Forwarded-For

        SSLProxyEngine on
        <Location />
                ProxyPass        http://localhost:8000/
                ProxyPassReverse http://localhost:8000/
        </Location>

        #
        # Let's Encrypt certificates
        #
        # Issued with:
        #    certbot certonly --webroot --webroot-path=/var/www/html -d pretix.example.com
        #
        <IfFile /etc/letsencrypt/live/pretix.example.com/cert.pem>
                SSLEngine on
                SSLCertificateFile      /etc/letsencrypt/live/pretix.example.com/cert.pem
                SSLCertificateKeyFile   /etc/letsencrypt/live/pretix.example.com/privkey.pem
                SSLCertificateChainFile /etc/letsencrypt/live/pretix.example.com/chain.pem
        </IfFile>
</VirtualHost>
```

Then:

```
sudo a2ensite pretix
systemctl restart apache2
certbot certonly --webroot --webroot-path=/var/www/html -d pretix.example.com
systemctl restart apache2
```

At this point, your frontend webserver should be available, with HTTPs, acting as a proxy for Pretix:

https://pretix.example.com/

At this point, be sure that the Pretix plaintext port is not exposed.

## Pretix Notes:

* https://docs.pretix.eu/en/latest/admin/installation/docker_smallscale.html#next-steps
* https://docs.pretix.eu/en/latest/development/setup.html

## Pretix Dashboard

* Dashboard: `http://localhost:8000/control/`
* User: `admin@localhost`
* Password: `admin`

1. Create an organizer
2. Create an event
3. Start presales

## License

The original repository has been originally created in 2019 by the kind Cody Redmond.

https://github.com/codydjango/pretix-docker-compose

As the repository was lacking a "LICENSE" file or similar indications,
and as such LICENSE was not clarified for more than 5 years after an user
requested such details ( https://github.com/codydjango/pretix-docker-compose/issues/1 ),
and as the repository consisted in system configuration files, without creative multimedia files,
without creative contents, or anyway without material with a reasonable threshold of originality;
therefore the original repository is in good faith considered as a work which cannot be
protected by copyright, and therefore we assume it's available under the Creative
Commons Public Domain Mark, as this work has been identified as being free of known
restrictions under copyright law.

https://creativecommons.org/publicdomain/mark/1.0/

As the project has been abandoned since May 2019, it was then forked in September 2026
by Valerio Bozzolan and contributors.

https://github.com/valerio-bozzolan/pretix-docker-compose

Pretix itself is available under the Free Software license GNU Affero General Public License v3.

https://pretix.eu/

https://github.com/pretix/pretix

PostgreSQL is Free Software available under the PostgreSQL License.

https://www.postgresql.org/

https://www.postgresql.org/about/licence/

Redis is a Free Software available under the GNU Affero General Public License v3+.

https://redis.io/

https://redis.io/legal/licenses/

P.S. The copyright holders of Redis are kindly invited in **not** changing the license of Redis yet again,
to become yet again a fake-open-source / "open bla bla" / proprietary software, tomorrow morning.
To be pretty much clear: if Redis will become a commercial monopoly again ("non commercial"),
I will just invest all my time resources to wipe Redis on sight from whatsoever Free/Libre and Open Source
organization I'm active in. Hugs.

-Valerio Bozzolan, 10 September 2026
