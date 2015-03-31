<properties 
	pageTitle="Análisis de almacenamiento" 
	description="Administración de la simultaneidad para los servicios BLOB, Cola, Tabla y Archivo" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Análisis de almacenamiento

El análisis de almacenamiento de Azure realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Portal de administración de Azure](https://manage.windowsazure.com/); para obtener información detallada, consulte [Cómo supervisar una cuenta de almacenamiento](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones [Get Blob Service Properties](https://msdn.microsoft.com/es-es/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/es-es/library/hh452243.aspx) y [Get Table Service Properties](https://msdn.microsoft.com/es-es/library/hh452238.aspx) para habilitar el Análisis de almacenamiento para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API del servicio Blob y del servicio Tabla.

El análisis de almacenamiento tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Para obtener más información sobre las directivas de facturación y retención de datos, consulte [Facturación del análisis de almacenamiento](https://msdn.microsoft.com/library/hh360997.aspx). Para obtener más información acerca de los límites de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](https://msdn.microsoft.com/library/dn249410.aspx).

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con el almacenamiento de Azure, consulte [Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)



<!--HONumber=47-->
