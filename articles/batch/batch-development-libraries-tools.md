<properties 
	pageTitle="Herramientas y bibliotecas de desarollo por lotes de Azure" 
	description="Obtenga estas bibliotecas y herramientas para desarrollar aplicaciones de Aplicaciones de Lote y Lote de Azure." 
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
<p> Obtenga estas bibliotecas y herramientas para desarrollar Aplicaciones de Lote y Lote de Azure.

## Lote

+ [Biblioteca de cliente de procesamiento de Lote para .NET (NuGet)](http://www.nuget.org/packages/Azure.Batch/): desarrolle aplicaciones cliente para ejecutar trabajos con el servicio de Lote
+ [Biblioteca de administración de Lote (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/): administre las cuentas de servicio de Lote
+ [Explorador de lotes](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN): aplicación GUI y ejemplo para examinar, acceder y actualizar los elementos más importantes en una cuenta de Lote, incluidos trabajos y tareas, máquinas virtuales de tarea (TVM) y grupos, y archivos en una TVM.

> [AZURE.TIP]El explorador de lotes es una excelente herramienta si no está familiarizado con Lote o desea supervisar o solucionar problemas de actividad de Lote. Dado que es un ejemplo de código, el código fuente muestra cómo se implementan las características mediante la API .NET de Lote. Consulte la [publicación de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Aplicaciones por lotes

+ [SDK de nube de Aplicaciones de Lote (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/): permiten a las aplicaciones descargar trabajos al servicio de proceso por lotes
+ [Extensión de Aplicaciones de Lote de Visual Studio (Galería de Visual Studio)](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a): habilite aplicaciones para la nube en Visual Studio mediante el SDK de nube de Aplicaciones de Lote
+ [SDK de cliente de Aplicaciones de Lote (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/): interactúe con aplicaciones habilitadas para descargar al servicio de Lote
+ [Cliente Python de Aplicaciones de Lote](https://github.com/Azure/azure-batch-apps-python) (GitHub): módulo del cliente Python para interactuar con aplicaciones configuradas en un servicio de Aplicaciones de Lote
+ [Ejemplo de Blender de Aplicaciones de Lote](https://github.com/Azure/azure-batch-apps-blender) (GitHub): complemento para el software de representación de código abierto Blender que utiliza el cliente de Python y el SDK de Aplicaciones de Lote para configurar una plataforma de representación basada en la nube


## Recursos adicionales

+ [Ejemplos de código](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Foro de Lote de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO4-->