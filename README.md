# credhub-certs-checker

Inspired by [credhub_exporter](https://github.com/orange-cloudfoundry/credhub_exporter), this is a small app running locally to check **valid** and **most relevant** certificates expiration from BOSH Director CredHub for the Ops Manager version prior to 2.7

* It doesn't cover Ops Manager ROOT CA and BOSH Director NATS CA, use Ops Manager API instead
* It lists all the bosh deployments and then check relevant CredHub certificates entries.
* It lists all the certificates and associated CA certificates.
* It has a pre-defined ignore list for PKS 1.4+
* By default it would check certificates at Director CredHub expiring within 6 months.
* It would list safe ignore list of CA certificates if only, e.g. `/bosh_dns_health_tls_ca` and `/dns_api_tls_ca`

## Usage

```
usage: credhub-certs-checker [<flags>]

Flags:
  -h, --help                     Show context-sensitive help (also try --help-long and --help-man).
  -e, --environment=ENVIRONMENT  Director environment name or URL [BOSH_ENVIRONMENT] e.g. 192.168.1.1
      --client=CLIENT            Override username or UAA client [BOSH_CLIENT]
      --client-secret=CLIENT-SECRET  
                                 Override password or UAA client secret [BOSH_CLIENT_SECRET]
      --ca-cert=CA-CERT          Director CA certificate path [BOSH_CA_CERT] e.g. /var/tempest/workspaces/default/root_ca_certificate
      --expires-within="6m"      Expiration Within Years/Months/Days e.g. 1y, 3m, 10d.
      --version                  Show application version.
```

Sample Output:

```
================================================================================
The following is CA certificates list would expire within 1y and associated with existing leaf certificates
================================================================================


================================================================================
The following is the leaf certificates list would expire within 1y and its associated CA path
================================================================================

/bosh_dns_health_client_tls,2020-12-03 03:23:40 +0000 UTC,/opsmgr/bosh_dns/tls_ca
/p-bosh/service-instance_f4bc0980-f9b9-41f3-977a-9ec59441aed1/tls-kubernetes-2018,2020-12-17 20:55:52 +0000 UTC,/p-bosh/pivotal-container-service-cee6884f6c3f1eea176c/kubo_odb_ca_2018
/dns_api_server_tls,2020-12-03 03:23:41 +0000 UTC,/opsmgr/bosh_dns/tls_ca
/bosh_dns_health_server_tls,2020-12-03 03:23:40 +0000 UTC,/opsmgr/bosh_dns/tls_ca
/dns_api_client_tls,2020-12-03 03:23:41 +0000 UTC,/opsmgr/bosh_dns/tls_ca

================================================================================
The following is the CA certificates would expire within 1y.
However they are NOT on the Ignore List nor associated with any existing leaf certificates.
It might be fine to ignore
================================================================================

/bosh_dns_health_tls_ca,2020-02-24 01:02:51 +0000 UTC
/p-bosh/pivotal-container-service-cee6884f6c3f1eea176c/kubo_odb_ca,2020-02-24 01:02:48 +0000 UTC
/dns_api_tls_ca,2020-02-24 01:02:51 +0000 UTC

================================================================================

```

## Predefined PKS 1.4+ Certificates Ignore List

most of them are replaced by new 2018 certificates

```
"kubo_ca"
"tls-kubelet"
"tls-kubelet-client"
"tls-kubernetes"
"tls-kube-controller-manager"
"tls-etcd-v0-17-0"
"tls-etcdctl"
"tls-metrics-server"
"tls-heapster"
"tls-influxdb"
"kubernetes-dashboard-ca"
"tls-kubernetes-dashboard"
"telemetry_server_tls"
"telemetry_ca"
"pks_api_internal"
"uaa_active_pks_saml_key"
"tls-docker"
"kubo_odb_ca"
```

## PAS

It can be used for PAS since BOSH Director CredHub leveraged for platform internal components certificates. 

It can check some entries not monitored by Ops Manager, for example `/services/tls_ca` used by [MySQL for Pivotal Platform](https://network.pivotal.io/products/pivotal-mysql/)

## CredHub References

* [CredHub Credential Types Doc](https://github.com/cloudfoundry-incubator/credhub/blob/master/docs/credential-types.md)
* [CredHub Credential Types Code](https://github.com/cloudfoundry-incubator/credhub-cli/blob/master/credhub/credentials/values/types.go)
