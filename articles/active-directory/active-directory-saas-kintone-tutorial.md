---
title: "Tutorial: Integración de Azure Active Directory con Kintone | Microsoft Docs"
description: "Aprenda a usar Kintone con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a25e030e8ade95db2ac9cd58fe4d94bbb7775ba


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integración de Azure Active Directory con Kintone
El objetivo de este tutorial es mostrar la integración de Azure y Kintone.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Kintone

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Kintone podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Kintone (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Kintone
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>Habilitación de la integración de aplicaciones para Kintone
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Kintone.

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Kintone:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. En el **cuadro de búsqueda**, escriba **Kintone**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. En el panel de resultados, seleccione **Kintone** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Kintone con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Kintone**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Kintone?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Kintone**, escriba su dirección URL con el patrón "*https://company.kintone.com*" y, después, haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. En la página **Configurar inicio de sesión único en Kintone**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de **Kintone** .

6. Haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. Haga clic en **Users & System Administration** (Administración del sistema y usuarios).
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. En **Administración del sistema \> Seguridad**, haga clic en **Inicio de sesión**.
   
    ![Inicio de sesión](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. Haga clic en **Habilitar autenticación SAML**.
   
    ![Autenticación SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. En la sección SAML Authentication (Autenticación SAML), realice los pasos siguientes:
    
    ![Autenticación SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Kintone**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   
    2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Kintone**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
    
    3. Haga clic en **Browse** (Examinar) para cargar el certificado descargado.
    
    4. Haga clic en **Guardar**.

11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Kintone, deben aprovisionarse en Kintone.  
En el caso de Kintone, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en el sitio de la compañía de **Kintone** como administrador.

2. Haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. Haga clic en **Users & System Administration** (Administración del sistema y usuarios).
   
    ![Administración del sistema y usuario](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. En **Administración de usuarios** haga clic en **Departamentos y usuarios**.
   
    ![Departamento y usuarios](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. Haga clic en **Nuevo usuario**.
   
    ![Nuevos usuarios](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. En la sección **Nuevo usuario** , lleve a cabo estos pasos:
   
    ![Nuevos usuarios](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. Escriba un **Nombre para mostrar**, **Nombre de inicio de sesión**, **Nueva contraseña**, **Confirmar contraseña**, **Dirección de correo electrónico** y otros detalles de una cuenta válida de AAD que quiera aprovisionar en los cuadros de texto relacionados.
 
    2. Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Kintone ofrecida por Kintone para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Para asignar usuarios a Kintone, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **Kintone**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


