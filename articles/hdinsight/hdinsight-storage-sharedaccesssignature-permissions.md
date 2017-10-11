---
title: "Restricción del acceso mediante firmas de acceso compartido - Azure HDInsight | Microsoft Docs"
description: "Obtener información acerca de cómo usar firmas de acceso compartido para restringir el acceso de HDInsight a datos almacenados en blobs de Almacenamiento de Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/11/2017
ms.author: larryfr
ms.openlocfilehash: 2e4b1a307fae06c0639d93b9804c6f0f703d5900
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Uso de firmas de acceso compartido de Azure Storage para restringir el acceso a datos en HDInsight

HDInsight tiene acceso total a los datos de las cuentas de Azure Storage asociadas con el clúster. Puede usar firmas de acceso compartido en el contenedor de blobs para restringir el acceso a los datos. Por ejemplo, para proporcionar acceso de solo lectura a los datos. Las firmas de acceso compartido (SAS) son una característica de las cuentas de Almacenamiento de Azure que permite limitar el acceso a los datos. Por ejemplo, al proporcionar acceso de solo lectura a los datos.

> [!IMPORTANT]
> Para una solución con Apache Ranger, considere la posibilidad de usar HDInsight unido a un dominio. Para más información, consulte el documento [Configuración de clústeres de HDInsight unidos a un dominio](hdinsight-domain-joined-configure.md).

> [!WARNING]
> HDInsight debe tener acceso total al almacenamiento predeterminado para el clúster.

## <a name="requirements"></a>Requisitos

* Una suscripción de Azure
* C# o Python. El código de ejemplo de C# se proporciona como una solución de Visual Studio.

  * Se debe usar la versión de Visual Studio 2013, 2015 o 2017
  * Se debe usar la versión de Python 2.7 o superior.

* Un clúster de HDInsight basado en Linux o [Azure PowerShell][powershell]: si ya tiene un clúster basado en Linux, puede usar Ambari para agregar una firma de acceso compartido al clúster. Si no es así, puede usar Azure PowerShell para crear un clúster y agregar una firma de acceso compartido durante la creación del clúster.

    > [!IMPORTANT]
    > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Los archivos de ejemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositorio contiene los siguientes elementos:

  * Un proyecto de Visual Studio que puede crear un contenedor de almacenamiento, una directiva almacenada y una SAS para su uso con HDInsight.
  * Un script de Python que puede crear un contenedor de almacenamiento, una directiva almacenada y una SAS para su uso con HDInsight.
  * Un script de PowerShell que puede crear un clúster de HDInsight y configurarlo para que use la SAS.

## <a name="shared-access-signatures"></a>Las firmas de acceso compartido

Hay dos formas de firmas de acceso compartido:

* Ad hoc: la hora de inicio, la hora de expiración y los permisos para la firma de acceso compartido se especifican en el URI de esta.

* Directiva de acceso almacenada: se define una directiva de acceso almacenada en un contenedor de recursos, como un contenedor de blobs. Una directiva puede usarse para administrar las restricciones de una o varias firmas de acceso compartido. Cuando asocia una SAS a una directiva de acceso almacenada, la SAS hereda las restricciones (hora de inicio, hora de expiración y permisos) definidas para la directiva de acceso almacenada.

La diferencia entre las dos formas es importante para un escenario principal: revocación. Una SAS es una dirección URL, por lo que cualquier persona que obtenga la SAS puede usarla, independientemente de quién la solicitó para comenzar. Si una SAS se encuentra disponible públicamente, cualquier persona del mundo puede usarla. Una SAS distribuida es válida hasta que se produzca una de las cuatro situaciones:

1. Se alcanza el tiempo de expiración especificado en la SAS.

2. Se alcanza la hora de expiración especificada en la directiva de acceso almacenada a la que hace referencia la SAS. Los escenarios siguientes hacen que se alcance la hora de expiración:

    * Ha transcurrido el intervalo de tiempo.
    * La directiva de acceso almacenada se ha modificado para que la hora de expiración haya pasado. Cambiar la hora de expiración es una manera de revocar la firma de acceso compartido.

3. Se elimina la directiva de acceso almacenada a la que hace referencia la SAS, que es otra forma de revocar la SAS. Si se vuelve a crear la directiva de acceso almacenada con el mismo nombre, todos los tokens de SAS de la directiva anterior son válidos (si la SAS no ha caducado). Si prevé revocar la SAS, asegúrese de usar un nombre distinto si vuelve a crear la directiva de acceso con una hora de expiración futura.

4. Se vuelve a generar la clave de cuenta que se usó para crear la SAS. Regenerar la clave hace que todas las aplicaciones que usan la clave anterior no se puedan autenticar. Actualice todos los componentes con la nueva clave.

> [!IMPORTANT]
> Los URI de firma de acceso compartido están asociados a la clave de la cuenta que se utiliza para crear la firma y a la directiva de acceso almacenada correspondiente (en su caso). Si no se especifica una directiva de acceso almacenada, la única forma de revocar una firma de acceso compartido es cambiar la clave de la cuenta.

Se recomienda usar siempre las directivas de acceso almacenadas. Al utilizar las directivas almacenadas, puede revocar las firmas o ampliar la fecha de expiración según sea necesario. Los pasos descritos en este documento utilizan directivas de acceso almacenadas para generar las SAS.

Para más información sobre firmas de acceso compartido, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

### <a name="create-a-stored-policy-and-sas-using-c"></a>Creación de una directiva almacenada y una SAS mediante C\#

1. Abra la solución en Visual Studio.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SASToke**n y seleccione **Propiedades**.

3. Seleccione **Configuración** y agregue valores para las siguientes entradas:

   * StorageConnectionString: la cadena de conexión de la cuenta de almacenamiento para la que desea crear una directiva almacenada y una SAS. El formato debe ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` donde `myaccount` es el nombre de la cuenta de almacenamiento y `mykey` es la clave para la cuenta de almacenamiento.

   * ContainerName: el contenedor de la cuenta de almacenamiento a la que desea restringir el acceso.

   * SASPolicyName: el nombre que se usará para la directiva almacenada que se va a crear.

   * FileToUpload: la ruta de acceso a un archivo que se carga en el contenedor.

4. Ejecute el proyecto. Una vez generada la firma de acceso compartido, se mostrará información similar al texto siguiente:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde el token de directiva de SAS, el nombre de la cuenta de almacenamiento y el nombre del contenedor. Estos valores se usan al asociar la cuenta de almacenamiento con el clúster de HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Creación de una directiva almacenada y una SAS mediante Python

1. Abra el archivo SASToken.py y cambie los valores siguientes:

   * policy\_name: el nombre que se usará para la directiva almacenada que se va a crear.

   * storage\_account\_name: el nombre de su cuenta de almacenamiento.

   * storage\_account\_key: la clave de su cuenta de almacenamiento.

   * storage\_container\_name: el contenedor de la cuenta de almacenamiento al que desea restringir el acceso.

   * example\_file\_path: la ruta de acceso a un archivo que se carga en el contenedor.

2. Ejecute el script. Cuando finalice el script, muestra un token de SAS similar al texto siguiente:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde el token de directiva de SAS, el nombre de la cuenta de almacenamiento y el nombre del contenedor. Estos valores se usan al asociar la cuenta de almacenamiento con el clúster de HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Uso de las SAS con HDInsight

Al crear un clúster de HDInsight, debe especificar una cuenta de almacenamiento principal y puede especificar, opcionalmente, cuentas de almacenamiento adicionales. Estos dos métodos para agregar almacenamiento requieren tener un acceso total a las cuentas de almacenamiento y los contenedores que se utilizan.

Para usar una firma de acceso compartido a fin de limitar el acceso a un contenedor, agregue una entrada personalizada a la configuración **core-site** para el clúster.

* Para clústeres de HDInsight **basados en Windows** o **basados en Linux**, puede agregar la entrada durante la creación del clúster mediante PowerShell.
* Para clústeres de HDInsight **basados en Linux**, cambie la configuración después de crear el clúster con Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Crear un clúster que usa la SAS

Se incluye un ejemplo de creación de un clúster de HDInsight que usa la SAS en el directorio `CreateCluster` del repositorio. Para ello, siga estos pasos:

1. Abra el archivo `CreateCluster\HDInsightSAS.ps1` en un editor de texto y modifique los valores siguientes al principio del documento.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Por ejemplo, cambie `'mycluster'` por el nombre del clúster que desea crear. Los valores de SAS deben coincidir con los valores de los pasos anteriores al crear una cuenta de almacenamiento y el token de SAS.

    Una vez que haya cambiado los valores, guarde el archivo.

2. Abra un nuevo símbolo del sistema de Azure PowerShell. Si no está familiarizado con Azure PowerShell, o si no lo ha instalado, consulte [Cómo instalar y configurar Azure PowerShell][powershell].

1. En el símbolo del sistema, use el siguiente comando para autenticarse en la suscripción de Azure:

    ```powershell
    Login-AzureRmAccount
    ```

    Cuando se le solicite, inicie sesión con la cuenta de la suscripción de Azure.

    Si la cuenta está asociada a varias suscripciones de Azure, puede que tenga que usar `Select-AzureRmSubscription` para seleccionar la suscripción que quiere usar.

4. En el símbolo del sistema, cambie los directorios al directorio `CreateCluster` que contiene el archivo HDInsightSAS.ps1. Después, use el siguiente comando para ejecutar el script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Mientras se ejecuta el script, registra la salida en el símbolo del sistema de PowerShell mientras crea las cuentas de grupo de recursos y de almacenamiento. Se le pedirá que escriba el usuario HTTP para el clúster de HDInsight. Esta cuenta se usa para proteger el acceso HTTP/s al clúster.

    Si está creando un clúster basado en Linux, se le solicitará un nombre de cuenta de usuario SSH y una contraseña. Esta cuenta se usa para el inicio de sesión remoto al clúster.

   > [!IMPORTANT]
   > Cuando se le pida el nombre de usuario SSH o HTTP/s y la contraseña, debe proporcionar una contraseña que cumpla los criterios siguientes:
   >
   > * Debe tener como mínimo 10 caracteres.
   > * Debe contener al menos un dígito.
   > * Debe incluir al menos un carácter no alfanumérico.
   > * Debe contener al menos una mayúscula o una minúscula.

Este script tarda un tiempo en completarse, normalmente unos 15 minutos. Una vez finalizado el script sin errores, se creará el clúster.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usar la SAS con un clúster existente

Si tiene un clúster existente basado en Linux, puede agregar las SAS para la configuración **core-site** mediante los pasos siguientes:

1. Abra la interfaz de usuario web Ambari del clúster. La dirección de esta página es https://NOMBREDESUCLÚSTER.azurehdinsight.net. Cuando se le solicite, autentíquese en el clúster con el nombre de administrador (admin) y la contraseña que usó al crear el clúster.

2. En el lado izquierdo de la interfaz de usuario web Ambari, seleccione **HDFS** y, a continuación, seleccione la pestaña **Configs** (Configuraciones) en el centro de la página.

3. Seleccione la pestaña **Advanced** (Avanzadas) y, a continuación, desplácese hasta encontrar la sección **Custom core-site** (Sitio principal personalizado).

4. Expanda la sección **Custom core-site** (Sitio principal personalizado), desplácese hasta el final y seleccione el vínculo **Add property...** (Agregar propiedad...). Utilice los siguientes valores para los campos **Key** (Clave) y **Value** (Valor):

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Value**: la SAS devuelta por la aplicación de C# o Python ejecutada anteriormente.

     Reemplace **CONTAINERNAME** por el nombre del contenedor utilizado con la aplicación de C# o de SAS. Reemplace **STORAGEACCOUNTNAME** por el nombre de la cuenta de almacenamiento utilizada.

5. Haga clic en el botón **Add** (Agregar) para guardar esta clave y este valor y, a continuación, haga clic en el botón **Save** (Guardar) para guardar los cambios de configuración. Cuando se le solicite, agregue una descripción del cambio ("Agregar acceso de almacenamiento de SAS", por ejemplo) y haga clic en **Save** (Guardar).

    Haga clic en **OK** (Aceptar) cuando se hayan completado los cambios.

   > [!IMPORTANT]
   > Debe reiniciar varios servicios para que el cambio surta efecto.

6. En la interfaz de usuario web Ambari, seleccione **HDFS** en la lista de la izquierda y, a continuación, seleccione **Restart All** (Reiniciar todos) en la lista desplegable **Service Actions** (Acciones del servicio) de la derecha. Cuando se le solicite, seleccione **Turn on maintenance mode** (Activar modo de mantenimiento) y, a continuación, "Confirm Restart All" ("Confirmar reiniciar todo").

    Repita este proceso para MapReduce2 y YARN.

7. Una vez reiniciados los servicios, seleccione cada uno de ellos y deshabilite el modo de mantenimiento en la lista desplegable **Service Actions** (Acciones del servicio).

## <a name="test-restricted-access"></a>Prueba de acceso restringido

Para comprobar que tiene el acceso restringido, utilice los métodos siguientes:

* Para clústeres de HDInsight **basados en Windows** , use el Escritorio remoto para conectarse al clúster. Para más información, vea [Connect to HDInsight using RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) (Conectarse a HDInsight mediante RDP).

    Una vez conectado, use el icono de **línea de comandos de Hadoop** en el escritorio para abrir un símbolo del sistema.

* Para clústeres de HDInsight **basados en Linux** , use SSH para conectarse al clúster. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado al clúster, siga estos pasos para comprobar que solo puede leer y listar elementos en la cuenta de almacenamiento de SAS:

1. Para mostrar el contenido del contenedor, utilice el siguiente comando desde el símbolo del sistema: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Reemplace **SASCONTAINER** por el nombre del contenedor creado para la cuenta de almacenamiento de SAS. Reemplace **SASACCOUNTNAME** por el nombre de la cuenta de almacenamiento utilizada para la firma de acceso compartido.

    La lista incluye el archivo que se cargó al crear el contenedor y la firma de acceso compartido.

2. Use el siguiente comando para comprobar que puede leer el contenido del archivo. Reemplace **SASCONTAINER** y **SASACCOUNTNAME** tal como lo ha hecho en el paso anterior. Reemplace **FILENAME** por el nombre de archivo que aparece en el comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Este comando muestra el contenido del archivo.

3. Use el siguiente comando para descargar el archivo en el sistema de archivos local:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Este comando descarga el archivo en un archivo local denominado **testfile.txt**.

4. Use el siguiente comando para cargar el archivo local en un nuevo archivo denominado **testupload.txt** en el almacenamiento de SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Recibirá un mensaje similar al texto siguiente:

        put: java.io.IOException

    Este error se produce porque la ubicación de almacenamiento es solo de lectura y lista. Use el siguiente comando para colocar los datos en el almacenamiento predeterminado para el clúster, que tiene permiso de escritura:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Esta vez, la operación debe completarse correctamente.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="a-task-was-canceled"></a>Se ha cancelado una tarea

**Síntomas**: al crear un clúster mediante el script de PowerShell, puede recibir el mensaje de error siguiente:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Causa**: este error se puede producir si usa una contraseña para el usuario administrador/HTTP del clúster o, en clústeres basados en Linux, el usuario SSH.

**Solución**: utilice una contraseña que cumpla los criterios siguientes:

* Debe tener como mínimo 10 caracteres.
* Debe contener al menos un dígito.
* Debe incluir al menos un carácter no alfanumérico.
* Debe contener al menos una mayúscula o una minúscula.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a agregar almacenamiento de acceso limitado al clúster de HDInsight, obtenga información acerca de otras maneras de trabajar con datos en el clúster:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
