---
title: 'Privileged Identity Management para Azure Resources: habilitación de la administración de suscripciones | Microsoft Docs'
description: Conozca cómo los administradores globales pueden administrar suscripciones en el inquilino.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Habilitación de la administración de suscripciones

Como administrador global de su directorio, puede que no tenga acceso de forma predeterminada a todos los recursos de suscripción del inquilino. En este artículo se describen los pasos para proporcionarse acceso a usted mismo a todas las suscripciones del inquilino, y un método recomendado para el cumplimiento restante con los controles de seguridad que necesita su organización después de recibir el acceso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>¿Quién puede habilitar la administración de suscripciones en mi directorio?

Cada usuario que tiene asignado el rol Administrador Global debe seguir los pasos siguientes para habilitar la administración de suscripciones. Una vez que ha habilitado la administración de suscripciones para usted mismo, puede agregar otros administradores globales que es posible que también necesiten acceso a recursos. No hay ninguna configuración de directorio que permita el acceso a todos los miembros del rol Administrador global.

## <a name="log-on-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Para comenzar, inicie sesión en Azure Portal con una cuenta válida o con un miembro activo del rol Administrador global. Si la cuenta es un administrador global válido, primero debe activar el rol antes de continuar con el siguiente paso.

## <a name="access-the-azure-ad-admin-center"></a>Acceso al centro de administración de Azure AD

Ahora que ha iniciado sesión en Azure Portal como administrador global, puede modificar la configuración que proporcionará acceso a las suscripciones de Azure. Vaya al centro de administración de Azure AD y busque y seleccione la pestaña Propiedades en el panel de navegación izquierdo.

![](media/azure-pim-resource-rbac/aad_properties.png)

En la lista de propiedades, cambie la opción titulada "Global admin can manage Azure subscriptions" (El administrador global puede administrar suscripciones de Azure) a "Sí".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Navegación a PIM de Azure AD

Con esta opción habilitada, su cuenta se agrega automáticamente al rol "Administrador de acceso de usuario" con cada recurso de suscripción del inquilino. Desde aquí, vaya a PIM de Azure AD y seleccione los recursos de Azure en la sección Administrar del panel de navegación izquierdo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Administración y detección de recursos

Si su organización ya usa PIM de Azure AD para proteger a los administradores de Azure Active Directory, verá una lista de suscripciones cuando se cargue la hoja.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Si no ve todos los recursos, compruebe lo siguiente:
>- El rol de administrador global no ha expirado 
>- Su organización tiene una suscripción de Azure

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configuración de asignaciones

Seleccione una suscripción de la lista y asígnese usted mismo (o a un grupo del que sea miembro) como propietario apto del recurso. 
[Más información sobre la asignación de roles](pim-resource-roles-assign-roles.md).

Repita este proceso con cada recurso antes de continuar con el paso siguiente.

## <a name="clean-up-standing-access"></a>Limpieza del acceso permanente

Ahora que tiene asignaciones aptas para las suscripciones importantes de su organización, puede limpiar el acceso permanente mediante la deshabilitación de la opción en las propiedades del directorio:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Pasos siguientes

[Detección de recursos](pim-resource-roles-discover-resources.md)

[Configurar los roles](pim-resource-roles-configure-role-settings.md)








