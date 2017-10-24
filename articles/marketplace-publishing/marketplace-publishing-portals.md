---
title: "Introducción a los diversos portales necesarios para crear una oferta en Marketplace | Microsoft Docs"
description: "Información general de los diversos portales necesarios para crear una oferta en Marketplace"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio
ms.openlocfilehash: 093d3ee3ecce89c86a12e7e216072ece57f82f9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="portals-you-will-need"></a>Portales que necesitará
Antes de que empiece el proceso de la publicación de una oferta, vamos a presentarle los diversos portales que necesitará. A continuación se muestra un resumen breve de los portales: Centro de desarrolladores, Portal de publicación de Azure y Portal de Azure, en el orden en que se interactúa con ellos.                                                                            

## <a name="developer-center"></a>Centro de desarrolladores
Vaya a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure).

### <a name="description"></a>Descripción
La creación de la cuenta del Centro de desarrolladores de Microsoft es una tarea que solo se realiza una vez. Asegúrese de que la compañía no tenga ya una cuenta del Centro de desarrolladores antes de intentar crear una nueva. Durante el proceso, recopilamos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía.

> [!NOTE]
> Si solo va a publicar ofertas gratuitas (u ofertas de tipo traiga su propia licencia), no necesitamos información bancaria ni fiscal.
> 
> 

### <a name="identityaccount-used"></a>Identidad y cuenta usadas
Idealmente, esta es una lista de distribución o un grupo de seguridad (por ejemplo, azurepublishing@*partnercompany*.com). La lista de distribución o el grupo de seguridad **debe** registrarse como cuenta de Microsoft.

> [!TIP]
> Se recomienda usar una lista de distribución o un grupo de seguridad porque que elimina la dependencia de cualquier persona aunque también se puede usar una cuenta individual.
> 
> 

## <a name="publishing-portal"></a>Portal de publicación
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>Descripción
Este es el portal para que usa para trabajar en la ofertas y publicarla (marketing, precios, publicación, certificación si es aplicable, etc.).

### <a name="identityaccount-used"></a>Identidad y cuenta usadas
Se debe usar la lista de distribución o el grupo de seguridad por primera vez para iniciar sesión en el portal de publicación. Más adelante, se pueden agregar otros usuarios como coadministradores. Así es cómo se asignan a los datos de registro del Centro para desarrolladores.

## <a name="azure-portal"></a>Portal de Azure
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>Descripción
Este es el portal donde puede ver sus ofertas publicadas y de ensayo en Azure Marketplace (aplicable a máquinas virtuales, plantillas de solución y servicios de desarrolladores basados en el Administrador de recursos de Azure).

### <a name="identityaccount-used"></a>Identidad y cuenta usadas
Mientras pone en ensayo una oferta del portal de publicación, es preciso que se incluya un identificador de suscripción en la lista blanca. Para probar la oferta de ensayo, hay que usar la misma suscripción (no hay nombre de usuario ni contraseña asociados) al iniciar sesión en este portal.

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

