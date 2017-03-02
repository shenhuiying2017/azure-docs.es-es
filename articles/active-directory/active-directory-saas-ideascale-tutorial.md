---
title: "Tutorial: integración de Azure Active Directory con IdeaScale | Microsoft Docs"
description: "Aprenda a usar IdeaScale con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2695483b7dad4418bf24f21ebe808dd145fb9503
ms.openlocfilehash: 6362e5b6232a3a42fdbad3cf0e5a36a2e4b178b8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: integración de Azure Active Directory con IdeaScale
El objetivo de este tutorial es mostrar la integración de Azure y IdeaScale.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único (SSO) en IdeaScale

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a IdeaScale podrán realizar un inicio de sesión único en la aplicación desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

*  Habilitación de la integración de aplicaciones para IdeaScale
*  Configuración del inicio de sesión único
*  Configuración del aprovisionamiento de usuario
*  Asignación de usuarios

![Escenario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Escenario")

## <a name="enable-the-application-integration-for-ideascale"></a>Habilitación de la integración de aplicaciones para IdeaScale
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para IdeaScale.

**Siga estos pasos para habilitar la integración de aplicaciones para IdeaScale:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **IdeaScale**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **IdeaScale** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en IdeaScale con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  

La configuración de un inicio de sesión único para IdeaScale requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **IdeaScale**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en IdeaScale?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de IdeaScale**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación IdeaScale (por ejemplo: "*https://company.IdeaScale.com*") y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en IdeaScale**, para descargar los metadatos, haga clic en **Descargar metadatos** y, después, guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de IdeaScale como administrador.
6. Vaya a **Configuración de la comunidad**.
   
   ![Configuración de la Comunidad](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configuración de la Comunidad")
7. Vaya a **Security \> Single Signon Settings** (Seguridad > Configuración de inicio de sesión único).
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Configuración de inicio de sesión único")
8. En **Single-Signon Type** (Tipo de inicio de sesión único), seleccione **SAML 2.0**.
   
   ![Tipo de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Tipo de inicio de sesión único")
9. En el cuadro de diálogo **Configuración de inicio de sesión único** , siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Configuración de inicio de sesión único")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en IdeaScale**, copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **SAML IdP Entity ID** (Id. de identidad del IdP de SAML).
   2. Copie el contenido del archivo de metadatos descargado y luego péguelo en el cuadro de texto **Metadatos del IdP de SAML** .
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en IdeaScale**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Logout Success URL** (Dirección URL de cierre de sesión correcto).
   4. Haga clic en **Guardar cambios**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en IdeaScale, deben aprovisionarse en IdeaScale. En el caso de IdeaScale, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en el sitio de la compañía de **IdeaScale** como administrador.
2. Vaya a **Configuración de la comunidad**.
   
   ![Configuración de la Comunidad](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configuración de la Comunidad")
3. Vaya a **Basic Settings \> Member Management** (Configuración básica > Administración de miembros).
4. Haga clic en **Agregar miembro**.
   
   ![Administración de miembros](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Administración de miembros")
5. En la sección Add New Member (Agregar nuevo miembro), lleve a cabo estos pasos:
   
   ![Agregar nuevo miembro](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Agregar nuevo miembro")
   
   1. En el cuadro de texto **Direcciones de correo electrónico** , escriba la dirección de correo electrónico de la cuenta de AAD válida que quiera aprovisionar.
   2. Haga clic en **Guardar cambios**. 
   
    >[!NOTE]
    >El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    >  

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de IdeaScale que proporcione IdeaScale para aprovisionar cuentas de usuario de AAD.
>  

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a IdeaScale, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **IdeaScale**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


