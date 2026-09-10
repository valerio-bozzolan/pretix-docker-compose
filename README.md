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

### Cron

Pretix has a job which should be executed at least every hour. Manual execution:

```
docker compose run --rm pretix cron
```

For automatic execution, just define a crontab entry:

```
sudo crontab -e
0 */10 * * * cd /path/to/pretix-docker-compose && docker compose run --rm pretix cron > /dev/null
```

### Pretix Notes:

* https://docs.pretix.eu/en/latest/admin/installation/docker_smallscale.html#next-steps
* https://docs.pretix.eu/en/latest/development/setup.html

### Pretix Dashboard

* Dashboard: `http://localhost:8000/control/`
* User: `admin@localhost`
* Password: `admin`

1. Create an organizer
2. Create an event
3. Start presales

### License

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
