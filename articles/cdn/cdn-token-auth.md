---
title: "Protección de activos de la red CDN de Azure con autenticación por tokens | Microsoft Docs"
description: "Obtenga información sobre cómo usar la autenticación por tokens para proteger el acceso a los recursos de Azure CDN."
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
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 700f4c49bbcda1eccbcc7eafc703e625697fa2b4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Protección de recursos de Azure Content Delivery Network con la autenticación por tokens

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general

La autenticación por tokens es un mecanismo que permite impedir que Azure Content Delivery Network (CDN) proporcione recursos a clientes no autorizados. Normalmente esto se hace para evitar la "vinculación activa" de contenido, donde otro sitio web, a menudo un panel de mensajes, usa los recursos sin permiso. La vinculación activa puede repercutir en los costos de la entrega de contenido. Al habilitar la autenticación por tokens en la red de entrega de contenido, los POP perimetrales de dicha red autentican las solicitudes antes de que la propia red entregue el contenido. 

## <a name="how-it-works"></a>Cómo funciona

La autenticación por tokens verifica que las solicitudes las genera un sitio de confianza, para lo que se requiere que contengan un valor de token con información codificada sobre el solicitante. El contenido solo se proporciona al solicitante cuando la información codificada cumple los requisitos; de lo contrario, las solicitudes se deniegan. Puede configurar los requisitos mediante el uso de uno o varios de los siguientes parámetros:

- País: permitir o denegar las solicitudes que se originan en los países especificados con su [código de país](https://msdn.microsoft.com/library/mt761717.aspx).
- Dirección URL: permitir solo las solicitudes que coinciden con el recurso o la ruta de acceso especificados.
- Host: permitir o denegar las solicitudes que usan los hosts especificados en el encabezado de la solicitud.
- Origen de referencia: permitir o denegar las solicitudes procedentes del origen de referencia especificado.
- Dirección IP: permitir solo las solicitudes cuyo origen es una dirección IP específica o subred IP concretas.
- Protocolo: permitir o denegar las solicitudes en función del protocolo usado para solicitar el contenido.
- Fecha de expiración: asignar un período de fecha y hora para asegurarse de que un vínculo solo es válido durante un tiempo limitado.

Para obtener más información, vea los ejemplos de configuración detallados para cada parámetro en [Configuración de la autenticación por tokens](#setting-up-token-authentication).

Después de generar un token cifrado, anéxelo como una cadena de consulta al final de la ruta de acceso de la dirección URL del archivo. Por ejemplo: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>Arquitectura de referencia

En el siguiente diagrama de flujo de trabajo se describe cómo la red de entrega de contenido usa la autenticación por tokens para trabajar con la aplicación web.

![Flujo de trabajo de la autenticación por tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validación de tokens en el punto de conexión de red CDN
    
En el diagrama de flujo siguiente se describe cómo Azure CDN valida la solicitud del cliente cuando se configura la autenticación por tokens en el punto de conexión de la red de entrega de contenido.

![Lógica de la validación de tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuración de la autenticación por tokens

1. En [Azure Portal](https://portal.azure.com), busque el perfil de la red CDN y haga clic en **Administrar** para iniciar el portal complementario.

    ![Botón de administración del perfil de la red CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Mantenga el mouse sobre **HTTP Large** (HTTP grandes) y haga clic en **Token Auth** (Autenticación por tokens) en el control flotante. Después puede configurar la clave y los parámetros de cifrado como sigue:

    1. Escriba una clave de cifrado única en el cuadro **Clave principal** y, opcionalmente, escriba una clave de copia de seguridad en el cuadro **Clave de copia de seguridad**.

        ![Clave de configuración de autenticación por tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configure los parámetros de cifrado con la herramienta de cifrado. Con la herramienta de cifrado, puede permitir o denegar las solicitudes según la fecha de expiración, el país, el origen de referencia, el protocolo y la dirección IP del cliente (con cualquier combinación). 

        ![Herramienta de cifrado de la red CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Escriba los valores para uno o varios de los siguientes parámetros de cifrado en el área **Encrypt Tool** (Herramienta de cifrado):  

       - **ec_expire**: asigna una fecha de expiración a un token, tras la cual el token expira. Las solicitudes que se presenten después de la fecha de expiración se deniegan. Este parámetro utiliza una marca de tiempo UNIX, que se basa en el número de segundos transcurridos desde la época estándar de `1/1/1970 00:00:00 GMT`. (Puede usar herramientas en línea para la conversión entre la hora estándar y la hora UNIX). Por ejemplo, si desea que el token expire en la fecha `12/31/2016 12:00:00 GMT`, utilice el valor de marca de tiempo UNIX, `1483185600`, como se indica a continuación. 
    
         ![Ejemplo de ec_expire de la red CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec_url_allow**: permite adaptar tokens para un recurso o ruta de acceso concretos. Restringe el acceso a las solicitudes cuya dirección URL comience con una ruta de acceso relativa específica. Las direcciones URL distinguen mayúsculas de minúsculas. Incluya varias rutas de acceso separadas por comas. En función de los requisitos, puede configurar otros valores para proporcionar distintos niveles de acceso. 
        
         Por ejemplo, para la dirección URL `http://www.mydomain.com/pictures/city/strasbourg.png`, estas solicitudes se permiten para los siguientes valores de entrada:

         - Valor de entrada `/`: se permiten todas las solicitudes.
         - Valor de entrada `/pictures`: se permiten las siguientes solicitudes:
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - Valor de entrada `/pictures/`: solo se permiten las solicitudes que contienen la ruta de acceso `/pictures/`. Por ejemplo: `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - Valor de entrada `/pictures/city/strasbourg.png`: solo se permiten las solicitudes de esta ruta de acceso y recurso específicos.
    
       - **ec_country_allow**: solo permite solicitudes que se originen en uno o más países especificados. Se deniegan las solicitudes originadas en los demás países. Use códigos de país separados por comas. Por ejemplo, si solo desea permitir el acceso desde Estados Unidos y Francia, escriba US, FR en el cuadro, como se muestra a continuación.  
        
           ![Ejemplo de ec_country_allow de la red CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec_country_deny**: deniega solicitudes que se originan en uno o más países especificados. Se permiten las solicitudes originadas en todos los demás países. Use códigos de país separados por comas. Por ejemplo, si desea denegar el acceso desde Estados Unidos y Francia, escriba US, FR en el cuadro.
    
       - **ec_ref_allow**: solo se permiten solicitudes del origen de referencia especificado. Un origen de referencia identifica la dirección URL de la página web vinculada al recurso que se solicita. No incluya el protocolo en el valor del parámetro del origen de referencia. Se permiten los siguientes tipos de entrada para el valor del parámetro:
           - Un nombre de host o un nombre de host y una ruta de acceso.
           - Varios orígenes de referencia. Para agregar varios orígenes de referencia, sepárelos por comas. Si especifica un valor para el origen de referencia, pero no se envía la información de este en la solicitud debido a alguna configuración del explorador, estas solicitudes se deniegan de forma predeterminada. 
           - Solicitudes en las que falta información sobre el origen de referencia. Para permitir estos tipos de solicitudes, escriba el texto "falta" o escriba un valor en blanco. 
           - Subdominios. Para permitir subdominios, escriba un asterisco (\*). Por ejemplo, para permitir todos los subdominios de `consoto.com`, escriba `*.consoto.com`. 
           
          En el ejemplo siguiente se muestra la entrada para permitir el acceso a las solicitudes de `www.consoto.com`, todos los subdominios de `consoto2.com` y las solicitudes sin origen de referencia o con un origen de referencia en blanco.
        
          ![Ejemplo de ec_ref_allow de la red CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec_ref_deny**: deniega solicitudes del origen de referencia especificado. La implementación es la misma que con el parámetro ec_ref_allow.
         
       - **ec_proto_allow**: solo se permiten solicitudes del protocolo especificado. Por ejemplo, HTTP o HTTPS.
            
       - **ec_proto_deny**: deniega las solicitudes del protocolo especificado. Por ejemplo, HTTP o HTTPS.
    
       - **ec_clientip**: restringe el acceso a la dirección IP del solicitante especificado. Se admiten tanto IPV4 como IPV6. Puede especificar una dirección IP de solicitud única o una subred IP.
            
         ![Ejemplo de ec_clientip de la red CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    3. Cuando termine de especificar los valores de los parámetros de cifrado, seleccione el tipo de clave para cifrar (si ha creado una clave principal y una clave de copia de seguridad) en la lista **Key To Encrypt** (Clave para cifrar), una versión de cifrado en la lista **Encryption Version** (Versión de cifrado) y después haga clic en **Cifrar**.
        
    4. También tiene la opción de probar el token con la herramienta de descifrado. Pegue el valor del token en el cuadro **Token to Decrypt** (Token para descifrar). Seleccione el tipo de clave de cifrado para descifrar en la lista desplegable **Key To Decrypt** (Clave para descifrar) y después haga clic en **Descifrar**.

    5. También puede personalizar el tipo de código de respuesta que se devuelve cuando se deniega una solicitud. Seleccione el código en la lista desplegable **Código de respuesta** y haga clic en **Guardar**. El código de respuesta **403** (Prohibido) está seleccionado de forma predeterminada. Para determinados códigos de respuesta, también puede especificar la dirección URL de la página de error en el cuadro **Valor de encabezado**. 

    6. Después de generar un token cifrado, anéxelo como una cadena de consulta al final del archivo en la ruta de acceso de la dirección URL. Por ejemplo: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

3. En **HTTP Large** (HTTP grandes), haga clic en **Motor de reglas**. Utilice el motor de reglas para definir las rutas de acceso para aplicar la característica, habilitar la característica de autenticación por tokens y habilitar otras funcionalidades relacionadas con la autenticación por tokens. Para más información, vea [Referencia del motor de reglas](cdn-rules-engine-reference.md).

    1. Seleccione una regla existente o cree una para definir el recurso o la ruta de acceso para los que desea aplicar la autenticación por tokens. 
    2. Para habilitar la autenticación por tokens según una regla, seleccione **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)** (Autenticación por tokens) en la lista desplegable **Características** y después seleccione **Habilitada**. Haga clic en **Actualizar** si va a actualizar una regla o en **Agregar** si va a crearla.
        
    ![Ejemplo de autenticación por tokens habilitada en el motor de reglas de la red CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. En el motor de reglas, también puede habilitar otras características relacionadas con la autenticación por tokens. Para habilitar cualquiera de las siguientes características, selecciónela en la lista desplegable **Características** y después seleccione **Habilitada**.
    
    - **[Token auth denial code](cdn-rules-engine-reference-features.md#token-auth-denial-code)** (Código de denegación de autenticación de token): determina el tipo de respuesta que se devuelve al usuario cuando se deniega una solicitud. Las reglas establecidas aquí invalidan el código de respuesta especificado en la sección **Custom Denial Handling** (Control de denegación personalizado) de la página de autenticación por tokens.
    - **[Token Auth Ignore URL Case](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)** (Ignorar mayúsculas y minúsculas en URL de autenticación por tokens): determina si la dirección URL usada para validar el token distingue mayúsculas de minúsculas.
    - **[Token Auth Parameter](cdn-rules-engine-reference-features.md#token-auth-parameter)** (Parámetro de autenticación por tokens): cambia el nombre del parámetro de cadena de consulta de autenticación por tokens que aparece en la dirección URL solicitada. 
        
    ![Ejemplo de configuración de la autenticación por tokens en el motor de reglas de la red CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Puede personalizar el token mediante el acceso al código fuente en [GitHub](https://github.com/VerizonDigital/ectoken).
Idiomas disponibles:
    
- C
- C#
- PHP
- Perl
- Java
- Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Precios de las características y los proveedores de Azure CDN

Para obtener información, vea [Información general sobre Azure CDN](cdn-overview.md).
