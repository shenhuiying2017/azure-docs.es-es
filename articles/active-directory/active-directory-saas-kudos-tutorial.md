---
title: "Tutorial: integración de Azure Active Directory con Kudos | Microsoft Docs"
description: "Aprenda a usar Kudos con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ea39ca12135fded44a8e70ec87616f79007e0200
ms.openlocfilehash: 79915c69e2d40529ec5ad96a632e2e5b2ac4dd5e
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integración de Azure Active Directory con Kudos
El objetivo de este tutorial es mostrar la integración de Azure y Kudos.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Kudos

Después de completar este tutorial, los usuarios de Azure AD asignados a Kudos podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Kudos (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Kudos
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Escenario")

## <a name="enabling-the-application-integration-for-kudos"></a>Habilitación de la integración de aplicaciones para Kudos
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Kudos.

**Siga estos pasos para habilitar la integración de aplicaciones para Kudos:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-kudos-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-kudos-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Kudos**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-kudos-tutorial/IC787800.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Kudos** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Kudos con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  

Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base&64;. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Kudos**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Kudos?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Kudos**, escriba su dirección URL con el siguiente patrón **https://company.kudosnow.com** y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Kudos**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Kudos.
6. En el menú de la parte superior, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-kudos-tutorial/IC787806.png "Configuración")
7. Haga clic en **Integrations \> SSO** (Integraciones > SSO).
8. En la sección **SSO** , siga estos pasos:
   
   ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Kudos**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Sign on URL** (URL de inicio de sesión).
   2. Cree un archivo **codificado en base&64;** a partir del certificado descargado. 
   
      >[!TIP]
      >Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   3. Abra el certificado codificado en base&64; en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509** .
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Kudos**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Logout To URL** (URL de cierre de sesión).
   5. En el cuadro de texto **Su dirección URL de Kudos** , escriba el nombre de su compañía.
   6. Haga clic en **Guardar**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para permitir que los usuarios de Azure AD inicien sesión en Kudos, deben aprovisionarse en Kudos. 

* En el caso de Kudos, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de **Kudos** .
2. En el menú de la parte superior, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-kudos-tutorial/IC787806.png "Configuración")
3. Haga clic en **Administrador de usuarios**.
4. Haga clic en la pestaña **Users** (Usuarios) y, a continuación, haga clic en **Add user** (Agregar un usuario).
   
   ![Administración de usuarios](./media/active-directory-saas-kudos-tutorial/IC787809.png "Administración de usuarios")
5. En la sección **Agregar un usuario** , realice estos pasos:
   
   ![Agregar un usuario](./media/active-directory-saas-kudos-tutorial/IC787810.png "Agregar un usuario")
   
  1. Escriba en los cuadros de texto correspondientes el **nombre**, los **apellidos**, el **correo electrónico** y otros detalles de la cuenta de Azure Active Directory válida que desee aprovisionar.
   2. Haga clic en **Crear usuario**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Kudos ofrecida por Kudos para aprovisionar cuentas de usuario de AAD.
>  

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Kudos, siga estos pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Kudos**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-kudos-tutorial/IC787811.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-kudos-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


