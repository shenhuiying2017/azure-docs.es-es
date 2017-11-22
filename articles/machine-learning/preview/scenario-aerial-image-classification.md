---
title: "Clasificación de imágenes aéreas | Microsoft Docs"
description: "Proporciona instrucciones para un escenario real de clasificación de imágenes aéreas"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 10/27/2017
ms.openlocfilehash: 07e74c64e587cce99612cd5047516bf131943f2e
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="aerial-image-classification"></a>Clasificación de imágenes aéreas

Este ejemplo muestra cómo utilizar Azure Machine Learning Workbench para la coordinación de entrenamiento distribuido y la operacionalización de modelos de clasificación de imagen. Para el entrenamiento, usamos dos planteamientos: i) ajustar una red neuronal profunda mediante el clúster de GPU de [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/), y ii) usar el paquete [Microsoft Machine Learning para Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) para caracterizar imágenes con modelos CNTK previamente entrenados y para entrenar clasificadores utilizando las características derivadas. A continuación, aplicamos los modelos entrenados de forma paralela a conjuntos de imágenes de gran volumen mediante un clúster de [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/), lo que nos permite escalar la velocidad de entrenamiento y de puesta en marcha mediante la adición o la supresión de nodos de trabajo.

En este ejemplo se resaltan los dos enfoques para transferir el aprendizaje, donde se aprovechan los modelos previamente entrenados para evitar los costos que supondría entrenar redes neurales profundas desde el principio. El reentrenamiento de una red neuronal profunda normalmente requiere proceso de GPU, pero puede conllevar una mayor precisión cuando el conjunto de entrenamiento es lo suficientemente grande. El entrenamiento de un clasificador simple sobre imágenes caracterizadas no requiere proceso de GPU, es inherentemente rápido y escalable de forma arbitraria y ajusta menos parámetros. Por lo tanto, este método es una excelente opción cuando hay pocos ejemplos de entrenamiento disponibles, como suele ser el caso en casos de uso personalizados. 

El clúster de Spark utilizado en este ejemplo tiene 40 nodos de trabajo y la hora de funcionamiento cuesta aproximadamente 40 $; los recursos del clúster Batch AI incluyen 8 GPU y la hora de funcionamiento cuesta aproximadamente 10 $. La realización de este tutorial requiere aproximadamente tres horas. Cuando haya terminado, siga las instrucciones de limpieza para eliminar los recursos que ha creado y no incurrir en más cargos.

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

El repositorio de GitHub público para este escenario real contiene todos los materiales, incluidos ejemplos de código, necesarios para este ejemplo:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Descripción del caso de uso

En este escenario, se entrenan modelos de aprendizaje automático para clasificar el tipo de terreno que se muestra en imágenes aéreas de trazados de 224 metros x 224 metros. Los modelos de clasificación de uso del terreno pueden usarse para realizar el seguimiento de la urbanización, deforestación, pérdida de humedales y otras tendencias medioambientales principales mediante imágenes aéreas recopiladas periódicamente. Hemos preparado conjuntos de imágenes de entrenamiento y validación basados en las imágenes del programa nacional de imágenes agrícolas de EE. UU. y las etiquetas de uso de terrenos publicadas por la base de datos nacional de uso de terrenos de EE. UU. A continuación se muestran imágenes de ejemplo de cada clase de uso del terreno:

![Regiones de ejemplo para cada etiqueta de uso del terreno](media/scenario-aerial-image-classification/example-labels.PNG)

Después de entrenar y validar el modelo de clasificación, se aplicará a imágenes aéreas del condado de Middlesex, MA (Sede del Centro de investigación y desarrollo de Microsoft en Nueva Inglaterra [NERD]) para demostrar cómo se pueden usar estos modelos para estudiar las tendencias en desarrollo urbano.

Para generar un clasificador de imágenes mediante la transferencia de aprendizaje, los científicos de datos a menudo construyen varios modelos con una variedad de métodos y seleccionan el modelo de mayor rendimiento. Azure Machine Learning Workbench puede ayudar a los científicos de datos en la coordinación del entrenamiento en entornos de proceso diferentes, realizar un seguimiento y comparan el rendimiento de los distintos modelos y aplicar el modelo elegido a grandes conjuntos de datos en la nube.

## <a name="scenario-structure"></a>Estructura del escenario

En este ejemplo, los datos de imágenes y los modelos previamente entrenados se almacenan en una cuenta de Azure Storage. Un clúster de Spark de Azure HDInsight lee estos archivos y crea un modelo de clasificación de imágenes mediante MMLSpark. El modelo entrenado y las predicciones, a continuación, se escriben en la cuenta de almacenamiento, donde se pueden analizar y visualizar con Jupyter Notebook en ejecución local. Azure Machine Learning Workbench coordina la ejecución remota de scripts en el clúster de Spark. También realiza un seguimiento de las métricas de precisión para varios modelos entrenados usando distintos métodos, lo que permite al usuario seleccionar el modelo de mayor rendimiento.

![Esquema del escenario real de clasificación de imágenes aéreas](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Las [instrucciones paso a paso](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) comienzan por la guía en la creación y preparación de una cuenta de Azure storage y un clúster de Spark, incluida la transferencia de datos y la instalación de dependencias. A continuación, describe cómo iniciar trabajos de entrenamiento y comparar el rendimiento de los modelos resultantes. Por último, describe cómo aplicar el modelo elegido a un gran conjunto de imágenes en el clúster de Spark y analizar los resultados de la predicción localmente.


## <a name="set-up-the-execution-environment"></a>Configuración del entorno de ejecución

Las instrucciones siguientes le guían a través del proceso de configuración del entorno de ejecución de este ejemplo.

### <a name="prerequisites"></a>Requisitos previos
- Una [cuenta de Azure](https://azure.microsoft.com/en-us/free/) (hay disponibles versiones gratuitas de prueba)
    - Creará un clúster de Spark de HDInsight con 40 nodos de trabajo (168 núcleos en total). Asegúrese de que su cuenta tiene suficientes núcleos disponibles revisando la pestaña "Uso y cuotas" de la suscripción en Azure Portal.
       - Si tiene menos núcleos disponibles, puede modificar la plantilla del clúster de HDInsight para reducir el número de nodos de trabajo aprovisionados. Se proporcionan instrucciones para esto en la sección "Creación del clúster de Spark de HDInsight ".
    - En este ejemplo se crea un clúster de entrenamiento de Batch AI con dos VM NC6 (1 GPU, 6 vCPU). Asegúrese de que su cuenta tiene suficientes núcleos disponibles en la región Este de EE. UU. revisando la pestaña "Uso y cuotas" de la suscripción en Azure Portal.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Siga la [Guía rápida de instalación y creación](quickstart-installation.md) para instalar Azure Machine Learning Workbench y crear cuentas de Experimentación y de administración del modelo.
- SDK de Python de [Batch AI](https://github.com/Azure/BatchAI) y la CLI de Azure 2.0
    - Instale el SDK de Batch AI y la CLI de Azure 2.0 siguiendo las instrucciones de la [sección de requisitos previos para las recetas](https://github.com/Azure/BatchAI/tree/master/recipes).
        - En el momento de redactar este documento, Azure Machine Learning Workbench usa una bifurcación independiente de la CLI de Azure 2.0. Para mayor claridad, nos referimos a la versión de Workbench de la CLI como "la CLI publicada a partir de Azure Machine Learning Workbench" y la versión de disponibilidad general (que incluye Batch AI) como "la CLI de Azure 2.0".
    - Cree una aplicación de Azure Active Directory y una entidad de servicio siguiendo [estas instrucciones](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Registre el identificador de cliente, el secreto y el identificador de inquilino.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), una utilidad gratuita para coordinar la transferencia de archivos entre cuentas de Azure Storage
    - Compruebe que la carpeta que contiene el ejecutable AzCopy está en la variable de entorno PATH del sistema. (Las instrucciones sobre cómo modificar variables de entorno están disponibles [aquí](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)).
- Un cliente de SSH; se recomienda [PuTTY](http://www.putty.org/).

Este ejemplo se ha probado en un equipo con Windows 10; debería ser capaz de ejecutarlo desde cualquier equipo Windows, incluidas las instancias de Azure Data Science Virtual Machine. La CLI de Azure 2.0 se instaló desde un archivo MSI según [estas instrucciones](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Pueden ser necesarias modificaciones menores (por ejemplo, cambios en rutas de acceso) cuando se ejecuta este ejemplo en macOS.

### <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure

Este ejemplo necesita un clúster de Spark de HDInsight y una cuenta de Azure Storage para alojar los archivos pertinentes. Siga estas instrucciones para crear estos recursos en un nuevo grupo de recursos de Azure:

#### <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Aerial Image Classification" (Clasificación de imágenes aéreas) y seleccione la plantilla
5.  Haga clic en **Crear**
 
#### <a name="create-the-resource-group"></a>Creación del grupo de recursos

1. Después de cargar el proyecto en Azure Machine Learning Workbench, para abrir una interfaz de línea de comandos (CLI), haga clic en Archivo -> Abrir símbolo del sistema.
    Utilice esta versión de la CLI para la mayor parte del tutorial. (Donde se indique, deberá abrir otra versión de la CLI para preparar los recursos de Batch AI).

1. Desde la interfaz de línea de comandos, inicie sesión en la cuenta de Azure ejecutando el comando siguiente:

    ```
    az login
    ```

    Se le pide que visite una dirección URL y que escriba un código temporal proporcionado; el sitio web solicita las credenciales de su cuenta de Azure.
    
1. Una vez completado el inicio de sesión, vuelva a la CLI y ejecute el comando siguiente para determinar qué suscripciones de Azure están disponibles para su cuenta de Azure:

    ```
    az account list
    ```

    Este comando muestra todas las suscripciones asociadas a su cuenta de Azure. Busque el identificador de la suscripción que quiere usar. Escriba el identificador de la suscripción donde se indica en el siguiente comando y luego establezca la suscripción activa mediante la ejecución del comando:

    ```
    az account set --subscription [subscription ID]
    ```

1. Los recursos de Azure creados en este ejemplo se almacenan juntos en un grupo de recursos de Azure. Elija un nombre de grupo de recursos único y escríbalo donde se indica. A continuación, ejecute ambos comandos para crear el grupo de recursos de Azure:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Creación de la cuenta de almacenamiento

Ahora se creará la cuenta de almacenamiento que hospeda los archivos de proyecto a los que se debe acceder mediante HDInsight Spark.

1. Elija un nombre de cuenta de almacenamiento único y escríbalo donde se indica en el siguiente comando `set`. A continuación, cree una cuenta de almacenamiento de Azure mediante la ejecución de ambos comandos:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Disponga una lista de las claves de las cuentas de almacenamiento ejecutando el comando siguiente:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Registre el valor de `key1` como clave de almacenamiento en el siguiente comando y luego ejecute el comando para almacenar el valor.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Cree un recurso compartido de archivos denominado `baitshare` en su cuenta de almacenamiento con el siguiente comando:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. En su editor de texto favorito, cargue el archivo `settings.cfg` del subdirectorio "Code" del proyecto de Azure Machine Learning Workbench e inserte el nombre y la clave de la cuenta de almacenamiento como se indica. Guarde y cierre el archivo `settings.cfg`.
1. Si no lo ha hecho ya, descargue e instale la utilidad [AzCopy](http://aka.ms/downloadazcopy). Compruebe que el ejecutable de AzCopy se encuentra en la ruta de acceso del sistema; para ello, escriba "AzCopy" y presione Entrar para mostrar su documentación.
1. Emita los siguientes comandos para copiar todos los datos de ejemplo, modelos ya entrenados y scripts de entrenamiento de modelos en las ubicaciones adecuadas de la cuenta de almacenamiento:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    La transferencia de archivos puede tardar hasta 20 minutos. Mientras espera, puede continuar con la siguiente sección. Puede que deba abrir otra interfaz de la línea de comandos mediante Workbench y volver a definir ahí las variables temporales.

#### <a name="create-the-hdinsight-spark-cluster"></a>Creación del clúster de HDInsight Spark

El método recomendado para crear un clúster de HDInsight es usar la plantilla de Resource Manager del clúster de HDInsight Spark incluida en la subcarpeta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" de este proyecto.

1. La plantilla del clúster de HDInsight Spark es el archivo "template.json" de la subcarpeta "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" de este proyecto. De forma predeterminada, la plantilla crea un clúster de Spark con 40 nodos de trabajo. Si debe ajustar ese número, abra la plantilla en el editor de texto que prefiera y reemplace todas las instancias de "40" por el número de nodos de trabajo de su elección.
    - Puede encontrar errores de memoria insuficiente si el número de nodos de trabajo que elija es pequeño. Para hacer frente a los errores de memoria, puede ejecutar los scripts de entrenamiento y operacionalización en un subconjunto de los datos disponibles como se describe más adelante en este documento.
2. Elija un nombre único y una contraseña para el clúster de HDInsight y escríbalos donde se ha indicado en el siguiente comando. Luego cree el clúster emitiendo los comandos:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

La implementación del clúster puede tardar hasta 30 minutos (incluidos el aprovisionamiento y la ejecución de acciones de script).

### <a name="set-up-batch-ai-resources"></a>Configuración de los recursos de Batch AI

Mientras espera que se complete la transferencia de archivos de la cuenta de Storage y la implementación del clúster de Spark, puede preparar el servidor Network File Server (NFS) y el clúster de GPU de Batch AI. Abra un símbolo del sistema de la CLI de Azure 2.0 y ejecute el comando siguiente:

```
az --version 
```

Confirme que `batchai` aparece entre los módulos instalados. Si no es así, puede usar una interfaz de línea de comandos diferente (por ejemplo, una abierta a través de Workbench).

A continuación, compruebe que se ha completado correctamente el registro de proveedor. (El registro del proveedor tarda hasta quince minutos y puede que aún esté en curso si ha completado recientemente las [instrucciones de configuración de Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)). Confirme que aparecen "Microsoft.Batch" y "Microsoft.BatchAI" con estado "Registered" en la salida del comando siguiente:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

En caso contrario, ejecute los siguientes comandos de registro de proveedores y espere unos 15 minutos a que el registro se complete.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Modifique los comandos siguientes para reemplazar las expresiones entre corchetes por los valores utilizados durante la creación de las cuentas de almacenamiento y el grupo de recursos. A continuación, almacene los valores como variables ejecutando los comandos:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identifique la carpeta que contiene el proyecto de Azure Machine Learning (por ejemplo, `C:\Users\<your username>\AzureML\aerialimageclassification`). Reemplace el valor entre corchetes por la ruta del archivo de la carpeta (sin barras diagonales inversas) y ejecute el comando:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Ahora está listo para crear los recursos de Batch AI necesarios para este tutorial.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Preparación del servidor Network File Server (NFS) de Batch AI

El clúster de Batch AI tiene acceso a los datos de entrenamiento en un servidor Network File Server. El acceso a los datos puede ser varios plegados más rápido cuando se accede a los archivos desde un NFS que cuando lo hace desde un recurso compartido de archivos de Azure o Azure Blob Storage.

1. Emita el comando siguiente para crear un servidor Network File Server:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_D2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Compruebe el estado de aprovisionamiento del servidor Network File Server con el comando siguiente:

    ```
    az batchai file-server list
    ```

    Cuando el valor de "provisioningState" en el servidor Network File Server denominado "landuseclassifier" sea "succeeded", estará listo para su uso. Lo normal es que el aprovisionamiento tarde unos cinco minutos.
1. Busque la dirección IP del servidor NFS en la salida del comando anterior (la propiedad "fileServerPublicIp" en "mountSettings"). Escriba la dirección IP donde se indique en el siguiente comando, y luego almacene el valor ejecutando el comando:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. Mediante la herramienta SSH que prefiera (en el comando de ejemplo siguiente se utiliza [PuTTY](http://www.putty.org/)), ejecute el script `prep_nfs.sh` del proyecto en el servidor NFS para transferir los conjuntos de imagen de validación y entrenamiento a esa ubicación.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    No se preocupe si las actualizaciones del progreso de extracción y la descarga de datos se desplazan a través de la ventana del shell tan rápidamente que no son legibles.

Si lo desea, puede confirmar que se ha realizado la transferencia de datos según lo previsto iniciando sesión en el servidor de archivos con la herramienta SSH que prefiera y comprobando los contenidos del directorio `/mnt/data`. Debería encontrar dos carpetas, training_images y validation_images, cada una con subcarpetas denominadas según las categorías de uso del terreno.  Los conjuntos de entrenamiento y validación deben contener unas 44 000 y 11 000 imágenes, respectivamente.

#### <a name="create-a-batch-ai-cluster"></a>Creación de un clúster de Batch AI

1. Emita los siguientes comandos para crear el clúster:

    ```
    set AZURE_BATCHAI_STORAGE_ACCOUNT=%STORAGE_ACCOUNT_NAME%
    set AZURE_BATCHAI_STORAGE_KEY=%STORAGE_ACCOUNT_KEY%
    az batchai cluster create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 
    ```

1. Use el siguiente comando para comprobar el estado de aprovisionamiento del clúster:

    ```
    az batchai cluster list
    ```

    Cuando el estado de asignación para el clúster denominado "landuseclassifier" cambie de "resizing" a "steady", será posible enviar trabajos. Sin embargo, los trabajos no empiezan a ejecutarse hasta que todas las VM del clúster han salido del estado "preparing". Si la propiedad "errors" del clúster no es null, se produjo un error durante la creación del clúster y no se debe usar.

#### <a name="record-batch-ai-training-credentials"></a>Registro de las credenciales de entrenamiento de Batch AI

Mientras espera que se complete la asignación de clústeres, abra el archivo `settings.cfg` del subdirectorio "Code" de este proyecto en el editor de texto que prefiera. Actualice las siguientes variables con sus propias credenciales:
- `bait_subscription_id` (el identificador de su suscripción a Azure de 36 caracteres)
- `bait_aad_client_id` (el identificador de aplicación/cliente de Azure Active Directory mencionado en la sección "Requisitos previos")
- `bait_aad_secret` (el secreto de aplicación de Azure Active Directory mencionado en la sección "Requisitos previos")
- `bait_aad_tenant` (el identificador de inquilino de Azure Active Directory mencionado en la sección "Requisitos previos")
- `bait_region` (cuando se redactó este documento, la única opción es: eastus)
- `bait_resource_group_name` (el grupo de recursos elegido anteriormente)

Una vez que haya asignado estos valores, las líneas modificadas del archivo settings.cfg deben parecerse al siguiente texto:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Guarde y cierre `settings.cfg`.

Ahora puede cerrar la ventana de la CLI en la que ejecutó los comandos de creación de recursos de Batch AI. Todos los demás pasos de este tutorial utilizan una CLI iniciada desde Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Preparación del entorno de ejecución de Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Registro del clúster de HDInsight como un destino de proceso de Azure Machine Learning Workbench

Una vez completada la creación del clúster de HDInsight, registre el clúster como destino de proceso para el proyecto como se indica a continuación:

1.  Emita el siguiente comando desde la interfaz de la línea del comandos de Azure Machine Learning:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Este comando agrega dos archivos, `myhdi.runconfig` y `myhdi.compute`, a la carpeta `aml_config` de su proyecto.

1. Abra el archivo `myhdi.compute` en el editor de texto que prefiera. Modifique la línea `yarnDeployMode: cluster` para que ponga `yarnDeployMode: client` y luego guarde y cierre el archivo.
1. Ejecute el siguiente comando para preparar el entorno de HDInsight para su uso:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Instalación de las dependencias locales

Abra una CLI desde Azure Machine Learning Workbench e instale las dependencias necesarias para la ejecución local con el siguiente comando:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Adquisición y comprensión de los datos

En este escenario se usan datos de imágenes aéreas disponibles públicamente en el [programa nacional de imágenes agrícolas](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) con una resolución de 1 metro. Se han generado conjuntos de archivos PNG de 224 x 224 píxeles recortados de los datos NAIP originales y ordenados de acuerdo con las etiquetas de uso del terreno de la [base de datos nacional de uso de terrenos](https://www.mrlc.gov/nlcd2011.php). Una imagen de ejemplo con la etiqueta "Developed" se muestra en tamaño completo:

![Un icono de ejemplo del terreno desarrollado](media/scenario-aerial-image-classification/sample-tile-developed.png)

Para el entrenamiento y la validación de los modelos, se han empleado conjuntos de imágenes de ~44 k y 11 k, respectivamente, con equilibrio de clase. La implementación del modelo se demuestra en un conjunto de imágenes de ~67 k en mosaico del condado de Middlesex, MA, sede del Centro de investigación y desarrollo de Microsoft en Nueva Inglaterra (NERD). Para más información sobre cómo se construyeron estos conjuntos de imágenes, consulte el [repositorio de Git de clasificación de imágenes embarazosamente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Ubicación del condado de Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durante la instalación, los conjuntos de imágenes aéreas usados en este ejemplo se han transferido a la cuenta de almacenamiento que ha creado. Las imágenes de entrenamiento, validación y operacionalización son todas archivos PNG de 224 x 224 píxeles con una resolución de un píxel por metro cuadrado. Las imágenes de entrenamiento y validación se han organizado en subcarpetas en función de su etiqueta de uso del terreno. (Las etiquetas de uso del terreno de las imágenes de operacionalización son desconocidas y, en muchos casos, ambiguas; algunas de estas imágenes contienen varios tipos de terrenos). Para más información sobre cómo se construyeron estos conjuntos de imágenes, consulte el [repositorio de Git de clasificación de imágenes embarazosamente paralelas](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Para ver imágenes de ejemplo en la cuenta de almacenamiento de Azure (opcional), siga estos pasos:
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Busque el nombre de la cuenta de almacenamiento en la barra de búsqueda de la parte superior de la pantalla. Haga clic en la cuenta de almacenamiento en los resultados de búsqueda.
2. Haga clic en el vínculo "Blob" en el panel principal de la cuenta de almacenamiento.
3. Haga clic en el contenedor llamado "train". Verá una lista de directorios designados según el uso del terreno.
4. Haga clic en cualquiera de estos directorios para cargar la lista de imágenes que contiene.
5. Haga clic en cualquier imagen y descárguela para verla.
6. Si lo desea, haga clic también en los contenedores llamados "test" y "middlesexma2016" para ver su contenido.

## <a name="modeling"></a>Modelado

### <a name="training-models-with-azure-batch-ai"></a>Entrenamiento de modelos con Azure Batch AI

El script `run_batch_ai.py` de la subcarpeta "Code\02_Modeling" del proyecto de Workbench se usa para emitir un trabajo de entrenamiento de Batch AI. Este trabajo entrena de nuevo un DNN de clasificador de imágenes elegidas por el usuario (AlexNet o ResNet 18 previamente entrenado en ImageNet). También se puede especificar la profundidad del reentrenamiento: el hecho de reentrenar solo la capa final de la red puede reducir el sobreajuste cuando hay pocos ejemplos de entrenamiento disponibles, mientras que ajustar toda la red (o, para AlexNet, las capas totalmente conectadas) puede conllevar un mejor rendimiento del modelo cuando el conjunto de entrenamiento es lo suficientemente grande.

Cuando se completa el trabajo de entrenamiento, este script guarda el modelo (junto con un archivo que describe la asignación entre la salida de enteros y las etiquetas de cadena del modelo) y las predicciones en el almacenamiento de blobs. El archivo de registro del trabajo BAIT se analiza para extraer el curso temporal de mejora de la tasa de error a lo largo de las épocas de entrenamiento. El curso temporal de la mejora de tasa de error se registra en la característica de historial de ejecución de AML Workbench para su posterior consulta.

Seleccione un nombre para el modelo entrenado, un tipo de modelo previamente entrenado y una profundidad de reentrenamiento. Escriba las selecciones donde se indica en el siguiente comando y comience de nuevo el entrenamiento ejecutando el comando desde una interfaz de la línea de comandos de Azure ML:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Se espera que la ejecución de Azure Machine Learning tarde aproximadamente media hora en completarse. Se recomienda ejecutar algunos comandos similares (variando el nombre del modelo de salida, el tipo de modelo previamente entrenado y la profundidad del reentrenamiento) para poder comparar el rendimiento de modelos entrenados con distintos métodos.

### <a name="training-models-with-mmlspark"></a>Entrenamiento de modelos con MMLSpark

El script `run_mmlspark.py` de la subcarpeta "Code\02_Modeling" del proyecto de Workbench se usa para entrenar un modelo [MMLSpark](https://github.com/Azure/mmlspark) para la clasificación de imágenes. El primer script caracteriza las imágenes del conjunto de entrenamiento mediante un DNN de clasificador de imágenes entrenado previamente en el conjunto de imágenes de ImageNet (AlexNet o ResNet de 18 capas). El script usa luego las imágenes caracterizadas para entrenar un modelo MMLSpark (un bosque aleatorio o un modelo de regresión logística) para clasificar las imágenes. A continuación, el conjunto de imágenes de prueba se caracteriza y se puntúa con el modelo entrenado. La exactitud de las predicciones del modelo en el conjunto de pruebas se calcula y se registra en la característica de historial de ejecución de Azure Machine Learning Workbench. Por último, el modelo MMLSpark entrenado y sus predicciones sobre el conjunto de pruebas se guardan en Blob Storage.

Seleccione un nombre de modelo de salida único para el modelo entrenado, un tipo de modelo previamente entrenado y un tipo de modelo MMLSpark. Escriba las selecciones donde se indica en la siguiente plantilla de comandos y comience de nuevo el entrenamiento ejecutando el comando desde una interfaz de la línea de comandos de Azure ML:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Se puede usar un parámetro `--sample_frac` adicional para entrenar y probar el modelo con un subconjunto de datos disponibles. Con una pequeña fracción de muestra se reducen el tiempo de ejecución y los requisitos de memoria, pero disminuye la exactitud del modelo entrenado. (Por ejemplo, una ejecución con `--sample_frac 0.1` se espera que tarde aproximadamente veinte minutos). Para más información sobre este y otros parámetros, ejecute `python Code\02_Modeling\run_mmlspark.py -h`.

Se aconseja a los usuarios que ejecuten este script varias veces con diferentes parámetros de entrada. El rendimiento de los modelos resultantes se puede comparar luego en la característica de historial de ejecución de Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparación del rendimiento de los modelos mediante la característica de historial de ejecución de Workbench

Después de que ha ejecutado dos o más series de entrenamiento de cualquier tipo, vaya a la característica Historial de ejecución de Workbench; para ello, haga clic en el icono de reloj en la barra de menú de la izquierda. Seleccione `run_mmlspark.py` en la lista de scripts de la izquierda. Se carga un panel donde se compara la exactitud del conjunto de pruebas en todas las ejecuciones. Para ver más detalles, desplácese hacia abajo y haga clic en el nombre de una ejecución individual.

## <a name="deployment"></a>Implementación

Para aplicar uno de los modelos entrenados a imágenes aéreas en mosaico del condado de Middlesex, MA mediante la ejecución remota en HDInsight, inserte el nombre del modelo deseado en el siguiente comando y ejecútelo:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Se puede usar un parámetro `--sample_frac` adicional para operacionalizar el modelo con un subconjunto de datos disponibles. Con una pequeña fracción de muestra se reducen el tiempo de ejecución y los requisitos de memoria, pero disminuye la exactitud de la predicción. Para más información sobre este y otros parámetros, ejecute `python Code\03_Deployment\batch_score_spark -h`.

Este script escribe las predicciones del modelo en su cuenta de almacenamiento. Las predicciones se pueden examinar como se describe en la sección siguiente.

## <a name="visualization"></a>Visualización

En el cuaderno de Jupyter "Model prediction analysis" de la subcarpeta "Code\04_Result_Analysis" del proyecto de Workbench se visualizan las predicciones de un modelo. Cargue y ejecute el cuaderno de la manera siguiente:
1. Abra el proyecto en Workbench y haga clic en el icono ("Files") de la carpeta junto al menú izquierdo para cargar el listado de directorios.
2. Vaya a la subcarpeta "Code\04_Result_Analysis" y haga clic en el cuaderno con el nombre "Model prediction analysis". Se muestra una representación en vista previa del cuaderno.
3. Haga clic en "Start Notebook Server" (Iniciar servidor de Notebook) para cargar el cuaderno.
4. En la primera celda, escriba donde se indica el nombre del modelo cuyos resultados desea analizar.
5. Haga clic en "Celda -> Ejecutar todo" para ejecutar todas las celdas del cuaderno.
6. Interprete los resultados conjuntamente con el cuaderno para aprender más sobre los análisis y las visualizaciones que presenta.

## <a name="cleanup"></a>Limpieza
Cuando se haya completado el ejemplo, se recomienda que elimine todos los recursos que ha creado mediante la ejecución del siguiente comando desde la interfaz de la línea de comandos de Azure:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referencias

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification) (El repositorio de clasificación de imágenes embarazosamente paralelas)
   - Describe la construcción de conjuntos de datos de imágenes y etiquetas disponibles de forma gratuita.
- Repositorio de GitHub de[MMLSpark](https://github.com/Azure/mmlspark)
   - Contiene ejemplos adicionales de entrenamiento y evaluación de modelos con MMLSpark

## <a name="conclusions"></a>Conclusiones

Azure Machine Learning Workbench ayuda a los científicos de datos a implementar fácilmente su código en destinos de proceso remotos. En este ejemplo, el código de entrenamiento de MMLSpark local se implementó para la ejecución remota en un clúster de HDInsight, y un script local inició un trabajo de aprendizaje en un clúster de GPU de Azure Batch AI. La característica de historial de ejecución de Azure Machine Learning Workbench ha permitido realizar el seguimiento del rendimiento de varios modelos y ha ayudado a identificar el modelo más exacto. La característica de cuadernos de Jupyter de Workbench ha ayudado a visualizar las predicciones de nuestros modelos en un entorno gráfico interactivo.

## <a name="next-steps"></a>Pasos siguientes
Para profundizar más en este ejemplo:
- En la característica de historial de ejecución de Machine Learning Workbench, haga clic en los símbolos de engranaje para seleccionar qué gráficos y métricas se van a mostrar.
- Examine los scripts de ejemplo de las instrucciones mediante la llamada a `run_logger`. Compruebe que comprende cómo se registra cada métrica.
- Examine los scripts de ejemplo de las instrucciones mediante la llamada a `blob_service`. Compruebe que comprende cómo se almacenan los modelos entrenados y las predicciones y cómo se recuperan de la nube.
- Explore el contenido de los contenedores creados en la cuenta de Blob Storage. Asegúrese de que entiende que un script o comando es responsable de crear cada grupo de archivos.
- Modifique el script de entrenamiento para entrenar un tipo de modelo MMLSpark diferente o para cambiar los hiperparámetros del modelo. Utilice la característica de historial de ejecución para determinar si los cambios aumentan o disminuyen la exactitud del modelo.
