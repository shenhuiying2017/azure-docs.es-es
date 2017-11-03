---
title: "Búsqueda de datos no estructurados en almacenamiento en la nube de Azure"
description: "Búsqueda de datos no estructurados mediante Azure Search."
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Búsqueda de datos no estructurados en almacenamiento en la nube

En este tutorial, aprenderá a buscar datos no estructurados mediante [Azure Search](../../search/search-what-is-azure-search.md) mediante los datos almacenados en blobs de Azure. Los datos no estructurados son datos que no están organizados de manera predefinida o que no tienen un modelo de datos. Un ejemplo sería un archivo .txt.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de recursos
> * Crear una cuenta de almacenamiento
> * Crear un contenedor
> * Cargar datos en el contenedor
> * Crear un servicio de búsqueda a través del portal
> * Usar el servicio de búsqueda para buscar en el contenedor

## <a name="download-the-sample"></a>Descarga del ejemplo

Dispone de un conjunto de datos de ejemplo que se ha preparado para usted. **Descargue [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** y descomprímalo en su propia carpeta.

El ejemplo consta de los archivos de texto obtenidos de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Puede usarlos como archivos de texto de ejemplo para realizar búsquedas con Azure.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en [Azure Portal](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Una cuenta de almacenamiento proporciona una ubicación única para almacenar y tener acceso a los objetos de datos de Azure Storage.

Actualmente, hay dos tipos de cuentas de almacenamiento, **blob** y de **uso general**. En este tutorial, creará una cuenta de almacenamiento de **uso general**.

Si no está familiarizado con el proceso de creación de una cuenta de almacenamiento de uso general, aquí se muestra cómo hacerlo:

1. En el menú izquierdo, seleccione **Cuentas de almacenamiento** y luego seleccione **Agregar**.

2. Escriba un nombre único para la cuenta de almacenamiento. 

3. Seleccione **Resource Manager** como **modelo de implementación** y seleccione **Uso general** en la lista desplegable **Tipo de cuenta**.

4. Seleccione **Almacenamiento con redundancia local (LRS)** en la lista desplegable **Replicación**.

5. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre único.

6. Seleccione una suscripción adecuada.

7. Elija una ubicación y seleccione **Crear.**

  ![Búsqueda no estructurada](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Crear un contenedor

Los contenedores son similares a carpetas y se usan para almacenar blobs.

En este tutorial, usará un único contenedor para almacenar los archivos de texto obtenidos de clinicaltrials.gov.

1. Vaya a la cuenta de almacenamiento en Azure Portal.

2. Seleccione **Examinar blobs** en **Blob Service**.

3. Agregue un nuevo contenedor.

4. Asigne al contenedor el nombre "data" y seleccione **Contenedor** para el nivel de acceso público.

5. Seleccione **Aceptar** para crear el contenedor. 

  ![Búsqueda no estructurada](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Carga de los datos de ejemplo

Ahora que tiene un contenedor, puede cargar los datos de ejemplo en él.

1. Seleccione el contenedor y seleccione **Cargar**.

2. Seleccione el icono de la carpeta azul representado junto al campo Archivos y vaya a la carpeta local en la que extrajo los datos de ejemplo.

3. Seleccione todos los archivos extraídos y seleccione **Abrir**.

4. Seleccione **Cargar** para comenzar el proceso de carga.

  ![Búsqueda no estructurada](media/storage-unstructured-search/upload.png)

El proceso de carga puede tardar unos instantes.

Cuando finalice, vuelva al contenedor de datos para confirmar la carga de los archivos de texto.

  ![Búsqueda no estructurada](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Creación de una instancia de Search Service

Azure Search es una solución de búsqueda como servicio en la nube que ofrece a los desarrolladores las API y las herramientas necesarias para agregar una experiencia de búsqueda de datos enriquecida en las aplicaciones web, para dispositivos móviles y empresariales.

Si no está familiarizado con el proceso de creación de un servicio de búsqueda, aquí se muestra cómo hacerlo:

1. Vaya a la cuenta de almacenamiento en Azure Portal.

2. Desplácese hacia abajo y haga clic en **Agregar Azure Search** en **BLOB SERVICE**.

3. En **Importar datos**, seleccione **Elegir el servicio**.

4. Seleccione **Haga clic aquí para crear un nuevo servicio de búsqueda**.

5. Dentro de **Nuevo servicio de búsqueda**, escriba un nombre único para el servicio de búsqueda en el campo **URL**.

6. En **Grupo de recursos**, seleccione **Usar existente** y elija el grupo de recursos que creó anteriormente.

7. En **Plan de tarifa**, seleccione el nivel **Gratis** y haga clic en **Seleccionar**.

8. Seleccione **Crear** para crear el servicio de búsqueda.

  ![Búsqueda no estructurada](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Conexión del servicio de búsqueda al contenedor

Ahora que tiene un servicio de búsqueda, puede asociarlo a su instancia de Blob Storage. Esta sección le guía en el proceso de elegir un origen de datos, crear un índice y crear un indexador.

1. Vaya a la cuenta de almacenamiento.

2. Seleccione **Agregar Azure Search** en **SERVICIO BLOB**.

3. Seleccione **Search Service** dentro de **Importar datos** y haga clic en el servicio de búsqueda que creó en la sección anterior. Esta acción abre el campo **Nuevo origen de datos**.

### <a name="new-data-source"></a>Nuevo origen de datos

  Un origen de datos especifica qué datos indexar y cómo acceder a los datos. Un origen de datos se puede usar varias veces por parte del mismo servicio de búsqueda.

1. Escriba el nombre del origen de datos. En **Datos que se extraerán**, seleccione **Contenido y metadatos**. El origen de datos especifica qué partes del blob se indexan.
    
    a. En sus propios escenarios futuros, también puede seleccionar **Solo metadatos de almacenamiento**. Esta es la selección que haría si quisiera limitar los datos que se indexan a propiedades de blob estándar o propiedades definidas por el usuario.
    
    b. También puede elegir **Todos los metadatos** para obtener las propiedades de blob estándar y *todos* los metadatos específicos del tipo de contenido. 

2. Puesto que los blobs que usa son archivos de texto, establezca el **modo de análisis** en **Texto**.
    
    a. En sus propios escenarios futuros, puede que quiera seleccionar [otros modos de análisis](../../search/search-howto-indexing-azure-blob-storage.md) según el contenido de los blobs.

  ![Búsqueda no estructurada](media/storage-unstructured-search/datasources.png)

3. Seleccione **Contenedor de almacenamiento** para enumerar las cuentas de almacenamiento disponibles.

4. Seleccione la cuenta de almacenamiento y luego el contenedor que creó anteriormente.

5. Haga clic en **Seleccionar** para volver a **Nuevo origen de datos** y seleccione **Aceptar** para continuar.

  ![Búsqueda no estructurada](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Configuración del índice

  Un índice es una colección de campos del origen de datos que se pueden buscar. El índice es la manera en que el servicio de búsqueda sabe cómo se deben buscar los datos.

1. En **Importar datos**, seleccione **Personalizar índice de destino**.
 
2. Escriba el nombre del índice en el campo **Nombre de índice**.

3. Active la casilla del atributo **Recuperable** en **metadata_storage_name**.

  ![Búsqueda no estructurada](media/storage-unstructured-search/valuestoselect.png)

4. Haga clic en **Aceptar**. Se abrirá **Crear indizador**.

Los parámetros de su índice y los atributos que asigne a esos parámetros son importantes. Los parámetros especifican *qué* datos se almacenarán y los atributos *cómo* se almacenarán esos datos.

La columna **Nombre de campo** contiene los parámetros. En la tabla siguiente se proporciona una lista de los atributos disponibles y sus descripciones.

### <a name="field-attributes"></a>Atributos de campo
| Atributo | Description |
| --- | --- |
| *Clave* |Una cadena que proporciona el identificador único de cada documento, que se usa para buscar los documentos. Todos los índices deben tener una clave. Solo un campo puede ser la clave y se debe establecer su tipo en Edm.String. |
| *Retrievable* |Establece si el campo se puede devolver en un resultado de búsqueda. |
| *Filterable* |Permite que el campo se use en consultas de filtro. |
| *Sortable* |Permite que una consulta ordene los resultados de búsqueda mediante este campo. |
| *Facetable* |Permite que un campo se use en una estructura de navegación con facetas para el filtrado autodirigido. Normalmente los campos que contienen valores repetitivos que se pueden usar para agrupar varios documentos (por ejemplo, varios documentos que forman parte de una única categoría de servicio o un único producto) funcionan mejor como facetas. |
| *Searchable* |Marca el campo como campo de búsqueda de texto completo. |


### <a name="create-an-indexer"></a>Creación de un indexador
    
  Un indexador conecta un origen de datos con un índice de búsqueda y proporciona una programación para volver a indexar los datos.

1. Escriba un nombre en el campo **Nombre** y seleccione **Aceptar**.

  ![Búsqueda no estructurada](media/storage-unstructured-search/exindexer.png)

2. Regresa a **Importar datos**. Seleccione **Aceptar** para finalizar el proceso de conexión.

Ahora ha conectado correctamente el blob al servicio de búsqueda. El portal tarda unos minutos en mostrar que el índice se ha rellenado. Sin embargo, el servicio de búsqueda comienza inmediatamente la indexación para que pueda empezar a buscar enseguida.

## <a name="search-your-text-files"></a>Búsqueda en los archivos de texto

Para buscar en los archivos, abra el Explorador de búsqueda dentro del índice de su servicio de búsqueda recién creado.

Los pasos siguientes muestran dónde encontrar el Explorador de búsqueda y proporcionan algunas consultas de ejemplo:

1. Navegue hasta todos los recursos para buscar el servicio de búsqueda recién creado.

  ![Búsqueda no estructurada](media/storage-unstructured-search/exampleurl.png)

3. Seleccione el índice para abrirlo. 

  ![Búsqueda no estructurada](media/storage-unstructured-search/overview.png)

4. Seleccione **Explorador de búsqueda** para abrir el Explorador de búsqueda, donde puede realizar consultas en vivo en los datos.

  ![Búsqueda no estructurada](media/storage-unstructured-search/indexespane.png)

5. Seleccione **Buscar** mientras el campo de cadena de consulta está vacío. Una consulta vacía devuelve *todos* los datos de sus blobs.

  ![Búsqueda no estructurada](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Búsqueda de texto completo 

Escriba "Miopía" en el campo **Cadena de consulta** y seleccione **Buscar**. Se inicia una búsqueda del contenido de los archivos y se devuelve un subconjunto de ellos que contiene la palabra "Miopía".

  ![Búsqueda no estructurada](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Búsqueda de propiedades del sistema

También puede crear consultas que buscan por propiedades del sistema mediante el parámetro `$select`. Escriba `$select=metadata_storage_name` en la cadena de consulta y presione Entrar; solo se devuelve ese campo en particular.
    
La cadena de consulta está modificando directamente la dirección URL, por lo que no se permiten espacios. Para buscar en varios campos, use una coma, por ejemplo: `$select=metadata_storage_name,metadata_storage_path`.
    
El parámetro `$select` solo puede usarse con campos que estén marcados como recuperables al definir el índice.

  ![Búsqueda no estructurada](media/storage-unstructured-search/metadatasearchunstructured.png) 

Ahora ha completado este tutorial y tiene un conjunto de datos no estructurados que permiten búsqueda.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió sobre la búsqueda de datos no estructurados con Azure Search y a:

> [!div class="checklist"]
> * Crear un grupo de recursos
> * Crear una cuenta de almacenamiento
> * Crear un contenedor
> * Carga de datos en el contenedor
> * Creación de una instancia de Search Service
> * Uso de la instancia de Search Service para realizar búsquedas en el contenedor

Siga este vínculo para aprender más sobre la indexación de documentos con Azure Search.

> [!div class="nextstepaction"]
> [Indexación de documentos en Azure Blob Storage con Azure Search](../../search/search-howto-indexing-azure-blob-storage.md)