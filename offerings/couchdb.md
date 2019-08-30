---

copyright:
  years: 2015, 2019
lastupdated: "2019-08-29"

keywords: apache couchdb support extension

subcollection: cloudant

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}

<!-- Acrolinx: 2017-05-10 -->

# Apache CouchDB
{: #apache-coucdhdb}

## Overview
{: #overview}

[Apache CouchDB](http://couchdb.apache.org/) is a powerful and scalable Apache-licensed open source database with an intuitive HTTP API. It scales from a developer's laptop to production database clusters with tens of nodes. Furthermore, the Apache CouchDB replication protocol allows data to be synchronized between clusters spread across the globe, to devices in your customers' pockets, and to {{site.data.keyword.cloudantfull}} managed service instances. It is proven for production and is available at no charge to download and use for any purpose.

{{site.data.keyword.IBM}} recommends the use of Apache CouchDB for any on-premise self-managed installations to pair alongside {{site.data.keyword.cloudant_short_notm}}. Using {{site.data.keyword.cloudant_short_notm}} and Apache CouchDB together allows teams to do the following:
- {{site.data.keyword.cloudant_short_notm}} has always been built with Apache CouchDB and maintains API compatibility for core features. The common replication protocol between these technologies enables hybrid multicloud architectures.
- Combine {{site.data.keyword.cloudant_short_notm}} and Apache CouchDB as an open data layer for your applications to build once and run anywhere. 
- Developers can use both Apache CouchDB and {{site.data.keyword.cloudant_short_notm}} database ecosystem to store data wherever they need it. For example, you could use {{site.data.keyword.cloudant_short_notm}} in the {{site.data.keyword.cloud}} and sync data with Apache CouchDB running at an edge location like an oil rig or tablet device. Alternatively, you could use {{site.data.keyword.cloudant_short_notm}} for large scale production environment while using Apache CouchDB for local development and testing. 

## Comparing Apache CouchDB and {{site.data.keyword.cloudant_short_notm}}
{: #couchdb-and-cloudant}

The foundation of the {{site.data.keyword.cloudant_short_notm}} managed database service is the Apache CouchDB database. {{site.data.keyword.IBM_notm}} is active in the Apache CouchDB committee, employs members of the PMC, and commits a vast majority of its feature, functions, and enhancements back to the open source project.  Over the last few years, {{site.data.keyword.IBM_notm}} has made a significant effort to align the core feature set of {{site.data.keyword.cloudant_short_notm}} and CouchDB, contributing key features like {{site.data.keyword.cloudant_short_notm}} Query/Mango query language, full-text search, and partition queries to CouchDB. 

Apache CouchDB and {{site.data.keyword.cloudant_short_notm}} are nearly fully API compatible, which means they can serve as drop-in replacements for each other in your application. They speak the same replication protocol so that you can seamlessly sync data between {{site.data.keyword.cloudant_short_notm}} and CouchDB instances. However, there are some differences between the  fully-managed cloud service {{site.data.keyword.cloudant_short_notm}} and self-managed open source Apache CouchDB, which this guide will help illuminate. 

For more information, see the [API comparison guide](https://cloud.ibm.com/docs/services/Cloudant?topic=cloudant-comparison-of-ibm-cloudant-and-couchdb-api-endpoints)  for a detailed breakdown of API endpoints available in Apache CouchDB 1.x, Apache CouchDB 2.x, and {{site.data.keyword.cloudant_short_notm}}.   

Below are the feature/function differences you should be cognizant of when using the Apache CouchDB and {{site.data.keyword.cloudant_short_notm}} data layer ecosystem. 

| Feature | CouchDB 1.6 | CouchDB 2.3.1 | CouchDB 3.0 | {{site.data.keyword.cloudant_short_notm}} on {{site.data.keyword.cloud_notm}} |
|--------------|----------------|-------------|---------------------| --- |
| Clustering    | No     | Yes | Yes | Yes |
| Fauxton Dashboard UI    | No     | Yes | Yes | Yes |
| MapReduce view    | Yes     | Yes | Yes | Yes |
| Mango/{{site.data.keyword.cloudant_short_notm}} Query    | No     | Yes | Yes | Yes |
| Full-text search    | No     | No | Yes, requires separate installer/container | Yes |
| Geospatial    | No     | No | No | Yes |
| Partition queries    | No     | No | Yes | Yes |
| Shard splitting    | No     | No | Yes | Available as tool for {{site.data.keyword.IBM_notm}} Ops |
| Selector on changes feed    | No     | Yes | Yes | Yes |
| Rate limits    | No    | No | No | User-defined [provisioned throughput capacity](https://cloud.ibm.com/docs/services/Cloudant?topic=cloudant-ibm-cloud-public#provisioned-throughput-capacity) settings |
| Request size    | 4GB (default)     | 4GB (default) | 4GB (default) | 11MB |
| Attachment size    | 4GB (default)     | 4GB (default) | 4GB (default) | 10MB |
| Security auth    | [CouchDB Auth](https://docs.couchdb.org/en/stable/intro/security.html#)     | [CouchDB Auth](https://docs.couchdb.org/en/stable/intro/security.html#) | [CouchDB Auth](https://docs.couchdb.org/en/stable/intro/security.html#) | [{{site.data.keyword.cloudant_short_notm}} legacy auth with API Keys](https://cloud.ibm.com/docs/services/Cloudant?topic=cloudant-authorization), [{{site.data.keyword.cloud_notm}} IAM](https://cloud.ibm.com/docs/services/Cloudant?topic=cloudant-ibm-cloud-identity-and-access-management-iam-), or [CouchDB Auth](https://docs.couchdb.org/en/stable/intro/security.html#) |
| LDAP    | No     | No | No | No |

## Installation and Documentation
{: #install-and-docs}

The current latest GA release is Apache CouchDB version 2.3.1. 

### Documentation
{: #docs}

For more information, see the [Apache CouchDB documentation](http://docs.couchdb.org/en/stable/).  

### Installation
{: #install}

The [Download CouchDB](http://couchdb.apache.org/#download) site contains links to install CouchDB on:
- Windows (x86)
- macOS
- Linux: Debian/Ubuntu/RHEL/CentOS

**Docker**

The Apache CouchDB docker image is available on [dockerhub](https://hub.docker.com/_/couchdb), along with [documentation](https://docs.couchdb.org/en/stable/install/docker.html). 

**Kubernetes**

An Apache CouchDB helm chart along with documentation is available on [github](https://github.com/helm/charts/tree/master/stable/couchdb). 

## Support
{: #support}

### Chat
The Apache CouchDB community has Slack and IRC channels where you can ask questions. For more information, see the Slack sign up form and the IRC details on the [Apache CouchDB website](http://couchdb.apache.org/).