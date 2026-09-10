# Pretix standalone Production Environment with Docker Compose

Use Pretix, standalone, with a single Docker Compose, without any need
for any other external service.

## Configuration

Copy the default configuration file:

```
cp ./data/postfix/etc/pretix.cfg.template ./data/postfix/etc/pretix.cfg
```

And fill in the blanks.

## Up

```
docker compose up
```

### Cron

Pretix has a job which should be executed at least every hour. Manual execution:

```
docker compose run --rm pretix cron
```

For automatic execution, just define a crontab entry:

```
sudo crontab -e
0 */10 * * * cd /path/to/project && docker compose run --rm pretix cron > /dev/null
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
