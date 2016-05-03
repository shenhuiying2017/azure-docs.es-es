<properties
   pageTitle="Opciones de contexto de proceso para R Server en HDInsight (versión preliminar) | Azure"
   description="Obtenga información sobre las distintas opciones de contexto de proceso disponibles para los usuarios con R Server en HDInsight (versión preliminar)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

#Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)

R Server en HDInsight (versión preliminar) ofrece las funcionalidades más recientes para el análisis basado en R mediante el uso de los datos almacenados en HDFS en un contenedor en la cuenta de almacenamiento de [Blobs de Azure](../storage/storage-introduction.md "Almacenamiento de blobs de Azure") o en el sistema de archivos de Linux local. Puesto que el servidor de R se basa en R de código abierto, las aplicaciones basadas en R que cree podrán usar cualquiera de los más de 8000 paquetes de R de código abierto, así como las rutinas de [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), el paquete de análisis de macrodatos de Microsoft incluido en el servidor de R. El nodo perimetral de los clústeres Premium proporciona una zona de aterrizaje muy útil para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, podrá ejecutar funciones distribuidas en paralelo de ScaleR en los núcleos del servidor del nodo perimetral o en los nodos del clúster mediante el uso de los contextos de proceso de Spark o MapReduce de Hadoop de ScaleR.

## Contextos de proceso de un nodo perimetral

En general, un script de R ejecutado en el servidor de R en el nodo perimetral se ejecutará dentro del intérprete de R de ese nodo, excepto en los pasos que llaman a una función ScaleR. Las llamadas a ScaleR se ejecutarán en un entorno de proceso determinado por la manera en que define el contexto de proceso de ScaleR. Los valores posibles del contexto de proceso cuando se ejecuta su script de R desde un nodo perimetral son secuencial local ("local"), paralelo local ("localpar"), MapReduce y Spark, donde:

| Contexto de proceso | Cómo definir | Contexto de ejecución |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Secuencial local | rxSetComputeContext(‘local’) | Ejecución secuencial (no paralelizada) en el servidor de nodo perimetral |
| Paralelo local | rxSetComputeContext(‘localpar’) | Ejecución paralelizada en los núcleos del servidor de nodo perimetral |
| Spark | RxSpark() | Ejecución distribuida paralelizada vía Spark en los nodos del clúster de HDI |
| MapReduce | RxHadoopMR() | Ejecución distribuida paralelizada vía MapReduce en los nodos del clúster de HDI |


Si desea la ejecución paralelizada con fines de rendimiento, tiene tres opciones. La opción que elija dependerá de la naturaleza de su trabajo de análisis y del tamaño y la ubicación de los datos.

## Elección de un contexto de proceso

En la actualidad, no existe una fórmula que le indique qué contexto de proceso debe usar; en lugar de eso, existen algunos principios rectores que lo guiarán a la opción correcta o, al menos, le ayudarán a restringir esta decisión antes de ejecutar un banco de pruebas si se requiere una opción óptima. Estos principios rectores incluyen:

1.	El sistema de archivos de Linux local será más rápido que HDFS.
2.	Los análisis repetidos serán más rápidos si los datos son locales y están en XDF. 
3.	La transmisión a partir de un origen de datos de texto para los datos pequeños; de lo contrario, conviértalos en XDF antes de realizar los análisis. 
4.	La sobrecarga que representa la copia o transmisión de los datos al nodo perimetral para realizar el análisis se hará insostenible para los datos demasiado grandes. 
5.	Spark será más rápido que MapReduce para realizar los análisis en Hadoop hasta que exista una gran cantidad de datos y ya no quepan en la memoria distribuida.

Con estos principios, las siguientes son algunas reglas generales para seleccionar un contexto de proceso:

### Paralelo local

- Si los datos a analizar son pequeños y no requieren un análisis repetido, transmítalos directamente a la rutina de análisis y use "localpar". 
- Si los datos a analizar son pequeños y requieren un análisis repetido, o bien son moderados, cópielos en el sistema de archivos local, impórtelos a XDF y analícelos a través de "localpar". 

### Hadoop Spark

- Si los datos a analizar son grandes, impórtelos a XDF en HDFS, a menos que el almacenamiento sea un problema, y analícelos a través de "Spark". 

### Hadoop MapReduce

- Si los datos a analizar son muy grandes y el rendimiento de Spark comienza a deteriorarse, intente realizar un análisis a través de "MapReduce".

## Ayuda insertada en rxSetComputeContext

Si desea obtener más información y ejemplos de contextos de proceso de ScaleR, consulte la ayuda insertada en R en el método rxSetComputeContext, por ejemplo:

    > ?rxSetComputeContext 

O bien, consulte "ScaleR Distributed Computing Guide" ("Guía de informática distribuida de ScaleR"), que se encuentra disponible en la biblioteca [MSDN del servidor de R](https://msdn.microsoft.com/library/mt674634.aspx "Servidor de R en MSDN").


## Pasos siguientes

Ahora que sabe cómo crear un nuevo clúster de HDInsight que incluya un servidor de R y los aspectos básicos del uso de la consola de R desde una sesión de SSH, use las siguientes opciones para descubrir otras formas de trabajar con el servidor de R en HDInsight.

- [Información general sobre el servidor de R en Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introducción al servidor de R en Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Agregar un servidor de RStudio a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure Storage options for R Server on HDInsight Premium (Opciones de almacenamiento de Azure del servidor de R en HDInsight Premium)](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0420_2016-->