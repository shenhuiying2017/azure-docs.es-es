---
title: "Tutorial: Integración de Azure Active Directory con ScreenSteps | Microsoft Docs"
description: "Aprenda cómo usar ScreenSteps con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integración de Azure Active Directory con ScreenSteps
El objetivo de este tutorial es mostrar la integración de Azure y ScreenSteps.
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de ScreenSteps

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ScreenSteps podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ScreenSteps (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para ScreenSteps
2. Configuración del inicio de sesión único (SSO) 
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Escenario")

## <a name="enable-the-application-integration-for-screensteps"></a>Habilitación de la integración de aplicaciones para ScreenSteps
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ScreenSteps.

**Siga estos pasos para habilitar la integración de aplicaciones para ScreenSteps:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **ScreenSteps**.

    ![Galería de aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **ScreenSteps** y haga clic en **Completar** para agregar la aplicación.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ScreenSteps con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **ScreenSteps**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en ScreenSteps?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar inicio de sesión único")

3. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ScreenSteps como administrador.

4. Haga clic en **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Administración de cuentas")

5. Haga clic en **Inicio de sesión único**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticación remota")

6. Haga clic en **Crear punto de conexión de inicio de sesión único**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticación remota")

7. En la sección **Crear punto de conexión de inicio de sesión único** , siga estos pasos:

    ![Creación de un punto de conexión de autenticación](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Creación de un punto de conexión de autenticación")

    1. En el cuadro de texto **Título** , escriba un título.
    2. En la lista **Modo**, seleccione **SAML**.
    3. Haga clic en **Crear**.

8. Edite el nuevo punto de conexión.

    ![Editar punto de conexión](./media/active-directory-saas-screensteps-tutorial/IC778528.png "Editar punto de conexión")

9. En la sección **Editar punto de conexión de inicio de sesión único** siga los pasos siguientes:

    ![Punto de conexión de autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Punto de conexión de autenticación remota")

    1. Copie la **Dirección URL del consumidor SAML** en el portapapeles.

10. En la página **Configurar URL de aplicación** del portal de Azure clásico, en el cuadro de texto **URL de inicio de sesión de ScreenSteps**, pegue la **Dirección URL del consumidor SAML** y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar dirección URL de la aplicación")

11. En la página **Configuración de inicio de sesión único en ScreenSteps**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar inicio de sesión único")


12. Vuelva a la sección **Editar punto de conexión de inicio de sesión único** y realice los pasos siguientes:

    ![Punto de conexión de autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Punto de conexión de autenticación remota")

    1. Haga clic en **Cargar nuevo archivo de certificado SAML** y cargue el certificado descargado.
    2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión remoto**.
    3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **URL de cierre de sesión**.
    4. Seleccione un **Grupo** al que asignar usuarios cuando sean aprovisionados.
    5. Haga clic en **Update**(Actualizar).
    6. Vuelva a **Editar punto de conexión de inicio de sesión único**.
    7. Haga clic en el botón **Predeterminar para cuenta** para usar este punto de conexión para todos los usuarios que inician sesión en ScreenSteps. También puede hacer clic en el botón **Agregar al sitio** para utilizar este punto de conexión para sitios específicos de **ScreenSteps**.


12. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario



## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a ScreenSteps, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **ScreenSteps**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.


Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


