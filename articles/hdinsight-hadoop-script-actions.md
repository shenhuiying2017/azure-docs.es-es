<properties 
	pageTitle="Desarrollo de la acción de script con HDInsight| Azure" 
	description="Obtenga información acerca de cómo personalizar clústeres de Hadoop mediante la acción de script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="bradsev"/> 

# Desarrollo de la acción de script con HDInsight 

Las acciones de script proporcionan una funcionalidad de Azure HDInsight que se utiliza para instalar software adicional que se ejecuta en un clúster de Hadoop o para cambiar la configuración de las aplicaciones instaladas en un clúster. Las acciones de script son scripts que se ejecutan en los nodos del clúster cuando se implementan clústeres de HDInsight y se ejecutan una vez que los nodos del clúster completan la configuración de HDInsight. La acción de script se ejecuta con privilegios de cuenta de administrador de sistema y proporciona derechos de acceso completo a los nodos del clúster. Cada clúster se puede proporcionar con una lista de acciones de scripts para ejecutar que se ejecutarán en el orden en que se especifican.

La acción de script puede implementarse desde PowerShell de Azure o mediante el SDK de .NET de HDInsight.  Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].


## En este artículo

- [Prácticas recomendadas para el desarrollo de script](#bestPracticeScripting)
- [Métodos auxiliares para scripts personalizados](#helpermethods)
- [Lista de comprobación para implementar una acción de script](#deployScript)
- [Cómo ejecutar una acción de script](#runScriptAction)
- [Ejemplos de scripts personalizados](#sampleScripts) 
- [Cómo probar un script personalizado con el emulador de HDInsight](#testScript)
- [Cómo depurar su script personalizado](#debugScript)
- [Consulte también](#seeAlso)


## <a name="bestPracticeScripting"></a>Prácticas recomendadas para el desarrollo de script

Al desarrollar un script personalizado para un clúster de HDInsight, hay varios procedimientos recomendados que hay que tener en cuenta:

* [Comprobar la versión de Hadoop](#bPS1)
* [Proporcionar vínculos estables a recursos de script](#bPS2)
* [El script de personalización de clústeres debe ser idempotente](#bPS3)
* [La ubicación en la que se instalan los componentes personalizados](#bPS4)
* [Arquitectura de clúster: asegurar una alta disponibilidad](#bPS5)
* [Configurar los componentes personalizados para utilizar WASB](#bPS6)

### <a name="bPS1"></a>Comprobar la versión de Hadoop
Solo HDInsight versión 3.1 (Hadoop 2.4) y versiones posteriores admiten el uso de la acción de script para instalar componentes personalizados en un clúster. En el script personalizado, debe utilizar el método auxiliar **Get-HDIHadoopVersion** para comprobar la versión de Hadoop antes de realizar otras tareas en el script.


### <a name="bPS2"></a>Proporcionar vínculos estables a recursos de script 
Los usuarios deben asegurarse de que todos los scripts y otros artefactos que se usan en la personalización de un clúster permanecen disponibles durante la vigencia del clúster y de que las versiones de estos archivos no cambian para la duración. Estos recursos son necesarios si es necesaria la recreación de imágenes de los nodos del clúster. Lo mejor es descargar y archivar todo el contenido de una cuenta de almacenamiento controlada por el usuario. Esta puede ser la cuenta de almacenamiento predeterminada o alguna de las cuentas de almacenamiento adicionales especificadas en el momento de la implementación de un clúster personalizado.
En los ejemplos de clústeres personalizados de Spark y R proporcionados en la documentación, por ejemplo, hemos realizado una copia local de los recursos de esta cuenta de almacenamiento.: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>El script de personalización de clústeres debe ser idempotente.
Debe esperar que se vuelve a crear una imagen de los nodos de un clúster de HDInsight durante la vigencia del clúster. El script de personalización de clústeres se ejecuta siempre que se vuelve a crear la imagen de un clúster. Este script debe estar diseñado para ser idempotente en el sentido de que tras la creación de la imagen, el script debe asegurarse de que el clúster se devuelve al mismo estado personalizado en que se encontraba cuando se ejecutó el script por primera vez cuando se creó inicialmente el clúster. Por ejemplo, si un script personalizado instaló una aplicación en D:\AppLocation en su primera ejecución, en cada ejecución posterior, al crear una nueva imagen, el script debe comprobar si la aplicación existe en la ubicación D:\AppLocation antes de continuar con otros pasos del script.


### <a name="bPS4"></a>La ubicación en la que se instalan los componentes personalizados 
Cuando se recrea la imagen de los nodo del clúster, la unidad de recursos C:\ y la unidad del sistema D:\ pueden volver a formatearse como resultado la pérdida de datos y aplicaciones que se han instalado en esas unidades. Esto también podría ocurrir si un nodo de la máquina virtual de Azure que forma parte del clúster deja de funcionar y se reemplaza por un nuevo nodo. Puede instalar los componentes en la unidad D:/ drive o en la ubicación C:\apps del clúster. Todas las demás ubicaciones en la unidad C:\ están reservadas. Especifique la ubicación donde las aplicaciones o bibliotecas se van a instalar en el script de personalización del clúster. 


### <a name="bPS5"></a>Arquitectura de clúster: asegurar una alta disponibilidad

HDInsight tiene una arquitectura activa-pasiva para alta disponibilidad, en la cual un nodo principal está en modo activo (donde se ejecutan los servicios de HDInsight) y el otro nodo principal está en modo de espera (en el qué los servicios de HDInsight no se están ejecutando). Los nodos cambian los modos activos y pasivos si se interrumpen los servicios HDInsight. Si se utiliza una acción de script para instalar servicios en ambos nodos principales de alta disponibilidad, tenga en cuenta que el mecanismo de conmutación por error de HDInsight no podrá conmutar por error automáticamente estos servicios instalados por el usuario. Por tanto los servicios instalados por el usuario en los nodos principales de HDInsight que se esperan que tengan una alta disponibilidad deben tener su propio mecanismo de conmutación por error si se encuentra en modo activo-pasivo o si se requiere que estén en modo activo-activo. 

Se ejecuta un comando de acción de script de HDInsight en ambos nodos principales cuando el rol del nodo principal se especifica como un valor en el parámetro *ClusterRoleCollection* (documentado a continuación en la sección [cómo ejecutar una acción de script](#runScriptAction)). Por tanto, cuando diseñe un script personalizado, asegúrese de que el script reconoce esta configuración. No debe encontrarse con problemas donde estén instalados los mismos servicios y se inicien en ambos headnodes terminen compitiendo entre sí. Además, tenga en cuenta que se perderán datos durante la recreación de imágenes, por lo que el software instalado con las acciones de script deben ser resistentes a dichos eventos. Las aplicaciones deben diseñarse para trabajar con datos de alta disponibilidad que se distribuyen entre varios nodos. Tenga en cuenta que se puede volver a crear imágenes de 1/5 de los nodos de un clúster como máximo a la vez.


### <a name="bPS6"></a>Configurar los componentes personalizados para utilizar WASB
Los componentes personalizados instalados en los nodos del clúster podrían tener una configuración predeterminada para utilizar el almacenamiento HDFS. Debe cambiar la configuración para utilizar en su lugar el Blob de almacenamiento de Azure (WASB). En una recreación de imagen del clúster, el sistema de archivos HDFS se podría formatear y se perderían todos los datos almacenados allí. El uso de WASB en su lugar garantiza que se conservarán los datos.

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados 

La acción de script proporciona los siguientes métodos auxiliares que puede utilizar al escribir scripts personalizados.

Método auxiliar | Descripción
-------------- | -----------
**Save-HDIFile** | Descargar un archivo desde el URI especificado en una ubicación en el disco local asociado con el nodo de Azure VM asignado al clúster
**Expand-HDIZippedFile** | Descomprimir un archivo comprimido
**Invoke-HDICmdScript** | Ejecutar un script desde cmd.exe
**Write-HDILog** | Escribir la salida del script personalizado para la acción de script
**Get-Services** | Obtener una lista de servicios que se ejecutan en el equipo donde se ejecuta el script
**Get-Service** | Con el nombre de servicio específico como entrada, devuelve información detallada de un servicio específico (nombre del servicio, identificador del proceso, estado, etc.) en el equipo donde se ejecuta la secuencia de comandos
**Get-HDIServices** | Obtener una lista de servicios de HDInsight que se ejecutan en el equipo donde se ejecuta el script
**Get-HDIService** | Con el nombre de servicio de HDInsight específico como entrada, devuelve información detallada de un servicio específico (nombre del servicio, identificador del proceso, estado, etc.) en el equipo donde se ejecuta la secuencia de comandos
**Get-ServicesRunning** | Obtener una lista de servicios que se ejecutan en el equipo donde se ejecuta el script
**Get-ServiceRunning** | Comprobar si un servicio específico (por nombre) se está ejecutando en el equipo donde se ejecuta el script
**Get-HDIServicesRunning** | Obtener una lista de servicios de HDInsight que se ejecutan en el equipo donde se ejecuta el script
**Get-HDIServiceRunning** | Comprobar si un servicio de HDInsight específico (por nombre) se está ejecutando en el equipo donde se ejecuta el script
**Get-HDIHadoopVersion** | Obtener la versión de Hadoop instalado en el equipo donde se ejecuta el script
**Test-IsHDIHeadNode** | Comprobar si el equipo donde se ejecuta el script es un nodo principal
**Test-IsActiveHDIHeadNode** | Comprobar si el equipo donde se ejecuta el script es un nodo principal activo
**Test-IsHDIDataNode** | Comprobar si el equipo donde se ejecuta el script es un nodo de datos
**Edit-HDIConfigFile** | Modificar los archivos de configuración hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml o yarn-site.xml
 

## <a name="deployScript"></a>Lista de comprobación para implementar una acción de script
Estos son los pasos que se llevaron a cabo al prepararse para implementar estos scripts:

1. Coloque los archivos que contengan los scripts personalizados en un lugar que sea accesible por los nodos del clúster durante la implementación. Esto puede ser cualquiera de las cuentas de almacenamiento adicionales o predeterminadas especificadas en el momento de la implementación del clúster o cualquier otro contenedor de almacenamiento públicamente accesible.
2. Agregue comprobaciones en secuencias de comandos para asegurarse de que se ejecutan de manera idempotente, para que la secuencia de comandos se pueda ejecutar varias veces en el mismo nodo.
3. Utilice el cmdlet de Powershell **Write-Output** para imprimir en STDOUT así como en STDERR. No utilice **Write-Host**.
4. Use una carpeta de archivo temporal, como $env:TEMP para mantener el archivo descargado utilizado por los scripts y, a continuación, eliminarlos una vez que se han ejecutado los scripts.
5. Instale el software personalizado solo en las siguientes ubicaciones: D:/ o C:/apps. No deben usarse otras ubicaciones de la unidad C: ya que están reservadas. Tenga en cuenta que la instalación de los archivos de la unidad C: fuera la carpeta C:/apps puede dar lugar a errores en la instalación durante las nuevas imágenes del nodo.
6. En el caso de que se hayan cambiado la configuración del nivel de sistema operativo o los archivos de configuración del servicio de Hadoop, puede que desee reiniciar los servicios de HDInsight por lo que puede seleccionar cualquier configuración del nivel de sistema operativo, como las variables de entorno establecidas en los scripts.


## <a name="runScriptAction"></a>Cómo ejecutar una acción de script

Puede utilizar las acciones de scripts para personalizar los clústeres de HDInsight mediante el Portal de administración de Azure, PowerShell o el SDK de .NET de HDInsight. Para obtener instrucciones, consulte [cómo usar la acción de script](./hdinsight-hadoop-customize-cluster/#howto). 


## <a name="sampleScripts"></a>Ejemplos de scripts personalizados

Microsoft proporciona scripts de ejemplo para instalar los componentes en un clúster de HDInsight. Los scripts de muestra e instrucciones sobre cómo utilizarlos están disponibles en los vínculos siguientes:

- [Instalación y uso de Spark 1.0 en clústeres de HDInsight][hdinsight-install-spark]
- [Instalación y uso de R en clústeres de Hadoop para HDInsight][hdinsight-r-scripts]
- [Instalación y uso de Solr en clústeres de HDInsight](../hdinsight-hadoop-solr-install)
- [Instalación y uso de Giraph en clústeres de HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE] El script de muestra solo funciona con el clúster de HDInsight versión 3.1 o superior. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/).

## <a name="testScript"></a>Cómo probar un script personalizado con el emulador de HDInsight

Una manera sencilla de probar un script personalizado antes de usarlo en el comando de acción de script de HDInsight es ejecutarlo en el emulador de HDInsight. El emulador se puede instalar localmente en una máquina virtual de Azure IaaS Windows Server 2012 R2 o en un equipo local y observar si se comporta correctamente. Tenga en cuenta que Windows Server 2012 R2 es la misma VM que HDInsight usa para sus nodos.

En esta sección se describe el procedimiento para usar el emulador de HDInsight localmente con fines de prueba, pero el procedimiento para usar una máquina virtual es similar.

**Instalación del emulador de HDInsight** Para ejecutar las acciones de script localmente, debe tener instalado el emulador de HDInsight. Para obtener instrucciones sobre cómo instalarlo, consulte [Introducción al emulador de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-get-started-emulator/)

**Establecer la directiva de ejecución de PowerShell de Azure:** abra Microsoft Azure PowerShell y ejecute (como administrador) los comandos siguientes para establecer la directiva de ejecución en la *LocalMachine* y para que sea *Unrestricted*.
 
	Set-ExecutionPolicy Unrestricted -Scope LocalMachine

Necesitamos que esta directiva esté restringida ya que los scripts no están firmados.

**Descargue la acción de script** que desea ejecutar en un destino local. Están disponibles los scripts Spark y R que se pueden ejecutar localmente, por ejemplo, desde las siguientes ubicaciones:

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1

**Ejecutar los scrips de acción**: Abra un nuevo Azure PowerShell en modo de administrador y ejecute el script de instalación Spark o R desde la ubicación local donde se hayan guardado.

**Ejemplos de uso**
Al utilizar los clústeres Spark y R, es posible que los archivos de datos necesarios no estén presentes en el emulador de HDInsight, por lo que necesitará cargar archivos .txt relevantes que contienen datos de alguna ruta de acceso en HDFS y, a continuación, usar esa ruta de acceso para tener acceso a los datos. Por ejemplo:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


Tenga en cuenta que, en algunos casos, un script personalizado puede depender realmente de componentes de HDInsight, como en la detección de si determinados servicios de Hadoop están activos. En este caso, deberá probar los scripts personalizados al implementarlos en un clúster de HDInsight real.


## <a name="debugScript"></a>Cómo depurar su script personalizado

Se almacenan los registros de errores de scripts, junto con otros resultados, en la cuenta de almacenamiento predeterminada que ha especificado para el clúster en su creación. Los registros se almacenan en una tabla con el nombre *u<\cluster-name-fragment><\time-stamp>setuplog*. Estos son registros agregados que tienen registros de todos los nodos (headnode y nodos de trabajo) en los que se ejecuta el script en el clúster.

Puede también conectarse en remoto en los nodos del clúster para ver el tanto el STDOUT y STDERR de los scripts personalizadas. Los registros en cada nodo son específicos de ese nodo y se registran en **C:\HDInsightLogs\DeploymentAgent.log**. Estos archivos de registro registran todas las salidas del script personalizado. Un fragmento de código de registro de ejemplo para una acción de script de Spark tiene este aspecto:

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

En caso de que se produzca un error de ejecución, también se incluirá la salida que lo describe en este archivo de registro. La información proporcionada en estos registros debe ser útil al depurar los problemas de scripts que puedan surgir.


## <a name="seeAlso"></a>Consulte también

[Personalizar los clústeres de HDInsight mediante la acción de script][hdinsight-cluster-customize]  


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/<!--HONumber=42-->
