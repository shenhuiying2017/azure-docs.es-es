---
title: "Tutorial: integración de Azure Active Directory con Directions on Microsoft | Microsoft Docs"
description: "Aprenda a usar Directions on Microsoft con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: efea82512dad3f2e5d1c0dd45e9e2f6db9a283d4
ms.openlocfilehash: a5538e89b1c23e4a2ad45c5515de8167ef95b46a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Tutorial: Integración de Azure Active Directory con Directions on Microsoft
El objetivo de este tutorial es mostrar la integración de Azure Active Directory y Directions on Microsoft.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción a Directions on Microsoft

Si no tiene todavía una suscripción federada a Directions on Microsoft, envíe por correo electrónico una solicitud a**service@DirectionsOnMicrosoft.com**.

Después de completar este tutorial, los usuarios de Azure Active Directory que hayan asignado a Directions on Microsoft podrán realizar inicios de sesión únicos en la aplicación usando el inicio de sesión único.

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Directions on Microsoft
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Escenario")

## <a name="enable-the-application-integration-for-directions-on-microsoft"></a>Habilitación de la integración de aplicaciones para Directions on Microsoft
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Directions on Microsoft.

**Siga estos pasos para habilitar la integración de aplicaciones para Directions on Microsoft:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Directions on Microsoft**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Directions on Microsoft**y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Escenario](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Escenario")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Directions on Microsoft con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

**Para configurar el inicio de sesión único (SSO), realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Directions on Microsoft**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar inicio de sesión único](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Habilitar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Directions on Microsoft?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Inicio de sesión único de Microsoft Azure AD")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto URL de inicio de sesión, escriba **https://www.directionsonmicrosoft.com/user/login** y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Directions on Microsoft**, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar inicio de sesión único")
5. Envíe el archivo de metadatos al equipo de soporte técnico de Directions on Microsoft (*service@DirectionsOnMicrosoft.com*). Para que el equipo de soporte técnico de Directions on Microsoft pueda buscar su pertenencia al sitio federado, incluya la información de su compañía en el correo electrónico.
   
   >[!NOTE]
   >El inicio de sesión único de Directions on Microsoft debe habilitarlo el equipo de soporte técnico de Directions on Microsoft. Recibirá una notificación cuando se haya habilitado el inicio de sesión único. 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
  ![Configurar inicio de sesión único](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Directions on Microsoft.  

Cuando un usuario asignado intenta iniciar sesión en Directions on Microsoft desde el Panel de acceso, Directions on Microsoft comprueba si el usuario existe. Si no hay cuentas de usuario disponibles, Directions on Microsoft crea una automáticamente.

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar a usuarios a Directions on Microsoft, siga estos pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Directions on Microsoft**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sí")


