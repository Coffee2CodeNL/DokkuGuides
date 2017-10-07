# Setting up Sentry

1. Let's start out with cloning [the Sentry repo](https://github.com/getsentry/onpremise)!
2. After this is done `cd` to the `onpremise` folder.
3. If you want plugins, run this command: `echo "sentry-plugins" >> requirements.txt`
4. Run `make build` to build the Docker image
5. Now it's time to create the app in Dokku, run `dokku apps:create sentry`
6. Let's add the dependent services
    - For Postgres, run `dokku postgres:create sentry-db && dokku postgres:link sentry-db sentry`
    - For Redis, run `dokku redis:create sentry-redis && dokku redis:link sentry-redis sentry`
_Take note of the `DATABASE_URL` and the `REDIS_URL`, we'll use them to configure Sentry_

Start with the `DATABASE_URL`, if you're following this guide to the dot it looks like this:
```
postgres://postgres:<password>@dokku-postgres-sentry-db:5432/sentry_db
```
_except for `<password>` of course_

It's called a DSN, and it's structured like this
```
postgres://<user>:<password>@<host>:<port>/<database name>
```

```
dokku config:set sentry \
SENTRY_POSTGRES_HOST=dokku-postgres-sentry-db \
SENTRY_POSTGRES_PORT=5432 \
SENTRY_DB_USER=postgres \
SENTRY_DB_PASSWORD=<password> \
SENTRY_DB_NAME=sentry_db
```

_replace `<password>` with the actual passwords_

And of course, we have to generate the secret key for sentry! Run `docker run --rm sentry-onpremise config generate-secret-key`
