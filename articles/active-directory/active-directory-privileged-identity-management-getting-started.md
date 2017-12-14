---
title: "Introducción a Azure AD Privileged Identity Management | Microsoft Docs"
description: "Aprenda a administrar identidades con privilegios con la aplicación Privileged Identity Management de Azure Active Directory en el Portal de Azure."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 11b5d1bc4e931ad902b623fe55e62097ae40d8ef
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Comenzar a usar Azure AD Privileged Identity Management

Con Privileged Identity Management de Azure Active Directory (AD), puede administrar, controlar y supervisar el acceso dentro de su organización. Este ámbito incluye el acceso a los recursos de recursos de Azure, Azure AD y otros servicios de Microsoft Online Services, como Office 365 o Microsoft Intune.

En este artículo se explica cómo agregar la aplicación PIM (Privileged Identity Management) de Azure AD al panel del Portal de Azure.

## <a name="add-the-privileged-identity-management-application"></a>Incorporación de la aplicación Privileged Identity Management

Antes de usar Privileged Identity Management de Azure AD, debe agregar la aplicación al panel del Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global de su directorio.
2. Si su organización tiene más de un directorio, seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure. Seleccione el directorio en el que quiere usar PIM.
3. Seleccione **Más servicios** y use el cuadro de texto Filtro para buscar **Azure AD Privileged Identity Management**.
4. Active **Anclar al panel** y haga clic en **Crear**. Se abre la aplicación Privileged Identity Management.

Si es la primera persona que usa Azure AD Privileged Identity Management en el directorio, se le asignarán automáticamente los roles de **Administrador de seguridad** y **Administrador de roles con privilegios** del directorio. Solo los administradores de rol con privilegios pueden administrar las asignaciones de roles de directorio de los usuarios de Azure AD. Además, puede elegir ejecutar el [asistente para la seguridad.](active-directory-privileged-identity-management-security-wizard.md) Esto le lleva a través de la experiencia inicial de detección y asignación.

## <a name="navigate-to-your-tasks"></a>Navegación a sus tareas

Después de configurar Azure AD Privileged Identity Management, verá la hoja de navegación cada vez que abra la aplicación. Utilice esta hoja para realizar las tareas de administración de identidades.

![Tareas de nivel superior para PIM (captura de pantalla)](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **My Roles** (Mis roles) muestra una lista de roles válidos y activos que tiene asignados. Aquí es donde puede activar cualquier función elegible asignada.
- **Aprobar solicitudes (versión preliminar)** muestra una lista de las solicitudes de los usuarios en su directorio para activar roles de directorio elegibles de Azure AD que usted tiene designados para aprobar. [Más información.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **Solicitudes pendientes (versión preliminar)** muestra cualquiera de las solicitudes pendientes para activar las asignaciones de rol elegibles.
- **Revisar acceso** enumera las revisiones de acceso activas que tiene asignadas para completar, tanto si revisa su acceso como el de otro usuario.
- **Roles de directorios de Azure AD** situado en la sección de administración del menú de navegación a la izquierda, es el panel para que los administradores de roles con privilegios administren las asignaciones de roles, cambien la configuración de activación de roles e inicien revisiones del acceso, entre otras operaciones. Este panel está deshabilitado para todos aquellos que no sean administradores de roles con privilegios. Estos usuarios tienen acceso a un panel especial denominado My view (Mi vista). El panel My view (Mi vista) solo muestra información sobre el acceso al panel del usuario, no del inquilino completo.
- **Recursos de Azure (versión preliminar)** situado en la sección de administración del menú de navegación a la izquierda, muestra una lista de recursos de suscripción para los que tiene que elegir las asignaciones de rol 

## <a name="next-steps"></a>Pasos siguientes
En la información general de [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) , se incluye más información sobre cómo administrar el acceso administrativo en una organización.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
