---
title: "Tutorial: integración de Azure Active Directory con BlueJeans | Microsoft Docs"
description: "Aprenda cómo usar BlueJeans con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 00f00d91e54d35fb9009a3e927dfc8833cd7571e


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>Tutorial: Integración de Azure AD con BlueJeans
El objetivo de este tutorial es mostrar la integración de Azure y BlueJeans.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en BlueJeans

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a BlueJeans podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de BlueJeans (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para BlueJeans
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Escenario")

## <a name="enabling-the-application-integration-for-bluejeans"></a>Habilitación de la integración de aplicaciones para BlueJeans
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para BlueJeans.

### <a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para BlueJeans:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **BlueJeans**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **BlueJeans** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en BlueJeans con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **BlueJeans**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en BlueJeans?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de BlueJeans**, escriba su dirección URL con el siguiente patrón *https://company.BlueJeans.com* y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en BlueJeans**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de **BlueJeans** .
6. Vaya a **ADMIN \> Group Settings \> Security**.
   
   ![Administración](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administración")
7. En la sección **Seguridad** , realice estos pasos:
   
   ![Inicio de sesión único de SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Inicio de sesión único de SAML")
   
   1. Seleccione **SAML Single Sign On**(Inicio de sesión único de SAML).
   2. Seleccione **Enable automatic provisioning**(Habilitar aprovisionamiento automático).
8. Continúe con los siguiente pasos:
   
   ![Ruta del certificado](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Ruta del certificado")
   
   1. Haga clic en **Elegir archivo**y cargue el certificado descargado.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en BlueJeans**, copie el valor de **Dirección URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en BlueJeans**, copie el valor de **Cambiar dirección URL de contraseña** y péguelo en el cuadro de texto **Dirección URL de cambio de contraseña**.
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en BlueJeans**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
9. Continúe con los siguiente pasos:
   
   ![Guardar cambios](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Guardar cambios")
   
   1. Escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** en **User ID Attribute Name** (Nombre de atributo de identificador de usuario).
   2. Escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** en **Email** (Correo electrónico).
   3. Haga clic en **Guardar cambios**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurar inicio de sesión único")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en BlueJeans, tienen que aprovisionarse en BlueJeans.  
En el caso de BlueJeans, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión como administrador en el sitio de la compañía de **BlueJeans** .
2. Vaya a **ADMIN \> Manage Users \> Add User**.
   
   ![Administración](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administración")
   
   > [!IMPORTANT]
   > La pestaña **Add User** (Agregar usuario) está disponible solo si en la pestaña **Security** (Seguridad), la opción **Enable automatic provisioning** (Habilitar aprovisionamiento automático) está desactivada.
   > 
   > 
3. En la sección **Add User** (Agregar usuario), realice estos pasos:
   
   ![Agregar usuario](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Agregar usuario")
   
   1. Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **BlueJeans Username** (Nombre de usuario de BlueJeans), **Email address** (Dirección de correo electrónico), **BlueJeans Meeting ID** (Id. de reunión de BlueJeans), **Moderator Passcode** (Código de acceso de moderador), **Full Name** (Nombre completo) y **Company** (Empresa).
   2. Haga clic en **Agregar usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de BlueJeans que proporcione BlueJeans para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Para asignar usuarios a BlueJeans, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **BlueJeans**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


