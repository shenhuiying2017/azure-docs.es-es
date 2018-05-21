---
title: 'Inicio rápido: crear una canalización de Cognitive Search en Azure Search mediante el portal | Microsoft Docs'
description: Ejemplo de aptitudes de extracción de datos, lenguaje natural y procesamiento de imágenes en Azure Portal utilizando datos de ejemplo.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.topic: quickstart
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 278fbd5d7f5925b802303910222c5a13379bfad6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>Inicio rápido: crear una canalización de Cognitive Search mediante aptitudes y datos de ejemplo

Cognitive Search (versión preliminar) agrega las aptitudes de extracción de datos, procesamiento de lenguaje natural (NLP) y procesamiento de imágenes a una canalización de indexación de Azure Search, lo que hace que el contenido no apto para la búsqueda o no estructurado sea más fácil de buscar. La información que crea una aptitud, como el reconocimiento de entidades o el análisis de imágenes, se agrega a un índice en Azure Search.

En esta guía de inicio rápido, pruebe la canalización de enriquecimiento en [Azure Portal](https://portal.azure.com) antes de escribir una sola línea de código:

* Comience con una muestra de datos en Azure Blob Storage.
* Configure el [asistente para importar datos](search-import-data-portal.md) para indexarlos y enriquecerlos. 
* Ejecute el asistente (una aptitud de la entidad detecta personas, ubicaciones y organizaciones).
* Use el [Explorador de búsqueda](search-explorer.md) para consultar los datos enriquecidos.

Puede probar Cognitive Search en el servicio Azure Search creado en las siguientes regiones:

* Centro-Sur de EE. UU
* Europa occidental

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

["What is cognitive search?"](cognitive-search-concept-intro.md) (¿Qué es Cognitive Search?) presenta la arquitectura de enriquecimiento y los componentes. 

Los servicios de Azure se utilizan exclusivamente en este escenario. Crear los servicios que necesita forma parte de la preparación.

+ Azure Blob Storage proporciona los datos de origen.
+ Azure Search controla la ingesta e indexación de datos, el enriquecimiento de Cognitive Search y las consultas de búsqueda de texto completo.

### <a name="set-up-azure-search"></a>Configurar Azure Search

En primer lugar, regístrese en el servicio Azure Search. 

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

1. Haga clic en **Crear un recurso**, busque Azure Search y haga clic en **Crear**. Consulte [Creación de un servicio Azure Search en el portal](search-create-service-portal.md) si está configurando un servicio de búsqueda por primera vez y necesita más ayuda.

  ![Panel del portal](./media/cognitive-search-tutorial-blob/create-service-full-portal.png "Creación de un servicio Azure Search en el portal")

1. En el grupo de recursos, cree un grupo de recursos en el que se incluirán todos los recursos que cree en esta guía rápida. De este modo, será más fácil borrar los recursos tras finalizar los pasos de la guía.

1. En cuanto a la ubicación, elija **Centro y Sur de EE. UU.** o **Europa Occidental**. Actualmente, la versión preliminar solo está disponible en estas regiones.

1. En la opción Plan de tarifa, puede crear un servicio **Gratis** para completar tutoriales y guías de inicio rápido. Si quiere realizar una investigación detallada con sus propios datos, cree un [servicio de pago](https://azure.microsoft.com/pricing/details/search/) como **Básico** o **Estándar**. 

  El servicio gratuito se limita a 3 índices, un tamaño máximo de blob de 16 MB y 2 minutos de indexación, lo que es insuficiente para usar todas las funcionalidades de Cognitive Search. Para revisar los límites de los distintos planes, consulte [Límites de servicio](search-limits-quotas-capacity.md).

  > [!NOTE]
  > Cognitive Search está disponible en la versión preliminar pública. Actualmente, la ejecución del conjunto de aptitudes está disponible en todos los planes, incluido el gratuito. Más adelante, se anunciarán los precios de esta funcionalidad.

1. Ancle el servicio al panel para acceder rápidamente a la información del servicio.

  ![Página de definición del servicio en el portal](./media/cognitive-search-tutorial-blob/create-search-service.png "Página de definición del servicio en el portal")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurar el servicio de Azure Blob y cargar los datos de ejemplo

La canalización de enriquecimiento extrae contenido de los orígenes de datos de Azure que admiten los [indexadores de Azure Search](search-indexer-overview.md). Para realizar este ejercicio, usaremos Blob Storage para mostrar varios tipos de contenido.

1. [Descargue los datos de ejemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) que están formados por un pequeño conjunto de archivos de diferentes tipos. 

1. Regístrese en Azure Blob Storage, cree una cuenta de almacenamiento, inicie sesión en el Explorador de Storage y cree un contenedor. Consulte [Azure Storage Explorer Quickstart](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) (Inicio rápido: Explorador de Azure Storage) para obtener instrucciones acerca de todos los pasos.

1. Use el Explorador de Azure Storage en el contenedor que creó y haga clic en **Cargar** para cargar los archivos de ejemplo.

  ![Archivos de origen en Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Crear la canalización de enriquecimiento

Vuelva a la página del panel de servicio de Azure Search y haga clic en **Importar datos** en la barra de comandos para configurar el enriquecimiento en cuatro pasos.

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

En **Conectarse a los datos** > **Azure Blob Storage**, seleccione la cuenta y el contenedor que creó. Asigne un nombre al origen de datos y use los valores predeterminados para el resto. 

   ![Configuración de blobs de Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)


Haga clic en **Aceptar** para crear el origen de datos.

Una ventaja de usar el asistente de **importación de datos** es que también puede crear el índice. A medida que se crean los datos, el asistente crea de forma simultánea un esquema del índice. Este puede tardar unos segundos en crear el índice.

### <a name="step-2-add-cognitive-skills"></a>Paso 2: agregar conocimientos cognitivos

A continuación, agregue los pasos de enriquecimiento a la canalización de indexación. El portal le ofrece aptitudes cognitivas predefinidas para el análisis de imágenes y de texto. En el portal, un conjunto de aptitudes opera en un solo campo de origen. Esto puede parecer un objetivo pequeño, pero para los blobs de Azure, el campo `content` contiene la mayor parte del documento de blobs (por ejemplo, un documento de Word o unas diapositivas de PowerPoint). Por lo tanto, este campo es una entrada ideal, ya que todo el contenido de un blob está ahí.

En la opción **Agregar aptitudes cognitivas**, elija las aptitudes que realizan el procesamiento de lenguaje natural. En esta guía de inicio rápido, elija el reconocimiento de entidades para los contactos, las organizaciones y las ubicaciones.

Haga clic en **Aceptar** para confirmar la definición.
   
  ![Definición del conjunto aptitudes](./media/cognitive-search-quickstart-blob/skillset.png)

Las aptitudes de procesamiento de lenguaje natural operan en el contenido de texto que se encuentra en el conjunto de datos de ejemplo. Puesto que no seleccionamos ninguna opción de procesamiento de imágenes, los archivos JPEG que se encontraron en el conjunto de datos de ejemplo no se procesarán en esta guía de inicio rápido. 

### <a name="step-3-configure-the-index"></a>Paso 3: configurar el índice

¿Recuerda el índice que creó con el origen de datos? En este paso, podrá ver su esquema y revisar cualquier configuración. 

En esta guía de inicio rápido, el asistente realiza un trabajo remarcable a la hora de configurar valores predeterminados razonables: 

+ Todos los índices deben tener un nombre. Para este tipo de origen de datos, el nombre predeterminado es *azureblob-index*.

+ Cada documento debe tener una clave. El asistente selecciona un campo de valores únicos. En esta guía de inicio rápido, la clave es *metadata_storage_path*.

+ Cada colección de campos debe tener campos con un tipo de datos que describa sus valores, y cada campo debe tener atributos de índice que describan cómo se usa en un escenario de búsqueda. 

Como ya definió el conjunto de aptitudes, el asistente supone que quiere recuperar el campo de datos de origen y los campos de salida que crearon las aptitudes. Por este motivo, el portal agrega campos de índice para `content`, `people`, `organizations` y `locations`. Tenga en cuenta que el asistente habilita automáticamente las opciones Retrievable y Searchable de estos campos.

En **Personalizar índice**, revise los atributos en los campos para ver cómo se utilizan en un índice. La opción Searchable indica que se puede buscar un campo. La opción Retrievable indica que un valor puede aparecer en los resultados. 

Tenga en cuenta que tiene la posibilidad de borrar Retrievable del campo `content`. En los blobs, este campo puede encontrarse con miles de líneas que son difíciles de leer en una herramienta como el **Explorador de búsqueda**.

Haga clic en **Aceptar** para confirmar la definición del índice.

  ![Campos de índice](./media/cognitive-search-quickstart-blob/index-fields.png)

> [!NOTE]
> Los campos que no se usan se han recortado de la captura de pantalla para abreviar. Si está siguiendo esta guía desde el portal, la lista le mostrará los campos adicionales.

### <a name="step-4-configure-the-indexer"></a>Paso 4: configurar el indexador

El indexador es un recurso de alto nivel que controla el proceso de indexación. Asimismo, especifica el nombre del origen de datos, el índice y la frecuencia de ejecución. El resultado final del asistente de la **importación de datos** es siempre un indexador que se puede ejecutar repetidamente.

En la página **Indexador**, póngale un nombre y use la opción predeterminada "ejecutar una vez" para ejecutarlo inmediatamente. 

  ![Definición del indexador](./media/cognitive-search-quickstart-blob/indexer-def.png)

Haga clic en **Aceptar** para importar, enriquecer e indexar los datos.

  ![Notificación de Azure Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Los procesos de enriquecimiento e indexación pueden tardar cierto tiempo y, por ello, se recomienda usar conjuntos de datos más pequeños para poder realizar una exploración temprana. Puede supervisar la indexación en la página de notificaciones de Azure Portal. 

## <a name="query-in-search-explorer"></a>Consulta en el Explorador de búsquedas

Después de crear un índice, puede enviar consultas para devolver documentos desde el índice. En el portal, utilice el **Explorador de búsquedas** para ejecutar consultas y ver los resultados. 

1. En la página del panel del servicio de búsqueda, haga clic en **Explorador de búsquedas** en la barra de comandos.

1. Seleccione **Cambiar índice** en la parte superior para seleccionar el índice que haya creado.

1. Escriba una cadena de búsqueda para consultar el índice como, por ejemplo, "John F. Kennedy".

Los resultados se devuelven en formato JSON, que suele ser detallado y difícil de leer, especialmente si se trata de documentos de gran tamaño que se crean en los blobs de Azure. 

Si no puede examinar los resultados con facilidad, utilice CTRL-F para buscar dentro de los documentos. En esta consulta, puede buscar dentro del contenido JSON escribiendo "John F. Kennedy", para así ver las instancias de ese término de búsqueda. 

CTRL-F también puede ayudarle a determinar cuántos documentos hay en un determinado conjunto de resultados. En cuanto a los blobs de Azure, el portal elige "metadata_storage_path" como clave, ya que cada valor es único en el documento. Use CTRL-F y busque "metadata_storage_path" para obtener un recuento de los documentos. Si nos fijamos en esta consulta, dos de los documentos del conjunto de resultados contienen el término "John F. Kennedy".

  ![Ejemplo del Explorador de búsquedas](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Puntos clave

Ya ha completado su primer ejercicio de indexación enriquecida. El objetivo de esta guía rápida es introducir conceptos importantes y guiarle a través del asistente para que pueda crear rápidamente el prototipo de una solución de búsqueda cognitiva utilizando sus propios datos.

Algunos conceptos clave que esperamos que haya tenido en cuenta incluyen la dependencia de los orígenes de datos de Azure. El enriquecimiento de Cognitive Search está vinculado a los indexadores, los cuales pertenecen a Azure y son específicos del origen. Aunque esta guía de inicio rápido usa Azure Blob Storage, también se pueden usar otros orígenes de datos de Azure. Para obtener más información, consulte [Indexadores de Azure Search](search-indexer-overview.md).

Otro concepto importante es que las aptitudes operan sobre los campos de entrada. En el portal, debe elegir un solo campo de origen para todas las aptitudes. En el código, las entradas pueden ser otros campos o la salida de una aptitud ascendente.

 Las entradas a una aptitud se asignan a un campo de salida en un índice. De forma interna, el portal establece las [anotaciones ](cognitive-search-concept-annotations-syntax.md) y define un [conjunto de aptitudes](cognitive-search-defining-skillset.md) que establece el orden de las operaciones y el flujo general. Estos pasos están ocultos en el portal, pero recuerde que estos conceptos serán importantes cuando comience a escribir el código.

Por último, aprendió que puede ver los resultados al consultar el índice. Al final, lo que proporciona Azure Search es un índice de búsqueda que puede consultar usando la sintaxis de consulta [simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) o [totalmente extendida](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Un índice que contenga campos enriquecidos es como cualquier otro. Si desea incorporar [analizadores personalizados](search-analyzers.md) o estándar, [perfiles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [sinónimos](search-synonyms.md), [filtros con facetas](search-filters-facets.md), búsquedas geográficas o cualquier otra característica de Azure Search, puede hacerlo cuando quiera.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya ha finalizado la exploración, la manera más rápida de borrar el contenido es eliminar el grupo de recursos que contiene el servicio Azure Search y el servicio Azure Blob.  

Si decide colocar ambos servicios en el mismo grupo, elimine el grupo de recursos para eliminar de manera permanente todo lo que contiene, incluyendo los servicios y cualquier contenido almacenado que haya creado para este ejercicio. En el portal, el nombre del grupo de recursos está en la página Información general de cada servicio.

## <a name="next-steps"></a>Pasos siguientes

Si quiere experimentar con la indexación y el enriquecimiento, vuelva a ejecutar el asistente con diferentes habilidades y campos de datos de origen. Para repetir los pasos, elimine el índice y el indexador y, a continuación, vuelva a crear el indexador con una nueva combinación de selecciones.

+ En **Introducción** > **Índices**, seleccione el índice que creó y, a continuación, haga clic en **Eliminar**.

+ En **Introducción**, haga doble clic en el icono **Indexadores**. Busque el indexador que creó y elimínelo.

Asimismo, también puede volver a usar los datos de ejemplo y los servicios que creó, y obtener información sobre cómo realizar las mismas tareas mediante programación en el tutorial siguiente. 

> [!div class="nextstepaction"]
> [Tutorial: obtener información sobre las API de REST de Cognitive Search](cognitive-search-tutorial-blob.md)