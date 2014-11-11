<properties title="Build an HBase application using Maven" pageTitle="Build an HBase application using Maven" description="Learn how to use Apache Maven to build a Java-based Apache HBase application, then deploy it to Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr" />

## Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)

Aprenda a crear y a compilar una aplicación [Apache HBase][Apache HBase] en Java mediante Apache Maven. Luego use la aplicación con Azure HDInsight (Hadoop).

[Maven][Maven] es una herramienta de administración y comprensión de proyectos de software que le permite compilar software, documentación e informes para proyectos Java. En este artículo, aprenderá a usarla para crear una aplicación Java básica que crea, consulta y elimina una tabla de HBase en un clúster de HDInsight de Azure.

## Requisitos

-   [Java platform JDK][Java platform JDK] 7 o superior

-   [Maven][Maven]

-   [Un clúster de HDInsight de Azure con HBase][Un clúster de HDInsight de Azure con HBase]

## Creación del proyecto

1.  Desde la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la que desea crear el proyecto. Por ejemplo, `cd code\hdinsight`.

2.  Use el comando **mvn**, que se instala con Maven, para generar el scaffolding del proyecto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esta acción creará un nuevo directorio en el directorio actual, con el nombre especificado por el parámetro **artifactID** (**hbaseapp** en este ejemplo). Este directorio contendrá los siguientes elementos.

    -   **pom.xml**: El modelo de objetos de proyectos ([POM][POM]) contiene la información y los detalles de configuración para compilar el proyecto.

    -   **src**: El directorio que contiene el directorio **main\\java\\com\\microsoft\\examples**, donde creará la aplicación.

3.  Elimine el archivo **src\\test\\java\\com\\microsoft\\examples\\apptest.java**, puesto que no se usará en este ejemplo.

## Actualización del modelo de objetos de proyectos

1.  Edite el archivo **pom.xml** y agregue el siguiente contenido a la sección `<dependencies>`.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Esta acción le indica a Maven que el proyecto requiere **hbase-client**, versión **0.98.4-hadoop2**. En el momento de compilación, esto se descargará desde el repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio de Maven][búsqueda del repositorio de Maven] para ver más información sobre esta dependencia.

2.  Agregue lo siguiente al archivo **pom.xml**. Este contenido se debe incluir entre las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

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

    Esta acción configura un recurso (**conf\\hbase-site.xml**), que contiene información de configuración de HBase.

    > [WACOM.NOTE] También puede establecer los valores de configuración mediante código. Vea los comentarios del ejemplo **CreateTable** a continuación para saber cómo hacer esto.

    Esta acción también configura el complemento [maven-shade-plugin][maven-shade-plugin], que se usa para impedir la duplicación de licencias en el archivo JAR que se compila en Maven. La razón de usar este complemento es que los archivos de licencia duplicados pueden provocar un error en tiempo de ejecución en el clúster de HDInsight. Usando el complemento maven-shade-plugin con la implementación de `ApacheLicenseResourceTransformer` se evita este error.

    El complemento maven-shade-plugin también producirá un uberjar (o fatjar), que contiene todas las dependencias que necesita la aplicación.

3.  Guarde el archivo **pom.xml**.

4.  Cree un nuevo directorio llamado **conf** en el directorio **hbaseapp**. En el directorio **conf**, cree un nuevo archivo llamado **hbase-site.xml** y utilice el siguiente contenido.

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
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeepernode0:2181 zookeepernode1:2181 zookeepernode2:2181</value>
          </property>

        </configuration>

    Este archivo se usará para cargar la configuración de HBase de un clúster de HDInsight.

    > [WACOM.NOTE] Este es un archivo hbase-site.xml mínimo, que contiene la configuración básica elemental del clúster de HDInsight. Para obtener una versión completa del archivo de configuración hbase-site.xml que usa HDInsight, [inicie una sesión de Escritorio remoto en el clúster de HDInsight][inicie una sesión de Escritorio remoto en el clúster de HDInsight]. El archivo está ubicado en el directorio C:\\apps\\dist\\hbase-\<número de versión\>-hadoop2\\conf. La parte del número de versión de la ruta de acceso al archivo cambiará cuando HBase se actualice en el clúster.

5.  Guarde el archivo **hbase-site.xml**.

## Creación de la aplicación

1.  Vaya al directorio **hbaseapp\\src\\com\\microsoft\\examples** y cambie el nombre del archivo app.java\_\_ por **CreateTable.java**.

2.  Abra el archivo **CreateTable.java** y sustituya el contenido existente por lo siguiente.

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
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

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

3.  Guarde el archivo **CreateTable.java**.

4.  En el directorio **hbaseapp\\com\\microsoft\\examples**, cree un nuevo archivo llamado **SearchByEmail.java**. Use el siguiente contenido para este archivo.

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

5.  Guarde el archivo **SearchByEmail.java**.

6.  En el directorio **hbaseapp\\com\\microsoft\\examples**, cree un nuevo archivo llamado **DeleteTable.java**. Use el siguiente contenido para este archivo.

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

    Esta clase es solo para limpiar este ejemplo, primero deshabilitando y luego eliminando la tabla creada por la clase **CreateTable**.

7.  Guarde el archivo **CreateTable.java**.

## Compilación y empaquetado de la aplicación

1.  Abra un símbolo del sistema y cambie los directorios por el directorio **hbaseapp**.

2.  Use el siguiente comando para compilar el archivo JAR que contiene la aplicación.

        mvn clean package

    Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y luego compilará y empaquetará la aplicación.

3.  Cuando el comando termine de ejecutarse, el directorio **hbaseapp\\target** contendrá un archivo llamado **hbaseapp-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] El archivo **hbaseapp-1.0-SNAPSHOT.jar** es un uberjar (en ocasiones llamado fatjar), que contiene todas las dependencias necesarias para ejecutar la aplicación.

## Carga del archivo JAR e inicio de un trabajo

> [WACOM.NOTE] Existen muchas maneras de cargar un archivo en el clúster de HDInsight, tal y como se describe en [Carga de datos para trabajos de Hadoop en HDInsight][Carga de datos para trabajos de Hadoop en HDInsight]. En los pasos que se indican a continuación se usa [Azure PowerShell][Azure PowerShell].

1.  Tras instalar y configurar [Azure PowerShell][Azure PowerShell], cree un nuevo archivo llamado **hbase-runner.psm1**. Use el siguiente contenido para este archivo.

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

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
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

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este archivo contiene dos módulos:

    -   **Add-HDInsightFile**: Se usa para cargar archivos en HDInsight

    -   **Start-HBaseExample**: Se usa para ejecutar las clases creadas anteriormente

2.  Guarde el archivo **hbase-runner.psm1**.

3.  Abra una nueva ventana de Azure PowerShell, cambie los directorios por el directorio **hbaseapp** y luego ejecute el siguiente comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambie la ruta por la ubicación del archivo **hbase-runner.psm1** creado anteriormente. Esta acción registrará el módulo para esta sesión de PowerShell.

4.  Use el siguiente comando para cargar el archivo **hbaseapp-1.0-SNAPSHOT.jar** en el clúster de HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Sustituya **hdinsightclustername** por el nombre del clúster de HDInsight. El comando cargará luego el archivo **hbaseapp-1.0-SNAPSHOT.jar** en la ubicación **example/jars** del almacenamiento rpincipal del clúster de HDInsight.

5.  Cuando los archivos se hayan cargado, use el siguiente comando para crear una nueva tabla con **hbaseapp**.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Sustituya **hdinsightclustername** por el nombre del clúster de HDInsight.

    Este comando creará una nueva tabla llamada **people** en el clúster de HDInsight.

6.  Para buscar entradas en la tabla, use el siguiente comando.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Sustituya **hdinsightclustername** por el nombre del clúster de HDInsight.

    De esta forma se usará la clase SearchByEmail para buscar filas donde la familia de columnas **contactinformation**, columna **email**, contenga la cadena **contoso.com**. Debe recibir los siguientes resultados:

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    Usando **fabrikam.com** como valor de `-emailRegex` se devolverán los usuarios que tienen **fabrikam.com** en el campo de correo electrónico. Dado que esta búsqueda se implementa usando un filtro basado en una expresión regular, también puede escribir expresiones regulares como **^r**, lo cual devolverá entradas en las que el correo electrónico empiece por la letra 'r'.

## Eliminación de la tabla

Cuando haya terminado con el ejemplo, use el siguiente comando desde la sesión de PowerShell para eliminar la tabla **people** usada en este ejemplo.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Sustituya **hdinsightclustername** por el nombre del clúster de HDInsight.

## Solución de problemas

### Sin resultados o resultados inesperados al usar **Start-HBaseExample**

Use el parámetro `-showErr` para ver el STDERR producido mientras se ejecutaba el trabajo.

  [Apache HBase]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Java platform JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Un clúster de HDInsight de Azure con HBase]: /es-es/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [búsqueda del repositorio de Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [inicie una sesión de Escritorio remoto en el clúster de HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Carga de datos para trabajos de Hadoop en HDInsight]: /es-es/documentation/articles/hdinsight-upload-data/
  [Azure PowerShell]: /es-es/documentation/articles/install-configure-powershell/
