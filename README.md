# ApacheDS

## Introduction
*Note:* This image is based on the docker image `h3nrik/apacheds` and the GitHub repo `g17/ApacheDS` which are no longer available.

This Docker image provides an [ApacheDS](https://directory.apache.org/apacheds/) LDAP server. Optionally it could be used to provide a [Kerberos server](https://directory.apache.org/apacheds/advanced-ug/2.1-config-description.html#kerberos-server) as well.

The project sources can be found on [GitHub](https://github.com/favll/apacheds). The Docker image on [Docker Hub](https://registry.hub.docker.com/u/favll/apacheds/).

## Build

```bash
git clone https://github.com/favll/apacheds.git
docker build -t favll/apacheds .
```

## Installation

The folder `/var/lib/apacheds-${APACHEDS_VERSION}` contains the runtime data and thus has been defined as a volume. A [volume container](https://docs.docker.com/userguide/dockervolumes/) could be used for that. The image uses exactly the file system structure defined by the [ApacheDS documentation](https://directory.apache.org/apacheds/advanced-ug/2.2.1-debian-instance-layout.html).

The container can be started issuing the following command:

```bash
docker run --name ldap -d -p 389:10389 favll/apacheds
```

## Usage

You can manage the ldap server with the admin user `uid=admin,ou=system` and the default password `secret`. The `default` instance comes with a pre-configured partition `dc=example,dc=com`.

An individual admin password should be set following [this manual](https://directory.apache.org/apacheds/basic-ug/1.4.2-changing-admin-password.html).

Then you can import entries into that partition via your own *ldif* file. A [sample.ldif](https://github.com/favll/apacheds/blob/master/sample/sample.ldif) file is provided with the sources:

```bash
ldapadd -v -h <your-docker-ip>:389 -c -x -D uid=admin,ou=system -w <your-admin-password> -f `pwd`/sample/sample.ldif
```

## Customization

It is also possible to start up your own defined Apache DS *instance* with your own configuration for *partitions* and *services*. Therefore you need to mount your [config.ldif](https://github.com/favll/apacheds/blob/master/instance/config.ldif) file and set the `APACHEDS_INSTANCE` environment variable properly. In the provided sample configuration the instance is named `default`. Assuming your custom instance is called `yourinstance` the following command will do the trick:

```bash
docker run --name ldap -d -p 389:10389 -e APACHEDS_INSTANCE=yourinstance -v /path/to/your/config.ldif:/bootstrap/conf/config.ldif:ro favll/apacheds
```

It would be possible to use this ApacheDS image to provide a [Kerberos server](https://directory.apache.org/apacheds/advanced-ug/2.1-config-description.html#kerberos-server) as well. Just provide your own `config.ldif` file for that. Don't forget to expose the right port, then.

Also other services are possible. For further information read the [configuration documentation](https://directory.apache.org/apacheds/advanced-ug/2.1-config-description.html).
