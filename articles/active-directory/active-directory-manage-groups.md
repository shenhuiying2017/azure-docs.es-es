---
title: Uso de grupos para administrar el acceso a recursos en Azure Active Directory | Microsoft Docs
description: "Cómo usar grupos en Azure Active Directory para administrar el acceso de los usuarios a recursos y aplicaciones en la nube y locales."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 714120d0-cdf9-465d-afee-39bef591c6b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 3a4764ddf2dea68bf25bd46b9691429ad3341678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Administración del acceso a recursos con grupos de Azure Active Directory
Azure Active Directory (Azure AD) es una completa solución de administración de identidades y acceso que proporciona un sólido conjunto de capacidades para administrar el acceso a aplicaciones locales y en la nube y recursos de Microsoft Online Services como Office 365 y todo un mundo de aplicaciones SaaS que no son de Microsoft. En este artículo se proporciona información general, pero si desea empezar a usar grupos de Azure AD ahora mismo, siga las instrucciones indicadas en [Administrar grupos de seguridad en Azure AD](active-directory-accessmanagement-manage-groups.md). Para ver cómo se puede usar PowerShell para administrar grupos en Azure Active Directory, consulte [Cmdlets de Azure Active Directory para administrar grupos](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Para usar Azure Active Directory, necesita una cuenta de Azure. Si aún no tiene ninguna, puede [registrarse para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).
>
>

Dentro de Azure AD, una de las principales características es la capacidad para administrar el acceso a los recursos. Estos recursos pueden formar parte del directorio, como en el caso de los permisos para administrar objetos a través de roles en el directorio o los recursos externos al directorio, como las aplicaciones SaaS, los servicios de Azure y los sitios de SharePoint o los recursos publicados en modo local. Existen cuatro formas de asignar a un usuario derechos de acceso a un recurso:

1. Asignación directa

    El propietario de un recurso puede entonces asignar directamente usuarios al mismo.
2. Pertenencia a grupos

    El propietario de un recurso puede asignarle un grupo y, al hacerlo, conceder acceso al recurso a los miembros de ese grupo. El propietario del grupo puede administrar entonces la pertenencia dicho grupo. El propietario del recurso delega efectivamente en el propietario del grupo el permiso para asignar a usuarios a sus recursos.
3. Basado en reglas

    El propietario del recurso puede usar una regla para indicar a qué usuarios se les debe asignar acceso a un recurso. El resultado de la regla depende de los atributos utilizados en dicha regla y de sus valores para usuarios específicos; si se hace así, el propietario del recurso delega efectivamente los derechos de administración de acceso en el origen de autoridad de los atributos usados en la regla. El propietario del recurso sigue pudiendo administrar la regla propiamente dicha y determina qué atributos y valores proporcionan acceso a su recurso.
4. Autoridad externa

    El acceso a un recurso se deriva de un origen externo, por ejemplo, un grupo que se sincroniza desde un origen de autoridad, como un directorio local o una aplicación de SaaS como WorkDay. El propietario del recurso asigna al grupo para proporcionar acceso al recurso y el origen externo administra a los miembros del grupo.

   ![Información general del diagrama de administración del acceso](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Vea un vídeo que explica la administración del acceso
Puede ver un breve vídeo que explica más detalladamente este tema:

**Azure AD: Introducción a las pertenencias dinámicas para grupos**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>¿Cómo funciona la administración de acceso en Azure Active Directory?
En el centro de la solución de administración de acceso de Azure AD se encuentra el grupo de seguridad. Usar un grupo de seguridad para administrar el acceso a los recursos es un paradigma conocido, lo que permite contar con una forma flexible y fácil de entender de proporcionar acceso a un recurso para el grupo de usuarios previsto. El propietario de los recursos (o el administrador del directorio) puede asignar un grupo para proporcionar determinados derechos de acceso a los recursos que posee. Los miembros del grupo recibirán el derecho de acceso y el propietario del recurso puede delegar en otra persona el derecho de administrar la lista de miembros de un grupo, por ejemplo, un administrador de departamento o un administrador de soporte técnico.

![Diagrama de administración de acceso de Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

El propietario de un grupo también puede hacer que ese grupo esté disponible para solicitudes de autoservicio. De esta forma, un usuario final puede buscar y encontrar el grupo, y solicitar unirse a él pidiendo efectivamente permiso para tener acceso a los recursos que se administran a través del grupo. El propietario del grupo puede configurar el grupo para que las solicitudes de pertenencia se aprueben automáticamente o requieran la aprobación por parte del propietario del grupo. Cuando un usuario realiza una solicitud para unirse a un grupo, la solicitud de pertenencia se reenvía a los propietarios del grupo. Si uno de los propietarios aprueba la solicitud, el usuario solicitante recibe una notificación y se une al grupo. Si uno de los propietarios rechaza la solicitud, el usuario solicitante recibe una notificación pero no se une al grupo.

## <a name="getting-started-with-access-management"></a>Introducción a administración de accesos
¿Ya está listo para comenzar? Pruebe algunas de las tareas básicas que se pueden hacer con los grupos de Azure AD. Utilice estas capacidades para proporcionar acceso especializado a distintos grupos de personas para los diferentes recursos de la organización. A continuación se incluye una lista de los primeros pasos básicos.

* [Crear una regla sencilla para configurar pertenencias dinámicas a un grupo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)
* [Uso de un grupo para administrar el acceso a las aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md)
* [Puesta a disposición de un grupo para el autoservicio del usuario final](active-directory-accessmanagement-self-service-group-management.md)
* [Sincronización de un grupo local con Azure mediante Azure AD Connect](active-directory-aadconnect.md)
* [Administración de propietarios de un grupo](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha comprendido los conceptos básicos de la administración de accesos, presentamos algunas capacidades avanzadas adicionales disponibles en Azure Active Directory para administrar el acceso a sus aplicaciones y recursos.

* [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md)
* [Administración de grupos de seguridad en Azure AD](active-directory-accessmanagement-manage-groups.md)
* [Configuración de grupos dedicados en Azure AD](active-directory-accessmanagement-dedicated-groups.md)
* [Referencia de API Graph para grupos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
