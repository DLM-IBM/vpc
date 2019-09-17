---

copyright:
  years: 2018, 2019
lastupdated: "2019-09-17"

keywords: vpc, early access, known issues, bugs

subcollection: vpc


---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}

# Known issues
{: #known-issues}

Known issues might change during the early access release, so check back occasionally.
{:shortdesc}


## API-1144: Virtual server instances (VSIs) must be stopped before they can be deleted
{: #API-1144}
- **Symptom:** The VSI cannot be deleted.
- **Workaround:** Stop the instance before you attempt to delete it.


## COM-1507: Invalid default repository setting in Ubuntu and Debian VSIs
{: #COM-1507}
- **Symptom:** The `apt update` command fails on a VSI created on a Debian stock image.
- **Workaround:** Update `/etc/apt/sources.list`, and replace `mirrors.service.networklayer.com` with `mirrors.adn.networklayer.com`. 
- **Fix:** Update the default `source.list` to use `mirrors.adn.networklayer.com`. Add an `APT` stanza in `cloud-init` to overwrite fully qualified domain name that is hard coded in Linux images


## RIOS-129: Inconsistent image names between VPC and VPC on Classic
{: #RIOS-129}
- **Symptom:** The names of stock images are different in VPC and VPC on Classic environments. The expected behavior is that names are consistent across VPC and VPC on Classic unified images.
- **Fix:** The image service is being rolled out in VPC on Classic first. When the service is deployed in VPC, unified image names across VPC on Classic and VPC will match, as the same image service will be running in both environments.
