<properties 
	pageTitle="Configurar una máquina virtual o una máquina virtual de SQL Server en Azure para ciencia de datos" 
	description="Configurar una máquina virtual de ciencia de datos" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Configurar una máquina virtual o una máquina virtual de SQL Server en Azure para ciencia de datos

Se pueden aprovisionar un configurar varios tipos de máquinas virtuales de Azure para usarse como parte de un entorno de ciencia de datos basado en la nube. La decisión sobre el tipo de máquina virtual que se debe usarse depende del tipo y la cantidad de datos que se quieran modelar con aprendizaje automático y el destino de los datos en la nube. Para obtener orientación sobre las cuestiones que se deben tener en cuenta al tomar esta decisión, consulte [Planear su entorno de ciencia de datos de aprendizaje automático de Azure](machine-learning-data-science-plan-your-environment.md). Los dos escenarios que se tratan aquí incluyen...

Se ofrecen instrucciones que describen cómo configurar una máquina virtual de Azure y una máquina virtual de Azure con el servicio de SQL como los servidores de Bloc de notas de IPython. La máquina virtual se ejecuta en Windows y se configura con herramientas de compatibilidad, como el Explorador de almacenamiento de Azure y AzCopy, así como otros paquetes que son útiles para los proyectos de ciencia de datos. El Explorador de almacenamiento de Azure y AzCopy, por ejemplo, permiten cargar de manera cómoda datos en el almacenamiento de blobs de Azure desde la máquina local o descargarlos en el equipo local desde el almacenamiento. Se proporcionan dos conjuntos de instrucciones:

* [Configurar una máquina virtual de Azure para ciencia de datos](machine-learning-data-science-setup-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de Azure con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos en que pueda usarse una forma de almacenamiento de Azure distinta de SQL para almacenar los datos. 

* [Configurar una máquina virtual de SQL Server de Azure para ciencia de datos](machine-learning-data-science-setup-sql-server-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de SQL Server de Azure con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos que tengan una base de datos SQL para almacenar los datos.

Una vez aprovisionadas y configuradas, estas máquinas virtuales están preparadas para usarse como servidores de Bloc de notas de IPython para la exploración y el procesamiento de datos, y para otras tareas junto con los procesos de ciencia de datos en la nube y de Aprendizaje automático de Azure. Este proceso puede incluir pasos que muevan datos a HDInsight, los procese y muestree allí como preparación para el aprendizaje a partir de los datos con Aprendizaje automático de Azure.

* Para obtener información sobre cómo mover datos a HDInsight desde el almacenamiento de blobs de Azure, consulte [Crear y cargar datos en tablas de Hive desde el almacenamiento de blobs de Azure](machine-learning-data-science-hive-tables.md).
* Para obtener información sobre el procesamiento de datos en HDInsight con consultas de Hive, vea [Enviar consultas de Hive a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube](machine-learning-data-science-hive-queries.md).
* Para obtener información sobre el muestreo de datos en HDInsight, consulte [Muestreo de datos en tablas de Hive en HDInsight de Azure](machine-learning-data-science-sample-data-hive.md).


> [AZURE.NOTE] Las máquinas virtuales de Azure tienen unas tarifas de tipo **pague solo por lo que use**. Para asegurarse de que no se le facture cuando no use la máquina virtual, debe estar en el estado **Detenida (desasignada)** en el [Portal de administración de Azure](http://manage.windowsazure.com/). Para obtener instrucciones paso a paso sobre cómo desasignar la máquina virtual, consulte [Apagado y desasignación de la máquina virtual cuando no esté en uso](machine-learning-data-science-setup-virtual-machine.md#shutdown) 




<!--HONumber=49-->