---
title: Características de datos del cliente de Azure Key Vault | Microsoft Docs
description: Obtenga información sobre los datos del cliente de Key Vault.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/15/2018
ms.author: barclayn
ms.openlocfilehash: 12a176a6eba3b6dd83ef7fb97f89ecfe99083059
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213324"
---
# <a name="azure-key-vault-customer-data-features"></a>Características de datos del cliente de Azure Key Vault

Azure Key Vault recibe los datos del cliente durante la creación o actualización de almacenes, claves, secretos, certificados y cuentas de almacenamiento administradas. Estos datos del cliente se pueden ver directamente en Azure Portal y con la API de REST. Los datos del cliente se pueden editar o eliminar mediante la actualización o eliminación del objeto que los contiene.

Los registros de acceso del sistema se generan cuando un usuario o una aplicación acceden a Key Vault. Los clientes tienen a su disposición los registros de acceso detallados en Azure Insights.

## <a name="identifying-customer-data"></a>Identificación de los datos del cliente

La información siguiente identifica los datos del cliente en Azure Key Vault:

- Las directivas de acceso de Azure Key Vault contienen identificadores de objeto que representan a usuarios, grupos o aplicaciones.
- Los asuntos de los certificados pueden incluir direcciones de correo electrónico u otros identificadores de usuarios o de la organización.
- Los contactos de los certificados pueden contener direcciones de correo electrónico, nombres o números de teléfono de los usuarios.
- Los emisores de certificados pueden contener direcciones de correo electrónico, nombres, números de teléfono, credenciales de cuentas y detalles de la organización.
- Se pueden aplicar etiquetas arbitrarias a objetos en Azure Key Vault. Estos objetos incluyen almacenes, claves, secretos, certificados y cuentas de almacenamiento. Las etiquetas utilizadas pueden contener datos personales.
- Los registros de acceso de Azure Key Vault contienen identificadores de objetos, [UPN](../active-directory/connect/active-directory-aadconnect-userprincipalname.md) y direcciones IP para cada llamada API de REST.
- Los registros de diagnóstico de Azure Key Vault pueden contener identificadores de objetos y direcciones IP para las llamadas API de REST.

## <a name="deleting-customer-data"></a>Eliminación de datos del cliente

Las mismas API de REST, la experiencia del portal y los SDK utilizados para crear almacenes, claves, secretos, certificados y cuentas de almacenamiento administradas pueden actualizar y eliminar también estos objetos.

La eliminación temporal le permite recuperar los datos eliminados durante los noventa días posteriores a la eliminación. Cuando se usa la eliminación temporal, se puede realizar una operación de purga para eliminar los datos de forma permanente antes de que expire el período de retención de noventa días. Si el almacén o la suscripción se han configurado para bloquear operaciones de purga, no es posible eliminar permanentemente los datos hasta que haya transcurrido el período de retención programado.

## <a name="exporting-customer-data"></a>Exportación de datos del cliente

Las mismas API de REST, la experiencia del portal y los SDK utilizados para crear almacenes, claves, secretos, certificados y cuentas de almacenamiento administradas también son las que le permiten ver y exportar estos objetos.

El registro de acceso de Azure Key Vault es una característica opcional que se puede activar para generar registros para cada llamada API de REST. Estos registros se transferirán a una cuenta de almacenamiento de la suscripción en la que aplica la directiva de retención que cumple los requisitos de la organización.

Los registros de diagnóstico de Azure Key Vault que contienen datos personales se pueden recuperar con una solicitud de exportación en el portal de privacidad del usuario. Esta solicitud se debe realizar mediante el administrador de inquilinos.

## <a name="next-steps"></a>Pasos siguientes

- [Registro de Azure Key Vault](key-vault-logging.md)

- [Información general sobre la eliminación temporal de Azure Key Vault](key-vault-soft-delete-cli.md)

- [Almacenes](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Operaciones con claves de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operaciones con secretos de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certificados y directivas de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Emisores de certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Operaciones con cuentas de almacenamiento de Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
