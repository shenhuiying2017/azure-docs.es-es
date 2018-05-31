---
title: Recomendaciones para la solución de problemas con Cognitive Search en Azure Search | Microsoft Docs
description: Recomendaciones y solución de problemas para configurar canalizaciones de Cognitive Search en Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 15fc879958bfd886210a90239e0247c60fe231f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786834"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Recomendaciones para solucionar problemas con Cognitive Search

En este artículo encontrará una lista de recomendaciones y trucos para avanzar sin pausa a medida que trabaja con las funcionalidades de Cognitive Search en Azure Search. 

Si aún no lo ha hecho, siga las instrucciones de [Tutorial: Learn how to call the cognitive search APIs](cognitive-search-quickstart-blob.md) (Tutorial: aprender a llamar a las API de Cognitive Search) para practicar la aplicación de las opciones de enriquecimiento de Cognitive Search a un origen de datos de blobs.

## <a name="tip-1-start-with-a-small-dataset"></a>Recomendación 1: comience con un pequeño conjunto de datos
La mejor forma de detectar problemas rápidamente es aumentar la velocidad de solución de esos problemas. La mejor forma de reducir el tiempo de indexación es reducir el número de documentos que se indexarán. 

Comience creando un origen de datos con un pequeño conjunto de documentos y registros. La muestra de documentos debe ser una representación adecuada de la variedad de documentos que se indexarán. 

Ejecute la muestra de documentos mediante la canalización de un extremo a otro, y compruebe que los resultados satisfagan sus necesidades. Una vez que esté satisfecho con los resultados, puede agregar más archivos a la fuente de datos.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Recomendación 2: asegúrese de que las credenciales del origen de datos sean correctas
La conexión del origen de datos no se validará hasta que se defina un indexador que la use. Si ve algún error que indique que el indexador no puede acceder a los datos, asegúrese de que:
- La cadena de conexión es correcta. Igualmente, cuando cree tokens de SAS, asegúrese de usar el formato que espera Azure Search. Consulte [la sección de especificación de credenciales ](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para conocer los diferentes formatos compatibles.
- El nombre del contenedor en el indexador es correcto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Recomendación 3: confirme los elementos que funcionan incluso si hay algunos errores
A veces, un pequeño error detiene el proceso del indexador. Esto no supone ningún problema si planea solucionar los errores uno por uno. Sin embargo, es posible que quiera ignorar un tipo particular de error, permitiendo así que el indexador continúe con el proceso para poder ver qué flujos están funcionando realmente.

En ese caso, le puede indicar al indexador que ignore los errores. Para ello, establezca los valores de *maxFailedItems* y *maxFailedItemsPerBatch* en -1, como parte de la definición del indexador.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Recomendación 4: examine los documentos enriquecidos en segundo plano 
Los documentos enriquecidos son estructuras temporales que se crean durante el enriquecimiento, y que se eliminan cuando se completa el proceso.

Para capturar una instantánea del documento enriquecido creado durante la indexación, agregue un campo denominado ```enriched``` al índice. El indexador vuelca automáticamente en el campo una representación de cadena del enriquecimiento del documento.

El campo ```enriched``` contendrá una cadena que es una representación lógica del documento enriquecido en memoria en formato JSON.  Sin embargo, el valor del campo es un documento JSON válido. Las comillas son caracteres de escape, por lo que deberá reemplazar `\"` por `"` para ver el documento con el formato JSON. 

El campo enriquecido está destinado para realizar únicamente operaciones de depuración, que le ayudarán a entender la forma lógica del contenido en el que se evalúan las expresiones. No debe depender de este campo para realizar la indexación.

Agregue un campo ```enriched``` como parte de la definición del índice para fines de depuración:

#### <a name="request-body-syntax"></a>Sintaxis del cuerpo de la solicitud
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Recomendación 5: el contenido esperado no aparece

Si falta contenido, esto puede deberse a que se descartaron documentos durante la indexación. Los niveles Gratuito y Básico tienen límites bajos en lo que respecta al tamaño de los documentos. Cualquier archivo que exceda el límite se descarta durante la indexación. Puede comprobar si hay documentos descartados en Azure Portal. En el panel del servicio de búsqueda, haga doble clic en el icono Indexadores. Revise la proporción de documentos que se indexaron con éxito. Si no es del 100 %, puede hacer clic en la proporción para obtener más detalles. 

Si el problema está relacionado con el tamaño del archivo, es posible que vea un error como este: "El blob <file-name> tiene un tamaño de <file-size> bytes, lo cual excede el tamaño máximo para la extracción de documentos de su nivel de servicio actual". Para obtener más información, consulte los [límites del servicio](search-limits-quotas-capacity.md).

Una segunda razón por la que el contenido no aparece, puede deberse errores de asignación de entradas y salidas relacionadas. Por ejemplo, el nombre de destino de salida es "Personas", pero el nombre del campo de índice es "personas" en minúsculas. El sistema puede devolver 201 mensajes de éxito de toda la canalización y hacerle creer que la indexación tuvo éxito, cuando en realidad uno de los campos está vacío. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Recomendación 6: ampliar el procesamiento más allá del tiempo máximo de ejecución (periodo de 24 horas)

El análisis de imágenes es un proceso intensivo a nivel computacional, incluso cuando se trata de casos simples; debido a ello, cuando las imágenes son especialmente grandes o complejas, los tiempos de procesamiento pueden exceder el tiempo máximo permitido. 

El tiempo máximo de ejecución varía según el nivel: varios minutos en el nivel gratuito, y una indexación de 24 horas en niveles de pago. Si el procesamiento no se completa dentro de un período de 24 horas según el procesamiento bajo demanda, use una programación en la que el indexador pueda retomar el procesamiento desde donde lo dejó. 

En cuanto a los indexadores programados, la indexación se reanuda según la programación del último documento válido conocido. Al usar una programación recurrente, el indexador puede abrirse camino a través de las imágenes pendientes durante una serie de horas o días, hasta que se procesen todas aquellas imágenes que no estén procesadas. Para obtener más información acerca de la sintaxis de programación, consulte el [paso 3: crear un indexador](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Si realiza una indexación basada en el portal (tal como se describe en la guía de inicio rápido), la elección de la opción del indexador "ejecutar una vez" limita el procesamiento a 1 hora (`"maxRunTime": "PT1H"`). Es posible que quiera extender el período de procesamiento para que sea algo más largo.

## <a name="tip-7-increase-indexing-throughput"></a>Recomendación 7: aumentar el rendimiento de la indexación

Para realizar una [indexación paralela](search-howto-reindex.md#parallel-indexing), coloque los datos en varios contenedores o carpetas virtuales múltiples dentro del mismo contenedor. A continuación, cree varios pares de orígenes de datos e indexadores. Todos los indexadores pueden usar el mismo conjunto de aptitudes y escribir en el mismo índice de búsqueda de destino, por lo que la aplicación de búsqueda no necesita conocer esta partición.
Para obtener más información, consulte [Indexing Large Datasets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets) (Indexar grandes conjuntos de datos).

## <a name="see-also"></a>Otras referencias
+ [Guía de inicio rápido: crear una canalización de Cognitive Search en el portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: obtener información sobre las API REST de Cognitive Search](cognitive-search-tutorial-blob.md)
+ [Especificar las credenciales de origen de datos.](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indexar grandes conjuntos de datos](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Cómo definir un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Asignar campos enriquecidos a un índice](cognitive-search-output-field-mapping.md)