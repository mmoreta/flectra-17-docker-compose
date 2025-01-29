# Installing Flectra 1.7 with one command (Supports multiple Flectra instances on one server).

## Quick Installation

Install [docker](https://docs.docker.com/get-docker/) and [docker-compose](https://docs.docker.com/compose/install/) yourself, then run the following to set up first Flectra instance @ `localhost:10016` (default master password: `minhng.info`):

``` bash
curl -s https://github.com/mmoreta/flectra-17-docker-compose/master/run.sh | sudo bash -s flectra-one 10016 20016
```
and/or run the following to set up another Flectra instance @ `localhost:11016` (default master password: `flectra@2025`):

``` bash
curl -s https://github.com/mmoreta/flectra-17-docker-compose/master/run.sh | sudo bash -s flectra-two 11016 21016
```

Some arguments:
* First argument (**flectra-one**): Flectra deploy folder
* Second argument (**10016**): Flectra port
* Third argument (**20016**): live chat port

If `curl` is not found, install it:

``` bash
$ sudo apt-get install curl
# or
$ sudo yum install curl
```

## Usage

Start the container:
``` sh
docker-compose up
```
Then open `localhost:10016` to access Flectra 1.7.

- **If you get any permission issues**, change the folder permission to make sure that the container is able to access the directory:

``` sh
$ sudo chmod -R 777 addons
$ sudo chmod -R 777 etc
$ sudo chmod -R 777 postgresql
```

- If you want to start the server with a different port, change **10016** to another value in **docker-compose.yml** inside the parent dir:

```
ports:
 - "10016:7073"
```

- To run Flectra container in detached mode (be able to close terminal without stopping Flectra):

```
docker-compose up -d
```

- To Use a restart policy, i.e. configure the restart policy for a container, change the value related to **restart** key in **docker-compose.yml** file to one of the following:
   - `no` =	Do not automatically restart the container. (the default)
   - `on-failure[:max-retries]` =	Restart the container if it exits due to an error, which manifests as a non-zero exit code. Optionally, limit the number of times the Docker daemon attempts to restart the container using the :max-retries option.
  - `always` =	Always restart the container if it stops. If it is manually stopped, it is restarted only when Docker daemon restarts or the container itself is manually restarted. (See the second bullet listed in restart policy details)
  - `unless-stopped`	= Similar to always, except that when the container is stopped (manually or otherwise), it is not restarted even after Docker daemon restarts.
```
 restart: always             # run as a service
```

- To increase maximum number of files watching from 8192 (default) to **524288**. In order to avoid error when we run multiple Flectra instances. This is an *optional step*. These commands are for Ubuntu user:

```
$ if grep -qF "fs.inotify.max_user_watches" /etc/sysctl.conf; then echo $(grep -F "fs.inotify.max_user_watches" /etc/sysctl.conf); else echo "fs.inotify.max_user_watches = 524288" | sudo tee -a /etc/sysctl.conf; fi
$ sudo sysctl -p    # apply new config immediately
``` 

## Custom addons

The **addons/** folder contains custom addons. Just put your custom addons if you have any.

## Flectra configuration & log

* To change Flectra configuration, edit file: **etc/flectra.conf**.
* Log file: **etc/flectra-server.log**
* Default database password (**admin_passwd**) is `flectra@2025`, please change it @ [etc/flectra.conf#L60](/etc/flectra.conf#L60)

## Flectra container management

**Run Flectra**:

``` bash
docker-compose up -d
```

**Restart Flectra**:

``` bash
docker-compose restart
```

**Stop Flectra**:

``` bash
docker-compose down
```

## Live chat

In [docker-compose.yml#L21](docker-compose.yml#L21), we exposed port **20016** for live-chat on host.

Configuring **nginx** to activate live chat feature (in production):

``` conf
#...
server {
    #...
    location /longpolling/ {
        proxy_pass http://0.0.0.0:20016/longpolling/;
    }
    #...
}
#...
```

## docker-compose.yml

* flectra:1.7
* postgres:14


