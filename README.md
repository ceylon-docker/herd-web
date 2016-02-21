# Web server for Ceylon Herd

The Web server is based on [Play 1.2.7](https://www.playframework.com) running on [Java 7](/_/java/) and needs a [PostgreSQL](http://www.postgresql.org/) server to store its data. The easiest way is to run the [ceylon-herd-dbase](../ceylon-herd-dbase) image especially created for this purpose but if you want [you can set up your own](https://github.com/ceylon/ceylon-herd).

The following images/tags are available:

 - `latest` ([herd-web/Dockerfile](https://github.com/ceylon-docker/herd-web/blob/master/Dockerfile))

To run the build perform the following steps:

 1. `docker pull ceylon/ceylon-herd-web:latest`
 2. `docker run -p 9000:9000 -d --link herddb:postgres ceylon/ceylon-herd-web:latest`

By default this will get the very latest `master` version of The Herd from GitHub and run it. If you want to run a specific version, eg. 1.22, you can specify it like this:

 - `docker run -e HERD_VERSION 1.22 -p 9000:9000 -d --link herddb:postgres ceylon/ceylon-herd-web:latest`

NB: The Herd web server image expects the database server to have the host name `postgres`. This is the reason that `postgres` appears as the second element of the `--link` argument seen above.
NB2: The `-p 9000:9000` option exposes the web server's default port locally on 9000 as well. This is not required but will make testing and following documentation easier because they expect the server to run locally.
NB3: In this example we're pulling and running the image that is tagged `:latest`. You can replace it with any other tag or you can leave it out if you want (it will default to `:latest` automatically).

### Creating an admin user

**Important**: if you are starting with a fresh (empty) database the Herd web server will create the database when a user accesses the site for the first time. But no admin user will be available to do any maintenance. So before you can do anything you must create an administrator account. The process [is explained here](https://github.com/ceylon/ceylon-herd).

But if you are using the provided [ceylon-herd-dbase](../ceylon-herd-dbase) image there's a somewhat easier way:

 1. First make sure you have connected at least once to the Herd web site: http://localhost:9000 if you are running the server locally
 2. Then run the following command that will be used to create a new user: `docker exec -t herddb /bin/bash -c ./add-herd-user.sh`

It will respond with: `Usage: add-herd-user <UserName> <FirstName> <LastName> <EMail> <BCryptHashedPassword> [<IsAdmin:true/false>] [<DBHost>] [<DBPort>`

Just run the command again with the requested values (you can [generate a BCrypted password online](http://bcrypthashgenerator.apphb.com/)), eg: `docker exec -it herddb ./add-herd-user.sh jsmall Joe Small jsmall@example.com "2345672547625673" true`. After that you can login with `jsmall` and administer the server.

If you a running a non-public test server there's an even quicker way:

 - `docker exec -t herddb ./add-herd-admin.sh`

Will create a user named `admin` and password `admin`.

### Quick setup web + dbase

For super quick setup install [docker-compose](https://docs.docker.com/compose/install/), get the [docker-compose.yml](https://raw.githubusercontent.com/ceylon-docker/herd-web/master/docker-compose.yml) from this repository and in the same directory run:

 - `docker-compose up -d`

The web server will be accessible at [http://localhost:9000](http://localhost:9000)

(Unfortunately you'll still need to manually create an admin user for now, see the section "Creating an admin user" here [ceylon-herd-web Dockerfile]( https://hub.docker.com/r/ceylon/ceylon-herd-web/))

