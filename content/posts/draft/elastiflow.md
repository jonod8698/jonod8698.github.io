---
title: "Elastiflow"
subtitle: ""
description: "Elastiflow"
date: 2022-05-29T00:52:51+10:00
lastmod: 2022-05-29T00:52:51+10:00
draft: false

author: "JD"
authorLink: ""

tags: []
categories: []


hiddenFromHomePage: false
hiddenFromSearch: false
featuredImage: ""
featuredImagePreview: ""
toc:
    enable: true
    auto: true

comment:
    enable: true

seo:
    images:
---
[Elastiflow](https://www.elastiflow.com/) is a high performance flow collector that provides unparalleled network observability. The community edition is free so it's perfect for homelabbers.

[Why use Elastiflow?](https://bidhankhatri.com.np/elk/network-traffic-analysis-using-elastiflow/)

Note: This guide is not recommended for production use. It is a POC for home lab use.

## Setting up Elastiflow

Visit [this link](https://www.elastiflow.com/basic-license) to obtain a Basic Free License.

Follow [these steps](https://docs.elastiflow.com/docs/install_linux) to install Elastiflow.

Don't follow the steps under **Running the Collector** to start Elastiflow yet.

## Setting up Elastic

Follow [these steps](https://docs.elastiflow.com/docs/elastic_install_ubuntu) to set up an Elastic stack. I was able to get away with 8GB RAM and 2 vCPUs for a small 100 mbps connection.

In addition tot the steps above:

- update `/etc/kibana/kibana.yml` with the correct IP addresses and certificate locations.
- update `/etc/elasticsearch/elasticsearch.yml` with the correct IP addresses and certificate locations.
- update `/etc/elasticsearch/jvm.options.d/heap.options` to an appropriate JVM heap size. 12GB was too high - I reduced this to 512MB for home lab purposes.
- update `/etc/systemd/system/flowcoll.service.d/flowcoll.conf`
  - Uncomment `Environment="EF_FLOW_ACCOUNT_ID=`, `Environment="EF_FLOW_LICENSE_KEY=`, `Environment="EF_FLOW_LICENSED_UNITS=1` and add the license details (you should receive this via email).
  - Uncomment and set the following values:`Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_ENABLE=true"` and `Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_ECS_ENABLE=true"`
  - Update the `Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_PASSWORD=`
  - Set `Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_TLS_ENABLE=true"`
  - Set `Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_TLS_SKIP_VERIFICATION=true"`
  - Set `Environment="EF_FLOW_OUTPUT_ELASTICSEARCH_TLS_CA_CERT_FILEPATH=/etc/elastiflow/ca/ca.crt"`
  -

## Setting up NetFlow on Unifi USG

Unifi USG Router uses [VyOS](https://docs.vyos.io/en/equuleus/configuration/system/flow-accounting.html). We will use this as a reference.

NetFlow config consists of two steps:

1. Configure interfaces to collect NetFlow data on.
2. Configure flow-accounting settings which apply to all interfaces.

SSH into the USG. Use `configure` to put the USG into edit mode.

For step one, with `ip -a` check the interfaces which you want to monitor. Use `set system flow-accounting interface <interface e.g. eth1>`.

For step two:

- `set system flow-accounting netflow enable-egress`
- `set system flow-accounting netflow enable-ingress`
- `set system flow-accounting netflow engine-id 1`
- `set system flow-accounting netflow server <server IP> port 9995`
- `set system flow-accounting netflow version 9`
- `set system flow-accounting netflow sampling-rate 10`
- `set system flow-accounting syslog-facility daemon`< not sure if this is required
- `commit`

To reverse any commands, substitute `set` with `delete`.

To show existing configuration, substitue `set` with `show`

To check if NetFlow is being exported, run `sudo tcpdump -i any -n port 9995` on the USG or the Elastiflow box.

Some references:

- https://www.plixer.com/blog/ubiquiti-netflow-support/
- https://davejlong.com/setting-up-netflow-on-unifi-security-gateway/

## Future Setup

- Persistent USG configuration.
- Enabling Elastiflow enrichment features.
- Multi-cluster Elastiflow with Kubernetes/Docker.
- Ansible automation.
