---
title: Desarrollo de acciones de script con HDInsight - Azure | Microsoft Docs
description: "Obtenga información acerca de cómo personalizar clústeres de Hadoop mediante la acción de script. La acción de se usa para instalar software adicional que se ejecuta en un clúster de Hadoop o para cambiar la configuración de las aplicaciones instaladas en un clúster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: d0e95014f6ebfc4e0286d3a12999c918f831b489
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desarrollo de acciones de script para clústeres basados en Windows de HDInsight
Aprenda a escribir acciones de script para HDInsight. Para obtener más información acerca del uso de acciones de script, vea [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md). Si desea leer el mismo artículo escrito para el clúster de HDInsight basado en Linux, consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Los pasos de este tutorial solo se aplican a clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para obtener información sobre el uso de las acciones de script con clústeres basados en Linux, consulte [Desarrollo de la acción de script con HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



La acción de se usa para instalar software adicional que se ejecuta en un clúster de Hadoop o para cambiar la configuración de las aplicaciones instaladas en un clúster. Las acciones de script son scripts que se ejecutan en los nodos del clúster cuando se implementan clústeres de HDInsight y que se ejecutan una vez que los nodos del clúster completan la configuración de HDInsight. Una acción de script se ejecuta con privilegios de cuenta de administrador de sistema y proporciona derechos de acceso completo a los nodos del clúster. Cada clúster se puede proporcionar con una lista de acciones de scripts que se ejecutarán en el orden en que se especifican.

> [!NOTE]
> Si recibe el siguiente mensaje de error:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: El término 'Save-HDIFile' no se reconoce como nombre de un cmdlet, función, archivo de script o programa ejecutable. Compruebe si escribió correctamente el nombre o, si incluyó una ruta de acceso, compruebe que dicha ruta es correcta e inténtelo de nuevo.
> Se debe a que no incluyó los métodos auxiliares.  Vea [Métodos auxiliares para scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Scripts de ejemplo
Para crear clústeres de HDInsight en el sistema operativo Windows, la acción de script es el script de Azure PowerShell. El script siguiente es un ejemplo para configurar los archivos de configuración del sitio:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

El script toma cuatro parámetros, el nombre del archivo de configuración, la propiedad que desea modificar, el valor que desea establecer y una descripción. Por ejemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Estos parámetros establecen el valor hive.metastore.client.socket.timeout en 90 en el archivo hive-site.xml.  El valor predeterminado es 60 segundos.

Este script de ejemplo se puede encontrar también en [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight proporciona varios scripts para instalar los componentes adicionales en clústeres de HDInsight:

| Nombre | Script |
| --- | --- |
| **Instalar Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Vea [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]. |
| **Instalar R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consulte [Instalación y uso de R en clústeres de HDInsight][hdinsight-r-scripts]. |
| **Instalar Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Vea [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md). |
| - **Instalar Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Vea [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). |

La acción de script puede implementarse desde el Portal de Azure, Azure PowerShell o mediante el SDK de HDInsight para .NET.  Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].

> [!NOTE]
> Los scripts de ejemplo solo funcionan con el clúster de HDInsight versión 3.1 o superior. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Métodos auxiliares para scripts personalizados
Los métodos auxiliares de la acción de script son utilidades que puede usar al escribir scripts personalizados. Estos métodos se definen en [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1) y se pueden incluir en los scripts mediante el ejemplo siguiente:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Estos son los métodos auxiliares proporcionados por este script:

| Método auxiliar | Description |
| --- | --- |
| **Save-HDIFile** |Descargar un archivo desde el Identificador uniforme de recursos (URI) especificado en una ubicación en el disco local asociado con el nodo de máquina virtual de Azure asignado al clúster. |
| **Expand-HDIZippedFile** |Descomprimir un archivo comprimido. |
| **Invoke-HDICmdScript** |Ejecutar un script desde cmd.exe. |
| **Write-HDILog** |Escribir la salida del script personalizado para la acción de script. |
| **Get-Services** |Obtener una lista de los servicios que se ejecutan en la máquina donde se ejecuta el script. |
| **Get-Service** |Con el nombre de servicio específico como entrada, devuelve información detallada de un servicio específico (nombre del servicio, identificador del proceso, estado, etc.) en la máquina donde se ejecuta el script. |
| **Get-HDIServices** |Obtener una lista de los servicios de HDInsight que se ejecutan en el equipo donde se ejecuta el script. |
| **Get-HDIService** |Con el nombre de servicio de HDInsight específico como entrada, devuelve información detallada de un servicio específico (nombre del servicio, identificador del proceso, estado, etc.) en el equipo donde se ejecuta el script. |
| **Get-ServicesRunning** |Obtener una lista de los servicios que se ejecutan en el equipo donde se ejecuta el script. |
| **Get-ServiceRunning** |Comprobar si un servicio específico (por nombre) se ejecuta en el equipo donde se ejecuta el script. |
| **Get-HDIServicesRunning** |Obtener una lista de los servicios de HDInsight que se ejecutan en el equipo donde se ejecuta el script. |
| **Get-HDIServiceRunning** |Comprobar si un servicio de HDInsight específico (por nombre) se ejecuta en el equipo donde se ejecuta el script. |
| **Get-HDIHadoopVersion** |Obtener la versión de Hadoop instalado en el equipo donde se ejecuta el script. |
| **Test-IsHDIHeadNode** |Comprobar si el equipo donde se ejecuta el script es un nodo principal. |
| **Test-IsActiveHDIHeadNode** |Comprobar si el equipo donde se ejecuta el script es un nodo principal activo. |
| **Test-IsHDIDataNode** |Comprobar si el equipo donde se ejecuta el script es un nodo de datos. |
| **Edit-HDIConfigFile** |Editar los archivos de configuración hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml o yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Prácticas recomendadas para el desarrollo de script
Al desarrollar un script personalizado para un clúster de HDInsight, hay varios procedimientos recomendados que hay que tener en cuenta:

* Comprobar la versión de Hadoop

    Solo HDInsight versión 3.1 (Hadoop 2.4) y versiones posteriores admiten el uso de la acción de script para instalar componentes personalizados en un clúster. En el script personalizado, debe usar el método auxiliar **Get-HDIHadoopVersion** para comprobar la versión de Hadoop antes de realizar otras tareas en el script.
* Proporcionar vínculos estables a recursos de script

    Los usuarios deben asegurarse de que todos los scripts y demás artefactos que se usan en la personalización de un clúster permanecen disponibles durante la vigencia de este y de que las versiones de estos archivos no cambian. Estos recursos son necesarios si hay que restablecer imágenes iniciales de los nodos del clúster. La práctica recomendada es descargar y archivar todo el contenido de una cuenta de almacenamiento controlada por el usuario. Esta puede ser la cuenta de almacenamiento predeterminada o alguna de las cuentas de almacenamiento adicionales especificadas en el momento de la implementación de un clúster personalizado.
    Así, en los ejemplos de clústeres personalizados de Spark y R proporcionados en la documentación, hemos realizado una copia local de los recursos de esta cuenta de almacenamiento: https://hdiconfigactions.blob.core.windows.net/.
* Asegurarse de que el script de personalización del clúster es idempotente

    Debe prever que se restablezcan imágenes iniciales de los nodos de un clúster de HDInsight durante la vigencia de este. El script de personalización de clústeres se ejecuta siempre que se restablece la imagen inicial de un clúster. Este script debe estar diseñado para ser idempotente, en el sentido de que después de restablecer la imagen inicial, el script debe asegurarse de que el clúster se devuelva al mismo estado personalizado en que se encontraba cuando se ejecutó el script por primera vez cuando se creó inicialmente el clúster. Por ejemplo, si un script personalizado instaló una aplicación en D:\AppLocation en su primera ejecución, en cada ejecución posterior, al restablecer la imagen inicial, el script debe comprobar si la aplicación existe en la ubicación D:\AppLocation antes de continuar con otros pasos del script.
* Instalación de componentes personalizados en la ubicación óptima

    Cuando se restablece la imagen inicial de los nodos del clúster, es posible volver a dar formato a la unidad de recursos C:\ y a la unidad de sistema D:\, lo que da lugar a la pérdida de los datos y las aplicaciones que se hubieran instalado en esas unidades. Esto también podría ocurrir si un nodo de la máquina virtual de Azure que forma parte del clúster deja de funcionar y se reemplaza por un nuevo nodo. Puede instalar los componentes en la unidad D:/ o en la ubicación C:\apps del clúster. Todas las demás ubicaciones en la unidad C:\ están reservadas. Especifique la ubicación donde las aplicaciones o bibliotecas se van a instalar en el script de personalización del clúster.
* Asegurar una alta disponibilidad de la arquitectura de clúster

    HDInsight tiene una arquitectura activa-pasiva para alta disponibilidad, en la cual un nodo principal está en modo activo (donde se ejecutan los servicios de HDInsight) y el otro nodo principal está en modo de espera (en el que los servicios de HDInsight no se están ejecutando). Los nodos cambian los modos activos y pasivos si se interrumpen los servicios HDInsight. Si se usa una acción de script para instalar servicios en ambos nodos principales para alta disponibilidad, tenga en cuenta que el mecanismo de conmutación por error de HDInsight no podrá realizar la conmutación por error de estos servicios instalados por el usuario de manera automática. Por tanto, los servicios instalados por el usuario en los nodos principales de HDInsight que se espera que tengan una alta disponibilidad, deben tener su propio mecanismo de conmutación por error si se encuentra en modo activo-pasivo o si se requiere que estén en modo activo-activo.

    Un comando de acción de script de HDInsight se ejecuta en ambos nodos de proceso cuando el rol del nodo de proceso se especifica como valor en el parámetro *ClusterRoleCollection* . Por tanto, cuando diseñe un script personalizado, asegúrese de que el script reconoce esta configuración. No debiera encontrarse con problemas donde estén instalados los mismos servicios y se inicien en ambos nodos principales y terminen compitiendo entre sí. Además, tenga en cuenta que se perderán datos durante el restablecimiento de la imagen inicial, por lo que el software instalado mediante la acción de script debe ser resistente a dichos eventos. Las aplicaciones deben diseñarse para trabajar con datos de alta disponibilidad que se distribuyen entre varios nodos. Tenga en cuenta que como máximo se pueden restablecer imágenes iniciales de 1/5 de los nodos de un clúster al mismo tiempo.
* Configurar los componentes personalizados para usar el almacenamiento de blobs de Azure

    Los componentes personalizados instalados en los nodos del clúster podrían tener una configuración predeterminada para utilizar el almacenamiento Sistema de archivos distribuido de Hadoop (HDFS). Debe cambiar la configuración para usar el almacenamiento de blobs de Azure. En un restablecimiento de imagen inicial del clúster, se aplica formato al sistema de archivos HDFS y se pierden todos los datos almacenados allí. El empleo de Azure Blob Storage garantiza la conservación de los datos.

## <a name="common-usage-patterns"></a>Patrones de uso común
Esta sección proporciona instrucciones sobre cómo implementar algunos de los patrones de uso común que podría encontrar mientras escribe su propio script personalizado.

### <a name="configure-environment-variables"></a>Configuración de las variables de entorno
A menudo, en el desarrollo de acciones de script, se siente la necesidad de establecer variables de entorno. Por ejemplo, un escenario bastante probable es cuando se descarga un archivo binario desde un sitio externo, se instala en el clúster y se agrega la ubicación de instalación a la variable de entorno "PATH". El fragmento de código siguiente muestra cómo establecer las variables de entorno del script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Esta instrucción establece la variable de entorno **MDS_RUNNER_CUSTOM_CLUSTER** en el valor "true", además de definir el ámbito de esta variable para que se aplique en todo el equipo. En algunas ocasiones es importante que las variables de entorno se definan en el ámbito correspondiente: máquina o usuario. Consulte [aquí][1] para obtener más información sobre cómo establecer las variables de entorno.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acceso a ubicaciones donde se almacenan los scripts personalizados
Los scripts usados para personalizar un clúster deben encontrarse en la cuenta de almacenamiento predeterminada del clúster o en un contenedor público de solo lectura en cualquier otra cuenta de almacenamiento. Si el script obtiene acceso a recursos ubicados en cualquier otra parte, estos recursos deben encontrarse en una ubicación a la que se pueda tener acceso de manera pública (al menos, con acceso público de solo lectura). Por ejemplo, es posible que desee tener acceso a un archivo y guardarlo con el comando SaveFile-HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

En este ejemplo, debe asegurarse de que el contenedor "somecontainer" de la cuenta de almacenamiento "somestorageaccount" es públicamente accesible. De lo contrario, el script genera una excepción y un error "No encontrado".

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Paso de los parámetros al cmdlet Add-AzureRmHDInsightScriptAction
Para pasar varios parámetros al cmdlet Add-AzureRmHDInsightScriptAction, debe dar formato al valor de cadena para que contenga todos los parámetros del script. Por ejemplo:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

o

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Inicio de excepción para error en implementación de clúster
Si quiere recibir una notificación precisa en caso de que una personalización de clúster no se realizara correctamente según lo esperado, es importante iniciar una excepción y producir un error en el aprovisionamiento del clúster. Por ejemplo, es posible que desee procesar un archivo si existe y controlar el error en casos donde no existe el archivo. Con esto se garantizará que el script se termina correctamente y que el estado del clúster se conoce sin problemas. El fragmento de código siguiente da un ejemplo de cómo lograr esto:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

En este fragmento de código, si el archivo no existía, llevaría a un estado donde el script en realidad se cierra correctamente después de imprimir el mensaje de error y el clúster alcanza el estado de ejecución suponiendo que ha completado "correctamente" el proceso de personalización del clúster. Si desea que se le notifique de manera precisa del hecho de que la personalización del clúster esencialmente no fue correcta como se esperaba debido a que falta un archivo, es más adecuado generar una excepción y obviar el paso de personalización del clúster. Para lograrlo, debe usar en su lugar el siguiente fragmento de código de ejemplo.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de comprobación para implementar una acción de script
Estos son los pasos que se llevaron a cabo al prepararse para implementar estos scripts:

1. Coloque los archivos que contengan los scripts personalizados en un lugar que sea accesible por los nodos del clúster durante la implementación. Este puede ser la cuenta de almacenamiento predeterminada o alguna de las cuentas de almacenamiento adicionales especificadas en el momento de la implementación del clúster, o bien, cualquier otro contenedor de almacenamiento con acceso público.
2. Agregue comprobaciones en secuencias de comandos para asegurarse de que se ejecutan de manera idempotente, para que la secuencia de comandos se pueda ejecutar varias veces en el mismo nodo.
3. Use el cmdlet **Write-Output** de Azure PowerShell para imprimir en STDOUT y en STDERR. No utilice **Write-Host**.
4. Use una carpeta de archivo temporal, como $env:TEMP, para conservar el archivo descargado que usan los scripts y, a continuación, limpie una vez que se ejecuten los scripts.
5. Instale el software personalizado solo en D:\ o en C:\apps. No deben usarse otras ubicaciones de la unidad C: ya que están reservadas. Tenga en cuenta que instalar archivos en la unidad C: fuera de la carpeta C:\apps puede generar errores de configuración durante el restablecimiento de imágenes iniciales del nodo.
6. En el caso de que los archivos de configuración de servicio de Hadoop o la configuración en el nivel de SO hayan cambiado, es posible que desee reiniciar los servicios de HDInsight para que puedan escoger cualquier configuración en el nivel de SO, tal como las variables de entorno definidas en los scripts.

## <a name="debug-custom-scripts"></a>Depuración de scripts personalizados
Se almacenan los registros de errores de scripts, junto con otros resultados, en la cuenta de almacenamiento predeterminada que ha especificado para el clúster en su creación. Los registros se almacenan en una tabla con el nombre *u<\cluster-name-fragment><\time-stamp>setuplog*. Estos son registros agregados que tienen registros de todos los nodos (el nodo principal y los nodos de trabajo) en los que se ejecuta el script en el clúster.

Una manera fácil de comprobar los registros es usar las herramientas de HDInsight para Visual Studio. Para obtener información sobre la instalación de las herramientas, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-and-upgrade-data-lake-tools-for-visual-studio)

**Para comprobar el registro mediante Visual Studio**

1. Abra Visual Studio.
2. Haga clic en **Ver**, y, luego, haga clic en **Explorador de servidores**.
3. Haga clic con el botón derecho en Azure, con el botón principal en Conectar a **Suscripciones de Microsoft Azure**y, luego, escriba sus credenciales.
4. Expanda **Almacenamiento**, expanda la cuenta de Azure Storage usada como sistema de archivos predeterminado, expanda **Tablas** y, luego, haga doble clic en el nombre de la tabla.

También puede conectarse de manera remota con los nodos del clúster para ver tanto STDOUT como STDERR de los scripts personalizados. Los registros de cada nodo son específicos solo para ese nodo y se registran en **C:\HDInsightLogs\DeploymentAgent.log**. Estos archivos de registro registran todas las salidas del script personalizado. Un fragmento de código de registro de ejemplo para una acción de script de Spark tiene este aspecto:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


En este registro, está claro que la acción de script de Spark se ha ejecutado en la máquina virtual denominada HEADNODE0 y no se generan excepciones durante la ejecución.

En caso de que se produzca un error de ejecución, también se incluye la salida que lo describe en este archivo de registro. La información proporcionada en estos registros debe ser útil en la depuración de los problemas de scripts que puedan surgir.

## <a name="see-also"></a>Otras referencias
* [Personalización de los clústeres de HDInsight mediante la acción de script][hdinsight-cluster-customize]
* [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]
* [Instalación y uso de R en clústeres de Hadoop de HDInsight][hdinsight-r-scripts]
* [Instalación y uso de Solr en clústeres de Hadoop de HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
