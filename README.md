# Voxpupuli Puppet Server container

[![Sponsored by betadots GmbH](https://img.shields.io/badge/Sponsored%20by-betadots%20GmbH-blue.svg)](https://www.betadots.de)
[![License](https://img.shields.io/github/license/voxpupuli/container-puppetserver.svg)](https://github.com/voxpupuli/container-puppetserver/blob/main/LICENSE)
[![CI](https://github.com/voxpupuli/container-puppetserver/actions/workflows/ci.yaml/badge.svg)](https://github.com/voxpupuli/container-puppetserver/actions/workflows/ci.yaml)

---

- [Voxpupuli Puppet Server container](#voxpupuli-puppet-server-container)
  * [Configuration](#configuration)
  * [Initialization Scripts](#initialization-scripts)
  * [Persistance](#persistance)
  * [How to Release the container](#how-to-release-the-container)
  * [How to contribute](#how-to-contribute)

---

This project hosts the Dockerfile and the required scripts to build a Puppet Server container image.

You can run a copy of Puppet Server with the following Docker command:

```bash
docker run --name puppet --hostname puppet ghcr.io/voxpupuli/container-puppetserver:v1.0.0-7
```

Although it is not strictly necessary to name the container `puppet`, this is
useful when working with the other Puppet images, as they will look for a server
on that hostname by default.

If you would like to start the Puppet Server with your own Puppet code, you can
mount your own directory at `/etc/puppetlabs/code`:

```bash
docker run --name puppet --hostname puppet -v ./code:/etc/puppetlabs/code/ ghcr.io/voxpupuli/container-puppetserver:v1.0.0-7
```

For compose file see: [CRAFTY](https://github.com/voxpupuli/crafty/tree/main/puppet/oss)

You can find out more about Puppet Server in the [official documentation](https://www.puppet.com/docs/puppet/7/server/about_server.html).

## Configuration

The following environment variables are supported:

| Name                                       | Usage / Default                                                                                                                                               |
|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PUPPETSERVER_HOSTNAME**                  | The DNS name used on the servers SSL certificate - sets the `certname` and `server` in puppet.conf<br><br>Defaults to unset.                                  |
| **DNS_ALT_NAMES**                          | Additional DNS names to add to the servers SSL certificate<br>**Note** only effective on initial run when certificates are generated                          |
| **PUPPETSERVER_PORT**                      | The port of the puppetserver<br><br>`8140`                                                                                                                    |
| **AUTOSIGN**                               | Whether or not to enable autosigning on the puppetserver instance. Valid values are `true`, `false`, and `/path/to/autosign.conf`.<br><br>Defaults to `true`. |
| **CA_ENABLED**                             | Whether or not this puppetserver instance has a running CA (Certificate Authority)<br><br>`true`                                                              |
| **CA_HOSTNAME**                            | The DNS hostname for the puppetserver running the CA. Does nothing unless `CA_ENABLED=false`<br><br>`puppet`                                                  |
| **CA_PORT**                                | The listening port of the CA. Does nothing unless `CA_ENABLED=false`<br><br>`8140`                                                                            |
| **CA_ALLOW_SUBJECT_ALT_NAMES**             | Whether or not SSL certificates containing Subject Alternative Names should be signed by the CA. Does nothing unless `CA_ENABLED=true`.<br><br>`false`        |
| **PUPPET_REPORTS**                         | Sets `reports` in puppet.conf<br><br>`puppetdb`                                                                                                               |
| **PUPPET_STORECONFIGS**                    | Sets `storeconfigs` in puppet.conf<br><br>`true`                                                                                                              |
| **PUPPET_STORECONFIGS_BACKEND**            | Sets `storeconfigs_backend` in puppet.conf<br><br>`puppetdb`                                                                                                  |
| **PUPPETSERVER_MAX_ACTIVE_INSTANCES**      | The maximum number of JRuby instances allowed<br><br>`1`                                                                                                      |
| **PUPPETSERVER_MAX_REQUESTS_PER_INSTANCE** | The maximum HTTP requests a JRuby instance will handle in its lifetime (disable instance flushing)<br><br>`0`                                                 |
| **PUPPETSERVER_JAVA_ARGS**                 | Arguments passed directly to the JVM when starting the service<br><br>`-Xms512m -Xmx512m`                                                                     |
| **USE_PUPPETDB**                           | Whether to connect to puppetdb<br>Sets `PUPPET_REPORTS` to `log` and `PUPPET_STORECONFIGS` to `false` if those unset<br><br>`true`                            |
| **PUPPETDB_SERVER_URLS**                   | The `server_urls` to set in `/etc/puppetlabs/puppet/puppetdb.conf`<br><br>`https://puppetdb:8081`                                                             |
| **PUPPETDB_HOSTNAME**                      | The DNS name of the puppetdb <br><br> Defaults to `puppetdb`                                                                                                  |
| **PUPPETDB_SSL_PORT**                      | The TLS port of the puppetdb <br><br> Defaults to `8081`                                                                                                      |
| **ADDITIONAL_CERTIFICATES**                | Generate and sign additional certificates. Comma-separated list, f.e.: ADDITIONAL_CERTIFICATES="puppetboard,test"                                             |

## Initialization Scripts

If you would like to do additional initialization, add a directory called `/docker-custom-entrypoint.d/` and fill it with `.sh` scripts.
These scripts will be executed at the end of the entrypoint script, before the service is ran.

## Persistance

If you plan to use the in-server CA, restarting the container can cause the server's keys and certificates to change, causing agents and the server to stop trusting each other.
To prevent this, you can persist the default cadir, `/etc/puppetlabs/puppetserver/ca`.
For example, `docker run -v $PWD/ca-ssl:/etc/puppetlabs/puppetserver/ca ghcr.io/voxpupuli/container-puppetserver:v1.0.0-7`.

## How to Release the container

[see here](https://github.com/voxpupuli/crafty/blob/main/docs/how-to-release.md)

## How to contribute

[see here](https://github.com/voxpupuli/crafty/blob/main/CONTRIBUTING.md)
