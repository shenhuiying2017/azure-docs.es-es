---
title: "Tutorial: Integración de Azure Active Directory con Mindflash | Microsoft Docs"
description: "Aprenda a usar Mindflash con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08753aa43d192e2e948b478638fa2868978cb575


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Tutorial: Integración de Azure Active Directory con Mindflash
El objetivo de este tutorial es mostrar la integración de Azure y Mindflash.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Mindflash

Después de completar este tutorial, los usuarios de Azure AD asignados a Mindflash podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Mindflash (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Mindflash
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenario")

## <a name="enabling-the-application-integration-for-mindflash"></a>Habilitación de la integración de aplicaciones para Mindflash
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Mindflash.

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Mindflash:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Add an application from gallerry")
6. En el**cuadro de búsqueda**, escriba **Mindflash**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Application Gallery")
7. En el panel de resultados, seleccione **Mindflash** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Mindflash con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Mindflash**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Mindflash?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Zoom**, escriba su dirección URL con el siguiente patrón "*http://company.mindflash.com*" y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Mindflash**, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configure Single Sign-On")
5. Envíe el archivo de metadatos al equipo de soporte técnico de Mindflash.
   
   > [!NOTE]
   > La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Mindflash. Tan pronto como se complete la configuración, recibirá una notificación.
   > 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Mindflash, deben aprovisionarse en Mindflash.  
En el caso de Mindflash, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión como administrador en el sitio de la compañía de **Mindflash** .
2. Vaya a **Administrar usuarios**.
   
   ![Administrar usuarios](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Manage Users")
3. Haga clic en **Agregar usuarios** y luego en **Nuevo**.
4. En la sección **Agregar nuevos usuarios** , realice estos pasos:
   
   ![Agregar nuevos usuarios](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Add New Users")
   
   1. Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **Nombre**, **Apellidos** y **Correo electrónico**.
   2. Haga clic en **Agregar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mindflash ofrecida por Mindflash para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Para asignar usuarios a Mindflash, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Mindflash**, haga clic en **Asignar usuario**.
   
   ![Asignar usuarios](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


