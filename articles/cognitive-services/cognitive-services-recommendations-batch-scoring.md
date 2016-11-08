
---
title: 'Obtención de recomendaciones por lotes: Recommendations API de Aprendizaje automático | Microsoft Docs'
description: Recomendaciones de Aprendizaje automático de Azure - Obtención de recomendaciones por lotes
services: cognitive-services
documentationcenter: ''
author: luiscabrer
manager: jhubbard
editor: cgronlun

ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: luisca

---
# Obtención de recomendaciones por lotes
> [!NOTE]
> Obtener recomendaciones por lotes resulta más complicado que obtenerlas una a una. Consulte las API para obtener información acerca de cómo obtener recomendaciones para una única solicitud:
> 
> [Item-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4) (Recomendaciones de Item-to-Item)<br> [User-to-Item recommendations](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd) (Recomendaciones de User-to-Item)
> 
> La puntuación por lotes solo funciona en las compilaciones creadas a partir del 21 de julio de 2016.
> 
> 

Hay situaciones en las que es necesario obtener recomendaciones para varios elementos a la vez. Es posible, por ejemplo, que quiera crear una memoria caché de recomendaciones o incluso analizar los tipos de recomendaciones que recibe.

Las operaciones de puntuación por lotes, tal y como las llamamos, son operaciones asincrónicas. Tiene que enviar la solicitud, esperar a que la operación se termine y recopilar después los resultados.

En concreto, estos son los pasos que debe seguir:

1. Cree un contenedor de Almacenamiento de Azure, si aún no se ha creado.
2. Cargue en Almacenamiento de blobs de Azure un archivo de entrada que describe cada solicitud de recomendación.
3. Inicio del trabajo de puntuación por lotes.
4. Espere hasta que se termine la operación asincrónica.
5. Cuando haya terminado la operación, recopile los resultados del Almacenamiento de blobs.

Vamos a ver en detalle cada uno de estos pasos.

## Creación de un contenedor de Almacenamiento, si aún no se ha creado
Vaya al [Portal de Azure](https://portal.azure.com) y cree una nueva cuenta de almacenamiento si aún no tiene una. Para ello, vaya a **Nuevo** > **Datos** y **almacenamiento** > **Cuenta de almacenamiento**.

Una vez que tenga la cuenta de almacenamiento, deberá crear los contenedores de blobs en los que almacenará la entrada y salida de la ejecución por lotes.

En un Almacenamiento de blobs, cargue un archivo de entrada en el que se describan todas las solicitudes de recomendaciones (en esta demostración, el archivo se llamará input.json). Una vez que tenga el contenedor, deberá cargar un archivo en el que se describan todas las solicitudes que necesita ejecutar en el Servicio de recomendaciones.

Un lote solamente puede realizar un tipo de solicitud en una compilación específica. En la siguiente sección, explicaremos cómo se define esta información. Por ahora, asumiremos que las recomendaciones de los elementos se van a realizar sin usar una compilación específica. El archivo de entrada contiene la información de entrada (en este caso, los elementos de inicialización) de cada una de las solicitudes.

En este ejemplo se muestra el aspecto que tendría el archivo input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Como puede ver, se trata de un archivo JSON en el que cada una de las solicitudes tiene la información necesaria para enviar una solicitud de recomendación. Cree un archivo JSON similar para las solicitudes que necesite realizar y cópielo en el contenedor que acaba de crear en Almacenamiento de blobs.

## Inicio del trabajo de puntuación por lotes
El siguiente paso consiste en enviar un nuevo trabajo por lotes. Para más información, consulte la [Referencia de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

En el cuerpo de la solicitud de la API, deben estar definidas las ubicaciones en las se van a guardar los archivos de entrada, salida y error. También debe definir las credenciales necesarias para acceder a dichas ubicaciones. Además, debe especificar algunos parámetros que se aplican a todo el lote, como el tipo de recomendación que se va a solicitar, el modelo o compilación que se va a utilizar, el número de resultados por llamada, etc.

En este ejemplo se muestra cómo quedaría el cuerpo de la solicitud:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Hay varios aspectos importantes que se deben tener en cuenta:

* En la actualidad, **AuthenticationType** siempre debe estar establecido en **PublicOrSas**.
* Necesita un token de Firma de acceso compartido (SAS) para que Recommendations API pueda leer la cuenta de Almacenamiento de blobs y escribir en ella. Para más información acerca de cómo generar tokens de SAS consulte [la página de Recommendations API](../storage/storage-dotnet-shared-access-signature-part-1.md).
* El único valor de **apiName** que se admite en la actualidad es **ItemRecommend**, que es el que se utiliza en las recomendaciones de Item-to-Item. El procesamiento por lotes no admite actualmente las recomendaciones User-to-Item.

## Espere hasta que se termine la operación asincrónica.
Cuando inicie la operación por lotes, esta operación devolverá el encabezado de Operation-Location que le proporcionará la información que necesita para hacer el seguimiento de la operación. Utilice la [Retrieve Operation Status API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), tal y como haría para hacer el seguimiento de una operación de compilación.

## Obtención de los resultados
Una vez que se ha terminado la operación (y suponiendo que no se han producido errores), puede recopilar los resultados del Almacenamiento de blobs de salida.

El ejemplo siguiente muestra el aspecto que podría tener la salida. Para abreviar, en este ejemplo se han incluido los resultados de un lote con solo dos solicitudes.

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## Obtenga información acerca de las limitaciones
* Solo se puede llamar a un único trabajo por lotes por cada suscripción.
* El tamaño de los archivos de entrada del trabajo por lotes no puede ser superior a 2 MB.

<!---HONumber=AcomDC_0914_2016-->