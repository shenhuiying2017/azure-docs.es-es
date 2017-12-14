---
title: "Búsqueda de un grupo de administración o una suscripción de Azure: Azure | Microsoft Docs"
description: "Navegación entre varios directorios para ver los grupos de administración y las suscripciones"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: f1b9c1ec2af8240ff71f6907516d8894c36ac9c3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Búsqueda de un grupo de administración o una suscripción de Azure

Si tiene dificultades para encontrar una suscripción o un grupo de administración en Azure, puede que esté buscando en el directorio equivocado. Esta situación puede darse cuando la cuenta existe en varios directorios de Azure Active Directory. Cada [directorio activo es independiente](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-directory-independence) y el acceso no se hereda a través de directorios.      

![Menú Cambiar directorio](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Cambio de directorios 
Puede cambiar fácilmente los directorios en Azure Portal.
1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.  Seleccione el nombre en la parte superior derecha de la pantalla. 
3.  En la parte inferior del menú se enumeran todos los directorios a los que tiene acceso.
4.  Seleccione un nombre de directorio para acceder. 

![Menú Cambiar directorio](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>¿No está disponible el recurso? 
Si recibe el mensaje de error "Este recurso no está disponible" al intentar acceder a una suscripción o un grupo de administración, significa que no tiene el rol correcto para ver este elemento.  

![recurso no encontrado](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Póngase en contacto con el administrador de la suscripción o de los grupos de administración para conseguir acceso.  
* Para las suscripciones, consulte el documento [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) para obtener ayuda sobre qué rol se necesita.
* Para los grupos de administración, el acceso RBAC no está disponible y lo estará próximamente. Póngase en contacto con el administrador de Enterprise Portal para que le asigne acceso.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Mejora de su experiencia con grupos de administración y suscripciones en el mismo directorio 
Puede transferir las suscripciones o los grupos de administración al directorio que elija para que todo se encuentre en el mismo lugar.  La consolidación de las suscripciones y los grupos de administración en el mismo directorio ayuda a reducir la necesidad de cambiar directorios y permitir que las directivas se hereden.  


### <a name="transfer-your-subscriptions"></a>Transferencia de suscripciones 
Puede mover una suscripción al directorio asociado a los grupos de administración. Este movimiento se consigue haciendo que el administrador de inscripción transfiera su suscripción a una cuenta en el directorio de destino. Para más información, inicie sesión en [Enterprise Portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 






