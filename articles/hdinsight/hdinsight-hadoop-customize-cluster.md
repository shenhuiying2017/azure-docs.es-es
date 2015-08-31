<properties 
	pageTitle="Personalizar los clústeres de HDInsight mediante acciones de script | Microsoft Azure" 
	description="Obtenga información acerca de cómo personalizar clústeres de HDInsight mediante la acción de script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Personalizar los clústeres de HDInsight mediante la acción de script

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Personalizar los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight proporciona una opción de configuración denominada **Acción de script** que invoca scripts personalizados, que definen la personalización que se realizará en el clúster en el proceso de aprovisionamiento. Estos scripts pueden utilizarse para instalar software adicional en un clúster o para cambiar la configuración de las aplicaciones de un clúster.


> [AZURE.NOTE]Acción de script sólo se admite en el clúster de HDInsight versión 3.1, o superior, con el sistema operativo Windows. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).
> 
> Acción de script está disponible como parte de las suscripciones estándar de HDInsight de Azure sin costo adicional.

Los clústeres de HDInsight se pueden personalizar de muchas maneras distintas, por ejemplo, con la inclusión de cuentas de almacenamiento de Azure adicionales, mediante el cambio de archivos de configuración de Hadoop (core-site.xml, hive-site.xml, etc.) o mediante la adición de bibliotecas compartidas (por ejemplo, Oozie o Hive) en ubicaciones comunes en el clúster. Estas personalizaciones pueden realizarse a través de Azure PowerShell, el SDK de .NET de HDInsight de Azure o el Portal de vista previa de Azure. Para obtener más información, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight con opciones personalizadas][hdinsight-provision-cluster].

## Acción de script en el proceso de aprovisionamiento de clústeres

Acción de script sólo se utiliza mientras un clúster está en proceso de creación. El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante el aprovisionamiento de los clústeres][img-hdi-cluster-states]

Cuando se ejecuta el script, el clúster entra en la fase **ClusterCustomization**. En esta fase, el script se ejecuta bajo la cuenta de administrador del sistema, de forma paralela a todos los nodos especificados en el clúster. Asimismo, proporciona privilegios completos de administrador en los nodos.

> [AZURE.NOTE]Puesto que dispone de privilegios de administrador en los nodos del clúster durante la fase **ClusterCustomization**, podrá usar el script para realizar operaciones como la detención o el inicio de servicios, incluidos los servicios de Hadoop. Por lo tanto, como parte del script, debe asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice el script. Estos servicios son necesarios para determinar correctamente el estado del clúster durante la creación. Si modifica cualquier configuración en el clúster que afecte a estos servicios, deberá usar las funciones auxiliares proporcionadas. Para obtener más información acerca de las funciones auxiliares, consulte [Desarrollo de la acción de script con HDInsight][hdinsight-write-script].

Los registros de errores y resultados del script se almacenan en la cuenta de almacenamiento predeterminada especificada para el clúster. Los registros se almacenan en una tabla con el nombre **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Estos son registros agregados desde la ejecución del script en todos los nodos (nodos principal y de trabajo) del clúster.


Cada clúster puede aceptar varias acciones de script, que se invocan en el orden en que se hayan especificado. Los scripts se pueden ejecutar en el nodo principal, los nodos de trabajo, o ambos.

## Llamada a scripts de acción de script

Los scripts de acción de script pueden usarse desde el Portal de vista previa de Azure, Azure PowerShell o el SDK de .NET de HDInsight. En este artículo se muestra cómo usar la acción de script desde el portal. Para obtener información sobre cómo usar PowerShell y el SDK de .NET para usar la acción de script, examine los ejemplos enumerados en la tabla siguiente.

HDInsight proporciona varios scripts para instalar los siguientes componentes en clústeres de HDInsight:

Nombre | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consulte [Instalación y uso de Spark en clústeres Hadoop de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consulte [Instalación y uso de R en clústeres de Hadoop para HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consulte [Instalación y uso de Solr en clústeres de Hadoop de HDInsight](hdinsight-hadoop-solr-install.md).
: **Instalar Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consulte [Instalar Giraph en clústeres de Hadoop de HDInsight y usar Giraph para procesar gráficos a gran escala](hdinsight-hadoop-giraph-install.md).



**En el Portal de vista previa de Azure**

1. Inicie el aprovisionamiento de un clúster tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](hdinsight-provision-clusters.md#portal). 
2. En Configuración opcional, para la hoja **Acciones de script**, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Uso de la acción de script para personalizar un clúster")
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el URI al script invocado para personalizar el clúster.</td></tr>
	<tr><td>Head/Worker</td>
		<td>Especifique los nodos (**Head** o **Worker**) en los que se ejecuta el script de personalización.</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script.</td></tr>
</table>Presione ENTRAR para agregar más de una acción de script para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración de la acción de script y continúe con el aprovisionamiento del clúster.
  

## Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight
El servicio Microsoft Azure HDInsight es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en la nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure proporciona el nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la sección **Ámbito de soporte técnico** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sitio web de Preguntas más frecuentes de soporte técnico de Azure</a>. Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados**: estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Una lista completa de los componentes del clúster está disponible en <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight</a>.
- **Componentes personalizados**: el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

Los componentes integrados son totalmente compatibles, y el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.

Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden utilizar, por ejemplo, el <a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">foro de MSDN para HDInsight</a> y <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a>. Además, los proyectos de Apache tienen sitios del proyecto en <a href="http://apache.org" target="_blank">Apache.org</a>; por ejemplo, <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> y <a href="http://spark.apache.org/" target="_blank">Spark</a>.

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se use el componente o cómo se instale en el clúster, se aplica el mismo nivel de soporte técnico. A continuación, se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.
2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.
3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

## Desarrollo de scripts de acción de script

Consulte [Desarrollo de scripts de acción de script para HDInsight][hdinsight-write-script].


## Consulte también

- [Aprovisionamiento de clústeres de Hadoop en HDInsight usando opciones personalizadas][hdinsight-provision-cluster] proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con otras opciones personalizadas.
- [Desarrollo de la acción de script con HDInsight][hdinsight-write-script]
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]
- [Instalación y uso de R en clústeres de Hadoop de HDInsight][hdinsight-install-r]
- [Instalación y uso de Solr en clústeres de Hadoop de HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar Giraph en clústeres de Hadoop de HDInsight y usar Giraph para procesar gráficos a gran escala](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante el aprovisionamiento del clúster"
 

<!---HONumber=August15_HO8-->