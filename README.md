# Consul Vault Enterprise POC Runbook

This tutorial walks you through setting up Consul and Vault Enterprise Grade Custers and services across 2 data centers. 

We initially install servers manually, then walt though how these steps can be automated.

> The results of this tutorial should not be viewed as production ready.


## Target Audience

The target audience for this tutorial is someone planning to support a production Consul & Vault clusters and wants to understand how everything fits together.

## Cluster Details

This tutorial guides you through bootstrapping a highly available Consul & Vault cluster with milti DC Wan Federation.

* [Consul](https://releases.hashicorp.com/consul/) v1.10.0_ent
* [Vault](https://releases.hashicorp.com/vault/) v1.8.2+ent
* [Envoy](https://github.com/containernetworking/cni) v0.9.1


## Steps

This tutorial assumes you have access to the [AWS](https://aws.amazon.com). While AWS is used for basic infrastructure requirements the lessons learned in this tutorial can be applied to other platforms.

* [Prerequisites](docs/01-prerequisites.md)
* [POC Architecture](docs/02-poc-architecture.md)

### Consul
* [Installing Consul Server 1 Manually](docs/03-consul-server-1-manual-install.md)
* [Provisioning the CA and Generating TLS Certificates](docs/04-provisioning-ca-generate-tls-cert.md)
* [Installing Consul Server 2 & 3 Using Scripts](docs/05-consul-serers-2-3-script-install.md)
* [Enable ACL on Consul Cluster](docs/05-consul-serers-2-3-script-install.md)
* [Set up Consul Agent, Envoy & Java Service](docs/05-consul-serers-2-3-script-install.md)


### Vault
* [Installing Vault Server 1 Manually](docs/06-install-vault-server-1-manually.md)
* [Installing Vault Servers 2 & 3 Using Scripts](docs/06-install-vault-server-1-manually.md)
* [Configure and Test Transit Encryption](docs/xx-configure-and-test-transit-encryption.md)
* [Configure and Test Format Preserving Encryption](docs/xx-configure-and-test-format-preserving-encryption.md)



