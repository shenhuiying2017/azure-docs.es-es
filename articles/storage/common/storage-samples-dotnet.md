---
title: Ejemplos de Azure Storage con .NET | Microsoft Docs
description: "Consulte, descargue y ejecute código de ejemplo y aplicaciones para Almacenamiento de Azure. Descubra ejemplos introductorios de blobs, colas, tablas y archivos, usando las bibliotecas de cliente de almacenamiento .NET."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 74777ed14ebb41ad31657f814e86724ff1e5e62e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-storage-samples-using-net"></a>Ejemplos de Azure Storage con .NET

## <a name="net-sample-index"></a>Índice de ejemplos .NET

En la tabla siguiente se proporciona información general sobre el repositorio de ejemplos y los escenarios que abarca cada ejemplo. Haga clic en los vínculos para ver el código de ejemplo correspondiente en GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Extremo</th><th style="font-size:110%">Escenario</th><th style="font-size:110%">Código de ejemplo</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Append Blob</td> 
<td><a href="https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Ejemplo del método CloudBlobContainer.GetAppendBlobReference</a></td> 
</tr> 
<tr> 
<td>Blob en bloques</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application (Aplicación web de la galería de fotos de Almacenamiento de blobs de Azure)</a></td>
</tr> 
<tr> 
<td>Cifrado de cliente</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Ejemplos de cifrado de blobs</a></td>
</tr> 
<tr> 
<td>Copia de blobs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application (Aplicación web de la galería de fotos de Almacenamiento de blobs de Azure)</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application (Aplicación web de la galería de fotos de Almacenamiento de blobs de Azure)</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>Metadatos/propiedades/estadísticas de blobs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>ACL/metadatos/propiedades de contenedor</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage Photo Gallery Web Application (Aplicación web de la galería de fotos de Almacenamiento de blobs de Azure)</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>Contenedor/blob de concesión</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>Contenedor/blob de lista</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introducción a blobs</a></td>
</tr> 
<tr> 
<td>Blob en páginas</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Introducción a blobs</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr>   
<tr> 
<td>Propiedades de servicio</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Introducción a blobs</a></td>
</tr>           
<tr> 
<td>Instantánea de blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Backup Azure Virtual Machine Disks with Incremental Snapshots</a> (Copias de seguridad de discos de máquinas virtuales de Azure con instantáneas incrementales)</td>
</tr> 
<tr> 
<td rowspan="9"><b>Archivo</b></td>
<td>Crear recursos compartidos/directorios/archivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr>
<tr> 
<td>Eliminar recursos compartidos/directorios/archivos</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Getting Started with Azure File Service in .NET (Introducción al servicio Archivo de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Metadatos/propiedades de directorio</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr> 
<tr> 
<td>Descargar archivos</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr> 
<tr> 
<td>Propiedades/metadatos/métricas de archivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr> 
<tr> 
<td>Propiedades de servicio de archivo</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr> 
<tr> 
<td>Directorios y archivos de lista</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr>
<tr> 
<td>Recursos compartidos de lista</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr>
<tr> 
<td>Propiedades/metadatos/estadísticas de recurso compartido</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Ejemplo de File Storage .NET de Azure Storage</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Cola</b></td>
<td>Agregar mensaje</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Cifrado de cliente</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Cifrado de cliente de Cola de Azure Storage en .NET</a></td> 
</tr> 
<tr> 
<td>Crear colas</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Eliminar mensaje/cola</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Inspección de mensajes</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>ACL/metadatos/estadísticas de cola</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Propiedades de Queue Service</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td>Actualizar mensaje</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Table</b></td>
<td>Crear tabla</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Administración de simultaneidad con Almacenamiento de Azure: aplicación de ejemplo</a></td> 
</tr> 
<tr> 
<td>Eliminar entidad/tabla</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introducción a Azure Table Storage mediante .NET</a></td> 
</tr> 
<tr> 
<td>Insertar/combinar/reemplazar entidad</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Administración de simultaneidad con Almacenamiento de Azure: aplicación de ejemplo</a></td> 
</tr> 
<tr> 
<td>Query Entities</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introducción a Azure Table Storage mediante .NET</a></td> 
</tr> 
<tr> 
<td>Consultar tablas</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Introducción a Azure Table Storage mediante .NET</a></td> 
</tr> 
<tr> 
<td>ACL/propiedades de tabla</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Introducción a Azure Table Storage mediante .NET</a></td> 
</tr> 
<tr> 
<td>Update Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Administración de simultaneidad con Almacenamiento de Azure: aplicación de ejemplo</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Biblioteca de ejemplos de código de Azure

Para ver la biblioteca de completa de ejemplos, vaya a la biblioteca [Ejemplos de código de Azure](https://azure.microsoft.com/resources/samples/?service=storage), que incluye ejemplos para Azure Storage que puede descargar y ejecutar localmente. En la biblioteca de código de ejemplo se proporciona código de ejemplo en formato zip. Como alternativa, puede explorar y clonar el repositorio de GitHub para cada ejemplo.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guías de introducción

Consulte las guías siguientes si busca instrucciones sobre cómo instalar las bibliotecas de cliente de Azure Storage y cómo empezar a usarlas.

* [Getting Started with Azure Blob Service in .NET (Introducción al servicio BLOB de Azure en .NET)](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Getting Started with Azure Queue Service in .NET (Introducción al servicio Cola de Azure en .NET)](../storage-dotnet-how-to-use-queues.md)
* [Getting Started with Azure Table Service in .NET (Introducción al servicio Tabla de Azure en .NET)](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Getting Started with Azure File Service in .NET (Introducción al servicio Archivo de Azure en .NET)](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Pasos siguientes

Para información sobre ejemplos para otros lenguajes:

* Java: [ejemplos de Azure Storage con Java](storage-samples-java.md)
* Todos los otros lenguajes: [ejemplos de Azure Storage](../storage-samples.md)
