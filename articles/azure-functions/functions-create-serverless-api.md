---
title: "Creación de una API sin servidor mediante Azure Functions | Microsoft Docs"
description: "Cómo crear una API sin servidor mediante Azure Functions"
services: functions
author: mattchenderson
manager: erikre
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/04/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2b11d08b95d658ae2b0303107367549e6d0b5dff
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-serverless-api-using-azure-functions"></a>Creación de una API sin servidor mediante Azure Functions

En este tutorial, aprenderá cómo Azure Functions permite crear API de gran escalabilidad. Azure Functions incluye una colección de enlaces y desencadenadores HTTP integrados que facilitan la creación de un punto de conexión en diversos lenguajes, como Node.JS, C# y muchos más. En este tutorial, va a personalizar un desencadenador HTTP para administrar acciones específicas en el diseño de la API. También va a prepararse para ampliar su API integrándola con Servidores proxy de Azure Functions y configurando API simuladas. Todo esto se logra en un entorno de proceso sin servidor de Functions, por lo que no tiene que preocuparse de escalar los recursos; puede centrarse simplemente en la lógica de la API.

## <a name="prerequisites"></a>Requisitos previos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

La función resultante se usará durante el resto de este tutorial.

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Abra Azure Portal. Para ello, inicie sesión en [https://portal.azure.com](https://portal.azure.com) con su cuenta de Azure.

## <a name="customize-your-http-function"></a>Personalización de la función HTTP

De forma predeterminada, la función desencadenada por HTTP está configurada para aceptar cualquier método HTTP. También hay una dirección URL predeterminada con el formato `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Si ha seguido la guía de inicio rápido, `<funcname>` probablemente es algo parecido a "HttpTriggerJS1". En esta sección, modificará la función para que solo responda a solicitudes GET en la ruta `/api/hello`, en su lugar. 

Vaya a su función en Azure Portal. Seleccione **Integrar** en el panel de navegación izquierdo.

![Personalización de una función HTTP](./media/functions-create-serverless-api/customizing-http.png)

Use la configuración de desencadenador HTTP como se especifica en la tabla.

| Campo | Valor de ejemplo | Descripción |
|---|---|---|
| Métodos HTTP permitidos | Métodos seleccionados | Determina qué métodos HTTP se pueden usar para invocar esta función. |
| Métodos HTTP seleccionados | GET | Permite que solo se usen los métodos HTTP seleccionados para invocar esta función. |
| Plantilla de ruta | /hello | Determina qué ruta se utiliza para invocar esta función. |

Observe que no se incluye el prefijo de ruta de acceso base `/api` en la plantilla de ruta, ya que esto se controla mediante una configuración global.

Haga clic en **Guardar**.

Puede aprender más sobre cómo personalizar funciones HTTP en [Enlaces HTTP y webhook en Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Prueba de la API

A continuación, pruebe la función para verla en acción con la nueva superficie de la API.

Para volver a la página de desarrollo, haga clic en el nombre de la función en el panel de navegación izquierdo.

Haga clic en **Obtener la dirección URL de la función** y copie la dirección URL. Debería ver que ahora usa la ruta `/api/hello`.

Copie la dirección URL en una nueva pestaña del explorador o el cliente de REST que prefiera. Los exploradores utilizarán GET de forma predeterminada.

Ejecute la función y confirme que funciona. Es posible que deba proporcionar el parámetro "name" como cadena de consulta para satisfacer el código de la guía de inicio rápido.

También puede intentar llamar al punto de conexión con otro método HTTP para confirmar que no se ejecuta la función. Para ello, debe usar a un cliente de REST, como cURL, Postman o Fiddler.

## <a name="proxies-overview"></a>Introducción a Servidores proxy

En la siguiente sección, expondrá la API a través de un servidor proxy. Servidores proxy de Azure Functions es una característica en versión preliminar que permite reenviar solicitudes a otros recursos. Se define un punto de conexión HTTP al igual que con el desencadenador HTTP pero, en lugar de escribir código que se ejecute cuando se llame a ese punto de conexión, se proporciona una dirección URL a una implementación remota. Esto le permite crear varios orígenes de API dentro de una sola superficie de API que es fácil de consumir para los clientes. Esto resulta especialmente útil si desea compilar su API como microservicio.

Un servidor proxy puede apuntar a cualquier recurso HTTP, como:
- Funciones de Azure 
- Aplicaciones de API en [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- Contenedores de Docker en [App Service en Linux](https://docs.microsoft.com/azure/app-service/app-service-linux-readme)
- Cualquier otra API hospedada

Para aprender más sobre los servidores proxy, consulte [Uso de Servidores proxy de Azure Functions (versión preliminar)].

## <a name="create-your-first-proxy"></a>Creación de su primer proxy

En esta sección, creará un proxy que actúa como front-end de la API en general. 

### <a name="setting-up-the-frontend-environment"></a>Configuración del entorno de front-end

Repita los pasos de [Creación de una aplicación de función](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) para crear una aplicación de función en la que creará el proxy. Esta nueva aplicación servirá como front-end para la API, mientras que la aplicación de función que editó antes actuará como back-end.

Vaya a la nueva aplicación de función de front-end en el portal.

Seleccione **Configuración**. Cambie **Habilitar servidores proxy de Azure Functions (versión preliminar)** a "Activado".

Seleccione **Configuración de plataforma** y elija **Configuración de la aplicación**.

Baje hasta **Configuración de la aplicación** y cree una configuración con la clave "HELLO_HOST". Establezca su valor en el host de la aplicación de función de back-end, como `<YourApp>.azurewebsites.net`. Esto forma parte de la dirección URL que copió antes al probar la función HTTP. Hará referencia a este valor en la configuración más adelante.

> [!NOTE] 
> Se recomienda la configuración de la aplicación para que la configuración del host evite una dependencia de entorno codificada de forma rígida para el servidor proxy. Al usarse la configuración de la aplicación, puede mover la configuración del proxy entre entornos, y se aplicará la configuración de la aplicación específica del entorno.

Haga clic en **Guardar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Creación de un proxy en el front-end

Vuelva a la aplicación de función de front-end en el portal.

En el panel de navegación izquierdo, haga clic en el signo más "+" junto a "Servidores proxy (versión preliminar)".

![Creación de un proxy](./media/functions-create-serverless-api/creating-proxy.png)

Use la configuración de proxy como se especifica en la tabla.

| Campo | Valor de ejemplo | Descripción |
|---|---|---|
| Nombre | HelloProxy | Un nombre descriptivo que se usa solo para la administración |
| Plantilla de ruta | /api/hello | Determina qué ruta se utiliza para invocar este proxy. |
| Dirección URL de back-end | https://%HELLO_HOST%/api/hello | Especifica el punto de conexión al que la solicitud debe remitir a través del proxy. |

Tenga en cuenta que en Servidores proxy no se proporciona el prefijo de ruta de acceso base `/api`, y esto debe incluirse en la plantilla de ruta.

La sintaxis `%HELLO_HOST%` hará referencia a la configuración de la aplicación que creó antes. La dirección URL resuelta apuntará a la función original.

Haga clic en **Crear**.

Puede probar el nuevo proxy; para ello, copie la dirección URL del proxy y pruébela en el explorador o con su cliente HTTP favorito.

## <a name="create-a-mock-api"></a>Creación de una API simulada

A continuación, utilizará un proxy para crear una API simulada para su solución. Esto permite que avance el desarrollo del cliente, sin necesidad de que el back-end esté totalmente implementado. Más adelante en el desarrollo, podría crear una aplicación de función que admita esta lógica y redirigir al proxy a ella.

Para crear esta API simulada, se va a crear un proxy, esta vez mediante el [Editor de App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Para empezar, vaya a la aplicación de función en el portal. Seleccione **Características de la plataforma** y busque **Editor de App Service**. Al hacer clic en él, se abrirá el Editor de App Service en una nueva pestaña.

Seleccione `proxies.json` en el panel de navegación izquierdo. Es el archivo que almacena la configuración para todos los servidores proxy. Si utiliza uno de los [métodos de implementación de Functions](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), es el archivo que mantendrá en el control de código fuente. Para aprender más sobre este archivo, consulte [Configuración avanzada de servidores proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Si ha seguido los pasos hasta ahora, su archivo proxies.json debería parecerse al siguiente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

A continuación, va a agregar su API simulada. Reemplace su archivo proxies.json por lo siguiente:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Esto agrega un nuevo proxy, "GetUserByName", sin la propiedad backendUri. En lugar de llamar a otro recurso, modifica la respuesta predeterminada desde Servidores proxy mediante una invalidación de la respuesta. Las invalidaciones de solicitud y respuesta también pueden utilizarse junto con una dirección URL de back-end. Esto resulta especialmente útil cuando se remite a través de un proxy a un sistema heredado, donde podría necesitar modificar los encabezados, los parámetros de consulta, etc. Para aprender más sobre las invalidaciones de solicitud y respuesta, consulte [Modificación de solicitudes y respuestas en Servidores proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Pruebe la API simulada mediante una llamada al punto de conexión `/api/users/{username}` con un explorador o el cliente de REST que prefiera. Asegúrese de reemplazar _{username}_ por un valor de cadena que represente un nombre de usuario.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a crear y personalizar una API en Azure Functions. También ha aprendido a reunir varias API, incluidas las simuladas, como una superficie de API unificada. Puede utilizar estas técnicas para crear API de cualquier complejidad, todo ello mientras ejecuta el modelo de proceso sin servidor proporcionado por Azure Functions.

Las siguientes referencias pueden serle de ayuda a medida que sigue desarrollando su API:

- [Enlaces HTTP y webhook en Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Uso de Servidores proxy de Azure Functions (versión preliminar)]
- [Documentación de una API de Azure Functions (versión preliminar)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Uso de Servidores proxy de Azure Functions (versión preliminar)]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
