---
title: "Configuración de la administración del acceso a aplicaciones de autoservicio en Azure Active Directory | Microsoft Docs"
description: "Creación y administración de grupos de seguridad o de grupos de Office 365 en Azure Active Directory y solicitud de la pertenencia a grupos de Office 365 o de seguridad"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 7a7370eb076ba8602a58a260a14bb863c55bc803
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Configuración de Azure Active Directory para la administración de grupos de autoservicio
Los usuarios pueden crear y administrar sus propios grupos de seguridad o grupos de Office 365 en Azure Active Directory (Azure AD). Los usuarios también pueden solicitar la pertenencia a grupos de seguridad o a grupos de Office 365; a continuación, el propietario del grupo puede aprobarla o rechazarla. El control diario de la pertenencia a grupos se puede delegar a personas que conocen el contexto empresarial de dicha pertenencia. Las características de administración de grupos de autoservicio solo están disponibles para grupos de seguridad y de Office 365, no para grupos de seguridad universal habilitados para correo electrónico ni listas de distribución.

La administración de grupos de autoservicio actualmente atiende dos escenarios esenciales: la administración de grupos delegados y la administración de grupos de autoservicio.

* **Administración de grupos delegados** Por ejemplo, un administrador que administra el acceso a una aplicación SaaS que su compañía usa. La administración de estos derechos de acceso se está volviendo compleja, por lo que este administrador solicita al propietario de la empresa la creación de un nuevo grupo. El administrador asigna acceso a la aplicación al grupo nuevo y agrega a dicho grupo todas las personas que ya acceden a la aplicación. Luego, el propietario de la empresa puede agregar más usuarios, y dichos usuarios se aprovisionan automáticamente en la aplicación. No es preciso que el propietario espere a que el administrador administre el acceso de los usuarios. Si el administrador concede el mismo permiso a un administrador de otro grupo de negocios, dicha persona también puede administrar el acceso de sus propios usuarios. Ni el propietario de una empresa ni el administrador pueden ver o administrar los usuarios del otro. El administrador podrá seguir viendo todos los usuarios que tienen acceso a la aplicación y bloquear los derechos de acceso si fuera necesario.
* **Administración de grupos de autoservicio** Por ejemplo, dos usuarios tienen sitios de SharePoint Online configurados de forma independiente. Ellos desean que los equipos del otro puedan acceder a sus sitios. Para ello, pueden crear un grupo en Azure AD, y en SharePoint Online cada uno de ellos selecciona dicho grupo para proporcionar acceso a sus sitios. Cuando alguien desea tener acceso, lo solicita en el Panel de acceso, y tras la aprobación obtiene acceso a ambos sitios de SharePoint Online automáticamente. Posteriormente, uno de ellos decide que todas las personas que accedan a su sitio también deben obtener acceso a una aplicación SaaS concreta. El administrador de la aplicación SaaS puede agregar derechos de acceso a la aplicación en el sitio de SharePoint Online. Desde ese momento, todas las solicitudes aprobadas darán acceso tanto a los dos sitios de SharePoint Online como a la aplicación SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Puesta a disposición de un grupo para el autoservicio del usuario
1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos** y, luego, **Todos los usuarios**.
3. Establezca **Administración de grupos de autoservicio habilitada** en **Sí**.
4. Establezca **Los usuarios pueden crear grupos de seguridad** o **Los usuarios pueden crear grupos de Office 365** en **Sí**.
  * Si estas opciones están habilitadas, todos los usuarios del directorio pueden crear nuevos grupos de seguridad y agregarles miembros. Estos grupos nuevos también se muestran en el Panel de acceso para los restantes usuarios. Si la configuración de la directiva en el grupo lo permite, otros usuarios pueden crear solicitudes para unirse a dichos grupos. 
  * Si están deshabilitadas, los usuarios no pueden crear grupos ni pueden cambiar los grupos existentes de los que sean propietarios. Sin embargo, pueden administrar la pertenencia a dichos grupos y aprobar las solicitudes de otros usuarios para unirse a ellos.

También puede utilizar la opción **Usuarios que pueden administrar los grupos de seguridad** y **Usuarios que pueden administrar los grupos de Office 365** para conseguir un control de acceso más específico sobre la administración de grupos de autoservicio para los usuarios. Cuando la opción **Los usuarios pueden crear grupos** esté habilitada, todos los usuarios de su inquilino podrán crear nuevos grupos y agregarles miembros. Al configurarla con **Algunos**, restringe el grupo de administración a solo un grupo limitado de usuarios. Si este modificador se establece en **Algunos**, debe agregar usuarios al grupo SSGMSecurityGroupsUsers para poder crear grupos nuevos y agregarles miembros. Si en **Usuarios que pueden utilizar el autoservicio para grupos de seguridad** y **Usuarios que pueden administrar grupos de Office 365** se selecciona **Todos**, todos los usuarios del inquilino pueden crear nuevos grupos.

También puede usar **Grupo que puede administrar los grupos de seguridad** o **Grupo que puede administrar los grupos de Office 365** para especificar un único grupo cuyos miembros pueden utilizar el autoservicio.

## <a name="next-steps"></a>Pasos siguientes
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integre las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
