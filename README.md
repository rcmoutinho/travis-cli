# Travis CLI using Docker

This project helps developers that don't want to install the Travis CLI locally but want to have all the benefits of using commands to automate processes instead of manual UI tasks.

Not very familiar with docker? Go to the [docker section](#docker-help) to get the basics to use this project.

## Usage

Check out [Travis CLI](https://github.com/travis-ci/travis.rb) documentation to understand what each command does.

The `/app` volume to your root project folder is useful to get CLI help to adjust and/or create for you the `.travis.yml` file inside of your project.

The `.travis` folder is very important to the process. It will store your _Travis Secret Key_ to maintain you session during the usage and also keep some other configurations to ease your usage. If you had installed manually, this folder would be located on your user home directory (`~/.travis`). **_>> NEVER <<_** commit the `.travis` folder. It has sensitive information that would expose your API secret key, giving open access to your account.

### Docker

Docker (for linux / unix systems)

```
docker run --rm -v $(pwd):/app -v $(pwd)/.travis:/root/.travis rcmoutinho/travis-cli <command>
```

Docker-Compose (local configuration)

```
docker-compose run --rm rcmoutinho-travis <command>
```

## Example from the scratch

To test out your _Docker_ configuration, try to get _travis-cli_ usage help, that will build your local image. On the first execution, the image will automatically build.

```
docker-compose run --rm travis
```

```
Building travis
Step 1/8 : FROM ruby:alpine
 ---> 47c30d96ab20
Step 2/8 : RUN apk --update add build-base git   && gem install travis   && apk del build-base   && rm -rf /var/cache/apk/*   && rm -rf /tmp/*   && mkdir app
 ---> Running in ccac9517fe54
fetch http://dl-cdn.alpinelinux.org/alpine/v3.10/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.10/community/x86_64/APKINDEX.tar.gz
(1/21) Upgrading musl (1.1.22-r2 -> 1.1.22-r3)
(2/21) Installing binutils (2.32-r0)
(3/21) Installing libmagic (5.37-r0)

# ... long output about the installation process

(14/15) Purging mpc1 (1.1.0-r0)
(15/15) Purging mpfr3 (3.1.5-r1)
Executing busybox-1.30.1-r2.trigger
OK: 41 MiB in 42 packages
Removing intermediate container ccac9517fe54
 ---> 4e3f18910ee3
Step 3/8 : WORKDIR app
 ---> Running in ea071375a006
Removing intermediate container ea071375a006
 ---> 0c0e3107d9d5
Step 4/8 : VOLUME ["/app"]
 ---> Running in cca2e189a712
Removing intermediate container cca2e189a712
 ---> 6f8459f63eb0
Step 5/8 : LABEL maintainer="twitter.com/rcmoutinho"
 ---> Running in bf5cb1e2036c
Removing intermediate container bf5cb1e2036c
 ---> 9df4a2a9a559
Step 6/8 : LABEL description="Travis CLI in a docker container"
 ---> Running in dc1a5d171a04
Removing intermediate container dc1a5d171a04
 ---> 9cb37aa14df8
Step 7/8 : ENTRYPOINT ["travis"]
 ---> Running in c58ecc064749
Removing intermediate container c58ecc064749
 ---> b229ba95d0ab
Step 8/8 : CMD ["--help"]
 ---> Running in d80e7bf0545a
Removing intermediate container d80e7bf0545a
 ---> bea6a8740685
Successfully built bea6a8740685
Successfully tagged travis-cli_travis:latest
WARNING: Image for service travis was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Usage: travis COMMAND ...

Available commands:

	accounts       displays accounts and their subscription status
	branches       displays the most recent build for each branch

# ... long command list
```

Or the following command that will run the `version` command. This time the execution would be wayyyyy faster (almost instantaneous) because the docker image already exists.

```
docker-compose run --rm travis version
```

```
1.8.10
```

The `docker-compose` service is configured with the name `travis`. But all the magic happens because the `Dockerfile` is configured with the _entrypoint_ `travis`. So that's why you just need to type the command you need because the configuration already expects it.

## Using docker-compose on development mode

In the case that your project already has a `docker-compose.yml` or something related, you can rename this project configuration to `docker-compose.dev.yml` or `docker-compose.version.yml` to make sure your automated release configuration won't affect your project. But remember that this change will request an extra parameter on your _compose_ commands. Let's use _Travis_ as an example:

```
COMPOSE_FILE=docker-compose.dev.yml docker-compose run --rm travis
```

You will need to specify the `COMPOSE_FILE` environment variable because the only command that accepts a different file as a parameter is the `docker-compose up`. So to run this configuration without worrying too much, you will need this _<s>hack</s>_ workaround.

## Docker (help!)

Some installations can be prevented using Docker if you want. This project don't has a `docker` folder that would handle (organize) all the `Dockerfile` for each scenario, separated by folders. This project has only one `Dockerfile`.

There is also a `docker-compose.yml` on the root folder to avoid typing _docker_ endless commands and configurations every time you need.

All configurations aim the minimum maintenance. So there is no need to create and maintain networks and volumes, only build the required image to run the desired task, once! To make it happen, use the following pattern for configurations.

```
docker-compose run --rm <docker-compose-service> <desired-command>
```

Using the command `run`, docker will take care to build the image using `Dockerfile` from the _service_ before running the container. And by using `--rm`, the container will be removed after completing the task, that would be your _desired command_.

The unique "garbage", would be the docker image created by the service(s), that you can get rid with the following command.

CAUTION: This command will remove all the images, volumes and networks related to this docker-compose configuration. Pay _VERY GOOD_ attention if you merged your `docker-compose` files before executing the following command.

```
docker-compose down --rmi local
```
