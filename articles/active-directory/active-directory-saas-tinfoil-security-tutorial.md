---
title: "Tutorial: integración de Azure Active Directory con Tinfoil Security | Microsoft Docs"
description: "Aprenda cómo usar Tinfoil Security con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 383ecb6978fc975bbee51474c467737720ff927e


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integración de Azure Active Directory con Tinfoil Security
El objetivo de este tutorial es mostrar la integración de Azure y Tinfoil Security.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Tinfoil Security

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Tinfoil Security podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Tinfoil Security (inicio de sesión iniciado por el proveedor de identidades) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Tinfoil Security
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>Habilitación de la integración de aplicaciones para Tinfoil Security
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Tinfoil Security.

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Tinfoil Security:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Tinfoil Security**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Application Gallery")
7. En el panel de resultados, seleccione **Tinfoil Security** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Tinfoil Security con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Tinfoil Security requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Tinfoil Security**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Tinfoil Security?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de respuesta de Tinfoil Security**, escriba la dirección URL del Servicio de consumidor de aserciones (ACS) de Tinfoil Security (por ejemplo: "*https://www.tinfoilsecurity.com/saml/consume*" y haga clic en **Siguiente**.
   
   > [!NOTE]
   > Debería poder obtener la dirección URL de ACS de los metadatos de Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).
   > 
   > 
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Tinfoil Security**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en como **c:\\Tinfoil Security.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Tinfoil Security.
6. En la barra de herramientas de la parte superior, haga clic en el icono de **Mi cuenta**.
   
   ![Panel](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")
7. Haga clic en **Seguridad**.
   
   ![Seguridad](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Security")
8. Siga estos pasos en la página de configuración **Inicio de sesión único** :
   
   ![Inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")
   
   1. Seleccione **Habilitar SAML**.
   2. Haga clic en **Configuración manual**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Tinfoil Security**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **SAML Post URL** (Dirección URL de publicación de SAML).
   4. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **SAML Certificate Fingerprint** (Huella digital de certificado de SAML).  
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. Copie **Su id. de cuenta**.
   6. Haga clic en **Save**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configure Single Sign-On")
10. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributes")
11. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributes")
    
    1. Haga clic en **agregar atributo de usuario**.
    2. En el cuadro de texto **Nombre de atributo**, escriba **accountid**.
    3. En el cuadro de texto **Valor de atributo**, pegue el valor del identificador de cuenta que ha copiado en la sección anterior.
    4. Haga clic en **Complete**.
12. Haga clic en **Aplicar cambios**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Tinfoil Security, deben aprovisionarse en Tinfoil Security.  
En el caso de Tinfoil Security, el aprovisionamiento es una tarea manual.

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Siga estos pasos para que se aprovisione un usuario:
1. Si el usuario forma parte de una cuenta de empresa, deberá ponerse en contacto con el equipo de soporte técnico de Tinfoil Security para que se cree la cuenta del usuario.
2. Si el usuario es usuario habitual de SaaS de Tinfoil Security, puede agregar un colaborador a cualquiera de sus sitios. Esto desencadena un proceso para enviar una invitación al correo electrónico especificado para crear una nueva cuenta de usuario de Tinfoil Security.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Tinfoil Security ofrecida por Tinfoil Security para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Para asignar usuarios a Tinfoil Security, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Tinfoil Security**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


