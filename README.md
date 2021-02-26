# Foundation

Foundation makes developing and running docker services easier. 

The main component of Foundation is 
[traefik proxy](https://doc.traefik.io/traefik/). Traefik is used for service discover of docker containers, allowing
you to access your services using hostnames you define, i.e. `myapp.test`.

Foundation also contains instructrions for setting up docker networks in a way that allow communication between different
docker-compose applications.

## Requirements

* dnsmasq (Tested using NetworkManager and dnsmasq, other local dns resolvers may work)
* [docker](https://docs.docker.com/get-docker/)
* [docker-compose](https://docs.docker.com/compose/install/)
* [mkcert](https://github.com/FiloSottile/mkcert)

### Optional

* jq (used for troubleshooting)

## Getting Started

#### Create the docker network

```docker network create --driver bridge --attachable --internal=false gateway --gateway=172.26.0.1 --subnet=172.26.0.0/16```

#### Setup DNS resolution for the `test` tld

The IP Address needs to be the same as the docker bridge network gateway, by default this is `172.17.0.1`. You can look
it up with this command though `docker network inspect bridge | jq '.[0].IPAM.Config[0].Gateway'`

This is specific for NetworkManager and dnsmasq on Arch Linux. You may need to adapt it for other distros.

```echo address=/test/172.17.0.1 > /etc/NetworkManager/dnsmasq.d/test```

#### Start traefik

```docker-compose up -d```

The `-d` runs it in detached mode

The traefik container is configured with `restart: always`. This will cause it to start automatically at startup. If you
want to stop it from running, just run: ```docker-compose stop``` in this directory

#### Enable traefik for a container

By default traefik doesn't expose containers, to expose a container you just need to
add a label to it's docker-compose configuration.
```
labels:
  - "traefik.enable=true"
```

You can customize lots of things using labels, see
[https://doc.traefik.io/traefik/providers/docker/](https://doc.traefik.io/traefik/providers/docker/) for more details.

## Accessing the Traefik dashboard

Once traefik is running you can access the dashboard a [traefik.test](http://traefik.test)

