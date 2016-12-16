---
title: "Tutorial: Integración de Azure Active Directory con Box | Microsoft Docs"
description: "Aprenda a usar Box con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5f3517f8-30f2-4be7-9e47-43d702701797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8254f41006c36509af3626cb14825602e6a4adb


---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Integración de Azure Active Directory con Box
El objetivo de este tutorial es mostrar la integración de Azure y Box.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de prueba en Box

Después de completar este tutorial, los usuarios de Azure AD asignados a Box podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Box (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Box
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuarios y grupos
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")

## <a name="enabling-the-application-integration-for-box"></a>Habilitación de la integración de aplicaciones para Box
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Box.

### <a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Box:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Box**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-box-tutorial/IC701023.png "Application gallery")
7. En el panel de resultados, seleccione **Box** y haga clic en **Completar** para agregar la aplicación.
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Box con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario cargar metadatos en Box.com.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Box**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Box?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inquilino de Box**, escriba la dirección URL del inquilino de Box (por ejemplo,: https://<mydomainname>.box.com) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")
4. En la página **Configurar inicio de sesión único en Box**, para descargar sus metadatos, haga clic en **Descargar metadatos** y guarde el archivo de datos localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")
5. Reenvíe el archivo de metadatos al equipo de soporte técnico de Box. El equipo de soporte técnico necesita configurar un inicio de sesión único para usted.
6. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Box.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Box**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**. 
   
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")
2. En la página de diálogo **Habilitar aprovisionamiento de usuarios en Box**, haga clic en **Habilitar aprovisionamiento de usuarios**. 
   
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")
3. En la página **Log in to grant access to Box** (Iniciar sesión para otorgar acceso a Box), proporcione las credenciales necesarias y haga clic en **Authorize** (Autorizar). 
   
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")
4. Haga clic en **Grant access to Box** (Otorgar acceso a Box) para autorizar esta operación y volver al Portal de Azure clásico. 
   
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")
5. En la página **Opciones de aprovisionamiento**, las casillas **Tipos de objeto para proveer** permiten seleccionar si se aprovisionan objetos de grupo en Box, además de objetos de usuario.  Para obtener más información, vea a continuación la sección "Asignación de usuarios y grupos".
6. Para finalizar la configuración, haga clic en el botón Completar. 
   
    ![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")

## <a name="assigning-a-test-user"></a>Asignación de un usuario de prueba
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-box-perform-the-following-steps"></a>Siga estos pasos para asignar usuarios a Box:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Box**, haga clic en **Asignar usuarios**. 
   
    ![Asignar usuarios](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación. 
   
   ![Sí](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Box.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento haciendo clic en la opción Panel de la página de integración de la aplicación DocuSign en el Portal de Azure clásico.

![Panel](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Estado de integración](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")

En su inquilino de Box, los usuarios sincronizados se muestran en **Usuarios administrados** en la **Consola de administración**.

![Estado de integración](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")

## <a name="assigning-users-and-groups"></a>Asignación de usuarios y grupos
La pestaña **Box > Usuarios y grupos** del Portal de Azure clásico permite especificar qué usuarios y grupos deben tener acceso a Box. La asignación de un usuario o un grupo hace que ocurra lo siguiente:

* Azure AD permite que el usuario asignado (ya sea mediante asignación directa o pertenencia al grupo) se autentique en Box. Si no se asigna un usuario, Azure AD no permitirá que inicie sesión en Box y devolverá un error en la página de inicio de sesión de Azure AD.
* Se agregará un icono de la aplicación de Box al [iniciador de aplicaciones](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)del usuario.
* Si está habilitado el aprovisionamiento automático, se agregan a la cola de aprovisionamiento los usuarios o grupos asignados para aprovisionarlos automáticamente.
  
  * Si solo se ha configurado el aprovisionamiento de objetos de usuario, todos los usuarios asignados directamente se colocan en la cola de aprovisionamiento, y todos los usuarios que son miembros de los grupos asignados se colocarán en la cola de aprovisionamiento. 
  * Si se ha configurado el aprovisionamiento de objetos de grupo, todos los objetos de grupo asignados se aprovisionan en Box, así como todos los usuarios que son miembros de esos grupos. Las pertenencias a grupos y usuarios se conservan una vez que se escriben en Box.

Puede usar la pestaña **Atributos > Inicio de sesión único** para configurar los atributos de usuario (o notificaciones) que se presentan en Box durante la autenticación basada en SAML, y la pestaña **Atributos > Aprovisionamiento** para configurar la forma en que los atributos de usuario y grupo fluyen de Azure AD a Box durante las operaciones de aprovisionamiento. Para obtener más información, consulte los recursos que se muestran a continuación.

## <a name="additional-resources"></a>Recursos adicionales
* [Personalización de notificaciones emitidas en el token SAML](active-directory-saml-claims-customization.md)
* [Aprovisionamiento: personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


