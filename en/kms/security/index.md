---
title: "Access management in {{ kms-full-name }}"
description: "Access management in {{ kms-full-name }}, an encryption key management system. This section describes which resources you can assign roles to, which roles exist in the service, and which roles are required for particular actions."
---

# Access management in {{ kms-name }}

In this section, you will learn:
* [Which resources you can assign roles to](#resources).
* [Which roles exist in the service](#roles-list).
* [Which roles are required](#choosing-roles) for particular actions.

{% include [about-access-management](../../_includes/iam/about-access-management.md) %}

## Which resources you can assign roles to {#resources}

Roles can be assigned for a [cloud](../../resource-manager/concepts/resources-hierarchy.md#cloud), [folder](../../resource-manager/concepts/resources-hierarchy.md#folder), or [key](../concepts/key). These roles also apply to nested resources. For example, the editor role includes all viewer role permissions. You can find the description of each role under the chart.

## Which roles exist in the service {#roles-list}

You can manage access to {{ kms-short-name }} keys using both service and primitive roles.

{% include [roles-intro](../../_includes/roles-intro.md) %}

![image](../../_assets/kms/service-roles-hierarchy.svg)

### Service roles {#service-roles}

Service roles provide more granular control over {{ kms-short-name }} keys, taking into account the service specifics: a strict division of subjects into key administrators (the `kms.admin` role) and users (the `kms.keys.encrypterDecrypter` role).

Users without the `resource-manager.clouds.owner` or `admin` role can't assign roles via the management console.

List of service roles:

* `kms.admin`: Enables you to assign random roles for keys using the CLI and API, delete keys and key versions, and update the primary version. It includes all permissions granted by the `kms.editor` role.
* `kms.editor`: Enables you to manage keys (view, create, update, rotate keys, and encrypt and decrypt data). It includes all permissions granted by the `kms.viewer`, `kms.keys.encrypterDecrypter`, `kms.asymmetricEncryptionKeys.publicKeyViewer`, and `kms.asymmetricEncryptionKeys.decrypter` roles.
* `kms.asymmetricEncryptionKeys.publicKeyViewer`: Enables you to obtain a public key from an asymmetric encryption key pair.
* `kms.asymmetricSignatureKeys.publicKeyViewer`: Enables you to obtain a public key from an asymmetric key pair of a digital signature.
* `kms.asymmetricSignatureKeys.signer`: Enables you to sign data with a private key from an asymmetric key pair of a digital signature.
* `kms.asymmetricEncryptionKeys.decrypter`: Enables you to decrypt data with a private key from an asymmetric encryption key pair.
* `kms.keys.encrypterDecrypter`: Enables you to encrypt and decrypt data in symmetric mode and view information about keys. It includes all permissions granted by the `kms.keys.encrypter` and `kms.keys.decrypter` roles.
* `kms.keys.encrypter`: Enables you to [encrypt](../operations/symmetric-encryption.md#encryption) data and view information about keys.
* `kms.keys.decrypter`: Enables you to [decrypt](../operations/symmetric-encryption.md#decryption) data and view information about keys.
* `kms.viewer`: Enables you to view information about signature and encryption keys. It includes all permissions granted by the `kms.auditor` role. It does not allow you to obtain a public key.
* `kms.auditor`: Enables you to view a list of signature and encryption keys and get information about signature and encryption permissions. It does not allow you to obtain a public key.

### Primitive roles {#primitive-roles}

{% include [roles-primitive](../../_includes/roles-primitive.md) %}

## What roles do I need {#choosing-roles}

**Example of key access control**

We recommend working with roles as follows:
1. The cloud owner (the `resource-manager.clouds.owner` role) or administrator (the `admin` role) assigns the `kms.admin` role to the {{ kms-short-name }} administrator.
1. The {{ kms-short-name }} administrator creates the required number of keys and assigns the roles (through the CLI or API) to use them; subjects from different teams are given the `kms.keys.encrypter`, `kms.keys.decrypter`, `kms.asymmetricEncryptionKeys.publicKeyViewer`, `kms.asymmetricEncryptionKeys.decrypter`, and `kms.editor` roles for keys and folders.

It is good practice to store {{ kms-short-name }} keys in a dedicated folder apart from other {{ yandex-cloud }} resources.

| Action | Methods | Required roles |
----- | ----- | -----
| **{{ kms-short-name }}** | |
| Getting information about keys and versions | `get`, `listVersions` | `kms.viewer` for key for folder |
| [Symmetric encryption and decryption](../api-ref/SymmetricCrypto/) operations | `encrypt`, `decrypt`, `reEncrypt`, `generateDataKey` | `kms.keys.encrypterDecrypter` for key (encryption and decryption), `kms.keys.encrypter` for key (encryption only), and `kms.keys.decrypter` for key (decryption only) |
| Obtaining a list of keys in a folder | `list` | `kms.auditor` for folder |
| Obtaining a public key of an asymmetric encryption key pair | | `kms.asymmetricEncryptionKeys.publicKeyViewer` for key |
| Decrypting data with a private key of an asymmetric encryption key pair | | `kms.asymmetricEncryptionKeys.decrypter` for key |
| [Creating](../operations/key.md#create) and [updating](../operations/key.md#update) a key | `create`, `update` | `kms.editor` for folder |
| [Rotating a key](../operations/key.md#rotate) | `rotate` | `kms.editor` for key |
| [Changing the primary version](../operations/version.md#make-primary) | `setPrimaryVersion` | `kms.admin` for key |
| [Destroying keys](../operations/key.md#delete) and [versions](../operations/version.md#delete) | `delete`, `scheduleVersionDestruction`, `cancelVersionDestruction` | `kms.admin` for a key |
| [Granting a role](../../iam/operations/roles/grant.md), [revoking a role](../../iam/operations/roles/revoke.md) | `setAccessBindings`, `updateAccessBindings` | `kms.admin` for key |
| Viewing roles granted for a key | `listAccessBindings` | `kms.auditor` for key |

#### What's next {#what-is-next}

* [How to use {{ yandex-cloud }} securely](../../iam/best-practices/using-iam-securely.md)
* [How to assign a role](../../iam/operations/roles/grant.md).
* [How to revoke a role](../../iam/operations/roles/revoke.md).
* [Learn more about access management in {{ yandex-cloud }}](../../iam/concepts/access-control/index.md).
* [Learn more about inheriting roles](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance).
