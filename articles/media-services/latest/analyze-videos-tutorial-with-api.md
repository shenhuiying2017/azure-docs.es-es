---
title: Análisis de vídeos con Azure Media Services | Microsoft Docs
description: Siga los pasos de este tutorial para analizar los vídeos con Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 54c49645722b6545d8ae872151b9b82674d44523
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Tutorial: Análisis de vídeos con Azure Media Services 

En este tutorial se muestra cómo analizar vídeos con Azure Media Services. Hay muchos escenarios en los que puede desear obtener información detallada sobre los vídeos grabados o el contenido de audio. Por ejemplo, para lograr una mayor satisfacción del cliente, las organizaciones pueden ejecutar el procesamiento de voz a texto para convertir las grabaciones de soporte técnico al cliente en un catálogo de búsqueda, con índices y paneles. A continuación, puede obtener información sobre su empresa, como una lista de las quejas más habituales, orígenes de dichas quejas, etc.

En este tutorial se muestra cómo realizar las siguientes acciones:    

> [!div class="checklist"]
> * Inicio de Azure Cloud Shell
> * Creación de una cuenta de Media Services
> * Acceso a la API de Media Services
> * Configuración de la aplicación de ejemplo
> * Examen el código de ejemplo con detalle
> * Ejecución de la aplicación
> * Examen de la salida
> * Limpieza de recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Si no tiene Visual Studio instalado, puede obtener [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de .NET en la máquina mediante el siguiente comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Examen el código de ejemplo con detalle

En esta sección se examinan las funciones definidas en el archivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) del proyecto *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Uso de las API de Media Services con SDK de .NET

Para empezar a usar las API de Media Services con. NET, debe crear un objeto **AzureMediaServicesClient**. Para crear el objeto, debe proporcionar las credenciales necesarias para que el cliente se conecte a Azure mediante Azure AD. Primero debe obtener un token y, después, crear un objeto **ClientCredential** desde el token devuelto. En el código que se clonó al comienzo del artículo, se ha usado el objeto **ArmClientCredential** para obtener el token.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Creación de un recurso de salida para almacenar el resultado de un trabajo 

El recurso de salida almacena el resultado del trabajo. El proyecto define la función **DownloadResults** que descarga los resultados de este recurso de salida en la carpeta "output", para que se pueda ver lo que tiene.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Creación de una transformación y de un trabajo que analiza vídeos

Cuando se codifica o procesa contenido en Media Services, es un patrón común configurar los ajustes de codificación como una receta. Después, podría enviar un **trabajo** para aplicar esa receta a un vídeo. Al enviar nuevos trabajos a cada nuevo vídeo, está aplicando esa receta a todos los vídeos de la biblioteca. Una receta en Media Services se llama **transformación**. Para más información, consulte [Transformaciones y trabajos](transform-concept.md). En el ejemplo descrito en este tutorial se define una receta que analiza el vídeo especificado. 

#### <a name="transform"></a>Transformación

Al crear una nueva instancia de la **transformación**, debe especificar qué desea originar como salida. El parámetro requerido es un objeto **TransformOutput**, como se muestra en el código anterior. Cada objeto **TransformOutput** contiene un **valor preestablecido**. El **valor preestablecido** describe las instrucciones paso a paso de las operaciones de procesamiento de vídeo o audio que se utilizarán para generar el objeto **TransformOutput** deseado. En este ejemplo, se utiliza el valor preestablecido de **VideoAnalyzerPreset**  y el idioma ("en-US") se pasa al constructor. Este valor preestablecido permite extraer diversa información de audio y vídeo desde un vídeo. Puede usar el valor preestablecido de **AudioAnalyzerPreset** si necesita extraer diversa información de audio desde un vídeo. 

Al crear una **transformación**, debe comprobar primero si ya existe una con el método **Get**, tal como se muestra en el código siguiente.  En Media Services v3, los métodos **Get** en las entidades devuelven **null** si la entidad no existe (una comprobación sin distinción entre mayúsculas y minúsculas en el nombre).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabajo

Como se mencionó anteriormente, el objeto **Transform** es la receta y un **trabajo** es la solicitud real a Media Services para aplicar que dicho objeto **Transform** a un determinado contenido de audio o vídeo de entrada. El **trabajo** especifica información como la ubicación del vídeo de entrada y la ubicación de la salida. Puede especificar la ubicación del vídeo mediante direcciones URL HTTPS, direcciones URL de SAS o recursos que están en la cuenta de Media Service. 

En este ejemplo, la entrada de trabajo es un vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Espere a que el trabajo se complete.

El trabajo tarda algún tiempo en completarse y cuando lo hace querrá recibir una notificación. Hay distintas opciones para obtener notificaciones sobre la realización del trabajo. La opción más sencilla (que se muestra aquí) es para utilizar el sondeo. 

El sondeo no es un procedimiento recomendado para aplicaciones de producción debido a la posible latencia. El sondeo se puede limitar si se sobreutiliza en una cuenta. Los desarrolladores deben utilizar en su lugar Event Grid.

Event Grid está diseñado para una alta disponibilidad, un rendimiento consistente y una escala dinámica. Con Event Grid, sus aplicaciones pueden escuchar y reaccionar a eventos de casi todos los servicios de Azure y de orígenes personalizados. El control de eventos sencillo y reactivo basado en HTTP le ayuda a crear soluciones eficaces con filtrado y enrutamiento de eventos inteligente. Consulte [Enrutamiento de eventos a un punto de conexión web personalizado](job-state-events-cli-how-to.md).

El **trabajo** normalmente pasa por los siguientes estados: **Programado**, **En cola**, **Procesando**, **Finalizado** (el estado final). Si el trabajo ha encontrado un error, obtendrá el estado **Error**. Si el trabajo está en proceso de cancelación, obtendrá **Cancelando** y **Cancelado** cuando haya terminado.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Descarga del resultado del trabajo

La siguiente función descarga los resultados del recurso de salida en la carpeta "output", para que pueda examinar los resultados del trabajo. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpieza de recursos en la cuenta de Media Services

Por lo general, debe limpiar todo excepto los objetos que piensa reutilizar (típicamente, reutilizará transformaciones y conservará objetos StreamingLocators, etc.). Si desea que la cuenta esté limpia después de la experimentación, debe eliminar los recursos que no piensa volver a usar. Por ejemplo, el código siguiente elimina los trabajos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Presione Ctrl+F5 para ejecutar la aplicación *AnalyzeVideos*.

Cuando se ejecuta el programa, el trabajo produce miniaturas para cada rostro que encuentra en el vídeo. También produce el archivo insights.json.

## <a name="examine-the-output"></a>Examen de la salida

El archivo de salida de análisis de vídeos se denomina insights.json. Este archivo contiene información detallada sobre el vídeo. Puede encontrar la descripción de los elementos que se encuentran en el archivo json en el artículo [Media intelligence](intelligence-concept.md) (Inteligencia multimedia).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida las cuentas de almacenamiento y de Media Services que ha creado en este tutorial, elimine el grupo de recursos que ha creado antes. Puede usar la herramienta **CloudShell**.

En **CloudShell**, ejecute el siguiente comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Los SDK de Azure Media Services v3 no son seguros para subprocesos. Al trabajar con una aplicación que admite multithreading, debe generar un nuevo objeto AzureMediaServicesClient por subproceso.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md)
