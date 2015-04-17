<properties 
	pageTitle="Facturación y análisis de almacenamiento" 
	description="Aprenda a aprovechar el análisis de almacenamiento para comprender mejor su factura de servicio de almacenamiento" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# Facturación y análisis de almacenamiento

En este tema se describe el coste del análisis de almacenamiento y cómo utilizar los datos de métricas y de registro para entender la factura de los servicios de almacenamiento.


## Coste del análisis de almacenamiento

El análisis de almacenamiento lo habilita el propietario de la cuenta de almacenamiento; no está habilitado de forma predeterminada. Todos los datos de métricas los escriben los servicios de una cuenta de almacenamiento. Como resultado, cada una de las operaciones de escritura realizadas por el análisis de almacenamiento es facturable. También lo es la cantidad de almacenamiento utilizado por los datos de métricas.

Son facturables las acciones siguientes realizadas por el análisis de almacenamiento:

- Solicitudes para crear blobs para el registro

- Solicitudes para crear entidades de tabla para las métricas

Si ha configurado una directiva de retención de datos, no se le cobrarán las transacciones de eliminación cuando el análisis de almacenamiento elimine los antiguos datos de métricas y de registro. Sin embargo, las transacciones de eliminación desde un cliente sí son facturables. Para obtener más información acerca de las directivas de retención, consulte [Establecer una directiva de retención de datos de análisis de almacenamiento ](https://msdn.microsoft.com/library/azure/hh343263.aspx).

## Descripción de las solicitudes facturables

Las solicitudes realizadas al servicio de almacenamiento de una cuenta pueden ser facturables o no facturables. El análisis de almacenamiento registra cada solicitud realizada a un servicio, incluyendo un mensaje de estado que indica cómo se administró la solicitud. De igual forma, el análisis de almacenamiento guarda las métricas para un servicio y para las operaciones de la API de dicho servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Todas estas características pueden ayudarle a analizar las solicitudes facturables, a llevar a cabo mejoras en la aplicación y a diagnosticar problemas en las solicitudes a los servicios. Para obtener más información sobre la facturación, consulte [Descripción de la facturación del almacenamiento de Azure: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Al examinar los datos de análisis de almacenamiento, puede usar las tablas del tema [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar qué solicitudes son facturables. De esta manera, podrá comparar los datos de métricas y de registro con los mensajes de estado para ver si se le cobró por una solicitud determinada. También puede utilizar las tablas del tema anterior para investigar la disponibilidad de un servicio de almacenamiento o de una operación de API determinada.

## Pasos siguientes
[Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343260.aspx) 

[Acerca del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343262.aspx) 

[Acerca de las métricas del análisis de almacenamiento](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->
