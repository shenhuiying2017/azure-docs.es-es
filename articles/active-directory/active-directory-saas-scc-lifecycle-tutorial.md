---
title: "Tutorial: Integración de Azure Active Directory con SCC LifeCycle | Microsoft Docs"
description: "Aprenda cómo usar SCC LifeCycle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integración de Azure Active Directory con SCC LifeCycle
El objetivo de este tutorial es mostrar la integración de Azure y SCC LifeCycle.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único (SSO) en SCC LifeCycle

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a SCC LifeCycle podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SCC LifeCycle (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SCC LifeCycle
2. Configuración del inicio de sesión único (SSO)
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Escenario")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Habilitación de la integración de aplicaciones para SCC LifeCycle
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SCC LifeCycle.

**Siga estos pasos con el fin de habilitar la integración de aplicaciones en SCC LifeCycle:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **SCC LifeCycle**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **SCC LifeCycle** y haga clic en **Completar** para agregar la aplicación.
   
    ![Ciclo de vida de SCC](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Ciclo de vida de SCC")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SCC LifeCycle con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SCC LifeCycle**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en SCC LifeCycle?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión** de, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de SCC LifeCycle con el patrón "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" y haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en SCC LifeCycle**, haga clic en **Descargar metadatos** y guarde el archivo de metadatos localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar inicio de sesión único")
5. Reenvíe el archivo de metadatos al equipo de soporte técnico de SCC LifeCycle.
   
   >[!NOTE]
   >El equipo de soporte técnico de SCC LifeCycle es el que debe habilitar el inicio de sesión único.
   > 
   > 

6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en SCC LifeCycle, deben aprovisionarse en SCC LifeCycle. No hay elemento de acción para que configure el aprovisionamiento de usuarios en SCC LifeCycle.

Cuando un usuario asignado intente iniciar sesión en LifeCycle, se creará automáticamente una cuenta de LifeCycle si es necesario.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SCC LifeCycle ofrecida por SCC LifeCycle para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a SCC LifeCycle, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **SCC LifeCycle**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

