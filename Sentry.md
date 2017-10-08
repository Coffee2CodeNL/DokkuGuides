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

#### Plugins
If you want plugins, run this command: `echo "sentry-plugins" >> requirements.txt`

#### Building
Run `make build` to build the Sentry Docker image

#### Getting the Secret Key
Run `docker run --rm sentry-onpremise config generate-secret-key` and copy the key somewhere.

### Dokku time!

Now it's time to create the Sentry app in Dokku, run `dokku apps:create sentry` to create it.  
After this is done we'll add the dependent services.
Take note of the `<password>` parts.

#### Postgres

Time to create a Postgres service.  
Run the following command to create it!
```
dokku postgres:create sentry-db && dokku postgres:link sentry-db sentry
```
If you're following this guide to the dot you should see this:
```
DATABASE_URL: postgres://postgres:<password>@dokku-postgres-sentry-db:5432/sentry_db
```
_except for `<password>` of course, it'll be the actual password..._

#### Redis

And now the Redis service.  
Run the following command to create it.

```
dokku redis:create sentry-redis && dokku redis:link sentry-redis sentry
```
If you're following this guide to the dot you should see this:
```
REDIS_URL: redis://sentry-redis:<password>@dokku-redis-sentry-redis:6379
```



### Base Sentry Configuration
To set up the Database and Redis, copy the text below and run `nano ~/sentry-db-redis-setup.sh`.  
Make sure to edit all the values surrounded by `<` and `>`  
Paste it into nano and save the file, then run `chmod +x ~/sentry-db-redis-setup.sh && ./sentry-db-redis-setup.sh` to run it.

```
dokku config:set sentry \
SENTRY_POSTGRES_HOST=dokku-postgres-sentry-db \
SENTRY_POSTGRES_PORT=5432 \
SENTRY_DB_USER=postgres \
SENTRY_DB_PASSWORD=<database password> \
SENTRY_DB_NAME=sentry_db \
SENTRY_REDIS_HOST=dokku-redis-sentry-redis \
SENTRY_REDIS_PORT=6379 \
SENTRY_REDIS_PASSWORD=<redis password> \
SENTRY_REDIS_DB=0 \
SENTRY_SECRET_KEY="<sentry secret key>"
```
_You copied the secret key earlier, if you lost it, generate a new one._  
_Leave the quotes around the secret key_

After this is done you have the base app setup, apart from some more required configuration.
