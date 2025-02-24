---
title: "How to export an OS Login certificate"
description: "Follow this guide to locally export an OS Login certificate so it can be used to connect over SSH to VMs with OS Login access configured."
---

# Exporting an OS Login certificate

{% include notitle [preview](../../../_includes/note-preview-by-request.md) %}

You can use a certificate to enable a user or a third-party tool, such as [Terraform](https://www.terraform.io/) or [Ansible](https://www.ansible.com/), to use SSH to connect to a VM with OS Login access enabled. To do this, locally export an OS Login certificate and use it to access a VM using a standard SSH client.

{% include [oslogin-export-certificate](../../../_includes/compute/oslogin-export-certificate.md) %}

You can use the exported OS Login certificate to [connect](./os-login.md#connect-via-exported-certificate) to a VM with OS Login access [configured](../vm-control/vm-update.md#enable-oslogin-access).