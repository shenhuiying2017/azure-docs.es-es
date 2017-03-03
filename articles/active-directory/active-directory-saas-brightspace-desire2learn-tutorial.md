---
title: "Tutorial: integración de Azure Active Directory con Brightspace by Desire2Learn | Microsoft Docs"
description: "Aprenda cómo usar Brightspace by Desire2Learn con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integración de Azure Active Directory con Brightspace by Desire2Learn
El objetivo de este tutorial es mostrar la integración de Azure y Brightspace by Desire2Learn.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único (SSO) de Brightspace by Desire2Learn

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Brightspace by Desire2Learn podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Brightspace by Desire2Learn (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Brightspace by Desire2Learn
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Escenario")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>Habilitación de la integración de aplicaciones para Brightspace by Desire2Learn
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Brightspace by Desire2Learn.

**Siga estos pasos para habilitar la integración de aplicaciones para Brightspace by Desire2Learn:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda** escriba **Brightspace by Desire2Learn**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Brightspace by Desire2Learn** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Brightspace by Desire2Learn con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Brightspace by Desire2Learn**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Brightspace by Desire2Learn?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurar dirección URL de la aplicación")   
   1. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en **Brightspace por Desire2Learn** (p. ej.: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
   2. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en Brightspace by Desire2Learn**, para descargar los metadatos, haga clic en **Descargar metadatos** y, después, guarde los metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurar inicio de sesión único")
5. Enviar el archivo de metadatos descargados al equipo de soporte técnico de Brightspace by Desire2Learn.
   
   >[!NOTE]
   >El equipo de soporte técnico de Brightspace by Desire2Learn es el que tiene que realizar la configuración real de SSO.
   >Cuando SSO se haya habilitado en su suscripción recibirá una notificación.
   > 
 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en Brightspace by Desire2Learn, tienen que aprovisionarse en Brightspace by Desire2Learn.  

En el caso de Brightspace by Desire2Learn, las cuentas de usuario tiene que crearlas el equipo de soporte técnico de Brightspace by Desire2Learn.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Brightspace by Desire2Learn que proporcione Brightspace by Desire2Learn para aprovisionar cuentas de usuario de Azure Active Directory. 
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar a usuarios a Brightspace by Desire2Learn, realice los pasos siguientes:**
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Brightspace by Desire2Learn**, haga clic en **Asignar usuarios**.
   
  ![Asignar usuarios](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
  ![Sí](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


