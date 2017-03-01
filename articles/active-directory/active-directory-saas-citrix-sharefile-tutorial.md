---
title: "Tutorial: Integración de Azure Active Directory con Citrix ShareFile | Microsoft Docs"
description: "Aprenda cómo usar Citrix ShareFile con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 62f9c25a4c1f34cad266c9948cdf82320f5e7011
ms.openlocfilehash: 0c46640d258a18ecbd3b0f8683412544272c63e0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integración con Azure Active Directory con Citrix ShareFile
El objetivo de este tutorial es mostrar la integración de Azure y Citrix ShareFile.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino Citrix ShareFile

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Citrix ShareFile podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Citrix ShareFile (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Citrix ShareFile
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Escenario")

## <a name="enable-the-application-integration-for-citrix-sharefile"></a>Habilitación de la integración de aplicaciones para Citrix ShareFile
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Citrix ShareFile.

**Siga estos pasos para habilitar la integración de aplicaciones para Citrix ShareFile:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
=   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda** escriba **Citrix ShareFile**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Citrix ShareFile** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo se habilita a los usuarios para autenticarse en Citrix ShareFile con su cuenta de Azure AD mediante federación basada en el protocolo SAML

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Citrix ShareFile**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar inicio de sesión único](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Habilitar inicio de sesión único")
2. En la página **How would you like users to sign on to Citrix ShareFile** (¿Cómo desea que los usuarios inicien sesión en Citrix ShareFile?), seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Citrix ShareFile**, escriba su dirección URL con el siguiente patrón `https://<tenant-name>.shareFile.com` y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Citrix ShareFile**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de **Citrix ShareFile** .
6. En la barra de herramientas de la parte superior, haga clic en **Administración**.
7. En el panel de navegación izquierdo, haga clic en **Configurar inicio de sesión único**.
   
   ![Administración de cuentas](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administración de cuentas")
8. En la página del cuadro de diálogo **Configuración de inicio de sesión único/SAML 2.0** en **Configuración básica**, realice los pasos siguientes:
   
   ![Inicio de sesión único](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Inicio de sesión único")
   1. Haga clic en **Enable SAML**(Habilitar SAML).
   2. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Citrix ShareFile**, copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **Your IDP Issuer/ Entity ID** (Su emisor de IDP/Id. de entidad).
   3. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Citrix ShareFile**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   4. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Citrix ShareFile**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
   5. Haga clic en **Cambiar** junto al campo **Certificado X.509** y luego cargue el certificado que descargó desde el portal clásico de Azure AD. 
   
      ![Configuración básica](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Configuración básica")
9. Haga clic en **Guardar** en el portal de administración de Citrix ShareFile.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en Citrix ShareFile, tienen que aprovisionarse en Citrix ShareFile.  

* En el caso de Citrix ShareFile, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino **Citrix ShareFile** .
2. Haga clic en **Manage Users \> (Administrar usuarios) Manage Users Home \> (Administrar página de inicio de usuarios) + Create Employee** (Crear empleado).
   
   ![Crear empleado](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Crear empleado")
3. Escriba los valores para **Email** (Correo electrónico), **First name** (Nombre) y **Last name** (Apellidos) de una cuenta de Azure AD válida que quiera aprovisionar.
   
   ![Información básica](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Información básica")
4. Haga clic en **Agregar usuario**.
   >[!NOTE]
   >El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active. 
   > 

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Citrix ShareFile ofrecida por Citrix ShareFile para aprovisionar cuentas de usuario de AAD.
>  

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Citrix ShareFile, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Citrix ShareFile**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


