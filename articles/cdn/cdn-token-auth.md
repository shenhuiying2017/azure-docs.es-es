---
title: "Protección de activos de la red CDN de Azure con autenticación por tokens | Microsoft Docs"
description: "Uso de la autenticación por tokens para proteger el acceso a los activos de la red CDN de Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Protección de los activos de la red CDN de Azure con autenticación por tokens

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Información general

La autenticación por tokens es un mecanismo que permite impedir que la red CDN de Azure sirva activos a clientes no autorizados.  Normalmente esto se hace para evitar la "vinculación activa" de contenido, donde otro sitio web, a menudo un panel de mensajes, usa los activos sin permiso,  lo que puede repercutir en los costos de la entrega de contenido. Al habilitar esta característica en la red CDN, los POP del borde CDN autenticarán las solicitudes antes de entregar el contenido. 

## <a name="how-it-works"></a>Cómo funciona

La autenticación por tokens comprueba que las solicitudes las genera un sitio de confianza, para lo que se requiere que contengan un valor de token con información codificada acerca del solicitante. El contenido solo se servirá al solicitante cuando la información codificada cumpla los requisitos, de lo contrario se denegarán las solicitudes. El requisito se puede configurar con uno o varios de los parámetros siguientes.

- País: permitir o denegar las solicitudes que se ha originado en determinados países.  [Lista de códigos de país válidos.](https://msdn.microsoft.com/library/mt761717.aspx) 
- Dirección URL: permitir solo la solicitud del activo o la ruta de acceso especificados.  
- Host: permitir o denegar las solicitudes mediante hosts especificados en el encabezado de la solicitud.
- Origen de referencia: permitir o denegar el origen de referencia especificado en la solicitud.
- Dirección IP: permitir sólo las solicitudes cuyo origen es una dirección IP específica o subred IP concretas.
- Protocolo: permitir o bloquear solicitudes en función del protocolo usado para solicitar el contenido.
- Fecha de expiración: asignar un período de fecha y hora para asegurarse de que un vínculo solo es válido durante un tiempo limitado.

Vea el ejemplo de configuración detallada de cada parámetro.

## <a name="reference-architecture"></a>Arquitectura de referencia

Vea a continuación de una arquitectura de referencia de configuración de autenticación por tokens en la red CDN para trabajar con la aplicación web.

![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validación de tokens en el punto de conexión de red CDN
    
Este gráfico describe cómo Azure CDN valida la solicitud del cliente cuando se configura la autenticación por tokens en el punto de conexión de red CDN.

![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuración de la autenticación por tokens

1. Desde [Azure Portal](https://portal.azure.com), busque el perfil de CDN y haga clic en el botón **Administrar** para iniciar el portal complementario.

    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Mantenga el mouse sobre **HTTP Large** (HTTP grandes) y haga clic en **Token Auth** (Autenticación por tokens) en el control flotante. En esta pestaña configurará la clave y los parámetros de cifrado.

    1. Escriba una clave de cifrado única para **Primary Key** (Clave principal).  Especifique otra para **Backup Key** (Clave de copia de seguridad).

        ![Clave de configuración de autenticación por tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configure los parámetros de cifrado con la herramienta de cifrado (permitir o denegar las solicitudes según el tiempo de expiración, el país, el origen de referencia, el protocolo y la dirección IP del cliente. Puede usar cualquier combinación).

        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec_expire: asigna una fecha de expiración a un token después de un tiempo especificado. Las solicitudes que se presenten después de la fecha de expiración se denegarán. Este parámetro utiliza la marca de tiempo de Unix (en función de los segundos transcurridos desde la época estándar de 1/1/1970 00:00:00 GMT. Puede usar herramientas en línea para proporcionar la conversión entre la hora estándar y la hora de Unix).  Por ejemplo, si desea configurar el token para que expire el 31/12/2016 12:00:00 GMT, use la hora de Unix: 1483185600 como sigue:
    
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec_url_allow: permite adaptar tokens para un activo o ruta de acceso concreto. Restringe el acceso a las solicitudes cuya dirección URL comience con una ruta de acceso relativa específica. Es posible incluir varias rutas de acceso si las separa con comas. Las direcciones URL distinguen mayúsculas de minúsculas. En función de los requisitos, puede configurar otro valor para proporcionar distintos niveles de acceso. A continuación se muestran un par de escenarios:
        
            Si tiene una dirección URL: http://www.mydomain.com/pictures/city/strasbourg.png. Consulte el valor de entrada "" y el nivel de acceso correspondiente

            1. Valor de entrada "/": se permitirán todas las solicitudes
            2. Valor de entrada "/pictures": se permitirán las solicitudes siguientes
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. Valor de entrada "/pictures/": solo se permitirán las solicitudes de /pictures/
            4. Valor de entrada "/pictures/city/strasbourg.png": solo permiten solicitudes para este activo
    
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec_country_allow: solo permite solicitudes que se originen en uno o más países especificados. Se denegarán las solicitudes que se originen en los demás países. Use el código de país para configurar los parámetros y separe los códigos de país con comas. Por ejemplo, si desea permitir el acceso desde Estados Unidos y Francia, escriba US, FR en la columna, como se muestra a continuación.  
        
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec_country_deny: deniega solicitudes que se originen en uno o más países especificados. Se permitirán las solicitudes que se originen en los demás países. Use el código de país para configurar los parámetros y separe los códigos de país con comas. Por ejemplo, si desea denegar el acceso desde Estados Unidos y Francia, escriba US, FR en la columna.
    
        - ec_ref_allow: solo se permiten solicitudes del origen de referencia especificado. Un origen de referencia identifica la dirección URL de la página web vinculada al activo que se solicita. El valor del parámetro del origen de referencia no debe incluir el protocolo. Puede escribir un nombre de host o una ruta de acceso determinada en ese nombre de host. También puede agregar varios orígenes de referencia dentro de un único parámetro si los separa con comas. Si ha especificado un valor para el origen de referencia, pero no se envía la información de este en la solicitud debido a alguna configuración del explorador, estas solicitudes se denegarán de forma predeterminada. Puede asignar "Ausente" o un valor en blanco en el parámetro para permitir estas solicitudes sin información del origen de referencia. También puede usar "*. consoto.com" para permitir todos los subdominios de consoto.com.  Por ejemplo, si desea permitir el acceso a las solicitudes de www.consoto.com, todos los subdominios de consoto2.com y las solicitudes con el remitente en blanco o sin este, introduzca el siguiente valor:
        
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec_ref_deny: deniega solicitudes del origen de referencia especificado. Consulte más detalles y un ejemplo en el parámetro "ec_ref_allow".
         
        - ec_proto_allow: solo se permiten solicitudes del protocolo especificado. Por ejemplo, http o https.
        
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec_proto_deny: deniega las solicitudes del protocolo especificado. Por ejemplo, http o https.
    
        - ec_clientip: restringe el acceso a la dirección IP del solicitante especificado. Se admiten tanto IPV4 como IPV6. Puede especificar la dirección IP de solicitud única o la subred IP.
            
        ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Puede probar el token con la herramienta de descifrado.

    4. También puede personalizar el tipo de respuesta que se devolverá al usuario cuando se deniega la solicitud. De forma predeterminada se usa 403.

3. Ahora haga clic en la ficha **Motor de reglas** en **HTTP Large** (HTTP grandes). Utilizará esta ficha para definir las rutas de acceso para aplicar la característica, habilitar la característica de autenticación de tokens y habilitar la autenticación de tokens adicional relacionada con las funcionalidades.

    - Utilice la columna "IF" para definir la ruta de acceso o los activos que desea aplicar la autenticación por tokens. 
    - Haga clic para agregar "Token Auth" (Autenticación por tokens) en la lista desplegable de característica para habilitar la autenticación por tokens.
        
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. En la ficha **Motor de reglas** hay algunas funcionalidades adicionales que puede habilitar.
    
    - Token auth denial code (Código de denegación de autenticación de token): determina el tipo de respuesta que se devolverá al usuario cuando se deniega una solicitud. Las reglas que configure aquí sobrescribirán la configuración de código de denegación en la ficha de autenticación por tokens.
    - Token auth ignore (Omitir autenticación por tokens): determina si la dirección URL usada para validar el token distingue mayúsculas de minúsculas.
    - Token auth parameter (Parámetro de autenticación por tokens): cambia el nombre del parámetro de cadena de consulta de autenticación por tokens que se muestra en la dirección URL solicitada. 
        
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Puede personalizar el token, que es una aplicación que genera el token para las características de autenticación basadas en tokens. Se puede acceder al código fuente aquí en [GitHub](https://github.com/VerizonDigital/ectoken).
Idiomas disponibles:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Precios de las características y los proveedores de Azure CDN

Consulte el tema de [introducción a la red CDN](cdn-overview.md).
