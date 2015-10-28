<properties 
	pageTitle="Planeación del entorno de análisis avanzado de Aprendizaje automático | Microsoft Azure" 
	description="Planee su entorno de análisis avanzado teniendo en cuenta las preguntas claves." 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Planeación del entorno de análisis avanzado de Aprendizaje automático de Azure

¿Qué escenario coincide con su problema de análisis cuando se esté preparando para configurar un entorno para realizar una análisis avanzado con el aprendizaje automático de Azure? Las opciones que elija respecto a los recursos necesarios se basarán en el tipo, el tamaño y la ubicación de origen de los datos y en el destino de los mismos. Este artículo trata esas preguntas que le ayudarán a identificar su escenario.

Cuando haya identificado el escenario correspondiente, el flujo de trabajo de la tecnología y proceso de análisis avanzados (ADAPT) que se presenta en la [Ruta de aprendizaje: Creación de soluciones de análisis avanzado de Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) le guía a través de una serie de tareas, desde la obtención de un conjunto de datos hasta la creación y publicación de un modelo como servicio web de Azure que las aplicaciones pueden consumir.

Este tema también enumera algunos de los recursos y herramientas que se usan por este proceso de análisis de avanzado.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Responder a estas preguntas
Responda a estas preguntas para determinar con qué escenario está trabajando antes de crear el entorno de análisis avanzado.

1. **¿Dónde se ubican los datos?** (Esta ubicación se conoce como ***origen de datos***). Por ejemplo:
	- Los datos están disponibles públicamente en una dirección HTTP.
	- Los datos residen en una ubicación de archivos de red o local.
	- Los datos están en una base de datos de SQL Server.
	- Los datos se almacenan en un contenedor de almacenamiento de Azure.
2. **¿Qué formato tienen los datos?** Por ejemplo:
    - Archivos separados por comas o por tabulaciones, sin comprimir.
    - Archivos separados por comas o por tabulaciones, comprimidos.
	- Blobs de Azure comprimidos o sin comprimir.
	- Tablas de SQL Server.
3. **¿Qué tamaño tienen los datos?**
    - Pequeño: menos de 2 GB
    - Medio: más de 2 GB y menos de 10 GB
	- Grande: más de 10 GB
	- Muy grande: 100 GB
4. **¿Está muy familiarizado con los datos?**
    - ¿Necesita explorar los datos para descubrir el esquema, las distribuciones de variables, los valores que faltan, etc.? 
	- ¿Necesitan los datos un procesamiento previo o una limpieza antes de poderlos transformar en una representación tabular? 
5. **¿Tiene intención (o es capaz) de mover todos los datos al almacenamiento de Azure?**
    - Sí, la intención es copiar todo el conjunto de datos en la nube para su procesamiento.
	- No, solo se copiará un subconjunto de los datos en Azure.
6. **¿Qué destino en la nube de Azure prefiere?** Por ejemplo:
	- Mover los datos a blobs de almacenamiento de Azure.
	- Almacenar los datos en discos duros virtuales de Azure que se pueden montar.
	- Cargar datos en una base de datos de SQL Server en una máquina virtual de Azure.
	- Asignar datos a tablas de Hive en HDInsight de Azure.

## ¿Cuál es su escenario?
Cuando haya respondido a las preguntas de la sección anterior, estará listo para determinar qué escenario se adapta mejor a su caso. Los escenarios de ejemplo que se describen en [Escenarios para análisis avanzado en Aprendizaje automático de Azure](../machine-learning-data-science-plan-sample-scenarios.md).

## Recursos de análisis avanzado de Azure
Según el escenario, puede que necesite algunas de las siguientes herramientas y recursos.

1.  Herramientas de Azure: 
	* 	[SDK de Azure PowerShell](../install-configure-powershell.md) 
	* 	[Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  Máquinas virtuales de Azure que ejecutan SQL Server
3.  HDInsight de Azure (Hadoop)
4.  Redes virtuales de Azure para el uso compartido de archivos entre local y Azure
5.  Factoría de datos de Azure para movimientos de datos programados






 

<!---HONumber=Oct15_HO3-->