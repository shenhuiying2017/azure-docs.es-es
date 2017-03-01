---
title: "Tutorial: integración de Azure Active Directory con Innotas | Microsoft Docs"
description: "Aprenda a usar Innotas con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: eb9e9c2c-4b09-4177-bbab-423fd657448e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf68d6ebbc353988e203bcfa773b2e3fe1539b8c
ms.openlocfilehash: ddd7d6dc48dcc1e61ebe348db51248330e24cdd7
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-innotas"></a>Tutorial: integración de Azure Active Directory con Innotas
El objetivo de este tutorial es mostrar la integración de Azure e Innotas.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Innotas

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Innotas podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Innotas (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Innotas
* Configuración del inicio de sesión único
* Configuración del aprovisionamiento de usuario
*  Asignación de usuarios

![Escenario](./media/active-directory-saas-innotas-tutorial/IC777331.png "Escenario")

## <a name="enable-the-application-integration-for-innotas"></a>Habilitación de la integración de aplicaciones para Innotas
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Innotas.

**Siga estos pasos para habilitar la integración de aplicaciones para Innotas:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-innotas-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-innotas-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-innotas-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Innotas**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-innotas-tutorial/IC777332.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Innotas** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Innotas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Innotas**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Innotas?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de Innotas**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino\>.Innotas.com*" y, luego, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en Innotas**, para descargar los metadatos, haga clic en **Descargar metadatos** y, después, guarde el archivo de datos en el equipo como **c:\\InnotasMetaData.xml**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurar inicio de sesión único")
5. Reenvíe el archivo de metadatos al equipo de soporte técnico de Innotas. El equipo de soporte técnico necesita configurar un inicio de sesión único para usted.
6. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Innotas.  
Cuando un usuario asignado intenta iniciar sesión en Innotas desde el panel de acceso, Innotas comprueba si el usuario existe.  

>[!NOTE]
>Si no hay cuentas de usuario disponibles, Innotas crea una automáticamente.
>

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Innotas, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Innotas**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-innotas-tutorial/IC777339.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-innotas-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


