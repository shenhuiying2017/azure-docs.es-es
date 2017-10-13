---
title: "Compilación de una aplicación de Java HBase para clústeres de Azure HDInsight basados en Windows | Microsoft Docs"
description: "Aprenda a usar Apache Maven para compilar una aplicación de Apache HBase basada en Java e implementarla después en un clúster de HDInsight de Azure basado en Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 59c9af5a91b107e68a676f02fe5a936f955b22fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Uso de Maven para compilar aplicaciones Java que utilicen HBase con HDInsight basado en Windows (Hadoop)
Aprenda a crear y compilar una aplicación de [Apache HBase](http://hbase.apache.org/) en Java con Apache Maven. Luego use la aplicación con Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) es una herramienta de administración y comprensión de proyectos de software que le permite compilar software, documentación e informes para proyectos Java. En este artículo, aprenderá a usarla para crear una aplicación Java básica que crea, consulta y elimina una tabla de HBase en un clúster de HDInsight de Azure.

> [!IMPORTANT]
> Los pasos de este documento requieren un clúster de HDInsight con Windows. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Requisitos
* [JDK de la plataforma Java 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o posterior
* [Maven](http://maven.apache.org/)
* Un clúster de HDInsight basado en Windows con HBase

    > [!NOTE]
    > Los pasos descritos en este documento se han probado con las versiones 3.2 y 3.3 del clúster de HDInsight. Los valores predeterminados proporcionados en los ejemplos corresponden a la versión 3.3 de un clúster de HDInsight.

## <a name="create-the-project"></a>Creación del proyecto
1. Desde la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la que desea crear el proyecto, por ejemplo, `cd code\hdinsight`.
2. Use el comando **mvn** , que se instala con Maven, para generar el scaffolding del proyecto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esta acción creará un directorio en el directorio actual, con el nombre especificado por el parámetro **artifactID** (**hbaseapp** en este ejemplo). Este directorio raíz contiene los siguientes elementos:

   * **pom.xml**: el modelo de objetos de proyectos ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene la información y los detalles de configuración usados para compilar el proyecto.
   * **src**: directorio que a su vez contiene el directorio **main\java\com\microsoft\examples**, donde creará la aplicación.
3. Elimine el archivo **src\test\java\com\microsoft\examples\apptest.java**, puesto que no se usará en este ejemplo.

## <a name="update-the-project-object-model"></a>Actualización del modelo de objetos de proyectos
1. Edite el archivo **pom.xml** y agregue lo siguiente dentro de la sección `<dependencies>`:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Esta sección le indica a Maven que el proyecto requiere la versión **1.1.2** de **hbase-client**. En tiempo de compilación, esta dependencia se descarga del repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio central de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para ver más información sobre esta dependencia.

   > [!IMPORTANT]
   > El número de versión debe coincidir con la versión de HBase que se proporciona con el clúster de HDInsight. Utilice la siguiente tabla para buscar el número de versión correcto.
   >
   >

   | Versión del clúster de HDInsight | Versión de HBase que se va a utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Para obtener más información sobre las versiones de HDInsight y los componentes, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md)
2. Si está usando la versión 3.3 de un clúster de HDInsight, también debe agregar lo siguiente a la sección `<dependencies>` :

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Esta dependencia cargará los componentes principales de Phoenix, que utilizan la versión 1.1.x de Hbase.
3. Agregue el siguiente código al archivo **pom.xml**. Esta sección debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

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
                    <source>1.7</source>
                    <target>1.7</target>
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

    La sección `<resources>` configura un recurso (**conf\hbase-site.xml**) que contiene información de configuración para HBase.

   > [!NOTE]
   > También puede establecer los valores de configuración mediante código. Vea los comentarios del ejemplo **CreateTable** que sigue para ver cómo hacer esto.
   >
   >

    Esta sección `<plugins>` configura [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) y [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). El complemento compiler se usa para compilar la topología. El complemento shade se usa para evitar la duplicación de licencias en el paquete JAR compilado por Maven. La razón de usar este complemento es que los archivos de licencia duplicados pueden provocar un error en tiempo de ejecución en el clúster de HDInsight. El uso del complemento maven-shade-plugin con la implementación de `ApacheLicenseResourceTransformer` evita este error.

    El complemento maven-shade-plugin también producirá un uberjar (o fatjar), que contiene todas las dependencias que necesita la aplicación.
4. Guarde el archivo **pom.xml** .
5. Cree un nuevo directorio llamado **conf** en el directorio **hbaseapp**. En el directorio **conf**, cree un archivo llamado **hbase-site.xml**. Use lo siguiente como contenido del archivo:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Este archivo se usará para cargar la configuración de HBase de un clúster de HDInsight.

   > [!NOTE]
   > Este es un archivo hbase-site.xml mínimo, que contiene la configuración básica elemental del clúster de HDInsight.

6. Guarde el archivo **hbase-site.xml**.

## <a name="create-the-application"></a>Creación de la aplicación
1. Vaya al directorio **hbaseapp\src\main\java\com\microsoft\examples** y cambie el nombre del archivo app.java a **CreateTable.java**.
2. Abra el archivo **CreateTable.java** y reemplace el contenido existente por el código siguiente:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

    Esta es la clase **CreateTable**, que creará una tabla llamada **people** y la rellenará con algunos usuarios predefinidos.
3. Guarde el archivo **CreateTable.java**.
4. En el directorio **hbaseapp\src\main\java\com\microsoft\examples**, cree un nuevo archivo denominado **SearchByEmail.java**. Use el siguiente código como contenido de este archivo:

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

            // Create a new regex filter
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

    La clase **SearchByEmail** se puede usar para consultar filas por dirección de correo electrónico. Dado que esta clase usa un filtro de expresiones regulares, puede proporcionar una cadena o una expresión regular cuando la utilice.
5. Guarde el archivo **SearchByEmail.java**.
6. En el directorio **hbaseapp\src\main\hava\com\microsoft\examples**, cree un nuevo archivo denominado **DeleteTable.java**. Use el siguiente código como contenido de este archivo:

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

    Esta clase es solo para limpiar este ejemplo. Para ello, primero se deshabilita y luego se elimina la tabla creada por la clase **CreateTable**.
7. Guarde el archivo **DeleteTable.java**.

## <a name="build-and-package-the-application"></a>Compilación y empaquetado de la aplicación
1. Abra un símbolo del sistema y cambie los directorios por el directorio **hbaseapp**.
2. Use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

        mvn clean package

    Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y luego compilará y empaquetará la aplicación.
3. Cuando el comando termine de ejecutarse, el directorio **hbaseapp\target** contendrá un archivo llamado **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]
   > El archivo **hbaseapp-1.0-SNAPSHOT.jar** es un uberjar (en ocasiones llamado fatjar) que contiene todas las dependencias necesarias para ejecutar la aplicación.

## <a name="upload-the-jar-file-and-start-a-job"></a>Carga del archivo JAR e inicio de un trabajo
Existen muchas formas de cargar un archivo en el clúster de HDInsight, tal y como se describe en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md). En los siguientes pasos se usa Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Tras instalar y configurar Azure PowerShell, cree un nuevo archivo llamado **hbase-runner.psm1**. Use el siguiente contenido para este archivo:

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

    Este archivo contiene dos módulos:

   * **Add-HDInsightFile**: se usa para cargar archivos en HDInsight.
   * **Start-HBaseExample**: se usa para ejecutar las clases creadas anteriormente.
2. Guarde el archivo **hbase-runner.psm1**.
3. Abra una nueva ventana de Azure PowerShell, cambie los directorios por el directorio **hbaseapp** y luego ejecute el siguiente comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambie la ruta de acceso a la ubicación del archivo **hbase-runner.psm1** creado anteriormente. Esta acción registrará el módulo para esta sesión de Azure PowerShell.
4. Use el comando siguiente para cargar el archivo **hbaseapp-1.0-SNAPSHOT.jar** en el clúster de HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Reemplace **hdinsightclustername** por el nombre del clúster de HDInsight. El comando cargará a continuación el archivo **hbaseapp-1.0-SNAPSHOT.jar** en la ubicación **example/jars** del almacenamiento principal del clúster de HDInsight.
5. Una vez cargados los archivos, use el comando siguiente para crear una tabla con **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Reemplace **hdinsightclustername** por el nombre del clúster de HDInsight.

    Este comando creará una nueva tabla llamada **people** en el clúster de HDInsight. Este comando no muestra ninguna salida en la ventana de la consola.
6. Para buscar entradas en la tabla, use el siguiente comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Reemplace **hdinsightclustername** por el nombre del clúster de HDInsight.

    Este comando usa la clase **SearchByEmail** para buscar filas donde la familia de columnas **contactinformation** y la columna **email** contengan la cadena **contoso.com**. Debe recibir los siguientes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Si se usa **fabrikam.com** como valor de `-emailRegex` se devolverán los usuarios que tienen **fabrikam.com** en el campo de correo electrónico. Dado que esta búsqueda se implementa usando un filtro basado en una expresión regular, también puede escribir expresiones regulares como **^r**, lo cual devuelve entradas en las que el correo electrónico empiece por la letra "r".

## <a name="delete-the-table"></a>Eliminación de la tabla
Cuando haya terminado con el ejemplo, use el siguiente comando desde la sesión de Azure PowerShell para eliminar la tabla **people** usada en este ejemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Reemplace **hdinsightclustername** por el nombre del clúster de HDInsight.

## <a name="troubleshooting"></a>Solución de problemas
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Sin resultados o resultados inesperados al usar Start-HBaseExample
Use el parámetro `-showErr` para ver el error estándar (STDERR) producido mientras se ejecutaba el trabajo.
