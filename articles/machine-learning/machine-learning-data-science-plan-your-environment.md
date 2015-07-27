<properties 
	pageTitle="Planeación del entorno de análisis avanzado de Aprendizaje automático | Microsoft Azure" 
	description="Planee su entorno de análisis avanzado teniendo en cuenta las preguntas claves." 
	services="machine-learning" 
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
	ms.date="05/29/2015" 
	ms.author="mohabib;bradsev" />


# Planeación del entorno de análisis avanzado de Aprendizaje automático de Azure

Deberá tomar ciertas decisiones al configurar un entorno de análisis avanzado de Aprendizaje automático de Azure. Las opciones que elija se basarán en el tipo, tamaño y ubicación de origen de los datos y en el destino de los mismos en la nube. El proceso de análisis avanzado es una serie de tareas completas que le guían desde los datos originales en algún origen a través de la creación y publicación de un modelo como un servicio web de Azure para su consumo en aplicaciones.

El flujo de trabajo de Tecnología y procesos de análisis avanzado se presenta en [Creación de soluciones de análisis avanzado de Azure](machine-learning-data-science-how-to-create-machine-learning-service.md). Para obtener más información sobre los pasos individuales de Tecnología y procesos de análisis avanzado (ADAPT), haga clic en los elementos relevantes de la guía.

En este artículo se describen las cuestiones que se deben considerar al configurar el entorno de análisis avanzado, se enumeran los recursos y herramientas que son útiles para este proceso y se ofrecen instrucciones sobre cómo utilizar la guía de Tecnología y procesos de análisis avanzado.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Cuestiones que se deben considerar

Antes de comenzar a crear el entorno de análisis avanzado, considere las siguientes cuestiones.

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
5. **¿Tiene intención (o es capaz) de mover todos los datos a Azure?**
    - Sí, la intención es copiar todo el conjunto de datos en la nube para su procesamiento.
	- No, solo se copiará un subconjunto de los datos en Azure.
6. **¿Qué destino en la nube de Azure prefiere?** Por ejemplo:
	- Mover los datos a blobs de almacenamiento de Azure.
	- Almacenar los datos en discos duros virtuales de Azure que se pueden montar.
	- Cargar datos en una base de datos de SQL Server en una máquina virtual de Azure.
	- Asignar datos a tablas de Hive en HDInsight de Azure.

## Recursos de análisis avanzado de Azure

Según el escenario, podría necesitar lo siguiente:

1.  Herramientas de Azure: [SDK de Azure PowerShell](../install-configure-powershell.md), [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/), [AzCopy](../storage-use-azcopy.md) y otras
2.  Máquinas virtuales de Azure que ejecutan SQL Server
3.  HDInsight de Azure (Hadoop)
4.  Redes virtuales de Azure para el uso compartido de archivos entre local y Azure
5.  Factoría de datos de Azure para movimientos de datos programados


## Uso de la guía Tecnología y procesos de análisis avanzado (ADAPT)

La guía proporcionada en [Creación de soluciones de análisis avanzado de Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) presenta una serie de ejercicios de ciencia de datos. En el mapa se muestran los pasos principales incluidos en un flujo de trabajo de análisis avanzado típico. No todos los pasos son necesarios en cada ejercicio de ciencia de datos. Además, el proceso es iterativo por naturaleza y la secuencia de pasos puede variar en un determinado ejercicio. Sus respuestas a las preguntas anteriores le ayudarán a decidir los pasos que son relevantes para su caso cuando sean necesarios en el proceso y las condiciones en que las iteraciones de los pasos sean necesarias.

En escenarios de ejemplo basados en el tamaño de los datos originales, la ubicación del origen de datos y el repositorio de destino en Azure, consulte [Escenarios para la Tecnología y procesos de análisis avanzado (ADAPT) en Aprendizaje automático de Azure.](../machine-learning-data-science-plan-sample-scenarios.md)


 

<!---HONumber=July15_HO2-->