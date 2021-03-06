---

copyright:
  years: 2019, 2020
lastupdated: "2020-09-09"

keywords: incident management, operations management, change management, security compliance, regulation compliance, disaster recovery

subcollection: Cloudant

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
{:external: target="_blank" .external}

<!-- Acrolinx: 2019-12-20 -->

# Understanding your responsibilities when you use {{site.data.keyword.cloudant_short_notm}}
{: #cloudant-responsibilities}

Learn about the management responsibilities, and terms and conditions, that you own when you use {{site.data.keyword.cloudant_short_notm}}. For a high-level view of the service types in {{site.data.keyword.cloud}}, and the breakdown of responsibilities between the customer and {{site.data.keyword.IBM_notm}} for each type, see [Shared responsibilities for {{site.data.keyword.cloud_notm}} offerings](/docs/overview?topic=overview-shared-responsibilities).
{: shortdesc}

Review the following sections to understand the specific responsibilities between you and {{site.data.keyword.IBM_notm}} when you use {{site.data.keyword.cloudant_short_notm}}. For the overall terms of use, see [{{site.data.keyword.cloud_notm}} Terms and Notices](/docs/overview/terms-of-use?topic=overview-terms).

## Incident and operations management
{: #incident-and-ops}

<!-- Include an introductory sentence or two about this table. Leave the cell blank for the responsible party column if they do not have responsibility for the given task.  -->

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|HA/DR (multi-zone region) | {{site.data.keyword.cloudant_short_notm}} stores all documents in triplicate on separate servers that are spread across three separate availability zones by default.  | |
|HA/DR (single-zone region)| {{site.data.keyword.cloudant_short_notm}} stores all documents in triplicate on three separate physical servers within the availability zone by default.  | |
|Back up and restore|   | Customer is responsible for backup and restore of data to roll back to a previous state in the database. See [{{site.data.keyword.cloudant_short_notm}} backup and recovery](/docs/Cloudant?topic=Cloudant-ibm-cloudant-backup-and-recovery) documentation for recommended tooling. |
{: caption="Table 1. Responsibilities for incident and operations" caption-side="top"}


## Change management
{: #change-management-responsibilities}

<!-- Include an introductory sentence or two about this table. Leave the cell blank for the responsible party column if they do not have responsibility for the given task.  -->

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|Scaling| {{site.data.keyword.IBM_notm}} scales infrastructure to meet capacity selected by the customer.  | Customer chooses the provisioned throughput capacity for their {{site.data.keyword.cloudant_short_notm}} instances. |
|Upgrades| {{site.data.keyword.IBM_notm}} handles all upgrades and patches of the {{site.data.keyword.cloudant_short_notm}} service for the customer.  | |
{: caption="Table 2. Responsibilities for change management" caption-side="top"}

## Security and regulation compliance
{: #security-compliance-responsibilities}

<!-- Include an introductory sentence or two about this table. Leave the cell blank for the responsible party column if they do not have responsibility for the given task.  -->

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|At-rest encryption| By default, {{site.data.keyword.IBM_notm}} encrypts all disks by using {{site.data.keyword.cloudant_short_notm}}-managed encryption keys.   | If the customer would like bring-your-own-key (BYOK) encryption, then the customer is required to use Key Protect to store the customer-managed encryption key, select an appropriate key management service instance, and select a disk encryption key option during provisioning of an {{site.data.keyword.cloudant_short_notm}} Dedicated Hardware plan instance. |
{: caption="Table 3. Responsibilities for security and regulation compliance" caption-side="top"}

## Disaster recovery
{: #disaster-recovery-responsibilities}

<!-- Include an introductory sentence or two about this table. Leave the cell blank for the responsible party column if they do not have responsibility for the given task.  -->

| Task | {{site.data.keyword.IBM_notm}} Responsibilities | Your Responsibilities |
|----------|-----------------------|--------|
|HA/DR (cross-region)|  | Customer is responsible for creating more {{site.data.keyword.cloudant_short_notm}} instances in separate regions and configuring replications to achieve the desired cross-region HA/DR architecture. See [Configuring {{site.data.keyword.cloudant_short_notm}} for cross-region disaster recovery](/docs/Cloudant?topic=Cloudant-configuring-ibm-cloudant-for-cross-region-disaster-recovery) for more details.  |
{: caption="Table 4. Responsibilities for disaster recovery" caption-side="top"}

