---
title: "Tutorial: integración de Azure Active Directory con Jitbit Helpdesk | Microsoft Docs"
description: "Aprenda a usar Jitbit Helpdesk con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: acffb1e0b3f1ac05034d130ca3a24246154a0cc2


---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integración de Azure Active Directory con Jitbit Helpdesk
El objetivo de este tutorial es mostrar la integración de Azure y Jitbit Helpdesk.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Jitbit Helpdesk

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Jitbit Helpdesk podrá realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Jitbit Helpdesk (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Jitbit Helpdesk
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")

## <a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Habilitación de la integración de aplicaciones para Jitbit Helpdesk
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Jitbit Helpdesk.

### <a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Jitbit Helpdesk:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Jitbit Helpdesk**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Application Gallery")
7. En el panel de resultados, seleccione **Jitbit Helpdesk** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Jitbit Helpdesk con su cuenta de Azure AD mediante federación basada en el protocolo SAML. .  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

> [!IMPORTANT]
> Para poder configurar el inicio de sesión único en el inquilino de Jitbit Helpdesk, deberá ponerse en contacto primero con el soporte técnico de Jitbit Helpdesk para habilitar esta característica.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Jitbit Helpdesk**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Jitbit Helpdesk?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de Jitbit Helpdesk**, escriba su dirección URL con el siguiente patrón "*https://\<nombre de inquilino\>.Jitbit.com*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configure app URL")
4. En la página **Configurar inicio de sesión único en Jitbit Helpdesk**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Jitbit Helpdesk.
6. En la barra de herramientas de la parte superior, haga clic en **Administración**.
   
   ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
7. Haga clic en **Configuración general**.
   
   ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
8. En la sección **Configuración de autenticación** , realice estos pasos:
   
   ![Configuración de autenticación](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")
   
   1. Seleccione **Enable SAML 2.0 single sign on** (Habilitar inicio de sesión único SAML 2.0) para iniciar sesión mediante inicio de sesión único (SSO) con **OneLogin**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Jitbit Helpdesk**, copie el valor de **Extremo iniciado por el proveedor de servicios** y péguelo en el cuadro de texto **EndPoint URL** (URL de punto de conexión).
   3. Cree un archivo **codificado en base 64** a partir del certificado descargado.
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra el certificado codificado en base 64, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509** .
   5. Haga clic en **Guardar cambios**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Jitbit Helpdesk, deben aprovisionarse en Jitbit Helpdesk.  
En el caso de Jitbit Helpdesk, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **Jitbit Helpdesk** .
2. En el menú de la parte superior, haga clic en **Administración**.
   
   ![Administración](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
3. Haga clic en **Usuarios, compañías y permisos**.
   
   ![Usuarios, compañías y permisos](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
4. Haga clic en **Agregar usuario**.
   
   ![Agregar usuario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")
5. En la sección Create (Crear), escriba los datos de la cuenta de Azure AD que desee aprovisionar en los cuadros de texto siguientes: **Username** (Nombre de usuario), **Email** (Correo electrónico), **First Name** (Nombre), **Last Name** (Apellido)
   
   ![Crear](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Create")
6. Haga clic en **Crear**.

> [!NOTE]
> Puede usar cualquier herramienta de creación de cuentas de usuario de Jitbit Helpdesk u otras API proporcionadas por Jitbit Helpdesk para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Para asignar usuarios a Jitbit Helpdesk, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Jitbit Helpdesk**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


