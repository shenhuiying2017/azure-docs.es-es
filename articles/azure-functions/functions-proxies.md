---
title: Trabajo con servidores proxy en Azure Functions | Microsoft Docs
description: "Información general sobre cómo usar Azure Functions Proxies"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Trabajo con Azure Functions Proxies (versión preliminar)

> [!Note] 
> Azure Functions Proxies actualmente se encuentra disponible en versión preliminar. La versión preliminar es gratuita, pero se aplica la facturación estándar de Functions a las ejecuciones de proxy. Para más información, vea los [precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

En este artículo se explica cómo configurar y usar Azure Functions Proxies. Esta característica permite especificar puntos de conexión en la Function App que otro recurso implementa. Puede utilizar a estos servidores proxy para dividir una API grande en varias Function App (como en una arquitectura de microservicio), mientras que presenta una superficie de API única para los clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Habilitar Azure Functions Proxies

Los servidores proxy no están habilitados de forma predeterminada. Puede crear servidores proxy mientras la característica está deshabilitada, o no se ejecutarán. En los pasos siguientes se explica cómo habilitar los servidores proxy:

1. Abra [Azure Portal] y vaya a la Function App.
2. Seleccione **Configuración de Function App**.
3. Cambie **Enable Azure Functions Proxies (preview)** [Habilitar Azure Functions Proxies (versión preliminar)] a activado.

También puede volver aquí para actualizar el tiempo de ejecución del proxy a medida que haya nuevas características disponibles.


## <a name="creating-a-proxy"></a>Creación de un proxy

En esta sección se explica cómo crear un proxy en el portal de Functions.

1. Abra [Azure Portal] y vaya a la Function App.
2. En el panel de navegación izquierdo, seleccione **Nuevo proxy**.
3. Proporcione un nombre para el proxy.
4. Configure el punto de conexión expuesto en esta Function App; para ello, especifique la **plantilla de ruta** y los **métodos HTTP**. Estos parámetros se comportan según las reglas de los [desencadenadores HTTP]
5. Establezca la **dirección URL de back-end** en otro punto de conexión. Podría tratarse de una función en otra Function App, o podría ser cualquier otra API.
6. Haga clic en Crear.

El proxy ahora existe como un nuevo punto de conexión en la Function App. Desde una perspectiva de cliente, es equivalente a un HttpTrigger en Azure Functions. Puede probar el nuevo proxy; para ello, copie la dirección URL del proxy y pruébela con el cliente HTTP favorito.


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>Modificación de solicitudes de back-end

El parámetro de dirección URL de back-end no necesita ser estático. Puede condicionarlo a la entrada de la solicitud o a la configuración de la aplicación.


### <a name="using-request-parameters"></a>Uso de parámetros de solicitud

Los parámetros utilizados en la plantilla de ruta se pueden usar como entradas para la propiedad de dirección URL de back-end. Se hace referencia a los valores por su nombre, entre llaves de cierre "{}".

Por ejemplo, si un proxy tiene una plantilla de ruta como `/pets/{petId}`, la dirección URL de back-end puede incluir el valor de `{petId}`, como en `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Si la plantilla de ruta finaliza en un carácter comodín, como `/api/{*restOfPath}`, el valor `{restOfPath}` será una representación de cadena de los segmentos de ruta de acceso restantes de la solicitud entrante.


### <a name="using-application-settings"></a>Uso de la configuración de la aplicación

También puede hacer referencia a la [configuración de la aplicación](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) rodeando el nombre de la configuración con signos de porcentaje "%".

Por ejemplo, en una dirección URL de back-end de `https://%ORDER_PROCESSING_HOST%/api/orders`, "%ORDER_PROCESSING_HOST%" se reemplaza por el valor de la configuración de ORDER_PROCESSING_HOST.

> [!TIP] 
> Use la configuración de la aplicación para hosts back-end si tiene varias implementaciones o entornos de prueba. De este modo, puede asegurarse de que siempre habla con el back-end correcto para ese entorno.



## <a name="deployment-methods"></a>Métodos de implementación

Los servidores proxy que configure se almacenan en un archivo proxies.json, ubicado en la raíz de un directorio de la Function App. Puede editar este archivo manualmente e implementarlo como parte de la aplicación cuando se utiliza cualquiera de los [métodos de implementación](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) compatibles con Functions.

La característica debe estar habilitada para poder procesar el archivo. Puede hacerlo siguiendo las instrucciones indicadas en [Habilitar Azure Functions Proxies](#enable).

El archivo proxies.json lo define un objeto de servidores proxy, compuesto de servidores proxy con nombre y de sus definiciones. Un archivo de ejemplo puede tener el siguiente aspecto:

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tiene un nombre descriptivo, como "proxy1" en el ejemplo anterior. El objeto de definición de proxy correspondiente se define mediante las siguientes propiedades:

* **matchCondition**: (requerida) un objeto que define las solicitudes que desencadenarán la ejecución de este proxy. Contiene dos propiedades compartidas con [desencadenadores HTTP]:
    * _methods_: se trata de una matriz de los métodos HTTP a los que responderá el proxy. Si no se especifica, el proxy responderá a todos los métodos HTTP de la ruta.
    * _route_: (requerida) define la plantilla de ruta y controla a qué direcciones URL de solicitud responderá el proxy. A diferencia de los desencadenadores HTTP, no hay ningún valor predeterminado.
* **backendUri**: la dirección URL del recurso de back-end a la que se redirigirá la solicitud mediante proxy. Este valor puede estar basado en una plantilla, como se describe en [Modificación de solicitudes de back-end](#modify-requests). Si esta propiedad no está incluida, Azure Functions responderá con el mensaje HTTP 200 OK (HTTP 200 correcto).

> [!Note] 
> La propiedad de ruta Azure Functions Proxies no respeta la propiedad routePrefix de la configuración del host de Functions. Si desea incluir un prefijo como /api, se debe incluir en la propiedad de ruta.



[Azure Portal]: https://portal.azure.com
[desencadenadores HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
