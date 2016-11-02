<properties
pageTitle="Migración desde HDInsight basado en Windows a HDInsight basado en Linux | Azure"
description="Más información sobre cómo migrar desde un clúster de HDInsight basado en Windows a un clúster de HDInsight basado en Linux."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/11/2016"
ms.author="larryfr"/>


#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migración desde un clúster de HDInsight basado en Windows a un clúster basado en Linux

Aunque HDInsight basado en Windows facilita el uso de Hadoop en la nube, tal vez descubra que requiere un clúster basado en Linux para aprovechar las herramientas y tecnologías necesarias para la solución. Muchos de los elementos del ecosistema de Hadoop se desarrollan en sistemas basados en Linux, y puede algunos no estén disponibles para su uso con HDInsight basado en Windows. Además, en muchos libros, vídeos y otros materiales de aprendizaje se da por sentado que cuando se trabaja con Hadoop se usa un sistema Linux.

En este documento se ofrece información sobre las diferencias entre HDInsight en Windows y en Linux, e instrucciones sobre cómo migrar cargas de trabajo existentes a un clúster basado en Linux.

> [AZURE.NOTE] Los clústeres de HDInsight usan Ubuntu Long Term Support (LTS) como el sistema operativo de los nodos del clúster. Los clústeres de HDInsight 3.3 y 3.4 utilizan Ubuntu 14.0.4 LTS; las versiones anteriores de HDInsight usan Ubuntu 12.04.05 LTS.

## <a name="migration-tasks"></a>Tareas de migración

El flujo de trabajo general de migración es el siguiente.

![Diagrama de flujo de trabajo de migración](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Leer cada sección de este documento para entender los cambios que pueden ser necesarios al migrar el flujo de trabajo existente, trabajos, etc. a un clúster basado en Linux.

2.  Cree un clúster basado en Linux como entorno de control de calidad o de pruebas. Para obtener más información sobre cómo crear un clúster basado en Linux, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Copie los trabajos, los orígenes de datos y los receptores existentes en el nuevo entorno. Consulte la sección Copia de datos en el entorno de prueba para obtener más detalles.

4.  Realice pruebas de validación para asegurarse de que los trabajos funcionan como se esperaba en el nuevo clúster.

Cuando haya comprobado que todo funciona según lo esperado, programe el tiempo de inactividad para la migración. Durante este tiempo de inactividad, realice las acciones siguientes.

1.  Haga copia de seguridad de todos los datos transitorios almacenados localmente en los nodos del clúster. Por ejemplo, si tiene datos que se almacenan directamente en un nodo principal.

2.  Elimine el clúster basado en Windows.

3.  Cree un clúster basado en Linux con el mismo almacén de datos predeterminado que se utilizaba en el clúster basado en Windows. Esto permitirá que el nuevo clúster siga trabajando con los datos de producción existentes.

4.  Importe los datos transitorios cuya copia de seguridad realizó.

5.  Inicie trabajos o continúe el procesamiento con el nuevo clúster.

### <a name="copy-data-to-the-test-environment"></a>Copia de datos en el entorno de prueba

Existen muchos métodos para copiar los datos y los trabajos, pero los dos que se describen en esta sección son los métodos más sencillos para mover archivos directamente a un clúster de prueba.

#### <a name="hdfs-dfs-copy"></a>Copia de HDFS DFS

Puede utilizar el comando HDFS de Hadoop para copiar directamente datos desde el almacenamiento para el clúster de producción existente, en el almacenamiento de un nuevo clúster de prueba mediante los siguientes pasos.

1. Busque la información de contenedor predeterminado y de cuenta de almacenamiento del clúster existente. Puede hacerlo mediante el siguiente script de Azure PowerShell.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Siga los pasos indicados en el documento Creación de clústeres basados en Linux en HDInsight para crear un nuevo entorno de prueba. Deténgase antes de crear el clúster y, en su lugar, seleccione **Configuración opcional**.

3. En la hoja Configuración opcional, seleccione **Cuentas de almacenamiento vinculadas**.

4. Seleccione **Agregar una clave de almacenamiento**y, cuando se le pida, la cuenta de almacenamiento que devolvió el script de PowerShell en el paso 1. Haga clic en la opción **Seleccionar** de cada hoja para cerrarlas. Por último, cree el clúster.

5. Una vez creado el clúster, conéctese a él utilizando **SSH**. Si no está familiarizado con el uso de SSH con HDInsight, consulte uno de los siguientes artículos:

    * [Uso de SSH con HDInsight basado en Linux desde clientes de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Uso de SSH con HDInsight basado en Linux desde clientes de Linux, Unix o Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

6. En la sesión de SSH, use el siguiente comando para copiar los archivos de la cuenta de almacenamiento vinculada a la nueva cuenta de almacenamiento predeterminada. Reemplace CONTAINER y ACCOUNT por la información de contenedor y de cuenta que devolvió el script de PowerShell en el paso 1. Reemplace la ruta de acceso a datos por la ruta de acceso a un archivo de datos.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Si la estructura de directorios que contiene los datos no existe en el entorno de prueba, puede crearla con el comando siguiente.

        hdfs dfs -mkdir -p /new/path/to/create

    El conmutador `-p` permite crear todos los directorios de la ruta de acceso.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copia directa entre blobs de almacenamiento de Azure

También puede utilizar el cmdlet de Azure PowerShell `Start-AzureStorageBlobCopy` para copiar blobs entre cuentas de almacenamiento fuera de HDInsight. Para más información, consulte la sección Administración de blobs de Azure de Uso de Azure PowerShell con Almacenamiento de Azure.

##<a name="client-side-technologies"></a>Tecnologías de cliente

Por lo general, las tecnologías de cliente, como los [cmdlets de Azure PowerShell](../powershell-install-configure.md), [Azure CLI](../xplat-cli-install.md) o el [SDK de .NET para Hadoop,](https://hadoopsdk.codeplex.com/) seguirán funcionando igual con clústeres basados en Linux, ya que se basan en las API de REST que son iguales en ambos tipos del SO del clúster.

##<a name="server-side-technologies"></a>Tecnologías de servidor

En la tabla siguiente se ofrece orientación sobre cómo migrar componentes de servidor que son específicos de Windows.

| Si utiliza esta tecnología... | Realice esta acción... |
| ----- | ----- |
| **PowerShell** (scripts de servidor, incluidas las acciones de script que se usan durante la creación del clúster) | Vuelva a escribirlos como scripts de Bash. Para acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md) y [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **CLI de Azure** (scripts de servidor) | Mientras que la CLI de Azure está disponible en Linux, no viene preinstalada en los nodos principales del clúster de HDInsight. Si la necesita para el scripting de servidor, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md) para obtener información sobre su instalación en plataformas basadas en Linux. |
| **Componentes de .NET** | .NET no es compatible actualmente con los clústeres de HDInsight basado en Linux, pero se agregará en una futura actualización. Si necesita migrar ahora, debe volver a escribir los componentes en Java o Python. |
| **Componentes de Win32 o de otras tecnologías de Windows** | La orientación depende del componente o la tecnología; es posible que encuentre una versión compatible con Linux o tal vez tenga que encontrar una solución alternativa o volver escribir este componente. |

##<a name="cluster-creation"></a>Creación de clústeres

Esta sección ofrece información sobre las diferencias en la creación de clústeres.

### <a name="ssh-user"></a>Usuario de SSH

Los clústeres de HDInsight basados en Linux usan el protocolo **Secure Shell (SSH)** para proporcionar acceso remoto a los nodos del clúster. A diferencia de los clústeres basados en el Escritorio remoto para Windows, la mayoría de los clientes SSH no ofrecen una experiencia gráfica de usuario, sino una línea de comandos que permite ejecutar comandos en el clúster. Algunos clientes (como [MobaXterm](http://mobaxterm.mobatek.net/)) ofrecen un explorador gráfico del sistema de archivos, además de una línea de comandos remota.

Durante la creación del clúster, debe especificar un usuario SSH y una **contraseña**, o bien un **certificado de clave pública** para la autenticación.

Se recomienda usar el certificado de clave pública, ya que es más seguro que usar una contraseña. La autenticación de certificado funciona generando un par de claves pública y privada con signo y especificando la clave pública al crear el clúster. Al conectarse al servidor mediante SSH, la clave privada en el cliente proporciona autenticación para la conexión.

Para más información sobre cómo usar SSH con HDInsight, consulte:

- [Uso de SSH con HDInsight desde clientes de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Uso de SSH con HDInsight desde clientes de Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personalización del clúster

**acciones de script** se utilizan con clústeres basados en Linux y deben escribirse en script de Bash. Aunque las acciones de script se pueden utilizar durante la creación del clúster, en el caso de clústeres basados en Linux también pueden usarse para realizar la personalización cuando un clúster está activo y en ejecución. Para más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md) y [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

Otra característica de personalización es **arranque**. En el caso de clústeres de Windows, permite especificar la ubicación de bibliotecas adicionales para su uso con Hive. Después de crear el clúster, estas bibliotecas están automáticamente disponibles para usarse con consultas de Hive sin necesidad de utilizar `ADD JAR`.

El arranque para clústeres basados en Linux proporciona esta funcionalidad. En su lugar, use la acción de script que se documenta en [Incorporación de bibliotecas de Hive durante la creación del clúster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes virtuales

Los clústeres de HDInsight basados en Windows solo funcionan con redes virtuales clásicas, mientras que los clústeres HDInsight basados en Linux requieren redes virtuales del Administrador de recursos. Si tiene recursos en una red virtual clásica a la que debe conectarse el clúster de HDInsight en Linux, consulte [Conexión de una red virtual clásica a una red virtual del Administrador de recursos](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para obtener más información sobre los requisitos de configuración para usar redes virtuales de Azure con HDInsight, consulte [Extensión de las funcionalidades de HDInsight con una red virtual](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Administración y supervisión

Es posible que muchas de las interfaces de usuario web que haya utilizado con HDInsight basado en Windows, como el historial de trabajos o la interfaz de usuario de Yarn, estén disponibles a través de Ambari. Además, la vista Ambari Hive ofrece una forma de ejecutar consultas de Hive mediante el explorador web. La interfaz de usuario web de Ambari está disponible en los clústeres basados en Linux que crea en https://NOMBREDELCLÚSTER>.azurehdinsight.net.

Para más información sobre cómo trabajar con Ambari, consulte los documentos siguientes:

- [Web de Ambari](hdinsight-hadoop-manage-ambari.md)

- [API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas de Ambari

Ambari tiene un sistema de alertas que puede indicarle posibles problemas con el clúster. Las alertas aparecen como entradas en rojo o amarillo en la interfaz de usuario de Web de Ambari, pero también se pueden recuperar a través de la API de REST.

> [AZURE.IMPORTANT] Las alertas de Ambari indican que *puede* que haya un problema, no que *exista* realmente. Por ejemplo, puede recibir una alerta que indica que no se puede tener acceso a HiveServer2, aunque pueda tener acceso a él normalmente.
>
> Las alertas se implementan como consultas basadas en intervalos en un servicio y esperan una respuesta en un plazo de tiempo específico. Por lo que la alerta no necesariamente significa que el servicio no funcione, sino que no devuelve resultados en el plazo previsto.

Por lo general, se debe evaluar si una alerta se ha venido produciendo durante un período prolongado o refleja problemas de los usuarios que anteriormente se han registrado en el clúster antes de realizar la acción en él.

##<a name="file-system-locations"></a>Ubicaciones del sistema de archivos

El sistema de archivos de clústeres de Linux se distribuye de manera diferente que en los clústeres de HDInsight basado en Windows. Utilice la siguiente tabla para encontrar archivos de uso habitual.

| Hay que encontrar... | Se encuentra... |
| ----- | ----- |
| Configuración | `/etc`. Por ejemplo, `/etc/hadoop/conf/core-site.xml` |
| Archivos de registro | `/var/logs` |
| HortonWorks Data Platform (HDP) | `/usr/hdp`. Aquí hay dos directorios ubicados, uno que corresponde a la versión HDP actual (por ejemplo, `2.2.9.1-1`) y `current`. El directorio `current` contiene vínculos simbólicos a archivos y directorios que se encuentran en el directorio de número de versión y se ofrece como una forma cómoda de acceder a archivos HDP, ya que el número de versión cambiará cuando se actualice la versión HDP. |
| hadoop-streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Por lo general, si conoce el nombre del archivo, puede utilizar el siguiente comando desde una sesión de SSH para encontrar la ruta de archivo:

    find / -name FILENAME 2>/dev/null

También puede utilizar caracteres comodín con el nombre de archivo. Por ejemplo, `find / -name *streaming*.jar 2>/dev/null` devolverá la ruta de acceso a los archivos JAR que contengan la palabra "streaming" en el nombre de archivo.

##<a name="hive,-pig,-and-mapreduce"></a>Hive, Pig y MapReduce

Las cargas de trabajo de Pig y MapReduce son muy similares en clústeres basados en Linux: la principal diferencia es que si se usa Escritorio remoto para conectarse a un clúster basado en Windows y ejecutar trabajos, se utilizará SSH con clústeres basados en Linux.

- [Uso de Pig con SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Uso de MapReduce con SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

El gráfico siguiente ofrece orientación sobre cómo migrar las cargas de trabajo de Hive.

| En basado en Windows, se usa... | En basado en Linux... |
| ----- | ----- |
| **Editor de Hive** | [Vista de Hive en Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` para habilitar Tez | Tez es el motor de ejecución predeterminado para clústeres basados en Linux, por lo que ya no se necesita la instrucción set. |
| Scripts o archivos CMD en el servidor que se invoca como parte de un trabajo de Hive | se usan scripts de Bash |
| `hive` desde Escritorio remoto | Uso de [Beeline](hdinsight-hadoop-use-hive-beeline.md) o [Hive en una sesión de SSH](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Storm

| En basado en Windows, se usa... | En basado en Linux... |
| ----- | ----- |
| Panel de Storm | El panel de Storm no está disponible. Consulte [Implementación y administración de topologías de Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md) para ver formas de enviar topologías. |
| UI de Storm | La interfaz de usuario de Storm está disponible en https://NOMBREDELCLÚSTER.azurehdinsight.net/stormui |
| Visual Studio para crear, implementar y administrar topologías de C# o híbridas | Los clústeres basados en Linux no son compatibles actualmente con topologías .NET; pero se agregará compatibilidad en una futura actualización. Si necesita migrar antes de que esto ocurra, tendrá que volver a implementar las topologías en Java. Consulte [Desarrollo de topologías basadas en Java](hdinsight-storm-develop-java-topology.md) para obtener más información sobre cómo crear topologías basadas en Java. |

##<a name="hbase"></a>HBase

En los clústeres basados en Linux, el elemento primario del ZNode para HBase es `/hbase-unsecure`. Debe establecerse en la configuración de las aplicaciones cliente de Java que usan la API nativa de Java de HBase.

Consulte [Compilación de una aplicación HBase basada en Java](hdinsight-hbase-build-java-maven.md) para ver un cliente de ejemplo que establece este valor.

##<a name="spark"></a>Spark

Los clústeres de Spark estaban disponibles en los clústeres de Windows durante la versión preliminar; pero, para versión, Spark solo está disponible con clústeres basados en Linux. No hay ninguna ruta de migración de un clúster de versión preliminar de Spark basado en Windows a un clúster de versión de Spark basado en Linux.

##<a name="known-issues"></a>Problemas conocidos

### <a name="azure-data-factory-custom-.net-activities"></a>Actividades de .NET personalizadas de Data Factory de Azure

Las actividades de .NET personalizadas de Data Factory de Azure no son compatibles actualmente con clústeres de HDInsight basado en Linux. En su lugar, se debe usar uno de los métodos siguientes para implementar actividades personalizadas como parte de la canalización de ADF.

-   Ejecute actividades de .NET en grupo de Lote de Azure. Consulte la sección Servicio vinculado de Lote de Azure de [Uso de actividades personalizadas en una canalización de Azure Data Factory](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Implemente la actividad como una actividad MapReduce. Consulte [Invocación de programas MapReduce desde Data Factory](../data-factory/data-factory-map-reduce.md) para obtener más información.

### <a name="line-endings"></a>Fin de línea

Por lo general, los fines de línea en sistemas basados en Windows usan CRLF, mientras que los sistemas basados en Linux usan LF. Si produce o espera datos con fines de línea CRLF, puede que tenga que modificar los productores o los consumidores para trabajar con el fin de línea LF.

Por ejemplo, el uso de Azure PowerShell para consultar HDInsight en un clúster basado en Windows devolverá datos con CRLF. La misma consulta con un clúster basado en Linux devolverá LF. En muchos casos, esto no importa para el consumidor de datos, pero debe investigarse antes de migrar a un clúster basado en Linux.

Si tiene scripts que se van a ejecutar directamente en los nodos de clúster de Linux (por ejemplo, un script de Python que se usa con un trabajo MapReduce o Hive), debe utilizar siempre LF como fin de la línea. Si utiliza CRLF, puede que vea errores al ejecutar los scripts en un clúster basado en Linux.

Si sabe que los scripts no contienen cadenas de caracteres CR incrustados, puede cambiar los fines de línea mediante uno de los métodos siguientes:

-   **Si tiene scripts que piensa cargar en el clúster**, use las siguientes instrucciones de PowerShell para cambiar los fines de línea de CRLF a LF antes de cargar el script en el clúster.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Si tiene scripts que ya se encuentran en el almacenamiento que utiliza el clúster**, puede usar el siguiente comando desde una sesión de SSH en el clúster basado en Linux para modificar el script.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Pasos siguientes

-   [Más información sobre cómo crear clústeres de HDInsight basado en Linux](hdinsight-hadoop-provision-linux-clusters.md)

-   [Conexión a un clúster basado en Linux mediante SSH desde un cliente de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Conexión a un clúster basado en Linux mediante SSH desde un cliente de Linux, Unix o Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Administración de un clúster basado en Linux mediante Ambari](hdinsight-hadoop-manage-ambari.md)



<!--HONumber=Oct16_HO2-->


