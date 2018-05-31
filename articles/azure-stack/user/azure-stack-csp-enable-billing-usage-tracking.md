---
title: Habilitación de un proveedor de servicios en la nube para administrar la suscripción de Azure Stack | Microsoft Docs
description: Habilite al proveedor de servicios para que tenga acceso a una suscripción en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357850"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitación de un proveedor de servicios en la nube para administrar la suscripción de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Si utiliza Azure Stack con un proveedor de servicios en la nube (CSP), puede elegir administrar su propia suscripción para acceder a los recursos en Azure y Azure Stack. También puede permitir que el proveedor administre la suscripción automáticamente. Este artículo muestra cómo:

 * Otorgue acceso al proveedor de servicios a su suscripción.
 * Asegúrese de que el proveedor de servicios pueda administrar el servicio.

> [!Note]
>  Si el CSP no administra su cuenta, y usted omite los pasos siguientes, el CSP no podrá administrar automáticamente la suscripción a Azure Stack.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Administración de la suscripción con un proveedor de servicios en la nube

Agregue el CSP como **usuario** a su suscripción.

1. Agregue el CSP como usuario invitado con el rol de usuario a su directorio de inquilino.  Para conocer los pasos sobre cómo agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).
2. El CSP crea automáticamente la suscripción local de Azure Stack.
3. Está listo para empezar a usar Azure Stack.
4. El CSP debe crear un recurso en la suscripción para comprobar que también puede administrar sus recursos. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitación del proveedor de servicios en la nube para administrar la suscripción con derechos de RBAC

Agregue el CSP como **propietario** a su suscripción.

1. Agregue el CSP como usuario invitado a su directorio de inquilino.  Para conocer los pasos sobre cómo agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).
2. Agregue el rol de propietario al usuario invitado de CSP. Para conocer los pasos para agregar el usuario de CSP a la suscripción, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
3. El CSP crea automáticamente la suscripción local de Azure Stack.
4. Está listo para empezar a usar Azure Stack.
5. El CSP debe crear un recurso en la suscripción para comprobar que puede administrar sus recursos.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](../azure-stack-billing-and-chargeback.md).
