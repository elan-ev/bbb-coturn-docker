# Coturn-in-Docker-Setup for BigBlueButton

This repo shows how to adapt the instructions for coturn in docker on the
[coturn github page](https://github.com/coturn/coturn/tree/docker/4.5.2-r14/docker)
for the use with BigBlueButton according to their
[respective docs](https://docs.bigbluebutton.org/admin/setup-turn-server.html).

## Prerequisites

You need to have [docker](https://www.docker.com/) and [docker-compose](https://docs.docker.com/compose/) installed.
If you have an active firewall you need to open the respective ports mapped in the [docker-compose file](docker-compose.yml).

## Usage

Aside from the [docker-compose.yml](docker-compose.yml) you need the file [turnserver.conf](turnserver.conf)
to configure coturn.
In the default configuration the two should be placed in the same directory.
After setting all relevant the variables in [turnserver.conf](turnserver.conf) you can start the service (`docker-compose up`).

## Configuration

You need to set at least these variables in `turnserver.conf`:

| key | value|
|:--|:--|
| `static-auth-secret` | the secret shared with the connecting bbb hosts |
| `listening-ip` | usually the ip of the coturn host  |
| `relay-ip` | usually the same as the `listening-ip` |
| `realm` | usually the domain of the host |

### TLS

To use TLS you will need certificates that are readable by the user that starts the container.
You should adjust the respective paths under the `volume`-key in the `docker-compose.yml`.
If you want to use a custom Diffie-Hellman parameter file, you also need to map a `dh-param.pem` file
in the `docker-compose.yml` and adjust the `dh-file` value `turnserver.conf` file.

### Docker Network Mode

Because coturn opens a lot of ports `network_mode` is set to `host`.
Otherwise mapping all the ports during the start of the container is _very_ slow.

### Additional Hardening

To prevent relaying traffic to other servers than your BigBlueButton nodes,
you can set `allowed-peer-ip` in the `turnserver.conf` to the IPs of your BigBlueButton nodes
(in the same way that `denied-peer-ip`s are set in this config example).
