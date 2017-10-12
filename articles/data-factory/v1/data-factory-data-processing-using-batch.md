---
title: Procesamiento de conjuntos de datos a gran escala mediante Data Factory y Batch | Microsoft Docs
description: "Describe cómo procesar grandes cantidades de datos en una canalización de Azure Data Factory mediante la funcionalidad de procesamiento paralelo de Azure Batch."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 75213a4d0297c96ec32200158d8b60db4b8b2da4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Procesamiento de datos a gran escala mediante Data Factory y Batch
En este artículo se describe la arquitectura de una solución de ejemplo en la que se mueven y se procesan conjuntos de datos a gran escala de forma automática y programada. También se incluye un tutorial paso a paso para implementar la solución con Azure Data Factory y Azure Batch.

Este artículo es más largo de lo habitual, ya que contiene un tutorial de una solución de ejemplo completa. Si no tiene experiencia con Batch y Data Factory, puede informarse sobre estos servicios y cómo interactúan. Si tiene algún conocimiento sobre los servicios y está diseñando una solución, puede consultar la [sección sobre arquitectura](#architecture-of-sample-solution) del artículo. Si está desarrollando un prototipo o una solución, tal vez le interese también seguir las instrucciones paso a paso que se describen en el [tutorial](#implementation-of-sample-solution). No dude en hacernos llegar cualquier comentario que tenga sobre el contenido y su uso.

En primer lugar, vamos a ver cómo los servicios Data Factory y Batch pueden ayudar a procesar grandes conjuntos de datos en la nube.     

## <a name="why-azure-batch"></a>¿Por qué elegir Azure Batch?
Azure Batch permite ejecutar a gran escala aplicaciones paralelas y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Se trata de un servicio de plataforma que programa el trabajo de proceso intensivo para que se ejecute en una colección administrada de máquinas virtuales, y que puede escalar automáticamente los recursos de proceso para satisfacer las necesidades de sus trabajos.

Con el servicio Batch, se definen los recursos de procesos de Azure para ejecutar las aplicaciones en paralelo y a escala. Puede ejecutar trabajos a petición o programados, y no tiene que crear, configurar ni administrar manualmente un clúster de HPC, máquinas virtuales individuales, redes virtuales ni una infraestructura compleja de programación de tareas y trabajos.

Si no está familiarizado con Azure Batch, consulte los siguientes artículos, que le ayudarán a comprender la arquitectura e implementación de la solución que se describe en este artículo.   

* [Datos básicos de Azure Batch](../../batch/batch-technical-overview.md)
* [Información general de las características de Batch](../../batch/batch-api-basics.md)

(Opcional) Para más información sobre Azure Batch, consulte la [ruta de aprendizaje de Azure Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>¿Por qué elegir Azure Data Factory?
Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Con el servicio Data Factory, puede crear canalizaciones de datos administradas que transfieran los datos desde un almacén de datos local y en la nube hasta un almacén de datos centralizado (por ejemplo: Azure Blob Storage) y que procesen o transformen los datos mediante servicios como HDInsight y Azure Machine Learning. También puede configurar las canalizaciones de datos para que ejecuten con arreglo a una programación (cada hora, cada día, cada semana, etc.), así como supervisarlas y administrarlas rápidamente para identificar problemas y emprender las acciones necesarias.

Si no está familiarizado con Azure Data Factory, consulte los siguientes artículos, que le ayudarán a comprender la arquitectura e implementación de la solución que se describe en este artículo.  

* [Introducción al servicio Data Factory de Azure](data-factory-introduction.md)
* [Creación de la primera canalización de datos](data-factory-build-your-first-pipeline.md)   

(Opcional) Para más información sobre Azure Data Factory, consulte la [ruta de aprendizaje de Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory y Batch juntos
Data Factory contiene actividades integradas, como la actividad de copia, que permite copiar o mover datos de un almacén de datos de origen a un almacén de destino, y la actividad de Hive, que permite procesar datos utilizando clústeres de Hadoop (HDInsight) en Azure. Consulte en este artículo la lista de [actividades de transformación de datos](data-factory-data-transformation-activities.md) admitidas.

Data Factory también permite crear actividades de .NET personalizadas para mover o procesar datos utilizando su propia lógica, así como ejecutar estas actividades en un clúster de HDInsight o en un grupo de máquinas virtuales de Azure Batch. Cuando use Azure Batch, podrá configurar el grupo para que se adapte automáticamente (se agreguen o quiten máquinas virtuales en función de la carga de trabajo) con arreglo a una fórmula especificada.     

## <a name="architecture-of-sample-solution"></a>Arquitectura de la solución de ejemplo
Aunque la arquitectura que se describe en este ejemplo corresponde a una solución sencilla, puede ser aplicable a escenarios complejos, como el modelado de riesgos por parte de servicios financieros, el procesamiento y la representación de imágenes, o el análisis de genomas.

En el diagrama, se muestra 1) cómo Data Factory organiza el movimiento y el procesamiento de datos y 2) cómo Azure Batch procesa los datos en paralelo. Descargue e imprima el diagrama para facilitar su consulta (11 x 17 pulgadas o tamaño A3): [Orquestación de HPC y de datos mediante Azure Batch y Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagrama de procesamiento de datos de gran escala](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

La lista siguiente proporciona los pasos básicos del proceso. La solución incluye código y explicaciones para compilar la solución completa.

1. **Configure Lote de Azure con un grupo de nodos de proceso (máquinas virtuales)**. Puede especificar el número de nodos y el tamaño de cada nodo.
2. **Cree una instancia de Azure Data Factory** que esté configurada con entidades que representen Azure Blob Storage, el servicio de proceso Azure Batch, los datos de entrada y salida y un flujo de trabajo o una canalización con las actividades que mueven y transforman datos.
3. **Cree una actividad personalizada de .NET en la canalización de Data Factory**. La actividad es el código de usuario que se ejecutará en el grupo de Azure Batch.
4. **Almacene grandes cantidades de datos de entrada como blobs en Azure Storage**. Los datos se dividen en segmentos lógicos (normalmente, en función de la fecha y la hora).
5. **Data Factory copia los datos que se procesan en paralelo** en la ubicación secundaria.
6. **Data Factory ejecuta la actividad personalizada con el grupo que asigna Lote**. Además, puede ejecutar actividades al mismo tiempo. Cada actividad procesa un segmento de datos. Los resultados se almacenan en Azure Storage.
7. **Data Factory mueve los resultados finales a una tercera ubicación**para que se distribuyan a través de una aplicación o para que se sigan procesando con otras herramientas.

## <a name="implementation-of-sample-solution"></a>Implementación de la solución de ejemplo
La solución de ejemplo se ha diseñado intencionadamente para que resulte sencilla, ya que el propósito es mostrarle cómo usar Data Factory y Batch juntos para procesar conjuntos de datos. La solución sencillamente cuenta el número de repeticiones de un término de búsqueda (“Microsoft”) en los archivos de entrada, que están organizados en una serie temporal. genera el recuento en archivos de salida.

**Tiempo**: si está familiarizado con Azure, Data Factory y Batch, y cumple los requisitos previos que se indican a continuación, se estima que esta solución tardará entre 1 y 2 horas en completarse.

### <a name="prerequisites"></a>Requisitos previos
#### <a name="azure-subscription"></a>Suscripción de Azure
Si carece de suscripción de Azure, puede crear una cuenta de prueba gratuita en tan solo unos minutos. Consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En este tutorial se usa una cuenta de Azure Storage para almacenar los datos. Si no dispone de una, consulte [Crear una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). En la solución de ejemplo, se usa Blob Storage.

#### <a name="azure-batch-account"></a>Cuenta de Azure Batch
Cree una cuenta de Azure Batch en [Azure Portal](http://manage.windowsazure.com/). Consulte [Creación y administración de una cuenta de Azure Batch en Azure Portal](../../batch/batch-account-create-portal.md). Anote el nombre y la clave de la cuenta de Azure Batch. También puede usar el cmdlet [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) para crear una cuenta de Azure Batch. Consulte [Introducción a los cmdlets de PowerShell para Azure Batch](../../batch/batch-powershell-cmdlets-get-started.md) para obtener instrucciones detalladas para usar este cmdlet.

La solución de ejemplo usa Azure Batch (de forma indirecta mediante una canalización de Azure Data Factory) para procesar datos en paralelo en un grupo de nodos de proceso, que es una colección administrada de máquinas virtuales.

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Grupo de máquinas virtuales (VM) de Azure Batch
Cree un **grupo Azure Batch** con al menos 2 nodos de proceso.

1. En [Azure Portal](https://portal.azure.com), haga clic en **Examinar** en el menú izquierdo y haga clic en **Cuentas de Batch**.
2. Seleccione la cuenta de Lote de Azure para abrir la hoja **Cuenta de Lote** .
3. Haga clic en el icono **Grupos** .
4. En la hoja **Grupos**, haga clic en el botón Agregar en la barra de herramientas para agregar un grupo.
   1. Especifique un identificador para el grupo (**Identificador del grupo**). Anote el **identificador del grupo**; lo necesitará al crear la solución de Data Factory.
   2. Especifique **Windows Server 2012 R2** en Familia del sistema operativo.
   3. Seleccione un **plan de tarifa de nodos**.
   4. Escriba **2** como valor en la configuración **Dedicada a destino**.
   5. Escriba **2** como valor en la configuración **Máximo de tareas por nodo**.
   6. Haga clic en **Aceptar** para crear el grupo.

#### <a name="azure-storage-explorer"></a>Explorador de Azure Storage
[Explorador de Azure Storage 6 (herramienta)](https://azurestorageexplorer.codeplex.com/) o [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (de ClumsyLeaf Software). Use estas herramientas para inspeccionar y modificar los datos en sus proyectos de Azure Storage, incluidos los registros de las aplicaciones hospedadas en la nube.

1. Cree un contenedor denominado **mycontainer** con acceso privado (sin acceso anónimo).
2. Si usa **CloudXplorer**, cree carpetas y subcarpetas con la estructura siguiente:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` y `outputfolder` son carpetas de nivel superior en `mycontainer`. `inputfolder` tiene subcarpetas con marcas de fecha y hora (AAAA-MM-DD-HH).

   Si usa el **Explorador de Azure Storage**, en el paso siguiente, tendrá que cargar archivos con los nombres: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`, y así sucesivamente. Este paso creará automáticamente las carpetas.
3. Cree un archivo de texto **file.txt** en el equipo con contenido que incluya la palabra clave **Microsoft**. Por ejemplo: "test custom activity Microsoft test custom activity Microsoft".
4. Cargue el archivo a las siguientes carpetas de entrada en Azure Blob Storage.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Si usa el **Explorador de Azure Storage**, cargue el archivo **file.txt** en **mycontainer**. Haga clic en **Copiar** en la barra de herramientas para crear una copia del blob. En el cuadro de diálogo **Copy Blob** (Copiar blob), cambie el **nombre del blob de destino** a `inputfolder/2015-11-16-00/file.txt`. Repita este paso para crear `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`, etc. Esta acción crea automáticamente las carpetas.
5. Cree otro contenedor denominado: `customactivitycontainer`. Va a cargar el archivo ZIP de actividad personalizada a este contenedor.

#### <a name="visual-studio"></a>Visual Studio
Instale Microsoft Visual Studio 2012 o una versión posterior para crear la actividad personalizada de Batch que se utilizará en la solución de Data Factory.

### <a name="high-level-steps-to-create-the-solution"></a>Pasos de alto nivel para crear la solución
1. Cree una actividad personalizada que contenga la lógica de procesamiento de datos.
2. Cree una instancia de Azure Data Factory que use la actividad personalizada:

### <a name="create-the-custom-activity"></a>Creación de la actividad personalizada
La actividad personalizada de Factoría de datos es el núcleo de esta solución de ejemplo. La solución de ejemplo usa Azure Batch para ejecutar la actividad personalizada. Consulte [Uso de actividades personalizadas en una canalización de Azure Data Factory](data-factory-use-custom-activities.md) para ver información básica para desarrollar actividades personalizadas y usarlas en las canalizaciones de Azure Data Factory.

Para crear una actividad personalizada de .NET que se pueda usar en una canalización de Azure Data Factory, deberá crear un proyecto de **biblioteca de clases .NET** con una clase que implemente la interfaz **IDotNetActivity**. Esta interfaz tiene un solo método, **Execute**. Esta es la firma de dicho método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

El método tiene algunos componentes clave que debe conocer.

* El método toma cuatro parámetros:

  1. **linkedServices**. Se trata de una lista enumerable de servicios vinculados que vincula los orígenes de datos de entrada y salida (por ejemplo: Azure Blob Storage) con la factoría de datos. En este ejemplo, hay solo un servicio vinculado del tipo Azure Storage que se utilice como entrada y salida.
  2. **datasets**. Se trata de una lista enumerable de conjuntos de datos. Este parámetro se puede usar para obtener las ubicaciones y esquemas que definen los conjuntos de datos de entrada y salida.
  3. **activity**. Este parámetro representa la entidad de proceso actual, en este caso, un servicio Azure Batch.
  4. **logger**. El parámetro logger permite escribir comentarios de depuración que se muestran como el registro de "User" en la canalización.
* El método devuelve un diccionario que se puede usar para encadenar actividades personalizadas en el futuro. Esta característica todavía no está implementada, así que devuelve un diccionario vacío del método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimiento: Creación de la actividad personalizada
1. Cree un proyecto de biblioteca de clases .NET en Visual Studio.

   1. Inicie **Visual Studio 2012**/**2013/2015**.
   2. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.
   3. Expanda **Plantillas** y seleccione **Visual C\#**. En este tutorial se usa C\#, pero puede usar cualquier lenguaje .NET para desarrollar la actividad personalizada.
   4. Seleccione **Biblioteca de clases** en la lista de tipos de proyecto de la derecha.
   5. Escriba **MyDotNetActivity** for the **Nombre**.
   6. Seleccione **C:\\ADF** para **Ubicación**. Cree la carpeta **ADF** si no existe.
   7. Haga clic en **Aceptar** para crear el proyecto.
2. Haga clic en **Herramientas**, seleccione **Administrador de paquetes de NuGet** y haga clic en **Consola del Administrador de paquetes**.
3. En la **Consola del Administrador de paquetes**, ejecute el siguiente comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe el paquete NuGet de **Azure Storage** en el proyecto. Necesita este paquete porque va a usar la API de Blob Storage en este ejemplo.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Agregue las siguientes directivas **using** al archivo de origen en el proyecto.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Cambie el nombre del **espacio de nombres** por **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Cambie el nombre de la clase a **MyDotNetActivity** y derívela desde la interfaz **IDotNetActivity** tal como se muestra a continuación.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implemente (agregue) el método **Execute** de la interfaz **IDotNetActivity** en la clase **MyDotNetActivity** y copie el siguiente código de ejemplo en el método. Consulte la sección [Método Execute](#execute-method) para obtener una explicación de la lógica usada en este método.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // create storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);
    
           // Calculate method returns the number of occurrences of
           // the search term (“Microsoft”) in each blob associated
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Agregue los siguientes métodos auxiliares a la clase. Estos métodos se invocan desde el método **Execute** . Y lo más importante, el método **Calculate** aísla el código que procesa una iteración de cada blob.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    El método **GetFolderPath** devuelve la ruta de acceso a la carpeta a la que apunta el conjunto de datos y el método **GetFileName** devuelve el nombre del blob o archivo a la que apunta el conjunto de datos.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    El método **Calculate** calcula el número de instancias de la palabra clave **Microsoft** en los archivos de entrada (los blobs de la carpeta). El término de búsqueda ("Microsoft") está codificado de forma rígida en el código.

1. Compile el proyecto. Haga clic en **Compilar** en el menú y haga clic en **Compilar solución**.
2. Inicie el **Explorador de Windows** y vaya a la carpeta **bin\\debug** o **bin\\release**, según el tipo de compilación.
3. Cree un archivo ZIP **MyDotNetActivity.zip** que contenga todos los archivos binarios en la carpeta **\\bin\\Debug**. Podría incluir el archivo MyDotNetActivity.**pdb** para obtener detalles adicionales, como el número de línea en el código fuente que causó el problema, en caso de error.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Cargue **MyDotNetActivity.zip** como blob en el contenedor de blobs: `customactivitycontainer` en la instancia de Azure Blob Storage que usa el servicio vinculado **StorageLinkedService** de **ADFTutorialDataFactory**. Cree el contenedor de blobs `customactivitycontainer` si aún no existe.

#### <a name="execute-method"></a>Método Execute
En esta sección se proporcionan más detalles y notas sobre el código del método Execute.

1. Los miembros para procesar una iteración en la colección de entrada se encuentran en el espacio de nombres [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . El procesamiento de una iteración en la colección de blobs requiere el uso de la clase **BlobContinuationToken** . En esencia, es preciso usar un bucle do-while con el token como mecanismo para salir del bucle. Para más información, consulte [Uso de Blob Storage de .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Aquí se muestra un bucle básico:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Para más información, consulte la documentación del método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) .
2. Lógicamente, el código para trabajar en el conjunto de blobs lógicamente está dentro del bucle do-while. En el método **Execute**, el bucle do-while pasa la lista de blobs a un método denominado **Calculate**. El método devuelve una variable de cadena denominada **output** que es el resultado de haber procesado una iteración en todos los blobs del segmento.

   Devuelve el número de apariciones del término de búsqueda (**Microsoft**) en el blob que se pasa al método **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Una vez que el método **Calculate** ha terminado el trabajo, debe escribirse en un blob nuevo. Por consiguiente, por cada conjunto de blobs que se procesa, se puede escribir un blob nuevo con los resultados. Para escribir en un blob nuevo, primero es preciso buscar el conjunto de datos de salida.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. El código también llama a un método auxiliar, **GetFolderPath**, para recuperar la ruta de acceso de la carpeta (el nombre del contenedor de almacenamiento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   El método **GetFolderPath** convierte el objeto DataSet en una clase AzureBlobDataSet, que tiene una propiedad denominada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. El código llama al método **GetFileName** para recuperar el nombre de archivo (nombre del blob). El código es similar al código anterior para obtener la ruta de acceso de la carpeta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. El nombre del archivo se escribe mediante la creación de un objeto URI. El constructor URI usa la propiedad **BlobEndpoint** para devolver el nombre del contenedor. Tanto la ruta de la carpeta como el nombre de archivo se agregan al construir el identificador URI del blob de salida.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Se ha escrito el nombre del archivo y ahora puede escribir la cadena de salida desde el método **Calculate** en un blob nuevo:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Creación de la factoría de datos
En la sección [Creación de la actividad personalizada](#create-the-custom-activity), se creó una actividad personalizada y se cargó el archivo ZIP con archivos binarios y el archivo PDB en un contenedor de blobs de Azure. En esta sección, creará una instancia de **Azure Data Factory** con una **canalización** que usa la **actividad personalizada**.

El conjunto de datos de entrada de la actividad personalizada representa los blobs (archivos) de la carpeta de entrada (`mycontainer\\inputfolder`) de Blob Storage. El conjunto de datos de salida de la actividad representa los blobs de salida de la carpeta de salida (`mycontainer\\outputfolder`) de Blob Storage.

Coloque uno o varios archivos en las carpetas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por ejemplo, coloque un archivo (file.txt) con el siguiente contenido en cada una de las carpetas.

```
test custom activity Microsoft test custom activity Microsoft
```

Cada carpeta de entrada corresponde a un segmento de Azure Data Factory, incluso si la carpeta contiene 2 o más archivos. Cuando la canalización procesa cada segmento, la actividad personalizada procesa una iteración en todos los blobs de la carpeta de entrada de dicho segmento.

Verá cinco archivos de salida con el mismo contenido. Por ejemplo, el archivo de salida resultante de procesar el archivo en la carpeta 2015-11-16-00 tiene el siguiente contenido:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Si coloca varios archivos (file.txt, file2.txt, file3.txt) con el mismo contenido en la carpeta de entrada, verá el siguiente contenido en el archivo de salida. Cada carpeta (2015-11-16-00, etc.) corresponde a un segmento en este ejemplo, incluso si la carpeta tiene varios archivos de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

El archivo de salida tiene ahora tres líneas, una por cada archivo de entrada (blob) en la carpeta asociada con el segmento (2015-11-16-00).

Se crea una tarea para cada ejecución de actividad. En este ejemplo, solamente hay una actividad en la canalización. Cuando se procesa un segmento en la canalización, la actividad personalizada se ejecuta en Azure Batch para procesar el segmento. Puesto que hay 5 segmentos (cada segmento puede tener varios blobs o archivos), se crearán 5 tareas en Azure Batch. Cuando se ejecuta una tarea en Lote, lo que realmente se está ejecutando es la actividad personalizada.

En el siguiente tutorial, se proporcionan más detalles.

#### <a name="step-1-create-the-data-factory"></a>Paso 1: Creación de la factoría de datos
1. Tras iniciar sesión en [Azure Portal](https://portal.azure.com/), siga estos pasos:

   1. Haga clic en **NUEVO** en el menú de la izquierda.
   2. Haga clic en **Datos y análisis** en la hoja **Nuevo**.
   3. Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.
2. En la hoja **Nueva factoría de datos**, escriba **CustomActivityFactory** en el campo Nombre. El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre "CustomActivityFactory" de factoría de datos no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, **suNombreCustomActivityFactory**) e intente crearla de nuevo.
3. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS**y seleccione un grupo de recursos existente o cree uno.
4. Compruebe que usa la suscripción y la región correctas en las que desea que se cree la factoría de datos.
5. Haga clic en **Crear** en la hoja **Nueva fábrica de datos**.
6. Verá la factoría de datos que se crea en el **Panel** de Azure Portal.
7. Tras crear correctamente la factoría de datos, se ve la página de la factoría de datos, que muestra el contenido de la misma.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Paso 2: Creación de servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. En este paso, vinculará su cuenta de **Azure Storage** y la cuenta de **Azure Batch** con su factoría de datos.

#### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado Azure Storage
1. Haga clic en el icono **Crear e implementar** de la hoja **FACTORÍA DE DATOS** de **CustomActivityFactory**. Aparecerá Data Factory Editor.
2. Haga clic en **Nuevo almacén de datos** en la barra de comandos y elija **Azure storage.** Debería ver el script JSON para crear un servicio vinculado de Azure Storage en el editor.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Reemplace **account name** por el nombre de la cuenta de Azure Storage y **account key** por la clave de acceso de la cuenta de Azure Storage. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Creación del servicio vinculado Azure Batch
En este paso, creará un servicio vinculado para su cuenta de **Azure Batch** que se usará para ejecutar la actividad personalizada de Data Factory.

1. Haga clic en **Nuevo proceso** en la barra de comandos y elija **Azure Batch.** Debería ver el script JSON para crear un servicio vinculado Azure Batch en el editor.
2. En el script JSON:

   1. Reemplace **account name** por el nombre de la cuenta de Azure Batch.
   2. Reemplace **access key** por la clave de acceso de la cuenta de Azure Batch.
   3. Escriba el identificador del grupo de la propiedad **poolName****.** Para esta propiedad, puede especificar cualquier nombre de grupo o id. de bloque.
   4. Escriba el identificador URI de lote para la propiedad **batchUri** de JSON.

      > [!IMPORTANT]
      > La **dirección URL** de la **hoja de la cuenta de Azure Batch** tiene el formato siguiente: \<nombreDeCuenta\>.\<región\>.batch.azure.com. Para la propiedad **batchUri** en el script JSON, necesitará **quitar "nombreDeCuenta"** de la dirección URL. Ejemplo: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      En la propiedad **poolName**, también puede especificar el identificador del grupo, en lugar del nombre del grupo.

      > [!NOTE]
      > El servicio de Factoría de datos no admite una opción a petición para el Lote de Azure como lo hace para HDInsight. Solo puede usar su propio grupo de Azure Batch en una instancia de Azure Data Factory.
      >
      >
   5. Especifique **StorageLinkedService** for the **linkedServiceName** . Ha creado este servicio vinculado en el paso anterior. Este almacenamiento se usa como área de almacenamiento provisional para archivos y registros.
3. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

#### <a name="step-3-create-datasets"></a>Paso 3: Creación de conjuntos de datos
En este paso, crea conjuntos de datos que representan los datos de entrada y salida.

#### <a name="create-input-dataset"></a>Creación de un conjunto de datos de entrada
1. En el **Editor** de Data Factory, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Azure Blob Storage** en el menú desplegable.
2. Reemplace el script JSON del panel derecho por el siguiente fragmento de código JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Más adelante, en este mismo tutorial, creará una canalización cuya hora de finalización es 2015-11-16T00:00:00Z, y cuya hora de finalización es 2015-11-16T05:00:00Z. Está programada para generar datos **cada hora**, por lo que hay cinco segmentos de entrada o salida (entre **00**:00:00 -\> **05**:00:00).

    Los valores de **frequency** e **interval** del conjunto de datos de entrada se establecen en **Hour** y **1** respectivamente, lo que significa que el segmento de entrada está disponible cada hora.

    Estas son las horas de inicio de cada segmento, que se representa mediante la variable de sistema **SliceStart** en el fragmento de código JSON anterior.

    | **Segmento** | **Hora de inicio**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    La propiedad **folderPath** se calcula usando la parte de año, mes, día y hora de la hora de inicio del segmento (**SliceStart**). Por lo tanto, aquí se muestra cómo se asigna una carpeta de entrada a un segmento.

    | **Segmento** | **Hora de inicio**          | **Carpeta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Haga clic en **Implementar** en la barra de herramientas para crear e implementar la tabla **InputDataset**.

#### <a name="create-output-dataset"></a>Creación del conjunto de datos de salida
En este paso, creará otro conjunto de datos de tipo AzureBlob para representar los datos de salida.

1. En el **Editor** de Data Factory, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Azure Blob Storage** en el menú desplegable.
2. Reemplace el script JSON del panel derecho por el siguiente fragmento de código JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Se genera un blob o archivo de salida para cada segmento de entrada. Así es cómo se asigna el nombre al archivo de salida de cada segmento. Todos los archivos de salida se generan en una sola carpeta de salida: `mycontainer\\outputfolder`.

    | **Segmento** | **Hora de inicio**          | **Archivo de salida**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Recuerde que todos los archivos de una carpeta de entrada (por ejemplo, 2015-11-16-00) forman parte de un segmento con la hora de inicio 2015-11-16-00. Cuando se procesa este segmento, la actividad personalizada examinan todos los archivos y produce una línea en el archivo de salida con el número de apariciones del término de búsqueda ("Microsoft"). Si hay tres archivos en la carpeta 2015-11-16-00, habrá tres líneas en el archivo de salida 2015-11-16-00.txt.

1. Haga clic en **Implementar** en la barra de herramientas para crear e implementar **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Paso 4: Creación y ejecución de la canalización con una actividad personalizada
En este paso, creará una canalización con la actividad personalizada que creó antes.

> [!IMPORTANT]
> Si no ha cargado **file.txt** a las carpetas de entrada en el contenedor de blobs, hágalo antes de crear la canalización. La propiedad **isPaused** está establecida en false en el código JSON de la canalización, por lo que esta se ejecutará de inmediato, ya que la fecha de inicio (**start**) ya ha pasado.
>
>

1. En el Editor de Factoría de datos, haga clic en **Nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (puntos suspensivos)** para verlo.
2. Reemplace el script JSON del panel derecho con el siguiente script JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Tenga en cuenta los siguientes puntos:

   * Solo hay una actividad en la canalización y es del tipo **DotNetActivity**.
   * **AssemblyName** se establece en el nombre del archivo DLL, **MyDotNetActivity.dll**.
   * **EntryPoint** se establece **MyDotNetActivityNS.MyDotNetActivity**. Es básicamente \<espacioDeNombres\>.\<nombreDeClase\> en el código.
   * **PackageLinkedService** está establecido en **StorageLinkedService**, que apunta a la instancia de Blob Storage que contiene el archivo ZIP de la actividad personalizada. Si usa diferentes cuentas de Azure Storage para los archivos de entrada y salida y el archivo ZIP de actividad personalizada, tendrá que crear otro servicio vinculado de Azure Storage. En este artículo, se da por supuesto que usa la misma cuenta de Azure Storage.
   * **PackageFile** se establece en **customactivitycontainer/MyDotNetActivity.zip**. Está en el formato \<contenedorDelZIP\>/\<nombreDelZIP.zip\>.
   * La actividad personalizada toma **InputDataset** como entrada y **OutputDataset** como salida.
   * La propiedad **linkedServiceName** de la actividad personalizada apunta a **AzureBatchLinkedService**, que indica a Azure Data Factory que la actividad personalizada debe ejecutarse en Azure Batch.
   * La configuración **concurrency** es importante. Si usa el valor predeterminado, que es 1, aunque tenga 2 o más nodos de proceso en el grupo de Azure Batch, los segmentos se procesarán uno tras otro. Por lo tanto, no se aprovecha la ventaja de la funcionalidad de procesamiento paralelo de Azure Batch. Si establece **concurrency** en un valor mayor, digamos 2, significa que se pueden procesar dos segmentos (que se corresponden con dos tareas en Azure Batch) al mismo tiempo, en cuyo caso,se usan ambas máquinas virtuales en el grupo de Azure Batch. Por tanto, establezca concurrency correctamente.
   * De manera predeterminada, solo se ejecuta una tarea (segmento) en una máquina virtual en un momento dado. Esto es porque, de forma predeterminada, **Máximo de tareas por máquina virtual** se establece en 1 para un grupo de Azure Batch. Como parte de los requisitos previos, ha creado un grupo con esta propiedad establecida en 2, por lo que los dos segmentos de Factoría de datos se pueden ejecutar en una máquina virtual al mismo tiempo.

    -   **isPaused** se establece en false de forma predeterminada. La canalización se ejecuta inmediatamente en este ejemplo, ya que los segmentos se inician en el pasado. Esta propiedad se puede establecer en true para pausar la canalización y se puede volver a establecer en false para reiniciarla.

    -   Hay una diferencia de cinco horas entre la hora de **inicio** y la de **finalización**, y los segmentos se producen cada hora, por lo que la canalización produce cinco segmentos.

1. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.

#### <a name="step-5-test-the-pipeline"></a>Paso 5: Prueba de la canalización
En este paso, probará la canalización colocando archivos en las carpetas de entrada. Comencemos a probar la canalización con un archivo por carpeta de entrada.

1. En la hoja Data Factory de Azure Portal, haga clic en **Diagrama**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. En la vista de diagrama, haga doble clic en el conjunto de datos de entrada, **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Debería ver la hoja **InputDataset** con los cinco segmentos listos. Observe los valores de **HORA DE INICIO DE SEGMENTO** y **HORA DE FINALIZACIÓN DE SEGMENTO** para cada segmento.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. En la **vista Diagrama**, haga clic en **OutputDataset**.
5. Si ya se han producido, debería ver que los cinco segmentos están en estado Listo.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Use Azure Portal para ver las **tareas** asociadas con los **segmentos** y para comprobar en qué máquina virtual se ejecuta cada segmento. Para obtener más información, consulte la sección [Integración de Data Factory y Batch](#data-factory-and-batch-integration) .
7. Debería ver los archivos de salida en la carpeta `outputfolder` de `mycontainer` en su instancia de Azure Blob Storage.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Debería ver cinco archivos de salida, uno para cada segmento de entrada. Cada archivo de salida debe tener contenido similar a la siguiente salida:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   En el siguiente diagrama, se ilustra cómo los segmentos de Data Factory se asignan a tareas de Azure Batch. En este ejemplo, un segmento tiene solo una ejecución.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Ahora, probemos con varios archivos en una carpeta. Cree los archivos **file2.txt**, **file3.txt**, **file4.txt** y **file5.txt** con el mismo contenido que file.txt en la carpeta **2015-11-06-01**.
9. En la carpeta de salida, **elimine** el archivo de salida **2015-11-16-01.txt**.
10. Ahora, en la hoja **OutputDataset**, haga clic con el botón derecho en el segmento con el valor de **HORA DE INICIO DE SEGMENTO** establecido en **11/16/2015 01:00:00 a.m.** y haga clic en **Ejecutar** para volver a ejecutar o procesar el segmento. Ahora, el segmento tiene cinco archivos en lugar de uno.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Una vez que se ejecuta el segmento y su estado es **Listo**, compruebe el contenido del archivo de salida para este segmento (**2015-11-16-01.txt**) en la carpeta `outputfolder` de `mycontainer` en Blob Storage. Debería aparecer una línea para cada archivo del segmento.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Si no ha eliminado el archivo de salida 2015-11-16-01.txt antes de probar con cinco archivos de entrada, verá una línea de la anterior ejecución del segmento y cinco líneas de la actual. De forma predeterminada, se anexa el contenido al archivo de salida si ya existe.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integración de Data Factory y Lote
El servicio Data Factory crea un trabajo en Azure Batch llamado: `adf-poolname:job-xxx`.

![Data Factory de Azure: trabajos por lotes](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Se crea una tarea en el trabajo para cada ejecución de actividad de un segmento. Si hay 10 segmentos listos para ser procesados, se crean 10 tareas en este trabajo. Si el grupo tiene varios nodos de procesos, puede haber más de un segmento en ejecución en paralelo. También puede haber más de un segmento en ejecución en el mismo proceso si el número máximo de tareas se establece un valor mayor que 1.

En este ejemplo, hay 5 segmentos y, por tanto, 5 tareas en Azure Batch. Ahora que el valor **concurrency** está establecido en **5** en el JSON de la canalización de Azure Data Factory y que **Maximum tasks per VM** (Máximo de tareas por máquina virtual) está establecido en **2** en el grupo de Azure Batch con **2** máquinas virtuales, las tareas se ejecutarán muy rápido (compruebe los tiempos de inicio y finalización de las tareas).

Use el portal para ver el trabajo de Lote y las tareas que están asociadas con los **segmentos** y para comprobar en qué máquina virtual se ejecuta cada segmento.

![Azure Data Factory: tareas de trabajos de Batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depuración de la canalización
La depuración se compone de varias técnicas básicas:

1. Si el segmento de entrada no está establecido en **Listo**, confirme que la estructura de la carpeta de entrada es correcta y que file.txt se encuentra en las carpetas de entrada.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. En el método **Execute** de la actividad personalizada, use el objeto **IActivityLogger** para registrar información que ayude a solucionar problemas. Los mensajes registrados se mostrarán en el archivo user\_0.log.

   En la hoja **OutputDataset**, haga clic en el segmento para ver la hoja **SEGMENTO DE DATOS** de dicho segmento. Verá **ejecuciones de actividad** para ese segmento. Debería ver una ejecución de actividad del segmento. Si hace clic en **Ejecutar** en la barra de comandos, podrá iniciar otra ejecución de actividad para el mismo segmento.

   Al hacer clic en la ejecución de actividad, verá la hoja **DETALLES DE LA EJECUCIÓN DE ACTIVIDAD** con una lista de archivos de registro. Los mensajes registrados se encuentran en el archivo **user\_0.log**. Si se produce un error, verá tres ejecuciones de actividad, ya que el número de reintentos está establecido en 3 en la canalización o actividad JSON. Al hacer clic en la ejecución de actividad, verá los archivos de registro que puede revisar para solucionar el error.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   En la lista de archivos de registro, haga clic en **user-0.log**. En el panel derecho, se encuentran los resultados del uso del método **IActivityLogger.Write** .

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Compruebe system-0.log para ver si hay alguna excepción o mensaje de error del sistema.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Incluya el archivo **PDB** en el archivo ZIP para que los detalles sobre un error contengan información como la **pila de llamadas** cuando se produce un error.
4. Todos los archivos incluidos en el archivo ZIP de la actividad personalizada deben estar en el **nivel superior** ; no debe haber subcarpetas.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Asegúrese de que en **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) y **packageLinkedService** (debe apuntar a Azure Blob Storage que contiene el archivo ZIP) se han seleccionado los valores correctos.
6. Si corrigió algún error y desea volver a procesar el segmento, haga clic con el botón derecho en el segmento, en la hoja **OutputDataset**, y haga clic en **Run**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Verá un **contenedor** en la instancia de Azure Blob Storage denominado: `adfjobs`. Este contenedor no se elimina automáticamente, pero puede eliminarlo de forma segura cuando termine de probar la solución. De la misma forma, la solución de Data Factory crea un **trabajo** de Azure Batch llamado: `adf-\<pool ID/name\>:job-0000000001`. Puede eliminarlo después de probar la solución, si lo desea.
   >
   >
7. La actividad personalizada no utiliza el archivo **app.config** desde el paquete. Por lo tanto, si el código lee cualquier cadena de conexión del archivo de configuración, no funcionará en tiempo de ejecución. El procedimiento recomendado al usar Azure Batch consiste en conservar los secretos en una instancia de **Azure Key Vault**, usar una entidad de servicio basada en certificados para proteger el almacén de claves y distribuir el certificado al grupo de Azure Batch. Tras ello, la actividad personalizada de .NET podrá acceder a los secretos desde el almacén de claves en tiempo de ejecución. Esta es una solución genérica y se puede extrapolar a cualquier tipo de secreto, no solo a cadenas de conexión.

    Existe una solución más sencilla, pero no es un procedimiento recomendado: puede crear un **servicio vinculado de SQL Azure** con configuración de cadena de conexión, crear un conjunto de datos que utilice el servicio vinculado y vincular el conjunto de datos (configurado con carácter de entrada ficticio) con la actividad de .NET personalizada. Tras ello, podrá acceder a la cadena de conexión del servicio vinculado del código de la actividad personalizada, que no debería tener problemas para funcionar bien en tiempo de ejecución.  

#### <a name="extend-the-sample"></a>Extensión del ejemplo
Puede extender este ejemplo para obtener más información acerca de las características Azure Data Factory y Azure Batch. Por ejemplo, para procesar los segmentos de un intervalo de tiempo diferente, realice los siguientes pasos:

1. Agregue las siguientes subcarpetas en `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09 y coloque archivos de entrada en ellas. Cambie la hora de finalización de la canalización de `2015-11-16T05:00:00Z` a `2015-11-16T10:00:00Z`. En el **vista de diagrama**, haga doble clic en **InputDataset** y confirme que los segmentos de entrada están listos. Haga doble clic en **OuptutDataset** para ver el estado de los segmentos de salida. Si se encuentran en el estado Listo, compruebe si los archivos de salida se encuentran en la carpeta de salida.
2. Aumente o disminuya la configuración de **concurrency** para saber cómo afecta al rendimiento de la solución, especialmente el procesamiento que se produce en Azure Batch. (Consulte Paso 4: Creación y ejecución de la canalización con una actividad personalizada para más información sobre la configuración **concurrency** ).
3. Cree un grupo con un valor mayor o menor en **Máximo de tareas por máquina virtual**. Actualice el servicio vinculado Azure Batch en la solución de Data Factory para que use el nuevo grupo que creó. (Consulte Paso 4: Creación y ejecución de la canalización con una actividad personalizada para más información sobre la configuración **Máximo de tareas por máquina virtual** ).
4. Cree un grupo de Azure Batch con la característica de **escalado automático** . El escalado automático de los nodos de ejecución de un grupo de Azure Batch es el ajuste dinámico de la potencia de procesamiento que usa su aplicación. 

    La fórmula del ejemplo logra el siguiente comportamiento: cuando el grupo se crea inicialmente, empieza con 1 máquina virtual. La métrica $PendingTasks define el número de tareas que están en ejecución o activas (en cola).  La fórmula busca el número promedio de tareas pendientes en los últimos 180 segundos y establece TargetDedicated en consecuencia. Garantiza que TargetDedicated nunca supera las 25 VM. Por tanto, a medida que se envían nuevas tareas, el grupo crece automáticamente y a medida que estás se completan, las VM se liberan una a una y el escalado automático las reduce. startingNumberOfVMs y maxNumberofVMs se pueden adaptar a sus necesidades.
 
    Fórmula de escalado automático:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para más información, consulte [Escalado automático de los nodos de proceso en un grupo de Lote de Azure](../../batch/batch-automatic-scaling.md) .

   Si el grupo usa el valor predeterminado de la propiedad [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), el servicio Lote puede tardar de 15 a 30 minutos en preparar la máquina virtual antes de ejecutar la actividad personalizada.  Si el grupo usa otro valor de autoScaleEvaluationInterval diferente, el servicio Lote podría tardar el valor de autoScaleEvaluationInterval más 10 minutos.
5. En la solución de ejemplo, el método **Execute** invoca al método **Calculate** que procesa un segmento de datos de entrada para generar un segmento de datos de salida. Puede escribir su propio método para procesar los datos de entrada y reemplazar la llamada al método Calculate en el método Execute por una llamada a su método.

### <a name="next-steps-consume-the-data"></a>Pasos siguientes: Consumo de los datos
Después de procesar datos, puede consumirlos con herramientas en línea como **Microsoft Power BI**. Estos vínculos lo ayudarán a comprender Power BI y aprender a usarlo en Azure:

* [Exploración de un conjunto de datos en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Actualizar datos en Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure y Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencias
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md)
  * [Introducción a Azure Data Factory](data-factory-build-your-first-pipeline.md)
  * [Uso de actividades personalizadas en una canalización de Factoría de datos de Azure](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Datos básicos de Azure Batch](../../batch/batch-technical-overview.md)
  * [Información general de las características de Azure Batch](../../batch/batch-api-basics.md)
  * [Creación y administración de una cuenta de Azure Batch en Azure Portal](../../batch/batch-account-create-portal.md)
  * [Introducción a la biblioteca de Azure Batch para .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
