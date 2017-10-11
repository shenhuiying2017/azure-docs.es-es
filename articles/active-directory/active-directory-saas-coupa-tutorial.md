---
title: "Tutorial: Integración de Azure Active Directory con Coupa | Microsoft Docs"
description: "Aprenda a usar Coupa con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integración de Azure Active Directory con Coupa
El objetivo de este tutorial es mostrar la integración de Azure y Coupa.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único (SSO) en Coupa

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Coupa podrá realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Coupa
* Configuración del inicio de sesión único
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-coupa-tutorial/IC791897.png "Escenario")

## <a name="enable-the-application-integration-for-coupa"></a>Habilitación de la integración de aplicaciones para Coupa
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Coupa.

**Siga estos pasos para habilitar la integración de aplicaciones para Coupa:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-coupa-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-coupa-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-coupa-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Coupa**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-coupa-tutorial/IC791898.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Coupa** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Coupa con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  

La configuración de un inicio de sesión único para Coupa requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

**Siga estos pasos para configurar el inicio de sesión único:**

1. Inicie sesión en su sitio de la compañía de Coupa como administrador.
2. Vaya a **Configuración \> Control de seguridad**.
   
   ![Controles de seguridad](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de seguridad")
3. Para descargar el archivo de metadatos de Coupa en el equipo, haga clic en **Descargar e importar metadatos de SP**.
   
   ![Metadatos SP de Coupa](./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadatos SP de Coupa")
4. En otra ventana de explorador, inicie sesión en el Portal de Azure clásico.
5. En la página de integración de aplicaciones de **Coupa**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurar inicio de sesión único")
6. En la página **¿Cómo desea que los usuarios inicien sesión en Coupa?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurar inicio de sesión único")
7. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurar dirección URL de la aplicación")   
   1. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación Coupa (por ejemplo, “*http://company.Coupa.com*”).
   2. Abra el archivo de metadatos de Coupa descargado y después copie la **dirección URL/índice AssertionConsumerService**.
   3. En el cuadro de texto **Dirección URL de respuesta de Coupa**, pegue el valor de la **dirección URL/índice AssertionConsumerService**.
   4. Haga clic en **Siguiente**.
8. En la página **Configurar inicio de sesión único en Coupa**, para descargar el archivo de metadatos, haga clic en **Descargar metadatos** y, después, guarde el archivo de forma local en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurar inicio de sesión único")
9. En el sitio de la compañía de Coupa, vaya a **Configuración \> Control de seguridad**.
   
   ![Controles de seguridad](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de seguridad")
10. En la sección **Iniciar sesión con credenciales de Coupa** , realice los pasos siguientes:  

   ![Inicio de sesión con credenciales de Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "Inicio de sesión con credenciales de Coupa") 
   1. Seleccione **Iniciar sesión con SAML**.
   2. Haga clic en **Examinar** para cargar el archivo de metadatos de Azure Active descargado.
   3. Haga clic en **Save**.
11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
   ![Configurar inicio de sesión único](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en Coupa, tienen que aprovisionarse en Coupa.  

* En el caso de Coupa, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión como administrador en el sitio de la compañía de **Coupa** .
2. En el menú en la parte superior, haga clic en **Configurar** y, después, en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-coupa-tutorial/IC791908.png "Usuarios")
3. Haga clic en **Create**(Crear).
   
   ![Creación de usuarios](./media/active-directory-saas-coupa-tutorial/IC791909.png "Creación de usuarios")
4. En la sección **Creación de usuario** , lleve a cabo estos pasos:
   
   ![Detalles del usuario](./media/active-directory-saas-coupa-tutorial/IC791910.png "Detalles del usuario")
   
   1. En los cuadros de texto relacionados, escriba los atributos **Nombre de usuario**, **Nombre**, **Apellidos**, **Id. de inicio de sesión único**, **Correo electrónico** de una cuenta válida de Azure Active Directory que quiera aprovisionar.
   2. Haga clic en **Crear**.   
   >[!NOTE]
   >El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active. 
   > 

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Canvas ofrecida por Coupa para aprovisionar cuentas de usuario de AAD. 
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Coupa, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En el ** Coupa ** página de integración de aplicaciones, haga clic en **asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-coupa-tutorial/IC791911.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-coupa-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

