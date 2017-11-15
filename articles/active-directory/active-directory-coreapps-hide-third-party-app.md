---
title: "Ocultación de una aplicación de terceros de la experiencia del usuario en Azure Active Directory | Microsoft Docs"
description: "Cómo ocultar una aplicación de terceros de la experiencia del usuario en Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Ocultación de una aplicación de terceros de la experiencia del usuario en Azure Active Directory

Si tiene una aplicación de terceros (es decir, una aplicación no publicada por Microsoft) que no desea que aparezca en los paneles de acceso de los usuarios o en los iniciadores de Office 365, hay una opción para ocultar el icono de esta aplicación. Al ocultar la aplicación, los usuarios todavía tendrán permisos de acceso a ella pero no podrán verla en los iniciadores de aplicaciones. Debe tener los permisos adecuados para administrar la aplicación de empresa y debe ser administrador global en el directorio.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>Ocultación de una aplicación de terceros de la experiencia del usuario
Siga estos pasos para ocultar una aplicación de terceros del panel de acceso de un usuario y de los iniciadores de aplicaciones de Office 365:

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>¿Cómo se puede ocultar una aplicación de terceros del panel de acceso del usuario y de los iniciadores de aplicaciones de Office 365?

1.  Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2.  Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y presione **ENTRAR**.
3.  En la pantalla **Azure Active Directory - *nombreDelDirectorio*** (es decir, la pantalla de Azure AD del directorio que está administrando), seleccione **Aplicaciones empresariales**.
![Aplicaciones empresariales](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  En la pantalla **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5.  En la pantalla **Aplicaciones empresariales - Todas las aplicaciones**, seleccione una aplicación.</br>
![Aplicaciones empresariales](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  En la pantalla ***nombreDeLaAplicación*** (es decir, la pantalla con el nombre de la aplicación seleccionada en el título), seleccione Propiedades.
7.  En la pantalla ***nombreDeLaAplicación* - Propiedades**, seleccione **Sí** en **¿Es visible para los usuarios?**
![Aplicaciones empresariales](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Haga clic en el comando **Guardar** .

## <a name="next-steps"></a>Pasos siguientes
* [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
* [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Cambio del nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
