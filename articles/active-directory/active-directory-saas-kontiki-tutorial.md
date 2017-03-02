---
title: "Tutorial: Integración de Azure Active Directory con Kontiki | Microsoft Docs"
description: "Aprenda a usar Kontiki con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 230facec9f8f83e8a94eaaac5a1495195ff45cc7
ms.openlocfilehash: 3af125b186e3905572dd5d621c948a6639f9b023
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integración de Azure Active Directory con Kontiki
El objetivo de este tutorial es mostrar la integración de Azure y Kontiki.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único (SSO) en Kontiki

Después de completar este tutorial, los usuarios de Azure AD asignados a Kontiki podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Kontiki (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Kontiki
* Configuración del inicio de sesión único
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Escenario")

## <a name="enable-the-application-integration-for-kontiki"></a>Habilitación de la integración de aplicaciones para Kontiki
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Kontiki.

**Siga estos pasos para habilitar la integración de aplicaciones para Kontiki:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Kontiki**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Kontiki** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Kontiki con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

*Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Kontiki**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Kontiki?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Kontiki**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en Kontiki (por ejemplo: *https://company.mc.eval.kontiki.com/*) y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Kontiki**, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurar inicio de sesión único")
5. Envíe el archivo de metadatos al equipo de soporte técnico de Kontiki.
   
   >[!NOTE]
   >La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Kontiki. Tan pronto como se complete la configuración, recibirá una notificación. 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
  ![Configurar inicio de sesión único](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Kontiki. Cuando un usuario asignado intenta iniciar sesión en Kontiki desde el Panel de acceso, Kontiki comprueba si el usuario existe.  

>!NOTE] Si no hay cuentas de usuario disponibles, Kontiki crea una automáticamente.
>

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Kontiki, siga estos pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Kontiki**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


