<properties
pageTitle="Uso de una función de Java definida por el usuario (UDF) con Hive en HDInsight | Microsoft Azure"
description="Aprenda a crear y utilizar una función de Java definida por el usuario (UDF) con Hive en HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/07/2016"
ms.author="larryfr"/>

#Utilización de una función definida por el usuario de Java con Hive en HDInsight

Hive resulta excelente para trabajar con datos en HDInsight pero, en ocasiones, se necesita un lenguaje con una finalidad más general. Hive le permite crear funciones definidas por el usuario (UDF) mediante diversos lenguajes de programación. En este documento, aprenderá a usar una función definida por el usuario de Java en Hive.

## Requisitos

* Una suscripción de Azure

* Un clúster de HDInsight (Windows o Linux)

    > [AZURE.NOTE] La mayoría de los pasos de este documento sirven para ambos tipos de clúster, sin embargo, los pasos utilizados para cargar la función definida por el usuario compilada al clúster y ejecutarla son específicos de los clústeres basados en Linux. Se proporcionan vínculos a información que puede utilizarse con clústeres basados en Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o posterior (o un equivalente como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Un editor de texto o IDE de Java

    > [AZURE.IMPORTANT] Si está utilizando un servidor HDInsight basado en Linux, pero creará los archivos de Python en un cliente Windows, debe utilizar un editor que utilice LF como final de línea. Si no está seguro de si el editor utiliza LF o CRLF, consulte la sección [Solución de problemas](#troubleshooting) para conocer los pasos que debe realizar para quitar el carácter CR con las utilidades del clúster de HDInsight.

## Creación de una función definida por el usuario de ejemplo

1. Desde una línea de comandos, utilice lo siguiente para crear un nuevo proyecto de Maven:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Si va a usar PowerShell, deberá colocar comillas alrededor de los parámetros. Por ejemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Esto creará un nuevo directorio denominado __exampleudf__, que contiene el proyecto de Maven.

2. Una vez creado el proyecto, elimine el directorio __exampleudf/src/test__ que se creó como parte del proyecto ya que no se utilizará para este ejemplo.

3. Abra el archivo __exampleudf/pom.xml__ y reemplace la entrada `<dependencies>` ya existente con lo siguiente:

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

    Estas entradas especifican la versión de Hadoop y Hive incluida con los clústeres de HDInsight 3.3 y 3.4. Puede encontrar información sobre las versiones de Hadoop y Hive proporcionadas con HDInsight en el artículo [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?](hdinsight-component-versioning.md).

    Después de realizar este cambio, guarde el archivo.

4. Cambie el nombre de __exampleudf/src/main/java/com/microsoft/examples/App.java__ a __ExampleUDF.java__ y, después, abra el archivo en el editor.

5. Reemplace el contenido del archivo __ExampleUDF.java__ por el código siguiente y, a continuación, guarde el archivo.

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

## Compilación e instalación de la función definida por el usuario

1. Utilice el siguiente comando para compilar y empaquetar la función definida por el usuario:

        mvn compile package

    Este compilará y empaquetará la UDF en __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Utilice el comando `scp` para copiar el archivo en el clúster de HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Reemplace __myuser__ por la cuenta de usuario SSH del clúster. Reemplace __mycluster__ por el nombre del clúster. Si usó una contraseña para proteger la cuenta SSH, se le pedirá que escriba la contraseña. Si utilizó un certificado, tal vez tenga que usar el parámetro `-i` para especificar el archivo de claves privadas.

3. Conéctese al clúster con SSH.

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes.

    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Desde la sesión SSH, copie el archivo jar en el almacenamiento de HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## Uso de la función definida por el usuario desde Hive

1. Use lo siguiente para iniciar el cliente Beeline desde la sesión SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Este comando supone que usó el valor predeterminado de __admin__ para la cuenta de inicio de sesión del clúster.

2. Una vez alcanzado el aviso `jdbc:hive2://localhost:10001/>`, escriba lo siguiente para agregar la función definida por el usuario a Hive y exponerla como una función.

        ADD JAR wasbs:///example/jar/ExampleUDF-1.0-SNAPSHOT.jar;
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

## Pasos siguientes

Para conocer otras formas de trabajar con Hive, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

Para más información sobre las funciones definidas por el usuario de Hive, consulte la sección [Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Operadores de Hive y funciones definidas por el usuario) de la wiki de Hive en apache.org.

<!---HONumber=AcomDC_0727_2016-->