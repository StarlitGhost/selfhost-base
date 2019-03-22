The base setup for my self-hosted docker services.

 - Traefik for reverse-proxying different subdomains to specific containers
 - a dockersock proxy to shield the docker socket from web-facing containers
 - watchtower to keep containers updated automatically
 - fail2ban to protect the Traefik dashboard from brute-force logins

# Setup

Create a `.env` file in the root dir of the project,
and fill out all the stuff you want to keep secret:

```Shell
PUID=
PGID=
TZ=
PERSISTENT_DIR=
ACME_EMAIL=
DOMAIN_BASE=
DOMAIN_SHORTENER=
DOMAIN_CHAT_STATS=
POSTGRES_USER=
POSTGRES_PW=
```

`PUID` and `PGID` are found using `id $user`.  
`TZ` is your timezone.  
`PERSISTENT_DIR` is the base directory you want all your containers' persistent data to be kept in.  
`ACME_EMAIL` is an email address for letsencrypt cert stuff.  
`DOMAIN_BASE` is the base domain you have pointed at the docker network.  
The rest are project specific; I symlink the same .env for all other subprojects.

Create the file where auto-requested letsencrypt certs will be stored:

`touch traefik/acme.json`

Create traefik.htpasswd to password-protect the traefik dashboard:

`htpasswd -c traefik/traefik.htpasswd <username>`

Now create a docker network for all web-facing containers:

`docker network create web`

And we're done, time to spin everything up!

`docker-compose up -d`

You should now be able to access the traefik dashboard at `traefik.${DOMAIN_BASE}`

## Dependent Projects

Of course, there's not much you can actually do with just this base setup,
but now we can easily spin up the actual services we want to run.  
I clone these as subdirs to this repo checkout, but really they could be anywhere.  
Traefik works via the web network and labels, which lets you be pretty flexible with organisation.

 - [Tiny Tiny RSS](https://github.com/StarlitGhost/selfhost-ttrss) - an RSS feed reader
 - [Jackett](https://github.com/StarlitGhost/selfhost-jackett) - a tracker search and indexing service
 - *more to come*

## What's Next

There's a few more things that would be nice to have as a base:

 - [Authelia](https://github.com/clems4ever/authelia) looks good for two-factor auth
   - [this issue](https://github.com/clems4ever/authelia/issues/88) should hopefully provide enough hints to get it working
   - once this is done, the htpasswd auth on the traefik dashboard can probably be dropped
 - [Portainer](https://github.com/portainer/portainer) for managing everything. I would want Authelia working first
 - Cloudflare maybe?
