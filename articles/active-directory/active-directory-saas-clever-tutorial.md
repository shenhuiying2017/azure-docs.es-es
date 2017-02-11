---
title: "Tutorial: integración de Azure Active Directory con Clever | Microsoft Docs"
description: "Aprenda a usar Clever con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fc748c70afede4be52e6c49db72932588be779ce


---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Tutorial: integración de Azure Active Directory con Clever
El objetivo de este tutorial es mostrar la integración de Azure y Clever. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Clever

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Clever podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Clever (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Clever
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")

## <a name="enabling-the-application-integration-for-clever"></a>Habilitación de la integración de aplicaciones para Clever
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Clever.

### <a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Clever:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Clever**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")
7. En el panel de resultados, seleccione **Clever** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Clever con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La aplicación Clever espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla muestra un ejemplo:

![Atributos](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Clever**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Clever?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Clever**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Clever (por ejemplo: *https://clever.com/in/azsandbox*) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Clever**, para descargar los metadatos, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Clever como administrador.
6. En la barra de herramientas, haga clic en **Inicio de sesión instantáneo**.
   
   ![Inicio de sesión instantáneo](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")
7. En la página **Instant Login** (Inicio de sesión instantáneo), realice los pasos siguientes:
   
   ![Inicio de sesión instantáneo](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")
   
   1. Escriba la **Dirección URL de inicio de sesión**.  
      
      > [!NOTE]
      > La **URL de inicio de sesión** es un valor personalizado.
      > El valor real puede obtenerlo del equipo de soporte técnico de Clever.
      > 
      > 
   2. En **Identity System** (Sistema de identidades), seleccione **ADFS**.
   3. Haga clic en **Guardar**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")
9. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
   
   ![Atributos](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")
10. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
    
    ![Atributos de token de SAML](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |
    | clever.student.credentials.district\_username |User.userprincipalname |
    
    1. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
    3. En el cuadro de texto **Valor de atributo** , seleccione el valor de atributo que se muestra para la fila.
    4. Haga clic en **Complete**.
11. Haga clic en **Aplicar cambios**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Clever, deben aprovisionarse en Clever.  
En el caso de Clever, el aprovisionamiento es una tarea manual que debe realizar el equipo de soporte técnico de Clever.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Clever ofrecida por Clever para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-clever-perform-the-following-steps"></a>Para asignar usuarios a Clever, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Clever**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


