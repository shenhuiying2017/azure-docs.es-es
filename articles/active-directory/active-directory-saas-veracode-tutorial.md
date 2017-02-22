---
title: "Tutorial: Integración de Azure Active Directory con Veracode | Microsoft Docs"
description: "Aprenda cómo usar Veracode con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: d6845f666c8b987319bd0b5a7bbe8726f8501249


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: integración de Azure Active Directory con Veracode
El objetivo de este tutorial es mostrar la integración de Azure y Veracode. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Veracode

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Veracode podrá realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Veracode
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

## <a name="enabling-the-application-integration-for-veracode"></a>Habilitación de la integración de aplicaciones para Veracode
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en Veracode.

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Veracode:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")

6. En el **cuadro de búsqueda**, escriba **Veracode**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")

7. En el panel de resultados, seleccione **Veracode** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Veracode con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.  
La aplicación Veracode espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla le muestra un ejemplo de esto.

![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Veracode**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")

2. En la página **¿Cómo quiere que los usuarios inicien sesión en Veracode?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")

3. En la página **Configurar las opciones de la aplicación**, haga clic en **Siguiente**.
   
    ![Configurar las opciones de la aplicación](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")

4. En la página **Configurar inicio de sesión único en Veracode**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Veracode como administrador.

6. En el menú de la parte superior, haga clic en **Configuración** y luego en **Administración**.
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7. Haga clic en la pestaña **SAML** .

8. En la sección **Configuración de SAML de organización** siga estos pasos:
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
    a. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Veracode**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
   
    b. Para cargar el certificado descargado, haga clic en **Elegir archivo**.
   
    c. Seleccione **Habilitar registro automático**.

9. En la sección **Configuración de registro automático**, siga este procedimiento y, después, haga clic en **Guardar**:
   
    ![Administración](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
    a. Como **Activación de nuevo usuario**, seleccione **No se requiere ninguna activación**.
   
    b. Como **Actualizaciones de datos de usuario**, seleccione **Datos de usuario de Veracode de preferencia**.
   
    c. Para **Detalles de atributo de SAML**, seleccione lo siguiente:
      * **Roles de usuario**
      * **Administrador de directivas**
      * **Revisor**
      * **Principal de seguridad**
      * **Ejecutivo**
      * **Remitente**
      * **Creador**
      * **Todos los tipos de detección**
      * **Pertenencias a equipos**
      * **Equipo predeterminado**

10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")

11. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")

12. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")
    
    | Nombre del atributo | Valor de atributo |
    |:--- |:--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.

    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.

    c. En el cuadro de texto **Valor de atributo** , seleccione el valor de atributo que se muestra para la fila.

    d. Haga clic en **Completo**.

13. Haga clic en **Aplicar cambios**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Veracode, deben aprovisionarse en Veracode.  
En el caso de Veracode, el aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted.

Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Veracode ofrecida por Veracode para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Para asignar usuarios a Veracode, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **Veracode**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


