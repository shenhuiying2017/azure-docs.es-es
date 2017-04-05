---
title: "PingAccess para el proxy de la aplicación de Azure AD | Microsoft Docs"
description: "Publique aplicaciones con PingAccess y el proxy de la aplicación que admitan la autenticación basada en encabezados."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 173607c481d0ba7ceece6310fcd131ff622a0677
ms.lasthandoff: 03/23/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-pingaccess-for-azure-ad"></a>Publicación de aplicaciones que admiten autenticación basada en encabezados con PingAccess para Azure AD

El proxy de la aplicación Azure Active Directory y PingAccess se han asociado para proporcionar a los clientes de Azure Active Directory acceso a más aplicaciones. PingAccess expande las [ofertas actuales del proxy de la aplicación](active-directory-application-proxy-get-started.md) para incluir el acceso remoto a aplicaciones que utilizan encabezados para la autenticación. 

## <a name="what-is-pingaccess-for-azure-ad"></a>¿Qué es PingAccess para Azure AD?

Para conceder a los usuarios acceso a aplicaciones que utilizan encabezados para la autenticación, la aplicación se publica para el acceso remoto tanto en el proxy de la aplicación como en PingAccess. El proxy de la aplicación trata estas aplicaciones como a las demás, usa Azure AD para autenticar el acceso y, después, pasa el tráfico a través del servicio de conector. PingAccess se coloca delante de las aplicaciones y convierte el token de acceso de Azure AD en un encabezado, con el fin de que la aplicación reciba la autenticación en un formato que pueda leer. 

Los usuarios no notarán ninguna diferencia al iniciar sesión para usar las aplicaciones corporativas. Podrán seguir trabajando desde cualquier lugar y en cualquier dispositivo. Cuando los usuarios están en la oficina, ni el proxy de la aplicación ni PingAccess interceptan el tráfico, por lo que los usuarios obtienen la misma experiencia de siempre.

Dado que los conectores del proxy de la aplicación dirigen el tráfico remoto a todas las aplicaciones, independientemente de su tipo de autenticación, seguirán equilibrando la carga de forma automática. 

## <a name="how-do-i-get-access"></a>¿Cómo puedo obtener acceso?

Dado que este escenario se ofrece a través de una asociación entre Azure Active Directory y PingAccess, se necesitarán licencias de ambos servicios. Las suscripciones de Azure Active Directory Premium incluyen una licencia de PingAccess, que permite configurar hasta 20 aplicaciones con este flujo. 

Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-first-application"></a>Publicación de la primera aplicación

Este artículo va dirigido a quienes publican por primera vez una aplicación con este escenario. Es una guía a través de cómo empezar a trabajar con Application y PingAccess, además de explicar los pasos de la publicación. Si ya ha configurado ambos servicios, pero desea que una actualización de los pasos de publicación, puede omitir las dos secciones sobre el registro.

>[!NOTE]
>Dado que este escenario es una asociación entre Azure AD y PingAccess, algunas de las instrucciones se encuentran en el sitio de Ping Identity. 

### <a name="install-an-application-proxy-connector"></a>Instalación de un conector del proxy de la aplicación

Si el proxy de la aplicación ya está habilitado y tiene un conector instalado, puede omitir estos pasos.

El conector del proxy de la aplicación es un servicio de Windows Server que dirige el tráfico de los empleados remotos a las aplicaciones publicadas. Para más información acerca de las instrucciones de instalación, consulte [Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global. 
2. Seleccione **Azure Active Directory** > **Proxy de la aplicación**.
3. Seleccione **Download Connector** (Descargar conector) para iniciar la descarga de conector del proxy de la aplicación. Siga las instrucciones de instalación. 
4. La descarga del conector debería habilitar automáticamente el proxy de la aplicación en el directorio, pero si no lo hace, puede seleccionar **Habilitar el proxy de la aplicación**. 

![Habilitar el proxy de la aplicación y descargar el conector](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Incorporación de la aplicación a Azure AD con el proxy de la aplicación

Esta sección tiene dos partes. En primer lugar, es preciso publicar la aplicación en Azure AD. Y después, hay que recopilar información acerca de la aplicación que se puede usar en los pasos de PingAccess. 

#### <a name="publish-the-app"></a>Publicación de la aplicación

1. Si no lo hizo en la sección anterior, inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global. 
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales**. 
3. Seleccione **Agregar** en la parte superior de la hoja. 
4. Seleccione **Aplicación local**.
5. Rellene los campos requeridos con la información de la aplicación nueva. Utilice las siguientes instrucciones para realizar la configuración:
  - **Dirección URL interna**: normalmente, especifica la dirección URL que lleva a la página de inicio de sesión la aplicación cuando esté en la red corporativa. Para esta asociación, el conector tiene que tratar el proxy PingAccess como la página principal de la aplicación. Use este formato: `https://<host name of your PA server>:<port>/<App path name>`. El puerto predeterminado es 3000, pero puedo configurar uno distinto en PingAccess.
  - **Método de autenticación previa**: Azure Active Directory.
  - **Traducir URL en encabezados**: No.
6. Seleccione **Agregar** en la parte inferior de la hoja. Se agrega la aplicación y se abre el menú de inicio rápido. 
7. En dicho menú, seleccione **Asignar un usuario para las pruebas** y agregue al menos un usuario a la aplicación. Asegúrese de que esta cuenta de prueba tiene acceso a la aplicación local. 
8. Seleccione **Asignar** para guardar la asignación del usuario de prueba. 
9. En la hoja de administración de la aplicación, seleccione **Inicio de sesión único**. 
10. Elija **Header-based sign-on** (Inicio de sesión basado en encabezado) en el menú desplegable. Seleccione **Guardar**.

  ![Selección del inicio de sesión basado en encabezados](./media/application-proxy-ping-access/sso-header.PNG)

11. Cierre la hoja Aplicaciones empresariales o desplácese completamente hacia la izquierda para volver al menú de Azure Active Directory. 
12. Seleccione **App registrations** (Registros de aplicaciones).
13. Seleccione la aplicación que se acaba de agregar y seleccione **Direcciones URL de respuesta**. 
14. Compruebe si la dirección URL externa que asignó a la aplicación en el paso 5 se encuentra en la lista de direcciones URL de respuesta. Si no está, agréguela ahora. 
15. En la hoja de configuración de la aplicación, seleccione **Permisos necesarios**. 
16. Seleccione **Agregar**. Para la API, elija **Microsoft Azure Active Directory** y, después, **Seleccionar**. Para los permisos, elija **Read and write all applications** (Leer y escribir en todas las aplicaciones) y **Sign in and read user profile** (Iniciar sesión y leer perfil de usuario), luego **Seleccionar** y, finalmente, **Listo**.  

  ![Selección de permisos](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>Recopilación de información para los pasos de PingAccess

1. Sin salir de la hoja de configuración de la aplicación seleccione **Propiedades**. Guarde el valor de **Id. de la aplicación**, ya que se usa para el identificador de cliente cuando se configura PingAccess.
2. En la misma hoja, seleccione **Claves**. 
3. Cree una clave, para lo que debe escribir una descripción de la misma y elegir una fecha de expiración en el menú desplegable. 
4. Seleccione **Guardar**. Aparece un GUID en el campo **Valor**. 

  Guarde este valor, ya que no lo volverá a ver una vez que cierre esta ventana. 

5. Cierre la hoja Registros de aplicaciones o desplácese completamente hacia la izquierda para volver al menú de Azure Active Directory.
6. Seleccione **Propiedades**.
7. Guarde el GUID de **Id. de directorio**. 

### <a name="download-pingaccess-and-configure-your-app"></a>Descarga de PingAccess y configuración de la aplicación

Los pasos detallados de la parte de PingAccess de este escenario se pueden encontrar en la documentación de Ping Identity, [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) (Configuración de PingAccess para Azure AD).

Dichos pasos le guiarán a través del proceso de obtención de una cuenta de PingAccess, en caso de que aún no la tenga, instalación del servidor de PingAccess y creación de una conexión de proveedor de OIDC de Azure AD con el Id. de directorio que ha copiado de Azure Portal. A continuación, utilice los valores de Id. de la aplicación y Clave para crear una sesión Web en PingAccess. Después, puede configurar la asignación de identidades y crear un host virtual, un sitio y una aplicación.

### <a name="test-your-app"></a>Prueba de la aplicación

Cuando haya completado todos los pasos, la aplicación debería estar en funcionamiento. Para probarla, abra un explorador y navegue a la dirección URL externa que creó al publicar la aplicación en Azure. Inicie sesión con la cuenta de prueba que asignó a la aplicación. 

## <a name="next-steps"></a>Pasos siguientes

- [Solucionar problemas de Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

