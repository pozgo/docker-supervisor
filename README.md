# Supervisor in a Docker (CentOS and Alpine version available)
[![Build Status](https://travis-ci.org/pozgo/docker-supervisor.svg)](https://travis-ci.org/pozgo/docker-supervisor)  
[![GitHub Open Issues](https://img.shields.io/github/issues/pozgo/docker-supervisor.svg)](https://github.com/pozgo/docker-supervisor/issues)  
[![Stars](https://img.shields.io/github/stars/pozgo/docker-supervisor.svg?style=social&label=Stars)]()
[![Fork](https://img.shields.io/github/forks/pozgo/docker-supervisor.svg?style=social&label=Fork)]()  
[![Docker Start](https://img.shields.io/docker/stars/polinux/supervisor.svg)](https://hub.docker.com/r/polinux/supervisor)
[![Docker Pulls](https://img.shields.io/docker/pulls/polinux/supervisor.svg)](https://hub.docker.com/r/polinux/supervisor)
[![Docker Auto](https://img.shields.io/docker/automated/polinux/supervisor.svg)](https://hub.docker.com/r/polinux/supervisor)  
[![](https://img.shields.io/github/release/pozgo/docker-supervisor.svg)](http://microbadger.com/images/polinux/supervisor)

Felling like supporting me in my projects use donate button. Thank You!  
[![](https://img.shields.io/badge/donate-PayPal-blue.svg)](https://www.paypal.me/POzgo)

This is a Supervisor Docker [polinux/supervisor](https://registry.hub.docker.com/u/polinux/supervisor/) image, perfect in case when you need to launch more then one process inside a container. This image is based on [armhf/alpine](https://registry.hub.docker.com/_/armhf/alpine)

## What's included

##### - bootstrap.sh script

The container has an **ENTRYPOINT** set to `/config/bootstrap.sh`. It iterates through all `/config/init/*.sh` scripts and runs them, then launches supervisord. See [bootstrap.sh](container-files/config/bootstrap.sh) for details.

By default, the **CMD** option in Dockerfile is empty, but the bootstrap.sh script is configured to run everything which is passed into it. Therefore you can launch it in several ways:
* detached mode, no argument(s) passed: supervisord starts in foreground mode and stays until container is stopped.
* detached mode, some argument(s) passed: arguments are executed; supervisord starts in foreground mode and stays until container is stopped.
* interactive mode with TTY (-it), no argument(s) passed: supervisord starts in background mode; interactive bash waits for user input. Exiting from bash (CMD+D) exists the container.
* interactive mode with TTY (-it), some argument(s) passed: supervisord starts in background mode, passed command is executed; container exits.

##### - supervisord

Supervisord is installed and loads services to run from `/etc/supervisor.d/` directory. Add your own files there to launch your services. For example in your `Dockerfile` you could put:
```ADD my-supervisord-service.conf /etc/supervisord.d/my-supervisord-service.conf```

Learn more about about [supervisord inside containers on official Docker documentation](https://docs.docker.com/articles/using_supervisord/).

##### - init scripts

You can add your .sh scripts to `/config/init` directory to have them executed when container starts. The bootstrap script is configured to run them just before supervisord starts.

##### - error logging

Logfile for supervisord is switched off to avoid logging inside container. Instead, all logs are easily available via `docker logs [container name]`.

This is probably the best approach if you would like to source your logs from outside the container via `docker logs` (also via CoreOS `journald') and you do not want to worry about logging and log management inside your container and/or data volume.

##### - /data volume

The `/data` directory is meant to be used to simply and easily deploy web applications using a volume binding on `/data`, presumably using [data only containers](https://docs.docker.com/userguide/dockervolumes/) pattern.

Recommended structure:
```
/data/run/ # pid, sockets
/data/conf/ # extra configs for your services
/data/logs/ # logs
/data/www/ # your web application data
```


## Usage

As explained above, this container is configured to run your service(s) both in interactive and non-interactive modes.

`docker run -it polinux/supervisor`: runs supervisord, then interactive bash shell and waits for user's input. Exiting from the shell kills the container.

`docker run -it polinux/supervisor ps aux`:  runs supervisord, then `ps aux` command inside container and exists.

`docker run -it polinux/supervisor top`:  runs supervisord, then `top` tool. Exiting from top exits the container.

`docker run -d polinux/supervisor`: detached, runs supervisord in foreground mode and its configured services

`docker run -d polinux/supervisor touch 'test-file'`: detached, runs `touch 'test-file'` command, then supervisord in foreground mode and its configured services


## Build

`docker build --tag=polinux/supervisor .`

## Author

* Przemyslaw Ozgo (<linux@ozgo.info>)
