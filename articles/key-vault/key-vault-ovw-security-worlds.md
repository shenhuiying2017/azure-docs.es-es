---
ms.assetid: 
title: Espacios de seguridad de Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Espacios de seguridad y límites geográficos de Azure Key Vault

Azure Key Vault es un servicio multiinquilino y usa un grupo de módulos de seguridad de hardware (HSM) en cada ubicación de Azure. 

Todos los HSM en ubicaciones de Azure de la misma región geográfica comparten el mismo límite criptográfico (Thales Security World). Por ejemplo, las regiones de este de EE. UU. y oeste de EE. UU. comparten en mismo espacio de seguridad porque pertenecen a la ubicación geográfica de EE. UU. Del mismo modo, todas las ubicaciones de Azure en Japón comparten el mismo entorno de seguridad, así como todas las ubicaciones de Azure en Australia, India, etc. 

Una copia de seguridad tomada de una clave en una instancia de Key Vault de una ubicación de Azure puede restaurarse en una instancia de Key Vault de otra ubicación de Azure, siempre que se den estas dos condiciones:

- Las dos ubicaciones de Azure pertenecen a la misma ubicación geográfica
- Las dos instancias de Key Vault pertenecen a la misma suscripción de Azure

Por ejemplo, una copia de seguridad tomada por una suscripción determinada de una clave en una instancia de Key Vault de oeste de la Indica solo se puede restaurar en otra instancia de Key Vault de la misma suscripción y ubicación geográfica: oeste de la India, centro de la India o India del Sur. 



