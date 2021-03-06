# Cloud9 Development Environment

This image contains a complete development environment utilizing Cloud9 IDE inside a Ubuntu container to provide everything needed to setup a development environment. It includes Cloud9, but also adds some common utilities in Ubuntu, Vim, and Git.

## Concept

The idea behind this image is to build a development environment that is/can be web-based, accessible anywhere, as well as portable, ephemeral, and flexible.

## Usage

```
docker run -d \ 
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/dev-env-home/:/root \
  -v ~/workspace/:/workspace \
  -p 8000-8020:8000-8020 \
  -e C9USER=user -e C9PASS=welcome -e C9PORT=8888 \
  -p 8888:8888 \
  fluidbyte/c9-dev-env
```

The above does the following (line-by-line):

```
-v /var/run/docker.sock:/var/run/docker.sock
```

**Mounts the host's docker socket into the container**

_This passes the socket through so `docker` in the container uses the host's socket. This preserves images and allows docker to run inside a container._

```
-v ~/dev-env-home/:/root
```

**Mounts the host's `~/dev-env-home` to the container's `/root` home**
_Since the container doesn't maintain state, this allows you to setup your bash profile, aliases, git config, vim config, etc and retain them on the host when the container is stopped_

```
-v ~/workspace/:/workspace
```

**Mounts the host's `~/workspace` to the `/workspace` volume in the container**

_Similar to the line above, this saves the files in the dev environment to the host so they are not lost when the container is stopped._

```
-p 8000-8020:8000-8020
```

**Exposes `8000-8020` for use during development**

_This is optional, but setting a range of open ports allows you to utilize these, for example, when testing a running service in the dev environment._

```
-e C9USER=user -e C9PASS=welcome -e C9PORT=8888
```

**Sets the `C9USER`, `C9PASS` and `C9PORT` settings on which Cloud9 will run**

_The `C9USER` and `C9PASS` will be used to authenticate, the `C9PORT` instructs what port the server should run over._

```
p 8888:8888
```

**Exposes the port set by `C9PORT` making the IDE accessible locally**

_Makes C9 available locally at the port designated_

## Customization and Profile

It's important to be able to customize a development environment. By creating a mounted volume from your host to the `/root` profile directory, you are able to customize the shell (supports `sh`, `bash`, and `zsh`) with things like [bash-it](https://github.com/Bash-it/bash-it), [sexy-bash-prompt](https://github.com/twolfson/sexy-bash-prompt), or [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh).

_Note: If you're using ZSH, C9 has issues with mouse scrolling in the terminal. To resolve, create a file `~/.tmux.conf` =, add the following; `setw -g mode-mouse on`, then restart any open terminals._

## Docker-In-Docker

While the image itself is ephemeral (except the home folder and workspace that are mounted) there are several options for maintaining state (i.e. not loosing things on restart). You can mount volumes to specific paths if needed though the better solution is to utilize Docker.

Tools such as [Binci](https://github.com/binci/binci) or [Docker-Compose](https://docs.docker.com/compose/) allow for running other needed binaries, configurations, databases, etc inside containers during development.

## Security

The image follows the standard Docker convention of using the `root` user as the default user in the container. This is safe if you're following best practices for Docker security (i.e. not exposing services which could be hijacked). Additionally, shutting down the container when not in use is good practice.

Ultimately, if serving publicly, it's also advisable to use a tool like [Let's Encrypt](https://letsencrypt.org/) and [nginx](https://www.nginx.com/) to setup [an SSL via proxy service](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04) on your exposed instance.
