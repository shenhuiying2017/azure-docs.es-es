<properties 
	pageTitle="Muestreo de datos del proceso de análisis de Cortana" 
	description="Exploración de datos de diversos entornos de almacenamiento" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Muestreo de datos del proceso de análisis de Cortana

Este **menú** está vinculado a temas que describen cómo realizar un muestreo de datos desde varios entornos de almacenamiento. Esta tarea es un paso en el proceso de análisis de Cortana (CAP).

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

## Introducción

En este documento se tratan los datos de muestreo almacenados en el almacenamiento de blobs de Azure mediante su descarga con programación y, a continuación, realizando un muestreo de los mismos con código Python de ejemplo. Los pasos para hacerlo son los siguientes:

Este documento trata cómo realizar una muestra de los datos que están almacenados en tres ubicaciones de uso frecuente en el proceso de análisis de Cortana:

- Se realiza la muestra de los **datos del contenedor de blobs de Azure** descargándolos mediante programación y, a continuación, realizando un muestreo de ellos con el código Python de ejemplo.
- Se realiza el muestreo de los **datos de SQL Server** con SQL y el lenguaje de programación Python. 
- Se realiza el muestreo de los **datos de las tablas de Hive** mediante consultas de Hive.

<!---HONumber=Nov15_HO1-->