---
title: "Tutorial: Integración de Azure Active Directory con Mozy Enterprise | Microsoft Docs"
description: "Aprenda a usar Mozy Enterprise con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6034434c289936089afbb0fa5bd803363cde882


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integración de Azure Active Directory con Mozy Enterprise
El objetivo de este tutorial es mostrar la integración de Azure y Mozy Enterprise.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Mozy Enterprise

Después de completar este tutorial, los usuarios de Azure AD asignados a Mozy Enterprise podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Mozy Enterprise (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Mozy Enterprise
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Habilitación de la integración de aplicaciones para Mozy Enterprise
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Mozy Enterprise.

### <a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Mozy Enterprise:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **mozy enterprise**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")
7. En el panel de resultados, seleccione **Mozy Enterprise** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
   ![mozy enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Mozy Enterprise con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Mozy Enterprise.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Mozy Enterprise**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Mozy Enterprise?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Mozy Enterprise**, escriba su dirección URL con el siguiente patrón "*https://\<nombre_inquilino\>.Mozyenterprise.com*" y, luego, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")
4. En la página **Configuración de inicio de sesión único en Mozy Enterprise**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Mozy Enterprise.
6. En la sección **Configuración**, haga clic en **Directiva de autenticación**.
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")
7. En la sección **Authentication Policy** (Directiva de autenticación), realice estos pasos:
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")
   
   1. Seleccione **Servicio de directorio** como **Proveedor**.
   2. Seleccione **Use LDAP Push**(Usar inserción de LDAP).
   3. Haga clic en la pestaña **Autenticación SAML** .
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **Dirección URL de autenticación**.
   5. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, copie el valor de **Identificador del proveedor de identidades** y péguelo en el cuadro de texto **Extremo de SAML**.
   6. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, pegue todo el certificado en el cuadro de texto **Certificado SAML** .
   8. Seleccione **Habilitar SSO para que los administradores inicien sesión con sus credenciales de red**.
   9. Haga clic en **Guardar cambios**.
8. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Mozy Enterprise**, seleccione la confirmación de configuración de inicio de sesión único y luego haga clic en **Completar**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Mozy Enterprise, deben aprovisionarse en Mozy Enterprise.  
En el caso de Mozy Enterprise, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **Mozy Enterprise** .
2. Haga clic en **Usuarios** y, luego, en **Agregar nuevo usuario**.
   
   ![Usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")
   
   > [!NOTE]
   > La opción **Agregar nuevo usuario** solo se muestra si **Mozy** está seleccionado como proveedor en **Directiva de autenticación**. Si la autenticación SAML está configurada, los usuarios se agregan automáticamente al iniciar sesión por primera vez con inicio de sesión único.
   > 
   > 
3. En el cuadro de diálogo Nuevo usuario, realice los pasos siguientes:
   
   ![Agregar usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")
   
   1. En la lista **Choose a Group** (Elija un grupo), seleccione un grupo.
   2. En la lista **Tipo de usuario** , seleccione un tipo.
   3. En el cuadro de texto **Username** (Nombre de usuario), escriba el nombre de usuario de Azure AD.
   4. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico del usuario de Azure AD.
   5. Seleccione **Send user instruction email**(Enviar correo electrónico al usuario con instrucciones).
   6. Haga clic en **Add User(s)**(Agregar usuarios).
   
   > [!NOTE]
   > Después de crear el usuario, se enviará un correo electrónico al usuario de Azure AD que incluye un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mozy Enterprise ofrecida por Mozy Enterprise para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>Para asignar usuarios a Mozy Enterprise, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Mozy Enterprise**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


