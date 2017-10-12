---
title: Desarrollo de acciones de script con HDInsight basado en Linux (Azure) | Microsoft Docs
description: "Obtenga información sobre cómo usar scripts de Bash para personalizar clústeres de HDInsight basado en Linux. La característica de acción de script de HDInsight le permite ejecutar scripts durante o después de la creación del clúster. Los scripts se pueden usar para cambiar la configuración de clúster o instalar software adicional."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: 7f1a0bd8c7e60770d376f10eaea136a55c632c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="script-action-development-with-hdinsight"></a>Desarrollo de la acción de script con HDInsight

Obtenga información sobre cómo personalizar su clúster de HDInsight mediante scripts de Bash. Las acciones de script son una forma de personalizar HDInsight durante la creación del clúster o después de crearlo.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>¿Qué son las acciones de script?

Las acciones de script son scripts de Bash que Azure ejecuta en los nodos del clúster para realizar cambios de configuración o instalar software. Una acción de script se ejecuta como raíz y proporciona derechos de acceso completo a los nodos del clúster.

Se puede aplicar una acción de script a través de los métodos siguientes:

| Use este método para aplicar un script... | Durante la creación del clúster... | En un clúster en ejecución... |
| --- |:---:|:---:|
| Portal de Azure |✓  |✓ |
| Azure PowerShell |✓ |✓ |
| Azure CLI |&nbsp; |✓ |
| SDK .NET de HDInsight |✓ |✓ |
| Plantilla de Azure Resource Manager |✓ |&nbsp; |

Para más información sobre el uso de estos métodos para aplicar acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Prácticas recomendadas para el desarrollo de scripts

Al desarrollar un script personalizado para un clúster de HDInsight, hay varios procedimientos recomendados que hay que tener en cuenta:

* [Concentrarse en la versión de Hadoop](#bPS1)
* [Concentrarse en el sistema operativo](#bps10)
* [Proporcionar vínculos estables a los recursos de script](#bPS2)
* [Usar recursos compilados previamente](#bPS4)
* [Asegurarse de que el script de personalización del clúster es idempotente](#bPS3)
* [Asegurar una alta disponibilidad de la arquitectura de clúster](#bPS5)
* [Configurar los componentes personalizados para usar el almacenamiento de blobs de Azure](#bPS6)
* [Escribir información en STDOUT y STDERR](#bPS7)
* [Guardar los archivos como ASCII con el fin de línea LF](#bps8)
* [Utilizar la lógica de reintento para recuperarse de errores transitorios](#bps9)

> [!IMPORTANT]
> Las acciones de script se tienen que completar dentro de un período de sesenta minutos o se producirá un error en el proceso. Durante el aprovisionamiento del nodo, el script se ejecuta simultáneamente con otros procesos de instalación y configuración. La competición por los recursos, como el ancho de banda de red o el tiempo de CPU puede ocasionar que el script tarde más en terminar que en el entorno de desarrollo.

### <a name="bPS1"></a>Concentrarse en la versión de Hadoop

Las distintas versiones de HDInsight tienen distintas versiones de componentes y servicios de Hadoop instalados. Si su script espera una versión específica de un servicio o componente, debe usar solamente el script con la versión de HDInsight que incluye los componentes necesarios. Puede encontrar información sobre las versiones de los componentes incluidos con HDInsight mediante el documento [Versiones de HDInsight y componentes](hdinsight-component-versioning.md) .

### <a name="bps10"></a> Concentrarse en el sistema operativo

HDInsight basado en Linux utiliza como cimientos la distribución de Ubuntu Linux. Las distintas versiones de HDInsight se basan en diferentes versiones de Ubuntu, que podrían cambiar el funcionamiento del script. Por ejemplo, HDInsight 3.4 y versiones anteriores se basan en versiones de Ubuntu que emplean Upstart. La versión 3.5 se basa en Ubuntu 16.04, que utiliza Systemd. Systemd y Upstart se basan en comandos diferentes, por lo que debe escribirse el script para usar los dos.

Otra diferencia importante entre HDInsight 3.4 y 3.5 es que `JAVA_HOME` ahora apunta a Java 8.

Puede comprobar la versión del sistema operativo mediante `lsb_release`. El código siguiente demuestra cómo determinar si el script se ejecuta en Ubuntu 14 o 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Puede encontrar el script completo que contiene estos fragmentos de código en https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para la versión de Ubuntu que usa HDInsight, lea el documento sobre la [versión del componente de HDInsight](hdinsight-component-versioning.md).

Para entender las diferencias entre Systemd y Upstart, consulte el artículo sobre [Systemd para usuarios de Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Proporcionar vínculos estables a los recursos de script

El script y los recursos asociados deben permanecer disponibles durante la vigencia del clúster. Estos recursos son necesarios si se agregan nodos nuevos al clúster durante operaciones de escalado.

La práctica recomendada es descargar y archivar todo el contenido de una cuenta de Azure Storage en su suscripción.

> [!IMPORTANT]
> La cuenta de almacenamiento usada tiene que ser la cuenta de almacenamiento predeterminada del clúster o un contenedor público de solo lectura en cualquier otra cuenta de almacenamiento.

Por ejemplo, los ejemplos proporcionados por Microsoft se almacenan en la cuenta de almacenamiento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/). Este es un contenedor público de solo lectura mantenido por el equipo de HDInsight.

### <a name="bPS4"></a>Usar recursos compilados previamente

Para minimizar el tiempo necesario para ejecutar el script, evite las operaciones que compilan recursos a partir del código fuente. Por ejemplo, compile previamente los recursos y almacénelos en un blob de cuenta de Azure Storage en el mismo centro de datos en que está HDInsight.

### <a name="bPS3"></a>Asegurarse de que el script de personalización del clúster es idempotente

Los scripts deben ser idempotentes. Si el script se ejecuta varias veces, debe devolver el clúster al mismo estado cada vez.

Por ejemplo, un script que modifique los archivos de configuración no debería agregar entradas duplicadas si se ejecuta varias veces.

### <a name="bPS5"></a>Asegurar una alta disponibilidad de la arquitectura de clúster

Los clústeres de HDInsight basados en Linux proporcionan dos nodos principales que están activos dentro del clúster, y las acciones de script se ejecutan en ambos nodos. Si los componentes instalados esperan únicamente un nodo principal, no instale los componentes en ambos nodos principales.

> [!IMPORTANT]
> Los servicios proporcionados como parte de HDInsight están diseñados para conmutar por error entre los dos nodos principales según sea necesario. Esta funcionalidad no se extiende a componentes personalizados instalados mediante acciones de script. Si necesita una disponibilidad elevada en componentes personalizados, deberá implementar su propio mecanismo de conmutación por error.

### <a name="bPS6"></a>Configurar los componentes personalizados para usar el almacenamiento de blobs de Azure

Los componentes que instaló en el clúster podrían tener una configuración predeterminada que usa el almacenamiento Sistema de archivos distribuido de Hadoop (HDFS). HDInsight usa Azure Storage o Data Lake Store como almacén predeterminado. Ambas soluciones proporcionan un sistema de archivos compatible con HDFS que conserva los datos incluso si se elimina el clúster. Podría tener que configurar los componentes instalados para que usen WASB o ADL en lugar de HDFS.

En la mayoría de las operaciones, no es necesario especificar el sistema de archivos. Por ejemplo, lo siguiente copia el archivo giraph-examples.jar del sistema de archivos local a un almacenamiento de clúster:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

En este ejemplo, el comando `hdfs` usa de forma transparente el almacenamiento de clúster predeterminado. En algunas operaciones debe especificar el URI. Por ejemplo, `adl:///example/jars` para Data Lake Store o `wasb:///example/jars` para Azure Storage.

### <a name="bPS7"></a>Escribir información en STDOUT y STDERR

HDInsight registra el resultado del script que se escribe en STDOUT y STDERR. Puede ver esta información mediante la interfaz de usuario de Ambari web.

> [!NOTE]
> Ambari solo estará disponible si el clúster se ha creado correctamente. Si usa una acción de script durante la creación del clúster y se produce un error, consulte la sección de solución de problemas [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para conocer otras formas de acceder a la información registrada.

La mayoría de las utilidades y paquetes de instalación escriben información en STDOUT y STDERR, pero puede que desee agregar un registro adicional. Para enviar texto a STDOUT, use `echo`. Por ejemplo:

```bash
echo "Getting ready to install Foo"
```

De forma predeterminada, `echo` enviará la cadena a STDOUT. Para dirigirla a STDERR, agregue `>&2` antes de `echo`. Por ejemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Esto redirige la información escrita en STDOUT a STDERR (2). Para obtener más información sobre la redirección de E/S, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obtener más información sobre la visualización de información registrada por las acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Guardar los archivos como ASCII con el fin de línea LF

Los scripts de Bash deben almacenarse con formato ASCII, con líneas terminadas con LF. Los archivos que se almacenan como UTF-8 o usan CRLF como el final de línea pueden generar el error siguiente:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Utilizar la lógica de reintento para recuperarse de errores transitorios

Al descargar archivos, instalar paquetes mediante apt-get o realizar otras acciones que transmiten datos por Internet, se puede producir un error en la acción debido a errores de red transitorios. Por ejemplo, puede suceder que el recurso remoto con el que se está comunicando esté a punto de conmutar por error a un nodo de copia de seguridad.

Para que el script sea resistente a los errores transitorios, puede implementar la lógica de reintento. La siguiente función muestra cómo implementar la lógica de reintentos. Vuelve a intentar la operación tres veces antes de desistir.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

En los ejemplos siguientes se muestra cómo utilizar esta función.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Los métodos auxiliares de la acción de script son utilidades que puede usar al escribir scripts personalizados. Estos métodos se encuentran en el script [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh). Para descargar y usarlos como parte de su script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Las siguientes aplicaciones auxiliares estén disponibles para su uso en el script:

| Uso de la aplicación auxiliar | Descripción |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Descarga un archivo del URI de origen en la ruta de acceso de archivo especificada. De forma predeterminada, no sobrescribirá un archivo existente. |
| `untar_file TARFILE DESTDIR` |Extrae un archivo tar (mediante `-xf`) en el directorio de destino. |
| `test_is_headnode` |Si se ejecutaba en un nodo principal del clúster, devuelve 1. En caso contrario, devuelve 0. |
| `test_is_datanode` |Si el nodo actual es un nodo de datos (trabajo), devuelve 1. En caso contrario, devuelve 0. |
| `test_is_first_datanode` |Si el nodo actual es el primer nodo (trabajo) de datos (llamado workernode0), devuelve 1. En caso contrario, devuelve 0. |
| `get_headnodes` |Devuelve el nombre de dominio completo de los nodos principales del clúster. Los nombres están delimitados por comas. En caso de error, se devuelve una cadena vacía. |
| `get_primary_headnode` |Obtiene el nombre de dominio completo del nodo principal primario. En caso de error, se devuelve una cadena vacía. |
| `get_secondary_headnode` |Obtiene el nombre de dominio completo del nodo principal secundario. En caso de error, se devuelve una cadena vacía. |
| `get_primary_headnode_number` |Obtiene el sufijo numérico del nodo principal primario. En caso de error, se devuelve una cadena vacía. |
| `get_secondary_headnode_number` |Obtiene el sufijo numérico del nodo principal secundario. En caso de error, se devuelve una cadena vacía. |

## <a name="commonusage"></a>Patrones de uso común

Esta sección proporciona instrucciones sobre cómo implementar algunos de los patrones de uso común que podría encontrar mientras escribe su propio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Paso de parámetros a un script

En algunos casos, un script puede requerir parámetros. Por ejemplo, puede que necesite la contraseña de administrador para el clúster si utiliza la API REST de Ambari.

Los parámetros que se pasan al script se conocen como *parámetros posicionales*, y se asignan a `$1` para el primer parámetro, `$2` para el segundo y así sucesivamente. `$0` contiene el nombre del script.

Los valores se pasan al script como parámetros deben estar rodeados de comillas simples ('). De este modo, se garantiza que el valor pasado se trate literalmente.

### <a name="setting-environment-variables"></a>Establecimiento de variables de entorno

La instrucción siguiente establece una variable de entorno:

    VARIABLENAME=value

Donde VARIABLENAME es el nombre de la variable. Para obtener acceso a la variable, use `$VARIABLENAME`. Por ejemplo, para asignar un valor proporcionado por un parámetro posicional como una variable de entorno denominada PASSWORD, use esta instrucción:

    PASSWORD=$1

Para el acceso posterior a la información puede usar `$PASSWORD`.

Las variables de entorno establecidas dentro del script solo existen en el ámbito del script. En algunos casos, puede que necesite agregar variables de entorno de todo el sistema que se conserven una vez finalizado el script. Para agregar variables de entorno de todo el sistema, agregue la variable a `/etc/environment`. Por ejemplo, la instrucción siguiente agrega `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acceso a ubicaciones donde se almacenan los scripts personalizados

Los scripts usados para personalizar un clúster deben almacenarse en una de las siguientes ubicaciones:

* Una __cuenta de Azure Storage__ asociada con el clúster.

* Una __cuenta de almacenamiento adicional__ asociada con el clúster.

* Un __URI legible públicamente__. Por ejemplo, una dirección URL a los datos almacenados en OneDrive, Dropbox u otro servicio de hospedaje de archivo.

* Una __cuenta de Azure Data Lake Store__ que esté asociada con el clúster de HDInsight. Para más información sobre el uso de Azure Data Lake Store con HDInsight, consulte [Creación de un clúster de HDInsight con Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > La entidad de servicio que HDInsight usa para acceder a Data Lake Store debe tener acceso de lectura al script.

Los recursos utilizados por el script también deben estar disponibles públicamente.

Almacenar los archivos en una cuenta de Azure Storage o Azure Data Lake Store proporciona un acceso rápido, ya que ambas soluciones están dentro de la red de Azure.

> [!NOTE]
> El formato de URI que se use para hacer referencia al script difiere en función del servicio que se utilice. Para las cuentas de almacenamiento asociadas con el clúster de HDInsight, use `wasb://` o `wasbs://`. Para los URI legibles públicamente, use `http://` o `https://`. Para Data Lake Store, use `adl://`.

### <a name="checking-the-operating-system-version"></a>Comprobación de la versión del sistema operativo

Las distintas versiones de HDInsight se basan en versiones específicas de Ubuntu. Puede haber diferencias entre las versiones del sistema operativo que debe comprobar en el script. Por ejemplo, debe instalar un archivo binario que esté asociado a la versión de Ubuntu.

Para comprobar la versión del sistema operativo, use `lsb_release`. Por ejemplo, el script siguiente muestra cómo hacer referencia a un archivo tar específico en función de la versión del sistema operativo:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Lista de comprobación para implementar una acción de script

Estos son los pasos que se llevaron a cabo al prepararse para implementar estos scripts:

* Coloque los archivos que contengan los scripts personalizados en un lugar que sea accesible por los nodos del clúster durante la implementación. Por ejemplo, el almacenamiento predeterminado para el clúster. Los archivos también se pueden almacenar en servicios de hospedaje visibles públicamente.
* Compruebe que el script sea idempotente. De ese modo, se permite el script que se ejecute varias veces en el mismo nodo.
* Use un directorio de archivo temporal /tmp para conservar los archivos descargados que usan los scripts y, a continuación, y realice una limpieza una vez que se ejecuten los scripts.
* Si se cambia la configuración del nivel de sistema operativo o los archivos de configuración de servicio de Hadoop, debería reiniciar los servicios de HDInsight.

## <a name="runScriptAction"></a>Ejecución de una acción de script

Puede usar acciones de script para personalizar los clústeres de HDInsight con los métodos siguientes:

* Portal de Azure
* Azure PowerShell
* Plantillas del Administrador de recursos de Azure
* El SDK de .NET para HDInsight.

Para obtener más información sobre el uso de cada método, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Ejemplos de scripts personalizados

Microsoft proporciona scripts de ejemplo para instalar los componentes en un clúster de HDInsight. Consulte los vínculos siguientes para ver más acciones de script de ejemplo.

* [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Instalación o actualización de Mono en clústeres de HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Solución de problemas

Estos son errores que pueden producirse al usar los scripts desarrollados:

**Error**: `$'\r': command not found`. A veces seguido de `syntax error: unexpected end of file`.

*Causa*: este error se produce si las líneas en un script terminan con CRLF. Los sistemas UNIX esperan solo LF como final de la línea.

Este problema suele producirse cuando se crea el script en un entorno Windows, ya que CRLF es una línea común final para muchos editores de texto en Windows.

*Resolución*: si es una opción en el editor de texto, seleccione formato UNIX o LF para el final de la línea. También puede usar los siguientes comandos en un sistema UNIX para cambiar un CRLF por un LF:

> [!NOTE]
> Los siguientes comandos son aproximadamente equivalentes en que deben cambiar los finales de línea CRLF a LF. Seleccione uno en función de las utilidades disponibles en el sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |Se realizará una copia de seguridad del archivo original con una extensión .BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contendrá una versión con finales de línea solo LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica el archivo directamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contendrá una versión con finales de línea solo LF. |

**Error**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: este error se produce cuando el script se guarda como UTF-8 con una marca de orden de bytes (BOM).

*Resolución*: guarde el archivo como ASCII o UTF-8 sin una marca BOM. También puede usar el siguiente comando en un sistema Linux o UNIX para crear un archivo sin marca BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Reemplace `INFILE` con el archivo que contiene la marca BOM. `OUTFILE` debe ser un nombre de archivo nuevo, que contendrá el script sin la marca BOM.

## <a name="seeAlso"></a>Pasos siguientes

* Obtenga información sobre cómo [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
* Use la [referencia del SDK de .NET de HDInsight](https://msdn.microsoft.com/library/mt271028.aspx) para obtener más información sobre la creación de aplicaciones .NET que administran HDInsight.
* Use la [API de REST de HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para obtener información sobre cómo usar REST para realizar acciones de administración en clústeres de HDInsight.
