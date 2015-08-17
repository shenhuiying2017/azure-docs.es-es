<properties
	pageTitle="Herramientas y bibliotecas de desarrollo de Lote de Azure"
	description="Obtenga las bibliotecas y herramientas que necesita para desarrollar aplicaciones de Lote de Azure."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Herramientas y bibliotecas de desarollo por lotes de Azure
<p> Obtenga estas bibliotecas y herramientas para desarrollar aplicaciones de Lote de Azure.

## Lote

+ [Biblioteca de cliente de procesamiento de Lote para .NET (NuGet)](http://www.nuget.org/packages/Azure.Batch/): desarrolle aplicaciones cliente para ejecutar trabajos con el servicio de Lote
+ [Biblioteca de administración de Lote](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet): administrar cuentas del servicio Lote
+ [Explorador de Lote](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub): aplicación GUI y muestra para examinar, acceder y actualizar los elementos más importantes en una cuenta de Lote, incluidos trabajos y tareas, nodos y grupos de proceso, y archivos en un nodo de proceso. Consulte la [publicación de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Aplicaciones por lotes

+ [SDK de nube de Aplicaciones de Lote (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview): permiten a las aplicaciones descargar trabajos al servicio de proceso por lotes
+ [Extensión de Aplicaciones de Lote de Visual Studio (Galería de Visual Studio)](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a): habilite aplicaciones para la nube en Visual Studio mediante el SDK de nube de Aplicaciones de Lote
+ [SDK de cliente de Aplicaciones de Lote (NuGet)](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview): interactúe con aplicaciones habilitadas para descargar al servicio de Lote
+ [Cliente Python de Aplicaciones de Lote](https://github.com/Azure/azure-batch-apps-python) (GitHub): módulo del cliente Python para interactuar con aplicaciones configuradas en un servicio de Aplicaciones de Lote

>[AZURE.IMPORTANT]Azure solo ofrecerá la API de aplicaciones de Lote en formato de vista previa. Solo deberá desarrollar con ella proyectos de prueba o de prueba de concepto. En futuras versiones del servicio se integrarán capacidades de aplicaciones de Lote clave en la API de Lote.

## Recursos adicionales

+ [Ejemplos de código](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Foro de Lote de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=August15_HO6-->