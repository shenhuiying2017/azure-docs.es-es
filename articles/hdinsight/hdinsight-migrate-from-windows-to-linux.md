---
title: "Migración desde HDInsight basado en Windows a HDInsight basado en Linux: Azure | Microsoft Docs"
description: "Más información sobre cómo migrar desde un clúster de HDInsight basado en Windows a un clúster de HDInsight basado en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: 764a41dc9e890de85c3bfab3d2f78d5a07b39dff
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migración desde un clúster de HDInsight basado en Windows a un clúster basado en Linux

En este documento se ofrecen detalles sobre las diferencias entre HDInsight en Windows y Linux. También se proporcionan instrucciones sobre cómo migrar las cargas de trabajo existentes a un clúster basado en Linux.

Si bien HDInsight basado en Windows permite usar fácilmente Hadoop en la nube, tal vez necesite migrar a un clúster basado en Linux. Por ejemplo, para aprovechar las ventajas de las herramientas basadas en Linux y las tecnologías que son necesarias para su solución. Muchos de los elementos del ecosistema de Hadoop se desarrollan en sistemas basados en Linux, y puede que no estén disponibles para su uso con HDInsight basado en Windows. En muchos libros, vídeos y otros materiales de aprendizaje se da por sentado que cuando se trabaja con Hadoop se usa un sistema Linux.

> [!NOTE]
> Los clústeres de HDInsight usan Ubuntu Long Term Support (LTS) como el sistema operativo de los nodos del clúster. Para obtener información sobre la versión de Ubuntu disponible con HDInsight, junto con otra información de control de versiones del componente, consulte el artículo sobre [versiones de componentes de HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tareas de migración

El flujo de trabajo general de migración es el siguiente.

![Diagrama de flujo de trabajo de migración](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Lea cada sección de este documento para entender los cambios que pueden ser necesarios al realizar la migración.

2. Cree un clúster basado en Linux como entorno de control de calidad o de pruebas. Para obtener más información sobre cómo crear un clúster basado en Linux, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copie los trabajos, los orígenes de datos y los receptores existentes en el nuevo entorno.

4. Realice pruebas de validación para asegurarse de que los trabajos funcionan como se esperaba en el nuevo clúster.

Cuando haya comprobado que todo funciona según lo esperado, programe el tiempo de inactividad para la migración. Durante este tiempo de inactividad, realice las acciones siguientes:

1. Haga copia de seguridad de todos los datos transitorios almacenados localmente en los nodos del clúster. Por ejemplo, si tiene datos que se almacenan directamente en un nodo principal.

2. Elimine el clúster basado en Windows.

3. Cree un clúster basado en Linux con el mismo almacén de datos predeterminado que se utilizaba en el clúster basado en Windows. El clúster basado en Linux puede seguir trabajando con sus datos de producción existentes.

4. Importe los datos transitorios cuya copia de seguridad realizó.

5. Inicie trabajos o continúe el procesamiento con el nuevo clúster.

### <a name="copy-data-to-the-test-environment"></a>Copia de datos en el entorno de prueba

Existen muchos métodos para copiar los datos y los trabajos, pero los dos que se describen en esta sección son los métodos más sencillos para mover archivos directamente a un clúster de prueba.

#### <a name="hdfs-copy"></a>Copia de HDFS

Siga estos pasos para copiar datos desde el clúster de producción al clúster de prueba. Estos pasos utilizan la utilidad `hdfs dfs` que se incluye con HDInsight.

1. Busque la información de contenedor predeterminado y de cuenta de almacenamiento del clúster existente. En el ejemplo siguiente, se usa PowerShell para recuperar esta información:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Para crear un entorno de prueba, siga los pasos indicados en el documento Creación de clústeres basados en Linux en HDInsight. Deténgase antes de crear el clúster y, en su lugar, seleccione **Configuración opcional**.

3. En la sección Configuración opcional, seleccione **Cuentas de almacenamiento vinculadas**.

4. Seleccione **Agregar una clave de almacenamiento**y, cuando se le pida, la cuenta de almacenamiento que devolvió el script de PowerShell en el paso 1. Haga clic en **Seleccionar** en cada sección. Por último, cree el clúster.

5. Una vez creado el clúster, conéctese a él utilizando **SSH**. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. En la sesión de SSH, use el siguiente comando para copiar los archivos de la cuenta de almacenamiento vinculada a la nueva cuenta de almacenamiento predeterminada. Reemplace el CONTENEDOR con la información del contenedor devuelta por PowerShell. Reemplace __CUENTA__ con el nombre de cuenta. Reemplace la ruta de acceso a datos por la ruta de acceso a un archivo de datos.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Si la estructura de directorios que contiene los datos no existe en el entorno de prueba, puede crearla con el comando siguiente:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    El conmutador `-p` permite crear todos los directorios de la ruta de acceso.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Copia directa entre blobs de Azure Storage

También puede utilizar el cmdlet de Azure PowerShell `Start-AzureStorageBlobCopy` para copiar blobs entre cuentas de almacenamiento fuera de HDInsight. Para más información, consulte la sección Administración de blobs de Azure de Uso de Azure PowerShell con Azure Storage.

## <a name="client-side-technologies"></a>Tecnologías de cliente

Las tecnologías en el lado del cliente como [Azure PowerShell cmdlets](/powershell/azureps-cmdlets-docs), [CLI de Azure](../cli-install-nodejs.md) o [.NET SDK for Hadoop](https://hadoopsdk.codeplex.com/) siguen funcionando con clústeres basados en Linux. Estas tecnologías se basan en API de REST que son iguales en los dos tipos del SO del clúster.

## <a name="server-side-technologies"></a>Tecnologías de servidor

En la tabla siguiente se ofrece orientación sobre cómo migrar componentes de servidor que son específicos de Windows.

| Si utiliza esta tecnología... | Realice esta acción... |
| --- | --- |
| **PowerShell** (scripts de servidor, incluidas las acciones de script que se usan durante la creación del clúster) |Vuelva a escribirlos como scripts de Bash. Para acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md) y [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **CLI de Azure** (scripts de servidor) |Mientras que la CLI de Azure está disponible en Linux, no viene preinstalada en los nodos principales del clúster de HDInsight. Para obtener más información sobre la instalación de la CLI de Azure, consulte [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Introducción a la CLI de Azure 2.0). |
| **Componentes de .NET** |.NET no es compatible con HDInsight basado en Linux a través de [Mono](https://mono-project.com). Para obtener más información, consulte [Migración de soluciones .​NE​T para HDInsight basado en ​Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Componentes de Win32 o de otras tecnologías de Windows** |Las indicaciones dependen del componente o tecnología. Puede que encuentre una versión compatible con Linux. De lo contrario, debe encontrar una solución alternativa o regenerar este componente. |

> [!IMPORTANT]
> El SDK de administración de HDInsight no es totalmente compatible con Mono. No debe usarse como parte de las soluciones que se implementan en el clúster de HDInsight.

## <a name="cluster-creation"></a>Creación de clústeres

Esta sección ofrece información sobre las diferencias en la creación de clústeres.

### <a name="ssh-user"></a>Usuario de SSH

Los clústeres de HDInsight basados en Linux usan el protocolo **Secure Shell (SSH)** para proporcionar acceso remoto a los nodos del clúster. A diferencia de Escritorio remoto para clústeres basados en Windows, la mayoría de clientes de SSH no ofrece experiencia de usuario gráfica. En su lugar, los clientes de SSH ofrecen una línea de comandos que le permite ejecutar comandos en el clúster. Algunos clientes (como [MobaXterm](http://mobaxterm.mobatek.net/)) ofrecen un explorador gráfico del sistema de archivos, además de una línea de comandos remota.

Durante la creación del clúster, debe especificar un usuario SSH y una **contraseña**, o bien un **certificado de clave pública** para la autenticación.

Se recomienda usar el certificado de clave pública, ya que es más seguro que usar una contraseña. La autenticación de certificado funciona generando un par de claves pública y privada con signo y especificando la clave pública al crear el clúster. Al conectarse al servidor mediante SSH, la clave privada en el cliente proporciona autenticación para la conexión.

Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalización del clúster

**acciones de script** se utilizan con clústeres basados en Linux y deben escribirse en script de Bash. Los clústeres basados en Linux pueden usar acciones de script durante la creación del clúster o después. Para más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md) y [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

Otra característica de personalización es **arranque**. En el caso de clústeres de Windows, esta función permite especificar la ubicación de bibliotecas adicionales para su uso con Hive. Después de crear el clúster, estas bibliotecas están automáticamente disponibles para usarse con consultas de Hive sin necesidad de utilizar `ADD JAR`.

La función de arranque para clústeres basados en Linux no proporciona esta funcionalidad. En su lugar, use la acción de script que se documenta en [Incorporación de bibliotecas de Hive durante la creación del clúster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtual Networks

Los clústeres de HDInsight basados en Windows solo funcionan con redes virtuales clásicas, mientras que los clústeres HDInsight basados en Linux requieren redes virtuales de Resource Manager. Si tiene recursos en una red virtual clásica a la que debe conectarse el clúster de HDInsight en Linux, consulte [Conexión de una red virtual clásica a una red virtual de Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para más información sobre los requisitos de configuración, vea [Extensión de las funcionalidades de HDInsight con una red virtual](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Administración y supervisión

Es posible que muchas de las interfaces de usuario web que haya utilizado con HDInsight basado en Windows, como el historial de trabajos o la interfaz de usuario de Yarn, estén disponibles a través de Ambari. Además, la vista Ambari Hive ofrece una forma de ejecutar consultas de Hive mediante el explorador web. La interfaz de usuario web de Ambari está disponible en los clústeres basados en Linux que crea en https://CLUSTERNAME.azurehdinsight.net.

Para más información sobre cómo trabajar con Ambari, consulte los documentos siguientes:

* [Web de Ambari](hdinsight-hadoop-manage-ambari.md)
* [API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas de Ambari

Ambari tiene un sistema de alertas que puede indicarle posibles problemas con el clúster. Las alertas aparecen como entradas en rojo o amarillo en la interfaz de usuario de Web de Ambari, pero también se pueden recuperar a través de la API de REST.

> [!IMPORTANT]
> Las alertas de Ambari indican que *puede* que haya un problema, no que *exista* realmente. Por ejemplo, puede recibir una alerta que indica que no se puede tener acceso a HiveServer2, aunque pueda tener acceso a él normalmente.
>
> Las alertas se implementan como consultas basadas en intervalos en un servicio y esperan una respuesta en un plazo de tiempo específico. Por lo que la alerta no necesariamente significa que el servicio no funcione, sino que no devuelve resultados en el plazo previsto.

## <a name="file-system-locations"></a>Ubicaciones del sistema de archivos

El sistema de archivos de clústeres de Linux se distribuye de manera diferente que en los clústeres de HDInsight basado en Windows. Utilice la siguiente tabla para encontrar archivos de uso habitual.

| Hay que encontrar... | Se encuentra... |
| --- | --- |
| Configuración |`/etc`. Por ejemplo, `/etc/hadoop/conf/core-site.xml` |
| Archivos de registro |`/var/logs` |
| HortonWorks Data Platform (HDP) |`/usr/hdp`Aquí hay dos directorios ubicados, uno que corresponde a la versión HDP actual y `current`. El directorio `current` contiene los vínculos simbólicos a archivos y directorios que se encuentran en el directorio del número de versión. El directorio `current` sirve para acceder cómodamente a archivos HDP, ya que el número de versión cambia conforme se actualiza la versión de HDP. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Por lo general, si conoce el nombre del archivo, puede utilizar el siguiente comando desde una sesión de SSH para encontrar la ruta de archivo:

    find / -name FILENAME 2>/dev/null

También puede utilizar caracteres comodín con el nombre de archivo. Por ejemplo, `find / -name *streaming*.jar 2>/dev/null` devuelve la ruta de acceso a los archivos JAR que contienen la palabra "streaming" en el nombre de archivo.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig y MapReduce

Las cargas de trabajo de Pig y MapReduce son similares en clústeres basados en Linux. Sin embargo, los clústeres de HDInsight basados en Linux se pueden crear usando versiones más recientes de Hadoop, Hive y Pig. Estas diferencias de versión pueden provocar cambios en el funcionamiento de las soluciones existentes. Para obtener más información sobre las versiones de los componentes incluidos en HDInsight, consulte el artículo relativo a las [versiones de componentes de HDInsight](hdinsight-component-versioning.md).

HDInsight basado en Linux no proporciona funcionalidad de escritorio remoto. En su lugar, puede usar SSH para conectar remotamente con los nodos principales del clúster. Para obtener más información, vea los documentos siguientes:

* [Uso de Hive con SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Uso de Pig con SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Uso de MapReduce con SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Si utiliza una tienda de metadatos de Hive externa, debe hacer una copia de seguridad de la tienda de metadatos antes de usarla con HDInsight basado en Linux. HDInsight basado en Linux está disponible con las versiones más recientes de Hive, lo que puede provocar incompatibilidades con las tiendas de metadatos creadas con versiones anteriores.

El gráfico siguiente ofrece orientación sobre cómo migrar las cargas de trabajo de Hive.

| En basado en Windows, se usa... | En basado en Linux... |
| --- | --- |
| **Editor de Hive** |[Vista de Hive en Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` para habilitar Tez |Tez es el motor de ejecución predeterminado para clústeres basados en Linux, por lo que ya no se necesita la instrucción set. |
| Funciones definidas por el usuario de C# | Para obtener información sobre cómo validar los componentes de C# con HDInsight basado en Linux, consulte [Migración de soluciones .​NE​T para HDInsight basado en ​Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts o archivos CMD en el servidor que se invoca como parte de un trabajo de Hive |se usan scripts de Bash |
| `hive` desde Escritorio remoto |Uso de [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) o [Hive en una sesión de SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| En basado en Windows, se usa... | En basado en Linux... |
| --- | --- |
| Funciones definidas por el usuario de C# | Para obtener información sobre cómo validar los componentes de C# con HDInsight basado en Linux, consulte [Migración de soluciones .​NE​T para HDInsight basado en ​Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts o archivos CMD en el servidor que se invocan como parte de un trabajo de Pig |se usan scripts de Bash |

### <a name="mapreduce"></a>MapReduce

| En basado en Windows, se usa... | En basado en Linux... |
| --- | --- |
| Componentes asignadores y reductores de C# | Para obtener información sobre cómo validar los componentes de C# con HDInsight basado en Linux, consulte [Migración de soluciones .​NE​T para HDInsight basado en ​Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts o archivos CMD en el servidor que se invoca como parte de un trabajo de Hive |se usan scripts de Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Si utiliza una tienda de metadatos de Oozie externa, debe hacer una copia de seguridad de la tienda de metadatos antes de usarla con HDInsight basado en Linux. HDInsight basado en Linux está disponible con las versiones más recientes de Oozie, lo que puede provocar incompatibilidades con las tiendas de metadatos creadas con versiones anteriores.

Los flujos de trabajo de Oozie permiten acciones de shell. Las acciones de shell usan el shell predeterminado del sistema operativo para ejecutar comandos de línea de comandos. Si tiene flujos de trabajo de Oozie que se basan en el shell de Windows, debe volver a escribir los flujos de trabajo para que se basen en el entorno de shell de Linux (Bash). Para obtener más información sobre el uso de acciones de shell con Oozie, consulte [Oozie shell action extension](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html) (Extensión de la acción del shell de Oozie).

Si tiene un flujo de trabajo que utiliza una aplicación de C#, valide estas aplicaciones en un entorno Linux. Para obtener más información, consulte [Migración de soluciones .​NE​T para HDInsight basado en ​Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| En basado en Windows, se usa... | En basado en Linux... |
| --- | --- |
| Panel de Storm |El panel de Storm no está disponible. Consulte [Implementación y administración de topologías de Storm en HDInsight basado en Linux](storm/apache-storm-deploy-monitor-topology-linux.md) para ver formas de enviar topologías. |
| UI de Storm |La interfaz de usuario de Storm está disponible en https://NOMBREDELCLÚSTER.azurehdinsight.net/stormui |
| Visual Studio para crear, implementar y administrar topologías de C# o híbridas |Visual Studio puede utilizarse para crear, implementar y administrar topologías de C# (SCP.NET) o híbridas en clústeres Storm en HDInsight basados en Linux. Solo se puede usar con los clústeres creados después del 28/10/2016. |

## <a name="hbase"></a>HBase

En los clústeres basados en Linux, el elemento primario del ZNode para HBase es `/hbase-unsecure`. Debe establecer este valor en la configuración de las aplicaciones cliente de Java que usan la API nativa de Java de HBase.

Consulte [Compilación de una aplicación HBase basada en Java](hdinsight-hbase-build-java-maven.md) para ver un cliente de ejemplo que establece este valor.

## <a name="spark"></a>Spark

Los clústeres de Spark estaban disponibles en los clústeres de Windows durante la vista previa. Spark GA solo está disponible con clústeres basados en Linux. No hay ninguna ruta de migración de un clúster de versión preliminar de Spark basado en Windows a un clúster de versión de Spark basado en Linux.

## <a name="known-issues"></a>Problemas conocidos

### <a name="azure-data-factory-custom-net-activities"></a>Actividades de .NET personalizadas de Azure Data Factory

Las actividades de .NET personalizadas de Azure Data Factory no son compatibles actualmente con clústeres de HDInsight basado en Linux. En su lugar, se debe usar uno de los métodos siguientes para implementar actividades personalizadas como parte de la canalización de ADF.

* Ejecute actividades de .NET en grupo de Azure Batch. Consulte la sección servicio vinculado de Azure Batch de [Uso de actividades personalizadas en una canalización de Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implemente la actividad como una actividad MapReduce. Para obtener más información, consulte [Invocación de programas MapReduce desde Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Fin de línea

Por lo general, los fines de línea en sistemas basados en Windows usan CRLF, mientras que los sistemas basados en Linux usan LF. Debe modificar los productores y consumidores existentes para que puedan trabajar con LF.

Por ejemplo, el uso de Azure PowerShell para consultar HDInsight en un clúster basado en Windows devuelve datos con CRLF. La misma consulta con un clúster basado en Linux devuelve LF. Haga una prueba para comprobar si el fin de línea causa algún problema con su solución antes de migrar a un clúster basado en Linux.

Utilice siempre LF como fin de línea para los scripts que se ejecutan en los nodos del clúster. Si utiliza CRLF, puede que vea errores al ejecutar los scripts en un clúster basado en Linux.

Si los scripts no contienen cadenas de caracteres CR insertados, puede cambiar en masa los fines de línea mediante alguno de los métodos siguientes:

* **Antes de cargar en el clúster**: use las siguientes instrucciones de PowerShell para cambiar los fines de línea de CRLF a LF antes de cargar el script en el clúster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Después de cargar en el clúster**: use el siguiente comando desde una sesión de SSH para el clúster basado en Linux para modificar el script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre cómo crear clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Uso de SSH para conectarse a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Administración de un clúster basado en Linux mediante Ambari](hdinsight-hadoop-manage-ambari.md)
