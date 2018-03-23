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
ms.openlocfilehash: 4bc5644425aa11fb210d81095e4166baefc6432e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Habilitación de un proveedor de servicios en la nube para administrar la suscripción de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Si está utilizando Azure Stack con un proveedor de servicios en la nube (CSP), el acceso a los recursos de su suscripción a Azure y de Azure Stack lo puede administrar el proveedor. O bien, puede administrar su propia suscripción. En este artículo se analiza cómo puede permitir que su proveedor de servicios acceda a su suscripción en su nombre, o para asegurarse de que el proveedor de servicios puede administrar su servicio.

> [!Note]  
>  Si se omiten los siguientes pasos y el CSP ya no está administrando su cuenta, el CSP no podrá administrar su suscripción a Azure Stack en su nombre.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Administración de la suscripción con un proveedor de servicios en la nube

1. Agregue el CSP como usuario invitado con el rol de usuario a su directorio de inquilino.  Para conocer los pasos sobre cómo agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).
2. El CSP, a continuación, creará automáticamente la suscripción local de Azure Stack.
3. Está listo para empezar a usar Azure Stack.
3. El CSP debe entonces crear un recurso en la suscripción para comprobar que también puede administrar sus recursos. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Habilitación del proveedor de servicios en la nube para administrar la suscripción con derechos de RBAC

Agregue el CSP como propietario a su suscripción. 

1. Agregue el CSP como usuario invitado, con el rol de propietario en el directorio de inquilino.  Para conocer los pasos sobre cómo agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).
2. Agregue el rol de propietario al usuario invitado de CSP. Para conocer los pasos para agregar el usuario de CSP a la suscripción, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).
3. El CSP, a continuación, creará automáticamente la suscripción local de Azure Stack.
4. Está listo para empezar a usar Azure Stack.
5. El CSP debe entonces crear un recurso en la suscripción para comprobar que puede administrar sus recursos. 

## <a name="next-steps"></a>Pasos siguientes

  - Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](../azure-stack-billing-and-chargeback.md).
