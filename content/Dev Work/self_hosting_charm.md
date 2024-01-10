# self_hosting_charm

I want to try and run a selfhosted version of skate.
Skate's backend is run by [charm](git@github.com:charmbracelet/charm.git), so this is a quick guide on how to self host skate.

I want to run it on my ubuntu linux box, running ubuntu 22.04.

So i first simply clone the repo and install:
```bash
git clone git@github.com:charmbracelet/charm.git
cd charm
sudo apt install golang -y
go install
```

Now, according to [this](https://github.com/charmbracelet/charm#self-hosting), simply using
`charm serve` should work.

## Using Docker.

Charm keeps up-to-date Docker images at **charmcli/charm**, so using a docker compose file like this should work:
```yaml
version: "3.1"
services:
  charm:
    image: charmcli/charm:latest
    container_name: charm
    volumes:
      - /path/to/data:/data
    ports:
      - 35353:35353
      - 35354:35354
      - 35355:35355
      - 35356:35356
    restart: unless-stopped
```

We want to mount the data directory, to keep it persistent.

No simply `docker-compose up -d` and charm should be running.

## Configuring the charm server.

Charm is configured using a set of environment variables, as described [here](https://github.com/charmbracelet/charm#server-settings).
These can be passed to docker using a .env file.

```
   │ File: .env
───────┼───────────────────────────────────────────
   1   │ CHARM_SERVER_HOST=charm.endler.tech
   2   │ CHARM_SERVER_PUBLIC_URL=charm.endler.tech
───────┴───────────────────────────────────────────
```

Now pass the .env to docker using:
```bash
docker-compose --env-file .env up
```

### Setting up skate


Follow [this](https://github.com/charmbracelet/skate#installation)

If you've finished setting up your very own Charm Cloud, great.
To make skate connect to your self-hosted instance, you'll need to change the `CHARM_HOST` environment variable to point to the domain/IP of where you hosted your Charm Cloud.

I just put 
`export CHARM_HOST=db.endler.tech` 
into my .bashrc and skate works great :)