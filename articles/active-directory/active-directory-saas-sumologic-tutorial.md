---
title: "Tutorial: Integración de Azure Active Directory con SumoLogic | Microsoft Docs"
description: "Aprenda cómo usar SumoLogic con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 084676f695165f6015b35ddc4bc8c4a6bc3d189e


---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: integración de Azure Active Directory con SumoLogic
El objetivo de este tutorial es mostrar la integración de Azure y SumoLogic.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de SumoLogic

Después de completar este tutorial, los usuarios de Azure AD que asignó a SumoLogic podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SumoLogic (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SumoLogic
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario")

## <a name="enabling-the-application-integration-for-sumologic"></a>Habilitación de la integración de aplicaciones para SumoLogic
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SumoLogic.

### <a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones en SumoLogic:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **sumologic**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Application gallery")
7. En el panel de resultados, seleccione **SumoLogic** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![sumologic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SumoLogic con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de SumoLogic.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SumoLogic**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en SumoLogic?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de SumoLogic**, escriba su dirección URL con el patrón siguiente "*https://\<nombre-de-inquilino\>.SumoLogic.com*" y, después, haga clic en **Siguiente**.
   
   ![Configurar URL aoo](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configure aoo URL")
4. En la página **Configurar inicio de sesión único en SumoLogic**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configure single sign-on")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SumoLogic como administrador.
6. Vaya a **Administrar \> Seguridad**.
   
   ![Administrar](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Manage")
7. Haga clic en **SAML**.
   
   ![Configuración de seguridad global](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Global security settings")
8. En la lista **Seleccionar una configuración o crear una nueva**, seleccione **Azure AD** y, después, haga clic en **Configurar**.
   
   ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configure SAML 2.0")
9. En el cuadro de diálogo **Configurar SAML 2.0** , realice los pasos siguientes:
   
   ![Configurar SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configure SAML 2.0")
   
   1. En el cuadro de texto **Nombre de configuración**, escriba **Azure AD**.
   2. Seleccione **Modo de depuración**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Dirección URL de solicitud de autenticación**.
   5. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego pegue todo el certificado en el cuadro de texto **Certificado X.509** .
   7. Como **Atributo de correo electrónico**, seleccione **Usar SAML Subject**.
   8. Seleccione **Configuración de inicio de sesión iniciada por el SP**.
   9. En el cuadro de texto **Ruta de acceso de inicio de sesión**, escriba **Azure**.
   10. Haga clic en **Guardar**.
10. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en SumoLogic**, seleccione la confirmación de configuración de inicio de sesión único y, después, haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en SumoLogic, deben aprovisionarse a SumoLogic.  
En el caso de SumoLogic, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **SumoLogic** .
2. Vaya a **Administrar \> Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Users")
3. Haga clic en **Agregar**.
   
   ![Usuarios](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Users")
4. En el cuadro de diálogo **Nuevo usuario** , realice los pasos siguientes:
   
   ![Nuevo usuario](./media/active-directory-saas-sumologic-tutorial/IC778563.png "New User")
   
   1. Escriba la información relacionada de la cuenta de Azure AD que quiere aprovisionar en los cuadros de texto **Nombre**, **Apellidos** y **Correo electrónico**.
   2. Seleccione un rol.
   3. Como **Estado**, seleccione **Activo**.
   4. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SumoLogic ofrecida por SumoLogic para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Para asignar usuarios a SumoLogic, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **SumoLogic**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


