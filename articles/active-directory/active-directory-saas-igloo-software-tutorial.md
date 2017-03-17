---
title: "Tutorial: integración de Azure Active Directory con Igloo Software | Microsoft Docs"
description: "Aprenda cómo usar Igloo Software con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06e70434d61af0f4d704bd5bc95e3c30672e4c15
ms.openlocfilehash: 5711f9957f0c982f8193d07d536d6665c7a46ec1
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integración de Azure Active Directory con Igloo Software
El objetivo de este tutorial es mostrar la integración de Azure e Igloo Software.  

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un suscripción habilitada para el inicio de sesión único en [Igloo Software](http://www.igloosoftware.com/)

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Igloo Software podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Igloo Software (inicio de sesión iniciado por el proveedor de servicios) o con las instrucciones que se indican en [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Igloo Software
2. Configuración del inicio de sesión único (SSO)
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Escenario")

## <a name="enable-the-application-integration-for-igloo-software"></a>Habilitación de la integración de aplicaciones para Igloo Software
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Igloo Software.

**Siga estos pasos para habilitar la integración de aplicaciones para Igloo Software:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Igloo Software**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Igloo Software** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
   
## <a name="configure-sso"></a>Configuración de SSO

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Igloo Software con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  

Como parte de este procedimiento, se requiere cargar un certificado codificado en base&64; en el inquilino de Central Desktop. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Igloo Software**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Igloo Software?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Inicio de sesión único de Microsoft Azure AD")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Igloo Software**, escriba su dirección URL con el siguiente patrón "*https://company.igloocommunities.com/?signin*" y, a continuación, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en Igloo Software**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de Igloo Software de la compañía.
6. Vaya a **Control Panel**(Panel de control).
   
   ![Panel de control](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Panel de control")
7. En la pestaña **Membership** (Pertenencia), haga clic en **Sign In Settings** (Configuración de inicio de sesión).
   
   ![Configuración de inicio de sesión](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Configuración de inicio de sesión")
8. En la sección SAML Configuration (Configuración de SAML), haga clic en **Configure SAML Authentication**(Configurar la autenticación de SAML).
   
   ![Configuración de SAML](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuración de SAML")
9. En la sección de **General Configuration** (Configuración general), realice los pasos siguientes:
   
   ![Configuración general](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuración general")
   1. En el cuadro de texto **Connection Name** (Nombre de conexión), escriba el nombre personalizado de la configuración.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Igloo Software**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión de IdP**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Igloo Software**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión de IdP**.
   4. En **Logout Response and Request HTTP Type** (Respuesta de cierre de sesión y tipo de solicitud HTTP), seleccione **POST**.
   5. Cree un archivo de texto a partir del certificado descargado.    
   
       >[!TIP]
       >Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
       > 
   6. Quite la primera línea y la última línea de la versión del archivo de texto de su certificado, copie el texto del certificado restante y después péguelo en el cuadro de texto **Certificado público** .
10. En **Response and Authentication Configuration**(Configuración de autenticación y respuesta), realice los pasos siguientes:
    
    ![Configuración de autenticación y respuesta](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuración de autenticación y respuesta")
  1. En **Identity Provider** (Proveedor de identidades), seleccione **Microsoft ADFS**.
  2. En **Identifier Type** (Tipo de identificador), seleccione **Email Address** (Dirección de correo electrónico).
  3. En el cuadro de texto **Atributo de correo electrónico**, escriba **emailaddress**.
  4. En el cuadro de texto **First Name Attribute** (Atributo de nombre), escriba **givenname**.
  5. En el cuadro de texto **Last Name Attribute** (Atributo de apellido), escriba **surname**.
11. Realice los pasos siguientes para completar la configuración:
    
    ![Creación de usuario de inicio de sesión](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Creación de usuario de inicio de sesión")   
  1. En **User creation on Sign in** (Creación de usuario al inicio de sesión), seleccione **Create a new user in your site when they sign in** (Crear un nuevo usuario en el sitio cuando se inicia sesión).
  2. En **Sign in Settings** (Configuración de inicio de sesión), seleccione **Use SAML button on "Sign in" screen** (Usar botón SAML en la pantalla "Iniciar sesión").
  3. Haga clic en **Guardar**.
12. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Igloo Software.  

Cuando un usuario asignado intenta iniciar sesión en Igloo Software desde el Panel de acceso, Igloo Software comprueba si el usuario existe.  Si no hay cuentas de usuario disponibles, Igloo Software crea una automáticamente.

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Igloo Software, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Igloo Software**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


