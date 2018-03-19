---
title: 'Azure Stack Storage: Diferencias y consideraciones'
description: "Comprender las diferencias entre Azure Stack Storage y Azure Storage, junto con las consideraciones de implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 7c4f030018f388302c3b60a41086bbd97c86513d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack Storage: Diferencias y consideraciones

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack Storage es el conjunto de servicios de almacenamiento en la nube de Microsoft Azure Stack. Azure Stack Storage proporciona blob, tabla, cola y funcionalidad de administración de cuenta con una semántica coherente de Azure.

En este artículo se resumen las diferencias entre Azure Stack Storage y Azure Storage. También se resumen otras consideraciones a tener en cuenta al implementar la Azure Stack. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el tema [Consideraciones clave](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Hoja de referencia rápida: Diferencias de almacenamiento

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Recursos compartidos de archivos SMB basado en la nube admitidos|Todavía no se admite
|Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)|Cifrado de AES de 256 bits|Cifrado AES de 128 bits de BitLocker
|Tipo de cuenta de almacenamiento|Cuentas de Azure Blob Storage y de uso general|Solo para uso general.
|Opciones de replicación|Almacenamiento con redundancia local, almacenamiento con redundancia geográfica, almacenamiento con redundancia geográfica con acceso de lectura y almacenamiento con redundancia de zona|Almacenamiento con redundancia local.
|Premium Storage|Totalmente compatible|Se pueden aprovisionar, pero no hay límite de rendimiento o garantía.
|Discos administrados|Premium y estándar admitidos|Todavía no se admite.
|Nombre de blob|1 024 caracteres (2 048 bytes)|880 caracteres (1 760 bytes)
|Tamaño máximo de blob en bloque|4,75 TB (100 MB x 50 000 bloques)|4,75 TB (100 MB x 50 000 bloques) para la actualización 1802 o versiones más recientes. 50 000 x 4 MB (aproximadamente 195 GB) para las versiones anteriores.
|Copia de instantánea de blob en páginas|Copia de seguridad de discos de máquina virtual no administrados conectados a una máquina virtual en ejecución compatible|Todavía no se admite.
|Copia de instantáneas incrementales del blob de página|Blobs en páginas de Azure estándar y premium admitidos|Todavía no se admite.
|Niveles de almacenamiento para Blob Storage|Niveles de almacenamiento de acceso frecuente, de acceso esporádico y de acceso de archivo.|Todavía no se admite.
Eliminación temporal para Blob Storage|Vista previa|Todavía no se admite.
|Tamaño máximo de blob en página|8 TB|1 TB
|Tamaño de página de blob en página|512 bytes|4 KB
|Clave de partición de tabla y tamaño de clave de fila|1 024 caracteres (2 048 bytes)|400 caracteres (800 bytes)

### <a name="metrics"></a>Métricas
También hay algunas diferencias con las métricas de almacenamiento:
* Los datos de transacción de las métricas de almacenamiento no distinguirán el ancho de banda de red interna o externa.
* Los datos de transacción de las métricas de almacenamiento no incluyen el acceso de la máquina virtual a los discos montados.

## <a name="api-version"></a>Versión de API
Solo las siguientes versiones son compatibles con Azure Stack Storage:

API de los servicios de Azure Storage:

Actualización 1802 o versiones más recientes:
 - [2017-04-17](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)

Versiones anteriores:
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)


API de administración de los servicios de Azure Storage:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Versiones del SDK

Las siguientes bibliotecas de cliente son compatibles con Azure Stack Storage:

| Biblioteca de cliente | Versión compatible de Azure Stack | Vínculo                                                                                                                                                                                                                                                                                                                                     | Especificación de punto de conexión       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | De la 6.2.0 a la 8.7.0.          | Paquete NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | archivo app.config              |
| Java           | De la 4.1.0 a la 6.1.0           | Paquete Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Configuración de la cadena de conexión      |
| Node.js        | De la 1.1.0 a la 2.7.0           | Vínculo NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Por ejemplo: ejecute "npm install azure-storage@2.7.0")<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Declaración de instancia de servicio |
| C++            | De la 2.4.0 a la 3.1.0           | Paquete NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Configuración de la cadena de conexión      |
| PHP            | De la 0.15.0 a la 1.0.0          | Versión de GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalación a través de Composer (consulte los detalles a continuación)                                                                                                                                                                                                                  | Configuración de la cadena de conexión      |
| Python         | De la 0.30.0 a la 1.0.0          | Versión de GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Declaración de instancia de servicio |
| Ruby           | De la 0.12.1 a la 1.0.1          | Paquete de RubyGems:<br>Común:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>Queue: https://rubygems.org/gems/azure-storage-queue/<br>Table: https://rubygems.org/gems/azure-storage-table/<br> <br>Versión de GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Configuración de la cadena de conexión      |

## <a name="next-steps"></a>Pasos siguientes

* [Empezar a trabajar con herramientas de desarrollo de Azure Stack Storage](azure-stack-storage-dev.md)
* [Introducción a Azure Stack Storage](azure-stack-storage-overview.md)

