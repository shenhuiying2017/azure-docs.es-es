---
title: "Copia de seguridad y restauración de SQL Server | Microsoft Docs"
description: "Describe las consideraciones de copia de seguridad y restauración de bases de datos de SQL Server que se ejecutan en Máquinas virtuales de Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 65557938673c5442758396a47873be1016e0f71b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure
## <a name="overview"></a>Información general
Azure Storage mantiene tres copias de cada disco de máquina virtual de Azure para garantizar la protección contra la pérdida de datos o los daños de los datos físicos. Por lo tanto, a diferencia de lo que ocurriría en un entorno local, no tiene que preocuparse por tales incidencias. Pese a todo, debería seguir creando copias de seguridad de las bases de datos de SQL Server para protegerse frente a errores de aplicaciones o de usuarios (como la inserción de datos incorrectos o la eliminación de tablas) y poder realizar una restauración a un momento dado.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Para SQL Server que se ejecuta en Máquinas virtuales de Azure, puede usar técnicas de copia de seguridad y restauración nativas mediante discos conectados para el destino de los archivos de copia de seguridad. No obstante, hay un límite en el número de discos que se pueden acoplar a una máquina virtual de Azure según el [tamaño de la máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Existe también una sobrecarga de la administración de discos que tener en cuenta.

A partir de SQL Server 2014, puede realizar la copia de seguridad o restauración en Almacenamiento de blobs de Microsoft Azure. SQL Server 2016 también proporciona mejoras para esta opción. Además, para archivos de base de datos almacenados en Almacenamiento de blobs de Microsoft Azure, SQL Server 2016 proporciona una opción para copias de seguridad prácticamente instantáneas y para restauraciones rápidas con capturas de pantalla de Azure. En este artículo se proporciona una introducción de estas opciones, y puede encontrar información adicional en [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

> [!NOTE]
> Para obtener una explicación de las opciones para la copia de seguridad de bases de datos de gran tamaño, vea [Estrategias de copia de seguridad de base de datos de SQL Server de varios terabytes para Máquinas virtuales de Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).
> 
> 

Las secciones siguientes incluyen información específica para diferentes versiones de SQL Server compatibles con Máquina virtual de Azure.

## <a name="sql-server-virtual-machines"></a>Máquinas virtuales de SQL Server
Cuando la instancia de SQL Server se ejecuta en una máquina virtual de Azure, los archivos de base de datos ya residen en discos de datos en Azure. Estos discos residen en el almacenamiento de blobs de Azure. Por ello, los motivos para la copia de seguridad de la base de datos y los enfoques que se adoptan cambian ligeramente. Tenga en cuenta lo siguiente. 

* Ya no tendrá que realizar copias de seguridad de base de datos para proporcionar protección frente a errores multimedia o de hardware porque Microsoft Azure proporciona esta protección como parte del servicio de Microsoft Azure.
* Deberá realizar copias de seguridad de base de datos para proporcionar protección frente a errores de usuario o para fines de archivado, razones legales o fines administrativos.
* Puede almacenar el archivo de copia de seguridad directamente en Azure. Para más información, consulte las secciones siguientes que proporcionan instrucciones para las distintas versiones de SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016
Microsoft SQL Server 2016 admite las características de [copia de seguridad y restauración mediante blobs de Azure](https://msdn.microsoft.com/library/jj919148.aspx) de SQL Server 2014. Sin embargo, también incluye las siguientes mejoras:

| Mejoras de la versión de 2016 | Detalles |
| --- | --- |
| **Seccionamiento** |Cuando realiza una copia de seguridad de Almacenamiento de blobs de Microsoft Azure, SQL Server 2016 admite la copia de seguridad de varios blobs para habilitar la copia de seguridad de bases de datos de gran tamaño, hasta un máximo de 12,8 TB. |
| **Copia de seguridad de instantáneas** |Mediante el uso de instantáneas de Azure, Copia de seguridad de instantáneas de archivos de SQL Server ofrece copias de seguridad prácticamente instantáneas y restauraciones rápidas de los archivos de base de datos almacenados con el servicio de Almacenamiento de blobs de Azure. Esta capacidad le permite simplificar las directivas de copia de seguridad y restauración. Copia de seguridad de instantáneas de archivos también es compatible con la restauración a un momento dado. Para obtener más información, vea [Copias de seguridad de instantáneas para archivos de base de datos en Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx). |
| **Programación de copia de seguridad administrada** |Copia de seguridad administrada de SQL Server en Azure es ahora compatible con programaciones personalizadas. Para obtener más información, vea [Copia de seguridad administrada de SQL Server en Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). |

Para obtener un tutorial de las capacidades de SQL Server 2016 cuando use Almacenamiento de blobs de Azure, vea [Tutorial: Uso del servicio de Almacenamiento de blobs de Microsoft Azure con bases de datos de SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014 incluye las siguientes mejoras:

1. **Copia de seguridad y restauración de Azure**:
   
   * *Copia de seguridad en URL de SQL Server* es ahora compatible en SQL Server Management Studio. La opción de copia de seguridad de Azure está ahora disponible cuando se utiliza la tarea de copia de seguridad o restauración, o se usa el Asistente para planes de mantenimiento en SQL Server Management Studio. Para obtener más información, vea [Copia de seguridad en URL de SQL Server](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
   * *Copia de seguridad administrada de SQL Server en Azure* dispone de una nueva funcionalidad que habilita la administración de copia de seguridad automatizada. Esto es especialmente útil para la administración de copia de seguridad automatizada para instancias de SQL Server 2014 que se ejecuta en Máquina de Azure. Para obtener más información, vea [Copia de seguridad administrada de SQL Server en Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
   * *Copia de seguridad automatizada* proporciona automatización adicional para habilitar automáticamente *Copia de seguridad administrada de SQL Server en Azure* en todas las bases de datos nuevas y existentes para una VM con SQL Server en Azure. Para obtener más información, vea [Copia de seguridad automatizada para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-automated-backup.md).
   * Para obtener información general de todas las opciones para Copia de seguridad de SQL Server 2014 en Azure, vea [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
2. **Cifrado**: SQL Server 2014 es compatible con el cifrado de datos cuando se crea una copia de seguridad. Admite varios algoritmos de cifrado y el uso de un certificado o una clave asimétrica. Para obtener más información, vea [Cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012
Para obtener información detallada sobre Copia de seguridad y restauración de SQL Server en SQL Server 2012, vea [Copia de seguridad y restauración de bases de datos de SQL Server (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

A partir de la actualización acumulativa de SQL Server 2012 SP1 2, puede realizar una copia de seguridad y restauración a partir del servicio de Almacenamiento de blobs de Azure. Esta mejora puede usarse para realizar la copia de seguridad de bases de datos de SQL Server en un servidor SQL Server que se ejecute en una máquina virtual de Azure o en una instancia local. Para obtener más información, vea [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Algunas de las ventajas de usar el servicio de Almacenamiento de blobs de Azure incluyen la capacidad de omitir el límite de 16 discos para discos conectados, la facilidad de administración, la disponibilidad directa del archivo de copia de seguridad en otra instancia de la instancia de SQL Server que se ejecuta en una máquina virtual de Azure o una instancia local para fines de recuperación ante desastres o migración. Para obtener una lista completa de las ventajas que supone usar un servicio de almacenamiento de blobs de Azure para copias de seguridad de SQL Server, vea la sección *Ventajas* en [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Para obtener recomendaciones sobre prácticas recomendadas e información sobre solución de problemas, vea [Prácticas recomendadas de copia de seguridad y restauración (servicio de Almacenamiento de blobs de Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008
Para obtener información sobre Copia de seguridad y restauración de SQL Server en SQL Server 2008 R2, vea [Copia de seguridad y restauración de bases de datos en SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Para obtener información sobre Copia de seguridad y restauración de SQL Server en SQL Server 2008, vea [Copia de seguridad y restauración de bases de datos en SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Pasos siguientes
Si está planeando la implementación de SQL Server en una máquina virtual de Azure, puede encontrar directrices de aprovisionamiento en el siguiente tutorial: [Aprovisionamiento de una máquina virtual de SQL Server en Azure con Azure Resource Manager](virtual-machines-windows-portal-sql-server-provision.md).

Aunque la copia de seguridad y la restauración pueden usarse para migrar los datos, existen rutas de acceso de migración de datos posiblemente más sencillas para SQL Server en una máquina virtual de Azure. Para obtener una descripción completa de las opciones de migración y recomendaciones, vea [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

Revise otros [recursos para ejecutar SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

