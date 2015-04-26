<properties 
	pageTitle="Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure" 
	description="Obtenga información acerca de cómo realizar un seguimiento de las llamadas con API Inspector en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Uso del API Inspector para hacer un seguimiento de las llamadas en Administración de API de Azure

Administración de API (vista previa) ofrece una herramienta API Inspector para ayudarle con la depuración y la solución de problemas de las API. El API Inspector se puede usar mediante programación desde las aplicaciones y también directamente desde el portal para desarrolladores. En esta guía se explica el uso del API Inspector.

## En este tema

-   [Uso de API Inspector para realizar el seguimiento de una llamada][Uso de API Inspector para realizar el seguimiento de una llamada]
-   [Inspección del seguimiento][Inspección del seguimiento]
-   [Pasos siguientes][Pasos siguientes]

## <a name="trace-call"> </a> Uso de API Inspector para realizar el seguimiento de una llamada

Para usar API Inspector, agregue un encabezado de solicitud **ocp-apim-trace: true** a la llamada a la operación y luego descargue e inspeccione el seguimiento con la URL indicada mediante el encabezado de respuesta **ocp-apim-trace-location**. Esto puede realizarse mediante programación y también directamente desde el portal para desarrolladores.

En este tutorial se muestra cómo usar el API Inspector para realizar el seguimiento de operaciones con la Echo API.

> Cada instancia del servicio Administración de API viene previamente configurada con una API Eco que se puede usar para experimentar con Administración de API y aprender de esta. La Echo API devuelve cualquier entrada que se le envíe. Para usarla, se puede invocar cualquier verbo HTTP, y el valor devuelto será simplemente el que se envíe.

Para comenzar, haga clic en **Portal para desarrolladores** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management developer portal][API Management developer portal]

Se puede llamar a las operaciones directamente desde el portal para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API. En este paso del tutorial, se llamará al método **Recurso Get** de **API eco**.

Haga clic en **API** en el menú superior y seleccione **API eco**.

![Echo API][Echo API]

> Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.

Seleccione la operación **Recurso GET** y haga clic en **Abrir consola**.

![Open console][Open console]

Mantenga los valores predeterminados de los parámetros y seleccione la clave de suscripción en la lista desplegable **subscription-key**.

Escriba **ocp-apim-trace: true** en el cuadro de texto **Encabezados de solicitud** y haga clic en **HTTP Get**.

![HTTP Get][HTTP Get]

En los encabezados de respuesta habrá una **ocp-apim-trace-location** con un valor similar al que aparece en el ejemplo siguiente.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

El seguimiento puede descargarse de la ubicación especificada y revisarse, como se demuestra en el paso siguiente.

## <a name="inspect-trace"> </a>Inspección del seguimiento

Para revisar los valores del seguimiento, descargue el archivo de seguimiento de la URL de **ocp-apim-trace-location**. Se trata de un archivo de texto en formato JSON y contiene entradas similares a las que aparecen en el ejemplo siguiente.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Pasos siguientes

-   Consulte el resto de temas en el tutorial [Introducción a la configuración de API avanzada][Introducción a la configuración de API avanzada].

  [Uso de API Inspector para realizar el seguimiento de una llamada]: #trace-call
  [Inspección del seguimiento]: #inspect-trace
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management developer portal]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Open console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Introducción a la configuración de API avanzada]: ../api-management-get-started-advanced

<!--HONumber=46--> 

<!--HONumber=46--> 
