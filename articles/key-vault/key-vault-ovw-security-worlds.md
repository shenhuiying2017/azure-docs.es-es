---
ms.assetid: 
title: Espacios de seguridad de Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 921bbd109c9ea98d8b5c286a7512bed026412d26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Espacios de seguridad y límites geográficos de Azure Key Vault

Azure Key Vault es un servicio multiinquilino y usa un grupo de módulos de seguridad de hardware (HSM) en cada ubicación de Azure. 

Todos los HSM en ubicaciones de Azure de la misma región geográfica comparten el mismo límite criptográfico (Thales Security World). Por ejemplo, las regiones de este de EE. UU. y oeste de EE. UU. comparten en mismo espacio de seguridad porque pertenecen a la ubicación geográfica de EE. UU. Del mismo modo, todas las ubicaciones de Azure en Japón comparten el mismo entorno de seguridad, así como todas las ubicaciones de Azure en Australia, India, etc. 

## <a name="backup-and-restore-behavior"></a>Tareas de copia de seguridad y restauración

Una copia de seguridad tomada de una clave en una instancia de Key Vault de una ubicación de Azure puede restaurarse en una instancia de Key Vault de otra ubicación de Azure, siempre que se den estas dos condiciones:

- Las dos ubicaciones de Azure pertenecen a la misma ubicación geográfica
- Las dos instancias de Key Vault pertenecen a la misma suscripción de Azure

Por ejemplo, una copia de seguridad tomada por una suscripción determinada de una clave en una instancia de Key Vault de oeste de la Indica solo se puede restaurar en otra instancia de Key Vault de la misma suscripción y ubicación geográfica: oeste de la India, centro de la India o India del Sur.

## <a name="regions-and-products"></a>Productos y regiones

- [Regiones de Azure](https://azure.microsoft.com/regions/)
- [Productos de Microsoft por región](https://azure.microsoft.com/regions/services/)

Las regiones se asignan a los ámbitos de seguridad, que se muestran como encabezados principales en las tablas:

En el artículo de productos por región, por ejemplo, la pestaña **América** contiene Este de EE. UU. , Centro de EE. UU. y Oeste de EE. UU. y todo ello se asigna a la región América. 

>[!NOTE]
>Una excepción es que el Departamento de Defensa del este y del centro de Estados Unidos tienen sus propios ámbitos de seguridad. 

De igual forma, en la pestaña **Europa**, tanto Europa del Norte como Europa occidental se asignan a la región de Europa. Lo mismo sirve también para la pestaña **Asia-Pacífico**.



