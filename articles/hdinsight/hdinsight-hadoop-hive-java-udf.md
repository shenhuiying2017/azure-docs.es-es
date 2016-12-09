---
title: "Uso de una función de Java definida por el usuario (UDF) con Hive en HDInsight | Microsoft Docs"
description: "Aprenda a crear y utilizar una función de Java definida por el usuario (UDF) con Hive en HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 240ba2289ededf7a65c71e2f5007cf3a52ca4f84


---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilización de una función definida por el usuario de Java con Hive en HDInsight
Hive resulta excelente para trabajar con datos en HDInsight pero, en ocasiones, se necesita un lenguaje con una finalidad más general. Hive le permite crear funciones definidas por el usuario (UDF) mediante diversos lenguajes de programación. En este documento, aprenderá a usar una función definida por el usuario de Java en Hive.

## <a name="requirements"></a>Requisitos
* Una suscripción de Azure
* Un clúster de HDInsight (Windows o Linux)
  
  > [!NOTE]
  > La mayoría de los pasos de este documento sirven para ambos tipos de clúster, sin embargo, los pasos utilizados para cargar la función definida por el usuario compilada al clúster y ejecutarla son específicos de los clústeres basados en Linux. Se proporcionan vínculos a información que puede utilizarse con clústeres basados en Windows.
  > 
  > 
* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o posterior (o un equivalente como OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* Un editor de texto o IDE de Java
  
  > [!IMPORTANT]
  > Si está utilizando un servidor HDInsight basado en Linux, pero creará los archivos de Python en un cliente Windows, debe utilizar un editor que utilice LF como final de línea. Si no está seguro de si el editor utiliza LF o CRLF, consulte la sección [Solución de problemas](#troubleshooting) para conocer los pasos que debe realizar para quitar el carácter CR con las utilidades del clúster de HDInsight.
  > 
  > 

## <a name="create-an-example-udf"></a>Creación de una función definida por el usuario de ejemplo
1. Desde una línea de comandos, utilice lo siguiente para crear un nuevo proyecto de Maven:
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > Si va a usar PowerShell, deberá colocar comillas alrededor de los parámetros. Por ejemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.
   > 
   > 
   
    Esto creará un nuevo directorio denominado **exampleudf**, que contiene el proyecto de Maven.
2. Una vez creado el proyecto, elimine el directorio **exampleudf/src/test** que se creó como parte del proyecto ya que no se utilizará para este ejemplo.
3. Abra el archivo **exampleudf/pom.xml** y reemplace la entrada `<dependencies>` ya existente con lo siguiente:
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    Estas entradas especifican la versión de Hadoop y Hive incluida con los clústeres de HDInsight 3.3 y 3.4. Puede encontrar información sobre las versiones de Hadoop y Hive proporcionadas con HDInsight en el artículo [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md) .
   
    Agregue una sección `<build>` antes de la línea `</project>` al final del archivo. Esta sección debe contener lo siguiente:
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
                        <filters>
                            <filter>
                                <artifact>*:*</artifact>
                                <excludes>
                                    <exclude>META-INF/*.SF</exclude>
                                    <exclude>META-INF/*.DSA</exclude>
                                    <exclude>META-INF/*.RSA</exclude>
                                </excludes>
                            </filter>
                        </filters>
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
   
    Estas entradas definen cómo compilar el proyecto. Específicamente, la versión de Java que el proyecto usa y cómo compilar un archivo uberjar para implementarlo en el clúster.
   
    Guarde el archivo una vez hechos los cambios.
4. Cambie el nombre de **exampleudf/src/main/java/com/microsoft/examples/App.java** a **ExampleUDF.java** y, después, abra el archivo en el editor.
5. Reemplace el contenido del archivo **ExampleUDF.java** por el código siguiente y, a continuación, guarde el archivo.
   
        package com.microsoft.examples;
   
        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;
   
        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }
   
    Esto implementa una función definida por el usuario que acepta un valor de cadena y devuelve una versión en minúsculas de esta.

## <a name="build-and-install-the-udf"></a>Compilación e instalación de la función definida por el usuario
1. Utilice el siguiente comando para compilar y empaquetar la función definida por el usuario:
   
        mvn compile package
   
    Este compilará y empaquetará la UDF en **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**.
2. Utilice el comando `scp` para copiar el archivo en el clúster de HDInsight.
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    Reemplace **myuser** por la cuenta de usuario SSH del clúster. Reemplace **mycluster** por el nombre del clúster. Si usó una contraseña para proteger la cuenta SSH, se le pedirá que escriba la contraseña. Si utilizó un certificado, tal vez tenga que usar el parámetro `-i` para especificar el archivo de claves privadas.
3. Conéctese al clúster con SSH. 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes.
   
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
4. Desde la sesión SSH, copie el archivo jar en el almacenamiento de HDInsight.
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Uso de la función definida por el usuario desde Hive
1. Use lo siguiente para iniciar el cliente Beeline desde la sesión SSH.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Este comando supone que usó el valor predeterminado de **admin** para la cuenta de inicio de sesión del clúster.
2. Una vez alcanzado el aviso `jdbc:hive2://localhost:10001/>` , escriba lo siguiente para agregar la función definida por el usuario a Hive y exponerla como una función.
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. Use la función definida por el usuario para convertir los valores recuperados de una tabla a cadenas de minúsculas.
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    Esto seleccionará la plataforma del dispositivo (Android, Windows, iOS, etc.) de la tabla, convertirá la cadena a minúsculas y, a continuación, las mostrará. La salida tendrá un aspecto similar al siguiente.
   
        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Pasos siguientes
Para conocer otras formas de trabajar con Hive, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

Para más información sobre las funciones definidas por el usuario de Hive, consulte la sección [Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Operadores de Hive y funciones definidas por el usuario) de la wiki de Hive en apache.org.




<!--HONumber=Nov16_HO3-->


