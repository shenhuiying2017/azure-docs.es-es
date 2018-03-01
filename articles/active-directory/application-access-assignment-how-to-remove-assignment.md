---
title: "Cómo quitar el acceso de un usuario a una aplicación | Microsoft Docs"
description: "Cómo quitar el acceso de un usuario a una aplicación"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: aec49b108c21b97646e622c47bf69728a81d6d33
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Cómo quitar el acceso de un usuario a una aplicación

En este artículo se le ayudará a entender cómo quitar el acceso de un usuario a una aplicación.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Deseo quitar la asignación de un usuario o grupo específicos a una aplicación

Para quitar la asignación de un usuario o grupo a una aplicación, siga los pasos indicados en el artículo [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

. ## Quiero deshabilitar el acceso a una aplicación para todos los usuarios

Para deshabilitar todos los inicios de sesión de usuarios a una aplicación, siga los pasos indicados en el artículo [Deshabilitación de los inicios de sesión de usuario de una aplicación empresarial en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Quiero eliminar una aplicación por completo

Para **eliminar una aplicación**, siga las instrucciones siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **Todos los servicios** en la parte superior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea eliminar.

7.  Una vez que se cargue la aplicación, haga clic en el icono **Eliminar** del panel **Información general** de la aplicación situada en la parte superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quiero deshabilitar todas las operaciones de consentimiento de usuario futuras para todas las aplicaciones

Deshabilitar el consentimiento del usuario para todo el directorio impide que los usuarios finales den consentimiento a cualquier aplicación. A pesar de ello, los administradores podrán seguir dando el consentimiento en nombre de los usuarios. Para más información sobre el consentimiento de aplicación y los motivos por los que es posible que quiera o no quiera hacer esto, lea el artículo [Descripción del consentimiento de usuario y administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Para **deshabilitar todas las operaciones de consentimiento de usuario futuras en todo el directorio**, siga estas instrucciones:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **Todos los servicios** en la parte superior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Usuarios y grupos** en el menú de navegación.

5.  Haga clic en **Configuración de usuario**.

6.  Deshabilite todas las operaciones de consentimiento de usuario futuras estableciendo la opción **Los usuarios pueden permitir que las aplicaciones accedan a sus datos** en **No**. Después, haga clic en el botón **Guardar**.


# <a name="next-steps"></a>pasos siguientes
[Administración del acceso a las aplicaciones](active-directory-managing-access-to-apps.md)
