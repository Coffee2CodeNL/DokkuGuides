# Sentry Configuration Guide

Hello there,  
In this guide I'll explain how to set up Sentry via Dokku!

### Setting up the Image

Let's start out with cloning the on-premise Docker image.  
To do this, run the following commands.
This will create a folder in your home folder, enters the folder, clones the repository we need and then enters the folder of the cloned repository.

```
mkdir ~/sentry-guide && cd ~/sentry-guide && git clone https://github.com/getsentry/onpremise.git && cd onpremise
```

If you want plugins, run this command: `echo "sentry-plugins" >> requirements.txt`
Now it's time to run `make build` to build the Sentry Docker image

### Dokku time!

Now it's time to create the Sentry app in Dokku, run `dokku apps:create sentry` to create it.

Let's add the dependent services!

#### Postgres
Time to create a Postgres service.  
Run the following command to create it!
```
dokku postgres:create sentry-db && dokku postgres:link sentry-db sentry
```
Start with the `DATABASE_URL`, if you're following this guide to the dot it looks like this:
```
postgres://postgres:<password>@dokku-postgres-sentry-db:5432/sentry_db
```
_except for `<password>` of course..._

This called a DSN, and it's structured like this.  
Usually this is used for databases, but redis has an unofficial one too.
```
service://<user>:<password>@<host>:<port>/<database name>
```

- For Redis, run `dokku redis:create sentry-redis && dokku redis:link sentry-redis sentry`

_Take note of the `DATABASE_URL` and the `REDIS_URL`, we'll use them to configure Sentry_


### Sentry Configuration

```
dokku config:set sentry \
SENTRY_POSTGRES_HOST=dokku-postgres-sentry-db \
SENTRY_POSTGRES_PORT=5432 \
SENTRY_DB_USER=postgres \
SENTRY_DB_PASSWORD=<password> \
SENTRY_DB_NAME=sentry_db
```

_Don't forget to replace `<password>` with the actual passwords!_

And of course, we have to generate the secret key for sentry! Run `docker run --rm sentry-onpremise config generate-secret-key`
