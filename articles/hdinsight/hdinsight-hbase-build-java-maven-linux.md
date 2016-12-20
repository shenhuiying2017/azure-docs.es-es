---
title: "Creación de una aplicación de HBase con Maven y Java, y posterior implementación en HDInsight basado en Linux | Microsoft Docs"
description: "Aprenda a usar Apache Maven para compilar una aplicación de Apache HBase basada en Java e implementarla después en HDInsight basado en Linux en la nube de Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: 
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0f321065b9c24075837bebb71251cbc5751a1854


---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Uso de Maven para compilar aplicaciones Java que utilicen HBase con HDInsight basado en Linux (Hadoop)
Aprenda a crear y compilar una aplicación de [Apache HBase](http://hbase.apache.org/) en Java con Apache Maven. A continuación, use la aplicación con un clúster de HDInsight basado en Linux.

[Maven](http://maven.apache.org/) es una herramienta de administración y comprensión de proyectos de software que le permite compilar software, documentación e informes para proyectos Java. En este artículo, aprenderá a usarla para crear una aplicación Java básica que crea, consulta y elimina una tabla de HBase en un clúster de HDInsight basado en Linux.

> [!NOTE]
> En este documento se da por hecho que usa un clúster de HDInsight basado en Linux. Para obtener información sobre cómo utilizar un clúster de HDInsight basado en Windows, consulte [Uso de Maven para compilar aplicaciones Java que utilicen HBase con HDInsight basado en Windows (Hadoop)](hdinsight-hbase-build-java-maven.md)
> 
> 

## <a name="requirements"></a>Requisitos
* [JDK de la plataforma Java 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o posterior
* [Maven](http://maven.apache.org/)
* [Un clúster de Azure HDInsight basado en Linux con HBase](hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)
  
  > [!NOTE]
  > Los pasos descritos en este documento se han probado con las versiones 3.2, 3.3 y 3.4 del clúster de HDInsight. Los valores predeterminados proporcionados en los ejemplos corresponden a la versión 3.4 de un clúster de HDInsight.
  > 
  > 
* **Familiaridad con SSH y SCP**. Para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:
  
  * **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md)
  * **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="create-the-project"></a>Creación del proyecto
1. Desde la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la que desea crear el proyecto, por ejemplo, `cd code/hdinsight`.
2. Use el comando **mvn** , que se instala con Maven, para generar el scaffolding del proyecto.
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    Esta acción crea un directorio en el directorio actual, con el nombre especificado por el parámetro **artifactID** (**hbaseapp** en este ejemplo). Este directorio contendrá los siguientes elementos:
   
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
   
    Esta acción le indica a Maven que el proyecto requiere la versión **1.1.2** de **hbase-client**. En el momento de compilación, esto se descargará desde el repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio central de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para ver más información sobre esta dependencia.
   
   > [!IMPORTANT]
   > El número de versión debe coincidir con la versión de HBase que se proporciona con el clúster de HDInsight. Utilice la siguiente tabla para buscar el número de versión correcto.
   > 
   > 
   
   | Versión del clúster de HDInsight | Versión de HBase que se va a utilizar |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 y 3.4 |1.1.2 |
   
    Para obtener más información sobre las versiones de HDInsight y los componentes, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md)
2. Si está usando la versión 3.3 o 3.4 de un clúster de HDInsight, también debe agregar lo siguiente a la sección `<dependencies>` :
   
        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
   
    Con esto, se cargarán los componentes principales de Phoenix, que utilizan versiones 1.1.x de Hbase.
3. Agregue el siguiente código al archivo **pom.xml**. Esto debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.
   
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
   
    Esta acción configura un recurso (**conf\hbase-site.xml**) que contiene información de configuración para HBase.
   
   > [!NOTE]
   > También puede establecer los valores de configuración mediante código. Vea los comentarios del ejemplo **CreateTable** que sigue para ver cómo hacer esto.
   > 
   > 
   
    Esta acción también configura [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) y [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). El complemento compiler se usa para compilar la topología. El complemento shade se usa para evitar la duplicación de licencias en el paquete JAR compilado por Maven. La razón de usar este complemento es que los archivos de licencia duplicados pueden provocar un error en tiempo de ejecución en el clúster de HDInsight. El uso del complemento maven-shade-plugin con la implementación de `ApacheLicenseResourceTransformer` evita este error.
   
    El complemento maven-shade-plugin también producirá un uberjar (o fatjar), que contiene todas las dependencias que necesita la aplicación.
4. Guarde el archivo **pom.xml** .
5. Cree un nuevo directorio llamado **conf** en el directorio **hbaseapp**. Se utilizará para almacenar información de configuración para conectarse a HBase.
6. Use el siguiente comando para copiar la configuración de HBase desde el servidor de HDInsight en el directorio **conf**. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.
   
        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
   
   > [!NOTE]
   > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
   > 
   > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`
   > 
   > 

## <a name="create-the-application"></a>Creación de la aplicación
1. Vaya al directorio **hbaseapp\src\main\java\com\microsoft\examples** y cambie el nombre del archivo app.java a **CreateTable.java**.
2. Abra el archivo **CreateTable.java** y reemplace el contenido existente por lo siguiente:
   
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
   
    Esta es la clase **CreateTable**, que creará una tabla llamada **people** y la rellenará con algunos usuarios predefinidos.
3. Guarde el archivo **CreateTable.java**.
4. En el directorio **hbaseapp\src\main\java\com\microsoft\examples**, cree un nuevo archivo denominado **SearchByEmail.java**. Use el siguiente contenido para este archivo:
   
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
6. En el directorio **hbaseapp\src\main\hava\com\microsoft\examples**, cree un nuevo archivo denominado **DeleteTable.java**. Use el siguiente contenido para este archivo:
   
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
1. En el directorio **hbaseapp**, use el siguiente comando para compilar un archivo JAR que contenga la aplicación:
   
        mvn clean package
   
    Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y luego compilará y empaquetará la aplicación.
2. Cuando el comando termine de ejecutarse, el directorio **hbaseapp\target** contendrá un archivo llamado **hbaseapp-1.0-SNAPSHOT.jar**.
   
   > [!NOTE]
   > El archivo **hbaseapp-1.0-SNAPSHOT.jar** es un uberjar (en ocasiones llamado fatjar) que contiene todas las dependencias necesarias para ejecutar la aplicación.
   > 
   > 

## <a name="upload-the-jar-file-and-run-jobs"></a>Carga del archivo JAR y ejecución de trabajos
1. Use el siguiente comando para cargar el archivo .jar en el clúster de HDInsight: Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.
   
        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    De este modo se cargará el archivo en el directorio particular de su cuenta de usuario SSH.
   
   > [!NOTE]
   > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
   > 
   > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`
   > 
   > 
2. Use SSH para conectarse al clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   > 
   > 
3. Una vez conectado, use el código siguiente para crear una tabla HBase mediante la aplicación de Java:
   
        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
   
    De este modo, se creará una tabla HBase denominada **people**, y se rellenará con datos.
4. A continuación, use el código siguiente para buscar direcciones de correo electrónico que se almacenan en la tabla:
   
        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
   
    Debe recibir los siguientes resultados:
   
        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

## <a name="delete-the-table"></a>Eliminación de la tabla
Cuando haya terminado con el ejemplo, use el siguiente comando desde la sesión de Azure PowerShell para eliminar la tabla **people** usada en este ejemplo:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable




<!--HONumber=Nov16_HO3-->


