<properties 
	pageTitle="Personalización de clústeres de HDInsight mediante la acción de scripts| Azure" 
	description="Obtenga información acerca de cómo personalizar clústeres de HDInsight mediante la acción de script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/> 

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Personalizar los clústeres de HDInsight mediante la acción de script

Los clústeres de HDInsight de Azure se pueden personalizar para instalar software adicional en un clúster o para cambiar la configuración de aplicaciones en el clúster. HDInsight proporciona una opción de configuración denominada **Acción de script** que invoca scripts personalizados que definen la personalización que se realizará en el clúster. Estos scripts pueden utilizarse para personalizar un clúster *tal como se implementa*.  

Los clústeres de HDInsight se pueden personalizar de muchas maneras distintas como, por ejemplo, con la inclusión de cuentas de almacenamiento adicionales, mediante el cambio de archivos de configuración de hadoop (core-site.xml, hive-site.xml, etc.) o mediante la adición de bibliotecas compartidas (por ejemplo, Hive, Oozie) en ubicaciones comunes en el clúster. Estas personalizaciones se pueden hacer mediante el PowerShell de HDInsight, con el SDK de .NET o con el Portal de administración de Azure. Para obtener más información, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight con opciones personalizadas][hdinsight-provision-cluster].



> [AZURE.NOTE] El uso de acciones de scripts para personalizar un clúster solo se admite en clústeres de HDInsight de la versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/).



## En este artículo

- [¿Cómo se utiliza el script durante la creación de clústeres?](#lifecycle)
- [¿Cómo escribir un script para la personalización de clústeres?](#writescript)
- [¿Cómo usar la acción de script para personalizar el clúster?](#howto)
- [Ejemplos de personalización de clústeres](#example)
- [Soporte técnico para software de código abierto utilizado en clúster de HDInsight](#support)


## <a name="lifecycle"></a>¿Cómo se utiliza el script durante la creación de clústeres?

Con la Acción de script, solo se pueden personalizar clústeres de HDInsight durante el proceso de creación. Durante su creación, los clústeres de HDInsight pasan por las siguientes fases:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

El script se invoca después de que la creación del clúster completa la fase *HDInsightConfiguration* y antes de la fase *ClusterOperational*. Cada clúster puede aceptar varias acciones de script que se invocan en el orden en que se especifican.

> [AZURE.NOTE] La opción para personalizar clústeres de HDInsight está disponible como parte de las suscripciones de Azure HDInsight estándar sin costo adicional.

### ¿Cómo funcionan los scripts?

Los scripts se pueden ejecutar en el nodo principal, en los nodos de trabajo o en ambos. Cuando se ejecuta el script, el clúster entra en la fase *ClusterCustomization*. En esta fase, el script se ejecuta bajo la cuenta de administrador del sistema, de forma paralela a todos los nodos especificados en el clúster. Asimismo, proporciona privilegios completos de administrador en los nodos. 

> [AZURE.NOTE] Puesto que dispone de privilegios de administrador en los nodos del clúster durante la fase de *Cluster customization*, podrá usar el script para realizar operaciones como, por ejemplo, la detención o el inicio de servicios, incluidos los servicios de Hadoop. Por lo tanto, como parte de la secuencia de comandos, debe asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice el script. Estos servicios son necesarios para determinar correctamente el estado del clúster durante la creación. Si modifica cualquier configuración en el clúster que afecte a estos servicios, deberá usar las funciones auxiliares proporcionadas. Para obtener más información acerca de las funciones auxiliares, vea [Desarrollo de acciones de script con HDInsight][hdinsight-write-script].

El resultado, así como los registros de errores del script se almacenan en la cuenta de almacenamiento predeterminada especificada para el clúster. Los registros se almacenan en una tabla con el nombre *u<\cluster-name-fragment><\time-stamp>setuplog*. Estos son registros agregados desde la ejecución del script en todos los nodos (nodos principal y de trabajo) del clúster.

## <a name="writescript"></a>¿Cómo escribir un script para la personalización de clústeres?

Para obtener información sobre cómo escribir un script de personalización de clúster, consulte [Desarrollo de acción de script con HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>¿Cómo usar la acción de script para personalizar el clúster?

Puede utilizar la acción de script desde el Portal de administración de Azure, los cmdlets de PowerShell o el SDK de .NET de HDInsight para personalizar un clúster. 

**Desde el Portal de administración**

1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-provision-clusters/#portal). 
2. En la página Acciones de script del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre</td>
			<td>Especifique un nombre para la acción de script.</td></tr>
		<tr><td>URI de script</td>
			<td>Especifique el URI al script que se ha invocado para personalizar el clúster.</td></tr>
		<tr><td>Tipo de nodo</td>
			<td>Especifica los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b>, o <b>Solo nodos de trabajo</b>.
		<tr><td>Parámetros</td>
			<td>Especifique los parámetros, si lo requiere el script.</td></tr>
	</table>

	Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster. 
  
**Uso de los cmdlets de PowerShell**

Utilice los comandos de PowerShell de HDInsight para ejecutar una acción de script única y varias acciones de script. Puede usar el cmdlet **<a href = "http://msdn.microsoft.com/es-es/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts personalizados. Para usar estos cmdlets, debe tener Azure PowerShell instalado y configurado. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

Use el siguiente comando de PowerShell para ejecutar una acción de script única al implementar un clúster de HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Utilice el siguiente comando de PowerShell para ejecutar varias acciones de script al implementar un clúster de HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Uso del SDK de .NET de HDInsight**

El SDK de .NET de HDInsight proporciona una clase <a href="http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> para invocar scripts personalizados. Para usar el SDK de .NET de HDInsight

1. Cree una aplicación de Visual Studio y, a continuación, instale el SDK desde Nuget. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. Ejecute los siguientes comandos en la consola para instalar el paquete:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Cree un clúster mediante el SDK. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando el SDK de .NET](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Utilice la clase **ScriptAction** para invocar un script personalizado, tal como se muestra a continuación:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Ejemplos de personalización de clústeres

Para comenzar, HDInsight proporciona scripts de ejemplo que permiten instalar los siguientes componentes en un clúster de HDInsight.

- **Instalar Spark**. Consulte [Instalación de Spark en clústeres de HDInsight][hdinsight-install-spark].
- **Instalar R**. Consulte [Instalación de R en clústeres de HDInsight][hdinsight-install-r].
- **Instalar Solr**. [Instalación y uso de Solr en clústeres de HDInsight](../hdinsight-hadoop-solr-install)
- **Instalar Giraph**. [Instalación y uso de Giraph en clústeres de HDInsight](../hdinsight-hadoop-giraph-install)

## <a name="support"></a>Soporte técnico para software de código abierto utilizado en clúster de HDInsight
El servicio Microsoft Azure HDInsight es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en una nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure proporciona el nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la <a href="http://azure.microsoft.com/es-es/support/faq/" target="_blank">sección del ámbito de soporte del sitio web de preguntas más frecuentes de soporte técnico de Azure</a>. Además, el servicio HDInsight ofrece nivel adicional de soporte técnico para algunos de los componentes que se describen a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio HDInsight:

- **Componentes integrados**. Estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de Yarn, el lenguaje de consulta Hive y la biblioteca Mahout pertenecen a esta categoría. La lista completa de los componentes del clúster está disponible <a href="http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/" target="_blank">aquí</a>.
- **Componentes personalizados**. Como usuario del clúster puede instalarlo o usarlo en la carga de trabajo, cualquier componente disponible en la comunidad o creado por el usuario.

Los componentes integrados son totalmente compatibles y el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.

Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, existen muchos sitios de comunidad que se pueden utilizar, como: <a href ="https://social.msdn.microsoft.com/Forums/azure/es-es/home?forum=hdinsight" target="_blank">Foro de MSDN para HDInsight (en inglés)</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. También los proyectos de Apache tienen sitios de proyectos en <a href="http://apache.org" target="_blank">http://apache.org</a>, por ejemplo: <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se usa o se instala en el clúster el componente se aplica el mismo nivel de soporte técnico. A continuación se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight.

1. Envío de trabajos. Se pueden enviar Hadoop u otros tipos de trabajos al clúster que ejecuta o usa componentes personalizados.
2. Personalización del clúster. Durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.
3. Muestras. En los componentes personalizados más populares Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.


## Consulte también##
[Aprovisionamiento de clústeres de Hadoop en HDInsight con opciones personalizadas][hdinsight-provision-cluster] proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con otras opciones personalizadas.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"
<!--HONumber=42-->
