---
title: Cliente Java HBase - Azure HDInsight | Microsoft Docs
description: "Aprenda a usar Apache Maven para compilar una aplicación de Apache HBase basada en Java e implementarla después en HBase en Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: 
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: 03c88397e36c0fc7f19410e49f6b6f1a607659f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="build-java-applications-for-apache-hbase"></a>Compilar aplicaciones Java para Apache HBase

Aprenda a crear una aplicación [HBase de Apache](http://hbase.apache.org/) en Java. Luego, use esa aplicación con HBase en Azure HDInsight.

En los pasos descritos en este documento se usa [Maven](http://maven.apache.org/) para crear y compilar el proyecto. Maven es una herramienta de administración y comprensión de proyectos de software que le permite compilar software, documentación e informes para proyectos Java.

> [!NOTE]
> Los pasos descritos en este documento se han probado recientemente con HDInsight 3.6.

> [!IMPORTANT]
> Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Requisitos

* [JDK de la plataforma Java 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o posterior.

    > [!NOTE]
    > HDInsight 3.5 y las versiones posteriores necesitan Java 8. Las versiones anteriores de HDInsight requieren Java 7.

* [Maven](http://maven.apache.org/)

* [Un clúster de Azure HDInsight basado en Linux con HBase](hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

  > [!NOTE]
  > Los pasos descritos en este documento se han probado con las versiones 3.4 y 3.5 del clúster de HDInsight. Los valores predeterminados proporcionados en los ejemplos corresponden a la versión 3.5 de un clúster de HDInsight.

## <a name="create-the-project"></a>Creación del proyecto

1. Desde la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la que desea crear el proyecto, por ejemplo, `cd code\hbase`.

2. Use el comando **mvn** , que se instala con Maven, para generar el scaffolding del proyecto.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Si va a usar PowerShell, deberá poner comillas dobles alrededor de los parámetros `-D`.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Esta acción crea un directorio con el mismo nombre que el parámetro **artifactID** (**hbaseapp** en este ejemplo). Este directorio raíz contiene los siguientes elementos:

   * **pom.xml**: el modelo de objetos de proyectos ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene la información y los detalles de configuración usados para compilar el proyecto.
   * **src**: directorio que a su vez contiene el directorio **main\java\com\microsoft\examples**, donde creará la aplicación.

3. Elimine el archivo `src/test/java/com/microsoft/examples/apptest.java`. No lo vamos a usar en este ejemplo.

## <a name="update-the-project-object-model"></a>Actualización del modelo de objetos de proyectos

1. Edite el archivo `pom.xml` y agregue el siguiente código dentro de la sección `<dependencies>`:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    En esta sección se indica que el proyecto necesita los componentes **hbase-client** y **phoenix-core**. En tiempo de compilación, estas dependencias se descargan del repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio central de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para ver más información sobre esta dependencia.

   > [!IMPORTANT]
   > El número de versión de hbase-client debe coincidir con la versión de HBase que se proporciona con el clúster de HDInsight. Utilice la siguiente tabla para buscar el número de versión correcto.

   | Versión del clúster de HDInsight | Versión de HBase que se va a utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 y 3.6 |1.1.2 |

    Para obtener más información sobre las versiones de HDInsight y los componentes, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md)

3. Agregue el siguiente código al archivo **pom.xml**. Este texto debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

   ```xml
    <build>
        <sourceDirectory>src</sourceDirectory>
        <resources>
        <resource>
            <directory>${basedir}/conf</directory>
            <filtering>false</filtering>
            <includes>
            <include>hbase-site.xml</include>
            </includes>
        </resource>
        </resources>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
            </configuration>
            <executions>
            <execution>
                <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
            </execution>
            </executions>
        </plugin>
        </plugins>
    </build>
   ```

    Esta sección configura un recurso (`conf/hbase-site.xml`) que contiene información de configuración de HBase.

   > [!NOTE]
   > También puede establecer los valores de configuración mediante código. Vea los comentarios en el ejemplo `CreateTable`.

    Esta sección también configura [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) y [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). El complemento compiler se usa para compilar la topología. El complemento shade se usa para evitar la duplicación de licencias en el paquete JAR compilado por Maven. Este complemento se usa para evitar errores por "archivos de licencia duplicados" en tiempo de ejecución en el clúster de HDInsight. El uso del complemento maven-shade-plugin con la implementación de `ApacheLicenseResourceTransformer` evita este error.

    El complemento maven-shade-plugin también producirá un uberjar, que contiene todas las dependencias que necesita la aplicación.

4. Guarde el archivo `pom.xml`.

5. Cree un directorio denominado `conf` en el directorio `hbaseapp`. Este directorio se utilizará para almacenar información de configuración para conectarse a HBase.

6. Use el siguiente comando para copiar la configuración de HBase desde el clúster HBase en el directorio `conf`. Reemplace `USERNAME` por el nombre del inicio de sesión de SSH. Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Para más información sobre cómo usar `ssh` y `scp`, vea [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Creación de la aplicación

1. Vaya al directorio `hbaseapp/src/main/java/com/microsoft/examples` y cambie el nombre del archivo app.java a `CreateTable.java`.

2. Abra el archivo `CreateTable.java` y reemplace el contenido existente por el texto siguiente:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;
    import org.apache.hadoop.hbase.HTableDescriptor;
    import org.apache.hadoop.hbase.TableName;
    import org.apache.hadoop.hbase.HColumnDescriptor;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Put;
    import org.apache.hadoop.hbase.util.Bytes;

    public class CreateTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Example of setting zookeeper values for HDInsight
        // in code instead of an hbase-site.xml file
        //
        // config.set("hbase.zookeeper.quorum",
        //            "zookeepernode0,zookeepernode1,zookeepernode2");
        //config.set("hbase.zookeeper.property.clientPort", "2181");
        //config.set("hbase.cluster.distributed", "true");
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

        // create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // create the table...
        HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
        // ... with two column families
        tableDescriptor.addFamily(new HColumnDescriptor("name"));
        tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
        admin.createTable(tableDescriptor);

        // define some people
        String[][] people = {
            { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
            { "2", "Franklin", "Holtz", "franklin@contoso.com" },
            { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
            { "4", "Rae", "Schroeder", "rae@contoso.com" },
            { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
            { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

        HTable table = new HTable(config, "people");

        // Add each person to the table
        //   Use the `name` column family for the name
        //   Use the `contactinfo` column family for the email
        for (int i = 0; i< people.length; i++) {
            Put person = new Put(Bytes.toBytes(people[i][0]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
            person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
            table.put(person);
        }
        // flush commits and close the table
        table.flushCommits();
        table.close();
        }
    }
   ```

    Esta es la clase **CreateTable**, que creará una tabla llamada **people** y la rellenará con algunos usuarios predefinidos.

3. Guarde el archivo `CreateTable.java`.

4. En el directorio `hbaseapp/src/main/java/com/microsoft/examples`, cree un archivo denominado `SearchByEmail.java`. Use el texto siguiente como contenido de este archivo:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Scan;
    import org.apache.hadoop.hbase.client.ResultScanner;
    import org.apache.hadoop.hbase.client.Result;
    import org.apache.hadoop.hbase.filter.RegexStringComparator;
    import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
    import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
    import org.apache.hadoop.hbase.util.Bytes;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class SearchByEmail {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Use GenericOptionsParser to get only the parameters to the class
        // and not all the parameters passed (when using WebHCat for example)
        String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
        if (otherArgs.length != 1) {
            System.out.println("usage: [regular expression]");
            System.exit(-1);
        }

        // Open the table
        HTable table = new HTable(config, "people");

        // Define the family and qualifiers to be used
        byte[] contactFamily = Bytes.toBytes("contactinfo");
        byte[] emailQualifier = Bytes.toBytes("email");
        byte[] nameFamily = Bytes.toBytes("name");
        byte[] firstNameQualifier = Bytes.toBytes("first");
        byte[] lastNameQualifier = Bytes.toBytes("last");

        // Create a regex filter
        RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
        // Attach the regex filter to a filter
        //   for the email column
        SingleColumnValueFilter filter = new SingleColumnValueFilter(
            contactFamily,
            emailQualifier,
            CompareOp.EQUAL,
            emailFilter
        );

        // Create a scan and set the filter
        Scan scan = new Scan();
        scan.setFilter(filter);

        // Get the results
        ResultScanner results = table.getScanner(scan);
        // Iterate over results and print  values
        for (Result result : results ) {
            String id = new String(result.getRow());
            byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
            String firstName = new String(firstNameObj);
            byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
            String lastName = new String(lastNameObj);
            System.out.println(firstName + " " + lastName + " - ID: " + id);
            byte[] emailObj = result.getValue(contactFamily, emailQualifier);
            String email = new String(emailObj);
            System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
        }
        results.close();
        table.close();
        }
    }
   ```

    La clase **SearchByEmail** se puede usar para consultar filas por dirección de correo electrónico. Dado que esta clase usa un filtro de expresiones regulares, puede proporcionar una cadena o una expresión regular cuando la utilice.

5. Guarde el archivo `SearchByEmail.java`.

6. En el directorio `hbaseapp/src/main/hava/com/microsoft/examples`, cree un archivo denominado `DeleteTable.java`. Use el texto siguiente como contenido de este archivo:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;

    public class DeleteTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // Disable, and then delete the table
        admin.disableTable("people");
        admin.deleteTable("people");
        }
    }
   ```

    Esta clase limpia las tablas de HBase creadas en este ejemplo. Para ello, primero se deshabilita y luego se elimina la tabla creada por la clase `CreateTable`.

7. Guarde el archivo `DeleteTable.java`.

## <a name="build-and-package-the-application"></a>Compilación y empaquetado de la aplicación

1. En el directorio `hbaseapp`, use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

    ```bash
    mvn clean package
    ```

    Este comando compila y empaqueta la aplicación en un archivo .jar.

2. Cuando el comando finalice, el directorio `hbaseapp/target` contendrá un archivo denominado `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > El archivo `hbaseapp-1.0-SNAPSHOT.jar` es un archivo uber-jar. Contiene todas las dependencias necesarias para ejecutar la aplicación.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Carga del archivo JAR y ejecución de trabajos (SSH)

En los siguientes pasos se usa `scp` para copiar el archivo JAR en el nodo primario principal de HBAse en el clúster de HDInsight. El comando `ssh`, se usa para conectarse al clúster y ejecutar el ejemplo directamente en el nodo principal.

1. Use el siguiente comando para cargar el archivo jar en el clúster:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Reemplace `USERNAME` por el nombre del inicio de sesión de SSH. Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.

2. Use el siguiente comando para conectarse al clúster de HBase:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Reemplace `USERNAME` por el nombre del inicio de sesión de SSH. Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.

3. Use el siguiente comando para crear una tabla HBase por medio de la aplicación Java:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Este comando crea una tabla HBase denominada **people**, que se rellenará con datos.

4. Use el siguiente comando para buscar direcciones de correo electrónico almacenadas en la tabla:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Recibirá los siguientes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Para eliminar la tabla, use el comando siguiente:

    

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Carga del archivo JAR y ejecución de trabajos (PowerShell)

En los siguientes pasos se usa Azure PowerShell para cargar el archivo JAR en el almacenamiento predeterminado del clúster de HBase. Los cmdlets de HDInsight se usan para ejecutar los ejemplos de forma remota.

1. Tras instalar y configurar Azure PowerShell, cree un archivo denominado `hbase-runner.psm1`. Use el texto siguiente como contenido de este archivo:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Este archivo contiene dos módulos:

   * **Add-HDInsightFile**: se usa para cargar archivos en el clúster.
   * **Start-HBaseExample**: se usa para ejecutar las clases creadas anteriormente.

2. Guarde el archivo `hbase-runner.psm1`.

3. Abra una nueva ventana de Azure PowerShell, cambie los directorios por el directorio `hbaseapp` y, luego, ejecute el siguiente comando:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Cambie la ruta de acceso a la ubicación del archivo `hbase-runner.psm1` creado anteriormente. Este comando registra el módulo con Azure PowerShell.

4. Use el siguiente comando para cargar `hbaseapp-1.0-SNAPSHOT.jar` en el clúster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Reemplace `hdinsightclustername` por el nombre del clúster. El comando carga el archivo `hbaseapp-1.0-SNAPSHOT.jar` en la ubicación `example/jars` en el almacenamiento principal del clúster.

5. Use el siguiente comando para crear una tabla mediante `hbaseapp`:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Reemplace `hdinsightclustername` por el nombre del clúster.

    Con este comando se crea una tabla denominada **people** en HBase en el clúster de HDInsight. Este comando no muestra ninguna salida en la ventana de la consola.

6. Para buscar entradas en la tabla, use el siguiente comando:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Reemplace `hdinsightclustername` por el nombre del clúster.

    Este comando usa la clase `SearchByEmail` para buscar filas donde la familia de columnas `contactinformation` y la columna `email` contengan la cadena `contoso.com`. Debe recibir los siguientes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Si se usa **fabrikam.com** como valor de `-emailRegex` se devolverán los usuarios que tienen **fabrikam.com** en el campo de correo electrónico. También puede utilizar expresiones regulares en el término de búsqueda. Por ejemplo, **^ r** devuelve las direcciones de correo que comienzan por la letra "r".

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Sin resultados o resultados inesperados al usar Start-HBaseExample

Use el parámetro `-showErr` para ver el error estándar (STDERR) producido mientras se ejecutaba el trabajo.

## <a name="delete-the-table"></a>Eliminación de la tabla

Cuando haya terminado con el ejemplo, use lo siguiente para eliminar la tabla **people** usada en este ejemplo:

__Desde una sesión `ssh`__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Desde Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Pasos siguientes

[Aprender a usar SQuirreL SQL con HBase](hdinsight-hbase-phoenix-squirrel-linux.md)
