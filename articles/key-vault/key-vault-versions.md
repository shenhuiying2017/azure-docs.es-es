---
title: Versiones de Key Vault
description: Las distintas versiones de Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011958"
---
# <a name="key-vault-versions"></a>Versiones de Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01: Claves de cuenta de almacenamiento administrada

Verano de 2017: la característica Claves de cuenta de almacenamiento supuso una integración más sencilla con Azure Storage. Para más información, consulte el tema de introducción, [Claves de cuenta de almacenamiento de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01: Eliminación temporal

Verano de 2017: la característica de eliminación temporal supuso una mejor protección de los datos de los almacenes de claves y de los objetos de estos. Para más información, consulte el tema de introducción [Información general sobre la eliminación temporal de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01: Administración de certificados

La administración de certificados se agregó como característica a la versión 2015-06-01 de disponibilidad general el 26 de septiembre de 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01: Disponibilidad general

La versión 2015-06-01 de disponibilidad general, se anunció el 24 de junio de 2015.

Se han realizado los siguientes cambios en esta versión:

- Eliminación de una clave: se ha quitado el campo "uso".
- Obtención de información acerca de una clave: se ha quitado el campo "uso".
- Importación de una clave en un almacén: se ha quitado el campo "uso".
- Restauración de una clave: se ha quitado el campo "uso".
- Se ha cambiado "RSA_OAEP" a "RSA-OAEP" para algoritmos RSA. Consulte [Información acerca de claves, secretos y certificados](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

La segunda versión preliminar de 2015-02-01-preview se anunció el 20 de abril de 2015. Para más información, consulte la entrada de blog [REST API Update](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) (Actualización de API REST).

Se actualizaron las siguientes tareas:

- Enumeración de las claves en un almacén: se ha agregado compatibilidad con la paginación a la operación.
- Enumeración de las versiones de una clave: se ha agregado una operación para enumerar las versiones de una clave.
- Enumeración de los secretos de un almacén: se ha agregado compatibilidad con la paginación.
- Enumeración de las versiones de un secreto: se ha agregado una operación para enumerar las versiones de un secreto.
- Todas las operaciones: se han agregado marcas de tiempo de creación o actualización a los atributos.
- Creación de un secreto: se ha agregado Content-Type a los secretos.
- Creación de una clave: se han agregado etiquetas como información opcional.
- Creación de un secreto: se han agregado etiquetas como información opcional.
- Actualización de una clave: se han agregado etiquetas como información opcional.
- Actualización de un secreto: se han agregado etiquetas como información opcional.
- Se ha cambiado el tamaño máximo de los secretos de 10 KB a 25 KB. Consulte [Información acerca de claves, secretos y certificados](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

La primera versión preliminar de 2014-12-08-preview se anunció el 8 de enero de 2015.

## <a name="see-also"></a>Otras referencias
- [Información acerca de claves, secretos y certificados](about-keys-secrets-and-certificates.md)
