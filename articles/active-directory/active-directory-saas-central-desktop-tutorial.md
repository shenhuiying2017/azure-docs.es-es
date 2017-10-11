---
title: "Tutorial: Integración de Azure Active Directory con Central Desktop | Microsoft Docs"
description: "Aprenda cómo usar Central Desktop con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: integración de Azure Active Directory con Central Desktop
El objetivo de este tutorial es mostrar la integración de Azure y Central Desktop. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción de Central Desktop con inicio de sesión único habilitado / Inquilino de Central Desktop

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Central Desktop
* Configuración del inicio de sesión único (SSO)
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Escenario")

## <a name="enable-the-application-integration-for-central-desktop"></a>Habilitación de la integración de aplicaciones para Central Desktop
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Central Desktop.

**Siga estos pasos para habilitar la integración de aplicaciones para Central Desktop:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Central Desktop**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Central Desktop** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Central Desktop con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, se requiere cargar un certificado codificado en base 64 en el inquilino de Central Desktop.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el portal de Azure clásico, en la **Central Desktop** página de integración de aplicaciones, haga clic en **configurar inicio de sesión único** para abrir el ** configurar inicio de sesión único ** cuadro de diálogo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Central Desktop?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**. 
   
   1. En el cuadro de texto **URL de inicio de sesión de Central Desktop**, escriba la dirección URL de su inquilino de Central Desktop (por ejemplo, *http://contoso.centraldesktop.com*).
   2. En el cuadro de texto dirección URL de respuesta de Central Desktop, escriba la dirección URL de AssertionConsumerService de Central Desktop (por ejemplo, https://contoso.centraldesktop.com/saml2-assertion.php).
   
   >[!NOTE]
   >Puede obtener el valor de los metadatos de Central Desktop (por ejemplo, *http://contoso.centraldesktop.com*).
   >  
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Central Desktop**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
  ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurar inicio de sesión único")
5. Inicie sesión en su inquilino de **Central Desktop** .
6. Vaya a **Configuración**, haga clic en **Avanzadas** y, luego en **Inicio de sesión único**.
   
  ![Programa de instalación: avanzado](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Programa de instalación: avanzado")
7. En la página **Configuración de inicio de sesión único** , siga estos pasos:
   
  ![Configuración de inicio de sesión único ](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Configuración de inicio de sesión único")
   
  1. Seleccione **Enable SAML v2 Single Sign On**(Habilitar inicio de sesión único de SAML).
  2. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL de emisor** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único**.
  3. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión SSO**.
  4. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión SSO**.
8. En la sección **Método de autenticación de firma de mensaje** , realice los pasos siguientes:
   
   ![Método de autenticación de firma de mensaje](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Método de autenticación de firma de mensaje")
   
  1. Seleccione **Certificado**.
  2. En la lista **Certificado SSO**, seleccione **RSH SHA256**.
  3. Cree un archivo de texto a partir del certificado descargado, copie el contenido del archivo de texto y, a continuación, péguelo en el campo **SSO Certificate** (certificado SSO).  
     >[!TIP]
     >Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Seleccione **Mostrar un vínculo a la página de inicio de sesión SAMLv2**.
9. Haga clic en **Actualizar**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurar inicio de sesión único")
    
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para que los usuarios de AAD puedan iniciar sesión, deben aprovisionarse a Central Desktop. En esta sección se describe cómo crear cuentas de usuario de AAD en Central Desktop .

**Para el aprovisionamiento de cuentas de usuario a Central Desktop:**
1. Inicie sesión en su inquilino de Central Desktop.
2. Vaya a **Contactos \> Miembros internos**.
3. Haga clic en **Agregar miembros internos**.
   
  ![Personas](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personas")
4. En el cuadro de texto **Dirección de correo electrónico de nuevos miembros**, escriba una cuenta AAD que quiera aprovisionar y luego haga clic en **Siguiente**.
   
  ![Direcciones de correo electrónico de los nuevos miembros](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Direcciones de correo electrónico de los nuevos miembros")
5. Haga clic en **Agregar miembros internos**.
   
  ![Agregar miembros internos](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Agregar miembros internos")
   
   >[!NOTE]
   >Los usuarios que ha agregado recibirán un correo electrónico que incluye un vínculo de confirmación en el que tienen que hacer clic para activar la cuenta.
   > 

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Central Desktop ofrecida por Central Desktop para aprovisionar cuentas de usuario de AAD.
>  

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Central Desktop, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Central Desktop**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

