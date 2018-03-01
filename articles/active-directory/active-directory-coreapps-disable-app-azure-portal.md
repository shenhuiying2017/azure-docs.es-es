---
title: "Deshabilitación de los inicios de sesión de usuario de una aplicación empresarial en Azure Active Directory | Microsoft Docs"
description: "Cómo deshabilitar una aplicación empresarial para que ningún usuario pueda iniciar sesión en ella en Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: e044d32406236aacaf7fffa2b4b19dadd96a7d5d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Deshabilitación de los inicios de sesión de usuario de una aplicación empresarial en Azure Active Directory
Deshabilitar una aplicación empresarial para que ningún usuario pueda iniciar sesión en ella en Azure Active Directory (Azure AD) es fácil. Debe tener los permisos adecuados para administrar la aplicación de empresa y debe ser administrador global en el directorio.

## <a name="how-do-i-disable-user-sign-ins"></a>¿Cómo puedo deshabilitar los inicios de sesión de usuario?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Todos los servicios**, escriba **Azure Active Directory** en el cuadro de texto y, después, seleccione **Entrar**.
3. En el panel **Azure Active Directory** -  ***nombreDelDirectorio*** (es decir, el panel de Azure AD del directorio que está administrando), seleccione **Aplicaciones empresariales**.

    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. En el panel **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5. En el panel **Aplicaciones empresariales - Todas las aplicaciones**, seleccione una aplicación.
6. En el panel ***nombreDeLaAplicación*** (es decir, el panel con el nombre de la aplicación seleccionada en el título), seleccione **Propiedades**.

    ![Selección del comando Todas las aplicaciones](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. En el panel ***nombreDeLaAplicación*** - **Propiedades**, en **¿Habilitado para que los usuarios inicien sesión?**, seleccione **No**.
8. Haga clic en el comando **Guardar** .

## <a name="next-steps"></a>pasos siguientes
* [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
* [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Cambio del nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
