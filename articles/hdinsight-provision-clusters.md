<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision HDInsight clusters | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision HDInsight clusters" authors="jgao" />

Aprovisionamiento de clústeres de HDInsight
===========================================

En este artículo, aprenderá a aprovisionar un clúster de HDInsight de varias formas.

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Ofertas para miembros](https://www.windowsazure.com/en-us/pricing/member-offers/) o [Evaluación gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

En este artículo
----------------

-   [Uso del Portal de administración de Azure](#portal)
-   [Uso de Azure PowerShell](#powershell)
-   [Uso de la línea de comandos entre plataformas](#cli)
-   [Uso del SDK .NET de HDInsight](#sdk)
-   [Pasos siguientes](#nextsteps)

Uso del Portal de administración de Azure
-----------------------------------------

Los clústeres de HDInsight utilizan contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/manage/services/hdinsight/howto-blob-store/). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento](/es-es/manage/services/storage/how-to-create-a-storage-account/).

> [WACOM.NOTE] Actualmente, solo se pueden hospedar clústeres de HDInsight en las siguientes regiones: sudeste de Asia, Europa del norte, Europa occidental, Estados Unidos del este y Estados Unidos del oeste.

Esta sesión describe el procedimiento para crear un clúster de HDInsight usando la opción de creación personalizada. Para obtener más información acerca de cómo usar la opción de creación rápida, consulte [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/).

**Para crear un clúster de HDInsight usando la opción de creación personalizada**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **+ NEW** en la parte inferior de la página, después en **DATA SERVICES**, a continuación en **HDINSIGHT** y, finalmente, en **CUSTOM CREATE**.
3.  En la página de detalles del clúster, escriba o seleccione los valores siguientes:

    <table  border="1">
         <tr><th>Propiedad</th>
    <th>Valor</th>
    </tr>
    
         <tr><td>CLUSTER NAME</td>
    
             <td><p>Dé un nombre al clúster. </p>
    
                 <ul>
                 <li>El nombre DNS debe empezar y terminar por caracteres alfanuméricos y puede contener guiones.</li>
    
                 <li>El campo debe ser una cadena con una longitud entre 3 y 63 caracteres.</li>
    
                 </ul>
    </td>
    </tr>
    
         <tr><td>DATA NODES</td>
    
             <td>Especifique el número de nodos del clúster. El valor predeterminado es 4.</td>
    </tr>
    
         <tr><td>HDINSIGHT VERSION</td>
    
             <td>Seleccione la versión. La versión predeterminada es 2.0 con los clústeres de Hadoop 1.2.  La versión 3.0 usa los clústeres de Hadoop 2.2. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/manage/services/hdinsight/versioning-in-hdinsight/">¿Qué versión de Hadoop tiene HDInsight de Azure?</a>
    .</td>
    </tr>
    
         <tr><td>REGION</td>
    
             <td>Especifique el centro de datos donde está instalado el clúster. La ubicación debe ser la misma que la del almacenamiento de blobs de Azure que se usará como sistema de archivos predeterminado. Actualmente solo se puede elegir los valores *Southeast Asia*, *North Europe*, *West Europe*, *East US* o *West US*.</td>
    
         </tr>
    
     </table>

    ![HDI.CustomProvision.Page1](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png)

4.  Haga clic en la flecha derecha situada en la esquina inferior derecha de la página.
5.  En la página de configuración del usuario del clúster, escriba o seleccione el valor siguiente:

    <table  border="1">
         <tr><th>Propiedad</th>
    <th>Valor</th>
    </tr>
    
         <tr><td>USER NAME</td>
    
             <td>Especifique el nombre del usuario del clúster de HDInsight.</td>
    </tr>
    
         <tr><td><p>PASSWORD</p>
    <p>CONFIRM PASSWORD</p>
    </td>
    
             <td>Especifique la contraseña del usuario del clúster de HDInsight.</td>
    </tr>
    
         <tr><td>Enter Hive/Oozie Metastore</td>
    
             <td>Especifique la base de datos SQL que se encuentra en el mismo centro de datos que se usará como metastore de Hive/Oozie.</td>
    </tr>
    
         <tr><td>HIVE META/OOZIESTORE DATABASE</td>
    
             <td>Especifique la base de datos SQL de Azure que se usará como metastore para Hive/OOzie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado en la página de detalles del clúster.</td>
    </tr>
    
         <tr><td>DATABASE USER</td>
    
             <td>Especifique el usuario de la base de datos SQL que se usará para conectarse a la base de datos.</td>
    </tr>
    
         <tr><td>DATABASE USER PASSWORD</td>
    
             <td>Especifique la contraseña del usuario de la base de datos SQL.</td>
    </tr>
    
     </table>

    Para la versión 2.0 del clúster de HDInsight, con las credenciales proporcionadas aquí solo se pude obtener acceso a los servicios del clúster. Se puede habilitar el escritorio remoto una vez que el clúster se haya creado.

    ![HDI.CustomProvision.Page2](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

6.  Haga clic en la flecha derecha situada en la esquina inferior derecha de la página.
7.  En la página de la cuenta de almacenamiento, escriba o seleccione los valores siguientes:

    Propiedad

    Valor

    STORAGE ACCOUNT

    Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:

    -   Use Existing Storage
    -   Create New Storage
    -   Use Storage From Another Subscription

    ACCOUNT NAME

    Cuando se selecciona **Use Existing Storage**, el cuadro de lista solo incluye las cuentas de almacenamiento ubicadas en el mismo centro de datos.

    ACCOUNT KEY

    Este campo solo está disponible si selecciona **Use Storage From Another Subscription** en el campo STORAGE ACCOUNT.

    DEFAULT CONTAINER

    Se usará el contenedor predeterminado de la cuenta de almacenamiento como sistema de archivos predeterminado para el clúster de HDInsight. Cuando se elige **Use Existing Storage** en el campo STORAGE ACCOUNT y **Create default container** en el campo DEFAULT CONTAINER, el nombre del contenedor predeterminado será el mismo que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente.

    ADDITIONAL STORAGE ACCOUNTS

    HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de administración, verá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique en este campo, se agregará una página de la cuenta almacenamiento adicional donde podrá especificar la información de la cuenta. Por ejemplo, en la siguiente captura de pantalla, donde se selecciona una cuenta de almacenamiento adicional, se agrega la página 4 al diálogo.

    ![HDI.CustomProvision.Page3](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

8.  Haga clic en la flecha derecha situada en la esquina inferior derecha de la página.
9.  En la página de la cuenta de almacenamiento, escriba la información de la cuenta de almacenamiento adicional:

    ![HDI.CustomProvision.Page4](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

10. Haga clic en el botón Complete (icono de marca de verificación) en la esquina inferior derecha para iniciar el proceso de aprovisionamiento del clúster de HDInsight.

El aprovisionamiento del clúster puede durar varios minutos. Una vez completado el proceso de aprovisionamiento, en la columna de estado del clúster se mostrará **Running**.

> [WACOM.NOTE] Una vez que haya seleccionado una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

Uso de Azure PowerShell
-----------------------

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/). Para obtener más información acerca del uso de PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](/es-es/manage/services/hdinsight/administer-hdinsight-using-powershell/). Para obtener más información acerca de los cmdlets de HDInsight PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](http://msdn.microsoft.com/es-es/library/windowsazure/dn479228.aspx).

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con PowerShell:

-   Creación de una cuenta de almacenamiento de Azure
-   Creación de un contenedor de blobs de Azure
-   Creación de un clúster de HDInsight

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight.

**Para crear una cuenta de Almacenamiento de Azure**

-   Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

          $storageAccountName = "<StorageAcccountName>"
          $location = "<MicrosoftDataCenter>"     # Por ejemplo, "West US".
            
          # Cree una cuenta de almacenamiento de Azure.
          New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de PowerShell para recuperar dicha información:

          # Incluya las cuentas de almacenamiento de la suscripción actual.
          Get-AzureStorageAccount

          # Enumere las claves de una cuenta de almacenamiento.
          Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contendor de almacenamiento de Azure**

-   Ejecute los comandos siguientes en la ventana de Azure PowerShell:

          $storageAccountName = "<StorageAccountName>"
          $storageAccountKey = "<StorageAccountKey>"
          $containerName="<ContainerName>"

          # Cree un objeto de contexto de almacenamiento.
          $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                                 -StorageAccountKey $storageAccountKey  
             
          # Cree un contenedor de almacenamiento de blobs.
          New-AzureStorageContainer -Name $containerName -Context $destContext

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

-   Ejecute los comandos siguientes en la ventana de Azure PowerShell:

          $subscriptionName = "<SubscriptionName>"     # El nombre de la suscripción de Azure.
          $storageAccountName = "<StorageAccountName>"    # La cuenta de almacenamiento de Azure que hospeda el contenedor predeterminado. Se usará el contenedor predeterminado como sistema de archivos predeterminado.
          $containerName = "<ContainerName>"              # El contenedor de almacenamiento de blobs de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight.

          $clusterName = "<HDInsightClusterName>"         # El nombre que dará a su clúster de HDInsight.
          $location = "<MicrosoftDataCenter>"             # La ubicación del clúster de HDInsight. Debe encontrarse en el mismo centro de datos que la cuenta de almacenamiento.
          $clusterNodes = <ClusterSizeInNodes>          # El número de nodos del clúster de HDInsight.

          # Obtenga la clave principal de almacenamiento basada en el nombre de la cuenta.
          Select-AzureSubscription $subscriptionName
          $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

          # Cree un clúster de HDInsight.
          New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    El aprovisionamiento del clúster puede durar varios minutos en completarse.

    ![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

También puede aprovisionar un clúster y configurarlo para que se conecte a más de un almacenamiento de blobs de Azure o a metastores personalizados de Hive y Oozie. Esta función avanzada le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

**Para aprovisionar un clúster de HDInsight usando la configuración**

-   Ejecute los comandos siguientes en la ventana de Windows PowerShell:

          $subscriptionName = "<SubscriptionName>"
          $clusterName = "<ClusterName>"
          $location = "<MicrosoftDataCenter>"
          $clusterNodes = <ClusterSizeInNodes>
            
          $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
          $containerName_Default = "<DefaultFileSystemContainerName>"
            
          $storageAccountName_Add1 = "<AdditionalStorageAccountName>"
            
          $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
          $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
          $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
          $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
            
          # Obtenga las claves de la cuenta de almacenamiento.
          Select-AzureSubscription $subscriptionName
          $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
          $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
            
          $oozieCreds = Get-Credential -Message "Oozie metastore"
          $hiveCreds = Get-Credential -Message "Hive metastore"
            
          # Cree un contenedor de almacenamiento de blobs.
          #$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
          #New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
            
          # Cree un clúster de HDInsight.
          $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
              Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
              Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
              Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                  New-AzureHDInsightCluster -Name $clusterName -Location $location

**Para enumerar los clústeres de HDInsight**

-   Ejecute los comandos siguientes en la ventana de Azure PowerShell:

          Get-AzureHDInsightCluster -Name <ClusterName>

Uso de la línea de comandos entre plataformas
---------------------------------------------

Otra opción para aprovisionar un clúster de HDInsight es la interfaz de línea de comandos entre plataformas. La herramienta de línea de comandos se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La herramienta de línea de comandos es de código abierto. El código fuente se administra en GitHub en <https://github.com/WindowsAzure/azure-sdk-tools-xplat>. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux](/es-es/develop/nodejs/how-to-guides/command-line-tools/). Para obtener una documentación de referencia completa, consulte [Herramienta de línea de comandos de Azure para Mac y Linux](/es-es/manage/linux/other-resources/command-line-tools/). Este artículo solo trata el uso de la interfaz de línea de comandos en Windows.

Los procedimientos siguientes son necesarios para aprovisionar un clúster de HDInsight con la línea de comandos entre varias plataformas:

-   Instalación de la línea de comandos entre varias plataformas
-   Descarga e importación de la configuración de publicación de la cuenta de Azure
-   Creación de una cuenta de almacenamiento de Azure
-   Aprovisionamiento de un clúster

La interfaz de línea de comandos se puede instalar usando *Node.js Package Manager (NPM)* o Windows Installer.

**Para instalar la interfaz de línea de comandos usando NPM**

1.  Vaya a **www.nodejs.org**.
2.  Haga clic en **INSTALL** y siga las instrucciones usando la configuración predeterminada.
3.  Abra **Command Prompt** (o bien *Azure Command Prompt* o *Símbolo del sistema para desarrolladores de VS2012*) desde su estación de trabajo.
4.  Ejecute el comando siguiente en la ventana del símbolo del sistema.

        npm install -g azure-cli

    > [WACOM.NOTE] Si aparece un error que indica que no se ha podido encontrar el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno de PATH: *C:\\Archivos de programa (x86)\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm* o *C:\\Archivos de programa\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm*

5.  Ejecute el siguiente comando para comprobar la instalación:

        azure hdinsight -h

    Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos usando windows installer**

1.  Vaya a **http://www.windowsazure.com/es-es/downloads/**.
2.  Desplácese hasta la sección **Command line tools** y, a continuación, haga clic en **Cross-platform Command Line Interface** y siga el asistente del instalador de plataforma web.

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo de configuración de publicación contiene información confidencial. Se recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.

**Para descargar e importar la configuración de publicación**

1.  Abra **Command Prompt**.
2.  Ejecute el comando siguiente para descargar el archivo de configuración de publicación.

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png)

    El comando muestra las instrucciones para descargar el archivo, incluyendo una URL.

3.  Abra **Internet Explorer** y vaya a la URL que se incluye en la ventana del símbolo del sistema.
4.  Haga clic en **Save** para guardar el archivo en la estación de trabajo.
5.  En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

        azure account import <file>

    En la captura de pantalla anterior, el archivo de configuración de publicación se guardó en la carpeta C:\\HDInsight de la estación de trabajo.

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

**Para crear una cuenta de Almacenamiento de Azure**

-   En la ventana del símbolo del sistema, ejecute el comando siguiente:

          azure account storage create [options] <StorageAccountName>

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure usando el Portal de administración de Azure, consulte [Creación de una cuenta de almacenamiento](/es-es/manage/services/storage/how-to-create-a-storage-account/).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

    -- Enumera las cuentas de almacenamiento
    azure account storage list
    -- Muestra la cuenta de almacenamiento.
    azure account storage show <StorageAccountName>
    -- Enumera las claves de una cuenta de almacenamiento.
    azure account storage keys list <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección *Visualización, copia y generación de claves de acceso al almacenamiento* de [Administración de cuentas de almacenamiento](/es-es/manage/services/storage/how-to-manage-a-storage-account/).

El comando *azure hdinsight cluster create* crea el contenedor si no existe. Si opta por crear el contenedor antes, puede usar el comando siguiente:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para crear un clúster de HDInsight**

-   En la ventana del símbolo del sistema, ejecute el comando siguiente:

          azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation](./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png)

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan sus trabajos y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

**Para aprovisionar un clúster de HDInsight usando un archivo de configuración**

-   En la ventana del símbolo del sistema, ejecute el comando siguiente:

          azure hdinsight cluster config create <file>
             
          azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
             
          azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
                 --storageAccountKey "<StorageAccountKey>"
             
          azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
             
          azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
                 --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
             
          azure hdinsight cluster create --config <file>

    ![HDI.CLIClusterCreationConfig](./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png)

**Para enumerar y mostrar los detalles del clúster**

-   Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

          azure hdinsight cluster list
          azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster](./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Enumerar y mostrar clústeres")

**Para eliminar un clúster**

-   Use el comando siguiente para eliminar un clúster:

          azure hdinsight cluster delete <ClusterName>

Uso del SDK .NET de HDInsight
-----------------------------

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight en .NET.

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con el SDK:

-   Instalación del SDK .NET de HDInsight
-   Creación de una aplicación de consola
-   Ejecución de la aplicación

**Para instalar el SDK .NET de HDInsight:** Puede instalar la compilación más reciente publicada del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear una aplicación de consola de Visual Studio**

1.  Abra Visual Studio 2012.

2.  En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3.  En Nuevo proyecto, escriba o seleccione los siguientes valores:

    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
    
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CrearClústerHDI</td>
    </tr>
    
     </table>

4.  Haga clic en **Aceptar** para crear el proyecto.

5.  En el menú **Herramientas**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.

         Install-Package Microsoft.Azure.Management.HDInsight

    Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7.  En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8.  Agregue la siguiente instrucción en la parte superior del archivo:

         using System.Security.Cryptography.X509Certificates;
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  En la función Main(), copie y pegue el siguiente código:

         string certfriendlyname = "<CertificateFriendlyName>";
         string subscriptionid = "<WindowsAzureSubscriptionID>";
         string clustername = "<HDInsightClusterName>";
         string location = "<MicrosoftDataCenter>";
         string storageaccountname = "<WindowsAzureStorageAccountName>";     // Se debe usar la ruta completa.
         string storageaccountkey = "<WindowsAzureStorageAccountKey>";
         string containername = "<HDInsightDefaultContainerName>";
         string username = "<HDInsightUsername>";
         string password = "<HDInsightUserPassword>";
         int clustersize = <NumberOfNodesInTheCluster>;

         // Obtenga el objeto de certificado del almacén de certificados usando el nombre descriptivo para identificarlo.
         X509Store store = new X509Store();
         store.Open(OpenFlags.ReadOnly);
         X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

         // Cree la cuenta de almacenamiento si no existe.

         // Cree el contenedor si no existe.

         // Cree un objeto HDInsightClient.
         HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
         var client = HDInsightClient.Connect(creds);

         // Proporcione la información del clúster.
         ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
         {
             Name = clustername,
             Location = location,
             DefaultStorageAccountName = storageaccountname,
             DefaultStorageAccountKey = storageaccountkey,
             DefaultStorageContainer = containername,
             UserName = username,
             Password = password,
             ClusterSizeInNodes = clustersize
         };

         // Cree el clúster.
         Console.WriteLine("Creating the HDInsight cluster ...");

         ClusterDetails cluster = client.CreateCluster(clusterInfo);

         Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
         Console.WriteLine("Press ENTER to continue.");
         Console.ReadKey();

10. Reemplace las variables al principio de la función main().

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.

Pasos siguientes
----------------

En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)
-   [Administración de HDInsight con PowerShell](/es-es/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Envío de trabajos de Hadoop mediante programación](/es-es/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

