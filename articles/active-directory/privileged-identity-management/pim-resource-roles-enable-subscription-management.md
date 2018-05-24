---
title: 'Privileged Identity Management para recursos de Azure: habilitar la administración de suscripciones | Microsoft Docs'
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
ms.openlocfilehash: e164d8adaf5df63dba31bb6aa8e56f768741479c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200882"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Habilitar la administración de suscripciones

Como administrador global del directorio, puede que no tenga acceso de forma predeterminada a todos los recursos de suscripción del inquilino. En este artículo se describen los pasos para dar acceso a todas las suscripciones en el inquilino. También se proporciona un enfoque recomendado para seguir cumpliendo con los controles de seguridad que la organización requiere después de recibir acceso.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>¿Quién puede habilitar la administración de suscripciones en mi directorio?

Cada usuario que tiene asignado el rol de administrador global debe seguir los pasos siguientes para habilitar la administración de suscripciones. Una vez que haya habilitado la administración de suscripciones para usted mismo, puede agregar otros administradores globales que también necesiten obtener acceso a los recursos. No hay ninguna configuración de directorio que permita el acceso de todos los miembros del rol de administrador global.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en Azure Portal con una cuenta válida o con un miembro activo del rol de administrador global. Si la cuenta es un administrador global válido, primero debe activar el rol antes de continuar con el siguiente paso.

## <a name="access-the-azure-active-directory-admin-center"></a>Obtenga acceso al centro de administración de Azure Active Directory

Ahora que ha iniciado sesión en Azure Portal como administrador global, puede modificar la configuración que le proporcionará acceso a las suscripciones de Azure. Vaya al centro de administración de Azure Active Directory (Azure AD) y seleccione **Propiedades**.

![Captura de pantalla del centro de administración de Azure AD, con las propiedades resaltadas](media/azure-pim-resource-rbac/aad_properties.png)

En la lista de propiedades, seleccione **Sí** en **Global admin can manage Azure subscriptions** (El administrador global puede administrar suscripciones de Azure).

![Captura de pantalla de la página Propiedades, con el control de alternancia establecido en Sí](media/azure-pim-resource-rbac/aad_properties_save.png)

Con esta opción habilitada, su cuenta se agrega automáticamente al rol de administrador de acceso de usuario para cada recurso de suscripción del inquilino.

## <a name="browse-to-azure-ad-pim"></a>Vaya a PIM de Azure AD

 Aquí, vaya a Azure AD Privileged Identity Management (PIM). En **Administrar**, seleccione **Recursos de Azure**.

![Captura de pantalla de PIM, con la opción de recursos de Azure resaltada](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Administración y detección de recursos

Si su organización ya usa PIM de Azure AD para proteger a los administradores de Azure AD, verá una lista de suscripciones cuando se cargue la hoja.

![Captura de pantalla de PIM, con la lista de suscripciones que se muestran en la hoja](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Si no ve los recursos, compruebe lo siguiente:
>- Que el rol de administrador global no haya expirado. 
>- Que la organización tiene una suscripción de Azure.

![Captura de pantalla de PIM, con la lista de recursos vacía](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Configuración de asignaciones

Seleccione una suscripción de la lista y asígnese usted mismo (o a un grupo del que sea miembro) como propietario apto del recurso. 
[Más información sobre la asignación de roles](pim-resource-roles-assign-roles.md).

Repita este proceso con cada recurso antes de continuar con el paso siguiente.

## <a name="clean-up-standing-access"></a>Limpieza del acceso permanente

Ahora que tiene asignaciones aptas para las suscripciones importantes de la organización, puede limpiar el acceso permanente mediante la deshabilitación de la opción en las propiedades del directorio.

![Captura de pantalla de la página Propiedades, con el control de alternancia establecido en No](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Pasos siguientes

[Detección de recursos](pim-resource-roles-discover-resources.md)

[Configurar los roles](pim-resource-roles-configure-role-settings.md)








