---
title: "Autenticación de IIS y Servidor Azure MFA | Microsoft Docs"
description: "Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación IIS y el servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/16/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: H1Hack27Feb2017,it-pro
ms.openlocfilehash: 6f63838b945f94133eaa737060ea14aaa2b27468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Configuración de Servidor Azure Multi-Factor Authentication para aplicaciones web de IIS

Use la sección Autenticación de IIS del Servidor Azure Multi-Factor Authentication (MFA) para habilitar y configurar la autenticación de IIS para la integración con aplicaciones web de Microsoft IIS. El Servidor Azure MFA instala un complemento que puede filtrar las solicitudes realizadas al servidor web IIS para agregar Azure Multi-Factor Authentication. El complemento IIS proporciona compatibilidad para la autenticación basada en formularios y la autenticación HTTP de Windows integrada. También se pueden configurar IP de confianza para eximir direcciones IP internas de la autenticación en dos fases.

![Autenticación IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Mediante la autenticación IIS basada en formularios con el servidor Azure Multi-Factor Authentication
Para proteger una aplicación web IIS que usa la autenticación basada en formularios, instale el Servidor Azure Multi-Factor Authentication en el servidor web IIS y configure el servidor de acuerdo con el procedimiento siguiente:

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña **Basados en formularios**.
3. Haga clic en **Agregar**.
4. Para detectar el nombre de usuario, la contraseña y las variables de dominio automáticamente, escriba la dirección URL de inicio de sesión (como https://localhost/contoso/auth/login.aspx) en el cuadro de diálogo Configuración automática de sitio web basado en formularios y haga clic en **Aceptar**.
5. Active la casilla **Requerir coincidencia de usuario de Multi-Factor Authentication** si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.
6. Si no se pueden detectar automáticamente las variables de página, haga clic en **Especificar manualmente** en el cuadro de diálogo Configuración automática de sitio web basado en formularios.
7. En el cuadro de diálogo Agregar sitio web basado en formularios, escriba la dirección URL en la página de inicio de sesión en el campo URL de envío y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
8. Seleccione el formato de solicitud correcto. Esto se establece en **POST o GET** para la mayoría de las aplicaciones web.
9. Especifique las variables de nombre de usuario, contraseña y dominio (si aparece en la página de inicio de sesión). Para buscar los nombres de los cuadros de entrada, vaya a la página de inicio de sesión en un explorador web, haga clic con el botón derecho en la página y seleccione **Ver código fuente**.
10. Active la casilla **Requerir coincidencia de usuario de Azure Multi-Factor Authentication** si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.
11. Haga clic en **Avanzada** para revisar la configuración avanzada, incluido lo siguiente:

  - Seleccionar un archivo de página de denegación personalizado
  - Almacenar en caché las autenticaciones correctas en el sitio web durante un período mediante las cookies.
  - Seleccionar si autenticar las credenciales principales en un dominio de Windows, directorio LDAP o servidor RADIUS.

12. Haga clic en **Aceptar** para volver al cuadro de diálogo Agregar sitio web basado en formularios.
13. Haga clic en **Aceptar**.
14. Una vez detectadas o especificadas las variables de página y dirección URL, los datos del sitio web se mostrarán en el panel Basados en formularios.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Uso de la autenticación integrada de Windows con el servidor Azure Multi-Factor Authentication
Para proteger una aplicación web IIS que usa la autenticación integrada HTTP de Windows, instale el Servidor Azure MFA en el servidor web IIS y configure el servidor mediante el procedimiento siguiente:

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.
2. Haga clic en la pestaña **HTTP**.
3. Haga clic en **Agregar**.
4. En el cuadro de diálogo Agregar URL base, escriba la dirección URL para el sitio web donde se realiza la autenticación HTTP (como http://localhost/owa) y proporcione un nombre de la aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.
5. Ajuste el tiempo de espera de inactividad y los tiempos de sesión máximos si el valor predeterminado no es suficiente.
6. Active la casilla **Requerir coincidencia de usuario de Multi-Factor Authentication** si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.
7. Active la casilla **Caché de cookies** si lo desea.
8. Haga clic en **Aceptar**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Habilitar complementos IIS para el servidor Azure Multi-Factor Authentication
Después de configurar las direcciones URL basadas en formularios o de autenticación HTTP y la configuración, seleccione las ubicaciones donde se deben cargar y habilitar los complementos de IIS de Azure Multi-Factor Authentication en IIS. Utilice el siguiente procedimiento:

1. Si se ejecuta en IIS 6, haga clic en la pestaña **ISAPI**. Seleccione el sitio web en el que se está ejecutando la aplicación web (por ejemplo, el sitio web predeterminado) para habilitar el complemento del filtro ISAPI de Azure Multi-Factor Authentication para ese sitio.
2. Si se ejecuta en IIS 7 o posterior, haga clic en la pestaña **Módulo nativo**. Seleccione el servidor, los sitios web o las aplicaciones para habilitar el complemento de IIS en los niveles deseados.
3. Haga clic en el cuadro **Habilitar autenticación IIS** en la parte superior de la pantalla. Azure Multi-Factor Authentication está ahora protegiendo la aplicación IIS seleccionada. Asegúrese de que los usuarios se han importado en el servidor.

## <a name="trusted-ips"></a>IP de confianza
Las IP de confianza permiten a los usuarios omitir Azure Multi-Factor Authentication para las solicitudes de sitio web procedentes de determinadas direcciones IP o subredes. Por ejemplo, puede que desee excluir a determinados usuarios de Azure Multi-Factor Authentication mientras inicia sesión desde la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza. Para configurar direcciones IP de confianza, use el procedimiento siguiente:

1. En la sección Autenticación de IIS, haga clic en la pestaña **IP de confianza**.
2. Haga clic en **Agregar**.
3. Cuando aparezca el cuadro de diálogo Agregar IP de confianza, seleccione el botón de radio **IP única**, **Intervalo IP** o **Subred**.
4. Escriba la dirección IP, el intervalo IP o la subred que debe estar en la lista blanca. Si especifica una subred, seleccione la máscara de red adecuada y haga clic en **Aceptar**. Se ha agregado a la lista blanca.
