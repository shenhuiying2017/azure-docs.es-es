<properties
	pageTitle="Máquinas virtuales de ciencia de datos de Azure | Microsoft Azure"
	description="Configurar una máquina virtual de ciencia de datos"
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="mohabib;xibingao;bradsev"/>

# Máquinas virtuales de ciencia de datos en Azure

Se pueden aprovisionar un configurar varios tipos de máquinas virtuales de Azure para usarse como parte de un entorno de ciencia de datos basado en la nube. La decisión sobre el tipo de máquina virtual que se debe usarse depende del tipo y la cantidad de datos que se quieran modelar con aprendizaje automático y el destino de los datos en la nube.


* Para obtener orientación sobre las cuestiones que se deben tener en cuenta al tomar esta decisión, consulte [Planear su entorno de ciencia de datos de aprendizaje automático de Azure](machine-learning-data-science-plan-your-environment.md). 
* Para un catálogo de algunos de los escenarios que se pueden encontrar al realizar el análisis avanzado, consulte [Escenarios para la Tecnología y procesos de análisis avanzado (ADAPT) en Aprendizaje automático de Azure](../machine-learning-data-science-plan-sample-scenarios.md).

Se ofrecen instrucciones que describen cómo configurar una máquina virtual de Azure y una máquina virtual de Azure con el servicio de SQL como los servidores de Bloc de notas de IPython. La máquina virtual de Windows se configura con herramientas de compatibilidad como Bloc de notas de IPython, el Explorador de almacenamiento de Azure y AzCopy, así como otras utilidades que son útiles para los proyectos de ciencia de datos. El Explorador de almacenamiento de Azure y AzCopy, por ejemplo, permiten cargar de manera cómoda datos en el almacenamiento de blobs de Azure desde la máquina local o descargarlos en el equipo local desde el almacenamiento. Se proporcionan dos conjuntos de instrucciones:

* [Configuración de una máquina virtual de Azure como servidor del Bloc de notas de IPython para realizar análisis avanzados](machine-learning-data-science-setup-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de Azure con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos en que pueda usarse una forma de almacenamiento de Azure distinta de SQL para almacenar los datos.

* [Configuración de una máquina virtual de Azure como servidor del Bloc de notas de IPython para realizar análisis avanzados](machine-learning-data-science-setup-sql-server-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de SQL Server de Azure con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos en que pueda usarse una base de datos sQL para almacenar los datos.

Una vez aprovisionadas y configuradas, estas máquinas virtuales están preparadas para usarse como servidores de Bloc de notas de IPython para la exploración y el procesamiento de datos, y para otras tareas necesarias junto con los procesos de ciencia de datos en la nube y de Aprendizaje automático de Azure. Los pasos siguientes del proceso de ciencia de datos se asignan en la [Guía de aprendizaje: procesamiento avanzado de datos en Azure](machine-learning-data-science-advanced-data-processing.md) y pueden incluir pasos que muevan datos a SQL Server o HDInsight, los procese y muestree allí como preparación para el aprendizaje a partir de los datos con Aprendizaje automático de Azure.


> [AZURE.NOTE]Las máquinas virtuales de Azure tienen unas tarifas del tipo **pague solo por lo que use**. Para asegurarse de que no se le facture cuando no use la máquina virtual, debe estar en el estado **Detenida (desasignada)** en el [Portal de administración de Azure](http://manage.windowsazure.com/). Para obtener instrucciones paso a paso sobre cómo desasignar la máquina virtual, consulte [Apagado y desasignación de la máquina virtual cuando no esté en uso](machine-learning-data-science-setup-virtual-machine.md#shutdown).
 

<!---HONumber=September15_HO1-->