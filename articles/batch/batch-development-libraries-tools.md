<properties 
	pageTitle="Herramientas y bibliotecas de desarollo por lotes de Azure" 
	description="Obtenga las bibliotecas y herramientas que necesita para desarrollar aplicaciones de proceso de Azure y las aplicaciones de proceso por lotes" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Herramientas y bibliotecas de desarollo por lotes de Azure 
<p> Obtenga estas bibliotecas y herramientas para desarrollar aplicaciones de proceso de Azure y las aplicaciones de proceso por lotes.

## Lote

+ [Biblioteca de cliente de proceso por lotes para .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – desarrolle aplicaciones cliente para ejecutar trabajos con el servicio de proceso por lotes
+ [Biblioteca de administración de proceso por lotes](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – cuentas de servicio de administrar lote
+ [Explorer lote](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN): aplicación GUI de ejemplo para examinar, obtener acceso y actualizar los elementos más importantes en una cuenta de proceso por lotes, incluidos los trabajos y tareas, las máquinas virtuales de tarea (TVMs) y grupos y los archivos en una TVM.

> [AZURE.TIP]Explorador de lote es una excelente herramienta si está familiarizado con el lote o desea supervisar o solucionar problemas de actividad de lote. Dado que es un ejemplo de código, el código fuente muestra cómo las características son implementa mediante la API .NET del lote. Consulte la [de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Aplicaciones por lotes

+ [Lote aplicaciones de nube SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet): permiten que las aplicaciones descargar trabajos para el servicio de proceso por lotes
+ [Extensión por lotes las aplicaciones de Visual Studio](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Galería de Visual Studio): las aplicaciones nube en Visual Studio mediante el SDK de lote aplicaciones de nube
+ [Lote aplicaciones cliente SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interactuar con aplicaciones habilitadas para descargar trabajos para el servicio de proceso por lotes
+ [Lote aplicaciones Python cliente](https://github.com/Azure/azure-batch-apps-python) (GitHub) - módulo de cliente de Python para interactuar con aplicaciones configurado en un servicio de aplicaciones de proceso por lotes
+ [Lote aplicaciones Blender ejemplo](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Addon para Blender abrir software de representación de código fuente que utiliza el cliente de Python y SDK de aplicaciones de proceso por lotes para configurar una plataforma de representación basada en la nube


## Recursos adicionales

+ [Ejemplos de código](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Foro de Azure por lotes](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Introducción a la biblioteca de lote de Azure para .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->