## Fork specific information (based on PR by @charlawl)

The below is based on the Heroku tutorial which can be found [here](https://devcenter.heroku.com/articles/container-registry-and-runtime#cli)
To deploy this image to Heroku the following steps should be followed:

1. Build the image: `docker buildx build --platform linux/amd64 -t <some-tag | image name> .` (This ensures that the image is built on the correct architecture. Heroku doesn't like images built to the M1 Mac specificaiton)
2. Tag the image: `docker tag <some-tag | image name> registry.heroku.com/x-supertokens/web`
3. Push the image to the Heroku registry: `docker push registry.heroku.com/x-supertokens/web`
4. Login to the Heroku cli (download it if you don't already have it)
5. Release the image: `heroku container:release web -a x-supertokens-staging`

## Quickstart

```bash
# This will start with an in memory database.

$ docker run -p 3567:3567 -d registry.supertokens.io/supertokens/supertokens-postgresql
```

## Configuration

You can use your own `config.yaml` file as a shared volume or pass the key-values as environment variables.

If you do both, only the shared `config.yaml` file will be considered.

#### Using environment variables

Available environment variables

-   **Core**
    -   API_KEYS
    -   SUPERTOKENS_HOST
    -   SUPERTOKENS_PORT
    -   ACCESS_TOKEN_VALIDITY
    -   ACCESS_TOKEN_BLACKLISTING
    -   ACCESS_TOKEN_SIGNING_KEY_DYNAMIC
    -   ACCESS_TOKEN_SIGNING_KEY_UPDATE_INTERVAL
    -   REFRESH_TOKEN_VALIDITY
    -   PASSWORD_RESET_TOKEN_LIFETIME
    -   EMAIL_VERIFICATION_TOKEN_LIFETIME
    -   INFO_LOG_PATH
    -   ERROR_LOG_PATH
        -   MAX_SERVER_POOL_SIZE
    -   PASSWORDLESS_MAX_CODE_INPUT_ATTEMPTS
    -   PASSWORDLESS_CODE_LIFETIME
    -   DISABLE_TELEMETRY
    -   BASE_PATH
    -   PASSWORD_HASHING_ALG
    -   ARGON2_ITERATIONS
    -   ARGON2_MEMORY_KB
    -   ARGON2_PARALLELISM
    -   ARGON2_HASHING_POOL_SIZE
    -   BCRYPT_LOG_ROUNDS
    -   LOG_LEVEL
    -   FIREBASE_PASSWORD_HASHING_POOL_SIZE
    -   FIREBASE_PASSWORD_HASHING_SIGNER_KEY
    -   IP_ALLOW_REGEX
    -   IP_DENY_REGEX
    -   TOTP_MAX_ATTEMPTS
    -   TOTP_RATE_LIMIT_COOLDOWN_SEC
-   **POSTGRESQL:**
    -   POSTGRESQL_CONNECTION_URI
    -   POSTGRESQL_USER
    -   POSTGRESQL_PASSWORD
    -   POSTGRESQL_PASSWORD_FILE
    -   POSTGRESQL_CONNECTION_POOL_SIZE
    -   POSTGRESQL_HOST
    -   POSTGRESQL_PORT
    -   POSTGRESQL_DATABASE_NAME
    -   POSTGRESQL_TABLE_NAMES_PREFIX
    -   POSTGRESQL_TABLE_SCHEMA

```bash
docker run \
	-p 3567:3567 \
	-e POSTGRESQL_CONNECTION_URI="postgresql://username:password@host:port/dbName" \
	-d registry.supertokens.io/supertokens/supertokens-postgresql

# OR

docker run \
	-p 3567:3567 \
	-e POSTGRESQL_USER="postgresqlUser" \
	-e POSTGRESQL_HOST="192.168.1.2" \
	-e POSTGRESQL_PORT="5432" \
	-e POSTGRESQL_PASSWORD="password" \
	-d registry.supertokens.io/supertokens/supertokens-postgresql
```

#### Using custom config file

-   In your `config.yaml` file, please make sure you store the following key / values:
    -   `core_config_version: 0`
    -   `host: "0.0.0.0"`
    -   `postgresql_config_version: 0`
    -   `info_log_path: null` (to log in docker logs)
    -   `error_log_path: null` (to log in docker logs)
-   The path for the `config.yaml` file in the container is `/usr/lib/supertokens/config.yaml`

```bash
docker run \
	-p 3567:3567 \
	-v /path/to/config.yaml:/usr/lib/supertokens/config.yaml \
	-d registry.supertokens.io/supertokens/supertokens-postgresql
```

## Logging

-   By default, all the logs will be available via the `docker logs <container-name>` command.
-   You can setup logging to a shared volume by:
    -   Setting the `info_log_path` and `error_log_path` variables in your `config.yaml` file (or passing the values asn env variables).
    -   Mounting the shared volume for the logging directory.

```bash
docker run \
	-p 3567:3567 \
	-v /path/to/logsFolder:/home/logsFolder \
	-e INFO_LOG_PATH="/home/logsFolder/info.log" \
	-e ERROR_LOG_PATH="/home/logsFolder/error.log" \
	-e POSTGRESQL_USER="postgresqlUser" \
	-e POSTGRESQL_PASSWORD="password" \
	-d registry.supertokens.io/supertokens/supertokens-postgresql
```

## Database setup

-   Before you start this container, make sure to initialize your database.
-   You do not need to ensure that the Postgresql database has started before this container is started. During bootup, SuperTokens will wait for ~1 hour for a Postgresql instance to be available.
-   If `POSTGRESQL_USER`, `POSTGRESQL_PASSWORD`, `POSTGRESQL_PASSWORD_FILE` and `POSTGRESQL_CONNECTION_URI` are not provided, then SuperTokens will use an in memory database.
