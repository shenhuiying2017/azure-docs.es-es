---
title: "Tutorial: Integración de Azure Active Directory con SpringCM | Microsoft Docs"
description: "Aprenda cómo usar SpringCM con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: 95fbe26a9bb886c6edbb862c9e15885ffc5eeed6
ms.lasthandoff: 12/14/2016


---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: integración de Azure Active Directory con SprinkCM
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory y SpringCM.

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en SpringCM

Después de completar este tutorial, los usuarios de Azure Active Directory que haya asignado a SpringCM podrán realizar inicios de sesión únicos mediante el panel de acceso de AAD.

1. Habilitación de la integración de aplicaciones para SpringCM
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Escenario")

## <a name="enabling-the-application-integration-for-springcm"></a>Habilitación de la integración de aplicaciones para SpringCM
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en SpringCM.

### <a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para SpringCM:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **SpringCM**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **SpringCM** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en SpringCM con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SpringCM**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en SpringCM?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de SpringCM**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación SpringCM y luego haga clic en **Siguiente**. 
   
    La dirección URL es la dirección URL del inquilino de SpringCM (por ejemplo, *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurar dirección URL de la aplicación")

4. En la página **Configurar inicio de sesión único en SpringCM**, para descargar el certificado, haga clic en **Descargar certificado** y, luego, guarde el archivo de certificado localmente en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **SpringCM** como administrador.

6. En el menú de la parte superior, haga clic en**IR A**, haga clic en **Preferencias** y, luego, en la sección **Preferencias de cuenta**, haga clic en**SSO de SAML**.
   
    ![SSO DE SAML](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SSO DE SAML")

7. En la sección de configuración del proveedor de identidades, realice los pasos siguientes:
   
    ![Configuración del proveedor de identidades](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuración del proveedor de identidades")
   
    a. Para cargar el certificado descargado de Azure Active Directory, haga clic en **Seleccionar certificado de emisor** o **Cambiar el certificado del emisor**.
   
    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SpringCM**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
   
    c. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en SpringCM**, copie el valor de **URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Punto de conexión iniciado por el proveedor de servicios (SP)**.
   
    d. Como **SAML habilitado**, seleccione **Habilitar**.
   
    e. Haga clic en **Save**.

8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure Active Directory inicien sesión en SpringCM, deben aprovisionarse en SpringCM.  
En el caso de SpringCM, el aprovisionamiento es una tarea manual.

> [!NOTE]
> Para obtener más información, vea [Crear y editar un usuario de SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)
> 
> 

### <a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario a SpringCM, realice los siguientes pasos:
1. Inicie sesión en su sitio de compañía de **SpringCM** como administrador.

2. Haga clic en **GOTO** y, luego, en **Libreta de direcciones**.
   
    ![Creación de usuarios](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Creación de usuarios")

3. Haga clic en **Crear usuario**.

4. Seleccione un **Rol de usuario**.

5. Seleccione **Enviar correo electrónico de activación**.

6. Escriba el nombre, el apellido y la dirección de correo electrónico de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.

7. Agregue el usuario a un **Grupo de seguridad**.

8. Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SpringCM ofrecida por SpringCM para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Para asignar usuarios a SpringCM, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de la aplicación **SpringCM**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


