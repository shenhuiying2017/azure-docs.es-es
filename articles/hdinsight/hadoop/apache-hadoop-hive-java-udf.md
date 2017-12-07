---
title: "Uso de una función definida por el usuario (UDF) de Java con Hive en HDInsight - Azure | Microsoft Docs"
description: "Aprenda cómo crear una función basada en Java y definida por el usuario (UDF) que funcione con Hive. Esta función de ejemplo definida por el usuario convierte una tabla de cadenas de texto en minúsculas."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: 20473f5e462bfcace9c982786b771e01df337eab
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilización de una función definida por el usuario de Java con Hive en HDInsight

Aprenda cómo crear una función basada en Java y definida por el usuario (UDF) que funcione con Hive. La función de Java definida por el usuario de este ejemplo convierte una tabla de cadenas de texto a caracteres en minúscula.

## <a name="requirements"></a>Requisitos

* Un clúster de HDInsight. 

    > [!IMPORTANT]
    > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    La mayoría de los pasos de este documento funcionan tanto en clústeres basados en Windows como en Linux. Sin embargo, los pasos utilizados para cargar la función definida por el usuario compilada al clúster y ejecutarla son específicos de los clústeres basados en Linux. Se proporcionan vínculos a información que puede utilizarse con clústeres basados en Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 o posterior (o un equivalente como OpenJDK).

* [Apache Maven](http://maven.apache.org/)

* Un editor de texto o IDE de Java

    > [!IMPORTANT]
    > Si crea los archivos de Python en un cliente Windows, debe usar un editor que emplee LF como final de línea. Si no está seguro de si el editor usa LF o CRLF, vea la sección [Solución de problemas](#troubleshooting) para conocer los pasos a seguir para quitar el carácter CR.

## <a name="create-an-example-java-udf"></a>Crear una función de Java definida por el usuario de ejemplo 

1. Desde una línea de comandos, utilice lo siguiente para crear un nuevo proyecto de Maven:

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Si va a usar PowerShell, deberá colocar comillas alrededor de los parámetros. Por ejemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Este comando crea un directorio denominado **exampleudf**, que contiene el proyecto Maven.

2. Una vez creado el proyecto, elimine el directorio **exampleudf/src/test** que se creó como parte del proyecto.

3. Abra **exampleudf/pom.xml** y sustituya la entrada `<dependencies>` existente por el XML siguiente:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Estas entradas especifican la versión de Hadoop y Hive incluida con el clúster de HDInsight 3.5. Puede encontrar información sobre las versiones de Hadoop y Hive proporcionadas con HDInsight en el artículo [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](../hdinsight-component-versioning.md) .

    Agregue una sección `<build>` antes de la línea `</project>` al final del archivo. Esta sección debe contener el siguiente XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.5  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
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
    ```

    Estas entradas definen cómo compilar el proyecto. Específicamente, la versión de Java que el proyecto usa y cómo compilar un archivo uberjar para implementarlo en el clúster.

    Guarde el archivo una vez hechos los cambios.

4. Cambie el nombre de **exampleudf/src/main/java/com/microsoft/examples/App.java** a **ExampleUDF.java** y, después, abra el archivo en el editor.

5. Reemplace el contenido del archivo **ExampleUDF.java** por el código siguiente y, a continuación, guarde el archivo.

    ```java
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
    ```

    Este código implementa una función definida por el usuario que acepta un valor de cadena y devuelve una versión en minúsculas de esta.

## <a name="build-and-install-the-udf"></a>Compilación e instalación de la función definida por el usuario

1. Utilice el siguiente comando para compilar y empaquetar la función definida por el usuario:

    ```bash
    mvn compile package
    ```

    Este comando compila y empaqueta la función definida por el usuario en el archivo `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Utilice el comando `scp` para copiar el archivo en el clúster de HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Sustituya `myuser` por la cuenta de usuario SSH del clúster. Reemplace `mycluster` por el nombre del clúster. Si usa una contraseña para proteger la cuenta SSH, se le solicita que escriba la contraseña. Si utilizó un certificado, tal vez tenga que usar el parámetro `-i` para especificar el archivo de claves privadas.

3. Conéctese al clúster con SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Desde la sesión SSH, copie el archivo jar en el almacenamiento de HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Uso de la función definida por el usuario desde Hive

1. Use lo siguiente para iniciar el cliente Beeline desde la sesión SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Este comando supone que usó el valor predeterminado de **admin** para la cuenta de inicio de sesión del clúster.

2. Una vez alcanzado el aviso `jdbc:hive2://localhost:10001/>` , escriba lo siguiente para agregar la función definida por el usuario a Hive y exponerla como una función.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > En este ejemplo se da por supuesto que el almacenamiento predeterminado para el clúster es Azure Storage. Si su clúster utiliza Data Lake Store en su lugar, cambie el valor `wasb:///` a `adl:///`.

3. Use la función definida por el usuario para convertir los valores recuperados de una tabla a cadenas de minúsculas.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Esta consulta seleccionará la plataforma del dispositivo (Android, Windows, iOS, etc.) de la tabla, convertirá la cadena a minúsculas y, a continuación, las mostrará. La salida es similar al siguiente texto:

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
