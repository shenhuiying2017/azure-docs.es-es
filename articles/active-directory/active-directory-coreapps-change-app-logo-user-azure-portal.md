---
title: Cambio del nombre o el logotipo de una aplicación empresarial en la versión preliminar de Azure Active Directory | Microsoft Docs
description: Cómo cambiar el nombre o el logotipo de una aplicación empresarial personalizada en Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Cambio del nombre o el logotipo de una aplicación empresarial en la versión preliminar de Azure Active Directory
Cambiar el nombre o el logotipo de una aplicación empresarial personalizada en la versión preliminar de Azure Active Directory (Azure AD) es fácil. [¿Qué es la versión preliminar?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para realizar estos cambios. En la versión preliminar actual, debe ser el creador de la aplicación personalizada.

## ¿Cómo puedo cambiar el nombre o el logotipo de una aplicación empresarial?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, después, presione **Intro**.
3. En la hoja **Azure Active Directory - *nombreDelDirectorio*** (es decir, la hoja de Azure AD del directorio que está administrando), seleccione **Enterprise applications** (Aplicaciones empresariales).
   
    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. En la hoja **Enterprise applications** (Aplicaciones empresariales), seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones**, seleccione una aplicación.
6. En la hoja ***nombreDeLaAplicación*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Propiedades**.
   
    ![Selección del comando Propiedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. En la hoja ***nombreDeLaAplicación*** **- Propiedades**, busque un archivo para utilizarlo como nuevo logotipo o edite el nombre de la aplicación, o ambas opciones.
   
    ![Cambio del logotipo o de la aplicación o comando de propiedades de nombre](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Haga clic en el comando **Guardar**.

## Pasos siguientes
* [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
* [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Quitar una asignación de usuario o grupo de una aplicación empresarial](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->