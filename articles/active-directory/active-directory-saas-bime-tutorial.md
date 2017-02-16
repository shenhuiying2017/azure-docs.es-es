---
title: "Tutorial: Integración de Azure Active Directory con Bime | Microsoft Docs"
description: "Aprenda a usar Bime con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 19adbe15694a6413842b23c27851ce582bfd727e


---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Tutorial: Integración de Azure Active Directory con Bime
El objetivo de este tutorial es mostrar la integración de Azure y Bime.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino Bime

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Bime podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Bime (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Bime
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-bime-tutorial/IC775552.png "Escenario")

## <a name="enabling-the-application-integration-for-bime"></a>Habilitación de la integración de aplicaciones para Bime
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Bime.

### <a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Bime:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-bime-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-bime-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-bime-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Bime**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-bime-tutorial/IC775553.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Bime** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Bime con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Bime requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Bime**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/IC771709.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Bime?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/IC775555.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Bime**, escriba su dirección URL con el siguiente patrón *https://\<nombreDeInquilino\>.Bimeapp.com* y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bime-tutorial/IC775556.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en Bime**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo como **c:\\Bime.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/IC775557.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Bime.
6. En la barra de herramientas, haga clic en **Admin** y, luego, en **Cuenta**.
   
   ![Administración](./media/active-directory-saas-bime-tutorial/IC775558.png "Administración")
7. En la página de configuración de la cuenta, realice los siguientes pasos:
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/IC775559.png "Configurar inicio de sesión único")
   
   1. Seleccione **Habilitar autenticación SAML**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bime**, copie el valor de la **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión remoto**.
   3. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint** (Huella digital del certificado).  
      
      > [!TIP]
      > Para más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. Haga clic en **Save**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/IC775560.png "Configurar inicio de sesión único")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Bime, tienen que aprovisionarse en Bime.  
En el caso de Bime, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su inquilino de **Bime** .
2. En la barra de herramientas, haga clic en **Admin** y, luego, en **Usuarios**.
   
   ![Administración](./media/active-directory-saas-bime-tutorial/IC775561.png "Administración")
3. En **Lista de usuarios**, haga clic en **Agregar nuevo usuario** (+).
   
   ![Usuarios](./media/active-directory-saas-bime-tutorial/IC775562.png "Usuarios")
4. En la página de diálogo **Detalles del usuario** , siga estos pasos:
   
   ![Detalles del usuario](./media/active-directory-saas-bime-tutorial/IC775563.png "Detalles del usuario")
   
   1. Escriba los valores de Nombre, Apellido, Inicio de sesión, Correo electrónico de una cuenta válida de AAD que desee aprovisionar.
   2. Haga clic en Guardar.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bime que proporcione Bime para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-bime-perform-the-following-steps"></a>Para asignar usuarios a Bime, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Bime**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-bime-tutorial/IC775564.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-bime-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO3-->


