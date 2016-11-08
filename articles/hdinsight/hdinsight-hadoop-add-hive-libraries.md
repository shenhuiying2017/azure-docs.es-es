---
title: Incorporación de bibliotecas de Hive durante la creación de clústeres de HDInsight | Microsoft Docs
description: Aprenda a agregar bibliotecas de Hive (archivos JAR) a un clúster de HDInsight durante la creación del clúster.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr

---
# Incorporación de bibliotecas de Hive durante la creación de clústeres de HDInsight
Este documento contiene información sobre el uso de una acción de script para cargar previamente bibliotecas durante la creación de un clúster, por lo que puede resultarle interesante si dispone de bibliotecas que utiliza con frecuencia con Hive en HDInsight. Con ello se posibilita que las bibliotecas estén disponibles globalmente en Hive (sin necesidad de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para cargarlas).

## Cómo funciona
Cuando cree un clúster, también tiene la posibilidad de especificar opcionalmente una acción de script que ejecute un script en los nodos del clúster mientras estos se crean. El script de este documento acepta un único parámetro, que es una ubicación de WASB que contiene las bibliotecas (almacenadas como archivos JAR) que se cargarán previamente.

Durante la creación del clúster, el script enumera los archivos, los copia en el directorio `/usr/lib/customhivelibs/` de los nodos principal y de trabajo y luego los agrega a la propiedad `hive.aux.jars.path` en el archivo `core-site.xml`. En los clústeres basados en Linux, también actualiza el archivo `hive-env.sh` con la ubicación de los archivos.

> [!NOTE]
> El uso de las acciones de script de este artículo permite que las bibliotecas estén disponibles en las situaciones siguientes:
> 
> * **HDInsight basado en Linux**: cuando se usa la **línea de comandos de Hive**, **WebHCat** (Templeton) y **HiveServer2**.
> * **HDInsight basado en Windows**: cuando se usa la **línea de comandos de Hive** y **WebHCat** (Templeton).
> 
> 

## La secuencia de comandos
**Ubicación del script**

En los **clústeres basados en Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

En los **clústeres basados en Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Requisitos**

* Las scripts deben aplicarse tanto a los **nodos principales** como a los **nodos de trabajo**.
* Los archivos JAR que se van a instalar deben almacenarse en el Almacenamiento de blobs de Azure en un **único contenedor**.
* La cuenta de almacenamiento que contiene la biblioteca de archivos JAR **debe** vincularse al clúster de HDInsight durante la creación. Esto puede realizarse de dos maneras:
  
  * Mediante su ubicación en un contenedor de la cuenta de almacenamiento predeterminada para el clúster.
  * Mediante su ubicación en un contenedor de almacenamiento vinculado. Por ejemplo, en el Portal puede agregar almacenamiento adicional mediante **Configuración opcional**, **Cuentas de almacenamiento vinculadas**.
* La ruta de acceso de WASB al contenedor debe especificarse como un parámetro para la acción de script. Por ejemplo, si los archivos JAR se almacenan en un contenedor denominado **libs** en una cuenta de almacenamiento denominada **mystorage**, el parámetro sería \_\_wasbs://libs@mystorage.blob.core.windows.net/__.
  
  > [!NOTE]
  > En este documento se supone que ha creado ya una cuenta de almacenamiento, contenedora de blobs, y ha cargado los archivos en ella.
  > 
  > Si no ha creado una cuenta de almacenamiento, puede hacerlo a través del [Portal de Azure](https://portal.azure.com). A continuación, puede usar una utilidad como el [Explorador de almacenamiento de Azure](http://storageexplorer.com/) para crear un nuevo contenedor en la cuenta y cargar archivos en él.
  > 
  > 

## Creación de un clúster mediante el script
> [!NOTE]
> Al seguir estos pasos, se crea un nuevo clúster de HDInsight basado en Linux. Para crear un nuevo clúster basado en Windows, seleccione **Windows** como sistema operativo del clúster en el momento de su creación y use el script de Windows (PowerShell) en lugar del script de Bash.
> 
> También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no complete la operación.
2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información tal y como se muestra a continuación:
   
   * **NOMBRE**: escriba un nombre descriptivo para la acción de script.
   * **URI DE SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
   * **PRINCIPAL**: active esta opción.
   * **TRABAJO**: active esta opción.
   * **ZOOKEEPER**: déjelo en blanco.
   * **PARÁMETROS**: escriba la dirección WASB que dirige al contenedor y la cuenta de almacenamiento que contiene los archivos JAR. Por ejemplo, \_\_wasbs://libs@mystorage.blob.core.windows.net/__.
3. En la parte inferior de **Acciones de script**, use el botón **Seleccionar** para guardar la configuración.
4. En la hoja **Configuración opcional**, seleccione **Cuentas de almacenamiento vinculadas** y luego el vínculo **Agregar una clave de almacenamiento**. Seleccione la cuenta de almacenamiento que contiene los archivos JAR y, a continuación, utilice los botones de **selección** para guardar la configuración y volver a la hoja **Configuración opcional**.
5. Use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.
6. Continúe aprovisionando el clúster tal como se describe en [Aprovisionamiento de clústeres de HDInsight n Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Una vez finalizada la creación del clúster, podrá utilizar los archivos JAR agregados a través de este script desde Hive sin tener que utilizar la instrucción `ADD JAR`.

## Pasos siguientes
En este documento ha aprendido a agregar bibliotecas de Hive contenidas en archivos JAR a un clúster de HDInsight durante la creación del clúster. Para obtener más información acerca del trabajo con Hive, consulte [Use Hive with HDInsight](hdinsight-use-hive.md) (Uso de Hive con HDInsight).

<!---HONumber=AcomDC_0921_2016-->