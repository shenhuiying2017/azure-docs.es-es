---
title: "Cómo usar Blitline para el procesamiento de imágenes - Guía de funciones de Azure"
description: "Obtenga información acerca de cómo usar el servicio de Blitline para procesar imágenes dentro de una aplicación de Azure."
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
ms.openlocfilehash: 1d90599e028b3407a513b04b878e3aefc39928a2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Cómo usar Blitline con Azure y Almacenamiento de Azure
Esta guía explica cómo obtener acceso a los servicios Blitline y cómo enviar trabajos a Blitline.

## <a name="what-is-blitline"></a>¿Qué es Blitline?
Blitline es un servicio de procesamiento de imágenes basado en la nube que ofrece un procesamiento de imágenes de nivel empresarial por una fracción del precio que le costaría realizar la generación por su cuenta.

En realidad, el procesamiento de imágenes se realiza una y otra vez. Normalmente se vuelve a generar desde el principio en cada sitio web. Sabemos esto porque las hemos generado un millón de veces. Un día decidimos que quizás era el momento de realizar el proceso para todos. Sabemos cómo hacerlo, de forma rápida y eficaz, y guardar el trabajo de todos a la vez.

Para obtener más información, consulte [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>¿Qué NO es Blitline?
Para aclarar para qué sirve Blitline, normalmente es más sencillo identificar qué NO hace Blitline antes de continuar.

* Blitline NO dispone de widgets HTML para cargar imágenes. Debe tener imágenes disponibles públicamente o con permisos restringidos disponibles para que los consiga Blitline.
* Blitline NO activa el procesamiento de imágenes, como Aviary.com.
* Blitline NO acepta cargas de imágenes y no puede insertar imágenes en Blitline directamente. Debe insertarlas en Almacenamiento de Azure o en otros lugares compatibles con Blitline y luego indicar a Blitline el lugar donde conseguirlas.
* Blitline funciona de una forma totalmente paralela y NO realiza ningún procesamiento sincrónico. Esto significa que debe proporcionarnos una dirección URL postback_url para que podamos indicarle cuándo acabamos el procedimiento.

## <a name="create-a-blitline-account"></a>Creación de una cuenta Blitline
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Creación de un trabajo Blitline
Blitline usa JSON para definir las acciones que desea realizar en una imagen. JSON está compuesto por algunos campos simples.

El ejemplo más simple es el siguiente:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aquí tenemos el elemento JSON que tomará una imagen "src" "...boys.jpeg" y, a continuación, cambiará el tamaño de la imagen a 240x140.

Puede encontrar el identificador de la aplicación en la pestaña **INFORMACIÓN DE CONEXIÓN** o **ADMINISTRAR** de Azure. Se trata del identificador secreto que le permite ejecutar trabajos en Blitline.

El parámetro "save" identifica información sobre dónde desea colocar la imagen una vez que se haya procesado. En este caso específico, no definimos ninguno. Si no se define ninguna ubicación, Blitline la almacenará localmente (o temporalmente) en una ubicación exclusiva de la nube. Podrá obtener esa ubicación desde el JSON devuelto por Blitline cuando realice el trabajo Blitline. El identificador "image" es obligatorio y se devuelve cuando se identifica esta imagen guardada en concreto.

Puede obtener más información sobre las *funciones* que ofrecemos aquí: <http://www.blitline.com/docs/functions>.

También puede encontrar información sobre las opciones de trabajo aquí: <http://www.blitline.com/docs/api>.

Una vez que disponga de JSON, lo único que tiene que hacer es **PUBLICARLO** en `http://api.blitline.com/job`.

Obtendrá un resultado JSON con una apariencia similar a la siguiente:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Esto indica que Blitline ha recibido la solicitud, la ha puesto en la cola de procesamiento y, cuando se haya completado, la imagen estará disponible en: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Almacenamiento de una imagen en la cuenta de Almacenamiento de Azure
Si dispone de una cuenta de Almacenamiento de Azure, puede hacer que Blitline inserte las imágenes procesadas en el contenedor de Azure. Si agrega "azure_destination", defina la ubicación y los permisos de inserción de Blitline.

Aquí tiene un ejemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Si completa los valores en MAYÚSCULAS por su cuenta, puede enviar JSON a http://api.blitline.com/job y la imagen "src" se procesará con un filtro de desenfoque y, a continuación, se insertará en un destino de Azure.

### <a name="please-note"></a>Tenga en cuenta lo siguiente:
SAS debe contener la dirección URL completa de SAS, incluido el nombre de archivo del archivo de destino.

Ejemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


También puede consultar la última edición de los documentos de Almacenamiento de Azure de Blitline [aquí](http://www.blitline.com/docs/azure_storage).

## <a name="next-steps"></a>Pasos siguientes
Visite blitline.com para conocer toda la información sobre otras características:

* Documentos del punto de conexión de la API de Blitline <http://www.blitline.com/docs/api>
* Funciones de la API de Blitline <http://www.blitline.com/docs/functions>
* Ejemplos de la API de Blitline <http://www.blitline.com/docs/examples>
* Biblioteca Nuget de terceros <http://nuget.org/packages/Blitline.Net>

