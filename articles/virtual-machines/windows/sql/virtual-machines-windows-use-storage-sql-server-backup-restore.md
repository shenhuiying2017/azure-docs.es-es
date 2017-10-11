---
title: "Uso del almacenamiento de Azure para copias de seguridad y restauración de SQL Server | Microsoft Docs"
description: Aprenda a realizar una copia de seguridad de SQL Server en Almacenamiento de Azure. Se explican las ventajas de realizar una copia de seguridad de bases de datos SQL en Almacenamiento de Azure.
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: d3df6b25fe524c500cf1a1333ac136e8a29d1484
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Uso de Almacenamiento de Azure para la copia de seguridad y la restauración de SQL Server
## <a name="overview"></a>Información general
A partir de SQL Server 2012 SP1 CU2, ahora puede escribir copias de seguridad de SQL Server directamente en el servicio Almacenamiento de blobs de Azure. Puede usar esta funcionalidad para realizar la copia de seguridad en el servicio BLOB de Azure y restaurar desde él con una base de datos SQL Server local o una base de datos SQL Server de una máquina virtual de Azure. Las copias de seguridad en la nube ofrecen ventajas de disponibilidad, almacenamiento externo ilimitado replicado geográficamente y facilidad de migración de datos en la nube y desde ella. Puede emitir instrucciones BACKUP o RESTORE mediante T-SQL o SMO.

SQL Server 2016 presenta nuevas funcionalidades; puede usar [copias de seguridad de instantánea de archivos](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad prácticamente instantáneas y restauraciones increíblemente rápidas.

En este tema se explica por qué podría decidir utilizar Azure Storage para realizar copias de seguridad de SQL y además se describen los componentes implicados. Puede usar los recursos que se proporciona al final del artículo para obtener acceso a tutoriales e información adicional para comenzar a usar este servicio con las copias de seguridad de SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Ventajas del uso del servicio BLOB de Azure para las copias de seguridad de SQL Server
A la hora de realizar una copia de seguridad de SQL Server, se enfrenta a varias dificultades. Entre estas cabe mencionar la administración del almacenamiento, el riesgo de error de almacenamiento, el acceso a almacenamiento sin conexión y la configuración del hardware. Muchas de estas dificultades se solucionan mediante el servicio Almacenamiento de blobs de Azure para las copias de seguridad de SQL Server. Considere las siguientes ventajas:

* **Facilidad de uso**: el almacenamiento de sus copias de seguridad en blobs de Azure puede ser una manera cómoda, flexible y fácil de acceder a opciones fuera del sitio. Crear almacenamiento externo para sus copias de seguridad de SQL Server puede ser tan fácil como modificar sus scripts o trabajos existentes para usar la sintaxis **BACKUP TO URL** . El almacenamiento externo normalmente debe estar lo suficientemente alejado de la base de datos de producción como para impedir que un solo desastre pueda afectar tanto a las ubicaciones de la base de datos de producción como a la externa. Al elegir la [replicación geográfica de los blobs de Azure](../../../storage/common/storage-redundancy.md), tiene una capa adicional de protección en caso de que se produzca un desastre que pudiera afectar a toda la región.
* **Archivo de copia de seguridad**: el servicio Almacenamiento de blobs de Azure ofrece una mejor alternativa a las opciones de cinta que se usan normalmente para archivar las copias de seguridad. El almacenamiento en cintas puede requerir transporte físico a unas instalaciones externas y medidas para proteger los soportes. Almacenar las copias de seguridad en el almacenamiento de blobs de Azure ofrece una opción de archivado instantánea, altamente disponible y resistente.
* **Hardware administrado**: con los servicios de Azure no hay sobrecarga por la administración del hardware. Los servicios de Azure administran el hardware y ofrecen replicación geográfica para conseguir redundancia y protección contra los errores del hardware.
* **Almacenamiento ilimitado**: al habilitar una copia de seguridad directa en blobs de Azure, tendrá acceso a un almacenamiento prácticamente ilimitado. Como alternativa, la copia de seguridad en un disco de máquina virtual de Azure tiene límites basados en el tamaño de la máquina. Existe un límite en el número de discos que se pueden conectar a una máquina virtual de Azure para copias de seguridad. Este límite es de 16 discos para una instancia muy grande e inferior para las instancias menores.
* **Disponibilidad de copia de seguridad**: las copias de seguridad almacenadas en blobs de Azure están disponibles desde cualquier lugar y en cualquier momento, y es fácil tener acceso a ellas para restauraciones en un servidor SQL Server local o en otro servidor SQL Server que se ejecute en una máquina virtual de Azure, sin necesidad de conectar o desconectar la base de datos o de descargar y conectar el VHD.
* **Costo**: pague solo por el servicio que use. Puede ser tan rentable como una opción de archivado de copias de seguridad externa. Consulte la [Calculadora de precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de precios") y el [Artículo de precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Artículo de precios") para más información.
* **Instantáneas de almacenamiento**: cuando los archivos de base de datos se almacenan en un blob de Azure y está utilizando SQL Server 2016, puede usar [copias de seguridad de instantánea de archivo](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad casi instantáneas y restauraciones increíblemente rápidas.

Para obtener más información, consulte [Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

En las dos secciones siguientes se presenta el servicio Almacenamiento de blobs de Azure, junto con los componentes necesarios de SQL Server. Es importante comprender los componentes y su interacción para usar correctamente la característica de copia de seguridad y restauración del servicio Almacenamiento de blobs de Azure.

## <a name="azure-blob-storage-service-components"></a>Componentes del servicio de almacenamiento de blobs de Azure
Los siguientes componentes de Azure se utilizan al realizar una copia de seguridad en el servicio Almacenamiento de blobs de Azure.

| Componente | Description |
| --- | --- |
| **Storage Account** |La cuenta de almacenamiento es el punto de partida de todos los servicios de almacenamiento. Para obtener acceso a un servicio de almacenamiento de blobs de Azure, primero debe crear una cuenta de almacenamiento de Azure. Para obtener más información acerca del servicio de almacenamiento de blobs de Azure, consulte [Uso del servicio de almacenamiento de blobs de Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contenedor** |Un contenedor ofrece la agrupación de un conjunto de blobs y puede almacenar un número ilimitado de ellos. Para realizar una copia de seguridad de SQL Server en un servicio BLOB de Azure, debe haber creado un contenedor raíz como mínimo. |
| **Blob** |Un archivo de cualquier tipo y tamaño. Los blobs son direccionables mediante el formato de dirección URL siguiente: **https://[cuenta de almacenamiento].blob.core.windows.net/[contenedor]/[blob]**. Para más información sobre los blobs en páginas, consulte la [descripción de los blobs en bloques y en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes de SQL Server
Se utilizan los siguientes componentes de SQL Server al realizar una copia de seguridad en el servicio de Almacenamiento de blobs de Azure.

| Componente | Description |
| --- | --- |
| **URL** |Una URL especifica el Identificador uniforme de recursos (URI) para un único archivo de copia de seguridad. La URL se usa para proporcionar la ubicación y el nombre del archivo de la copia de seguridad de SQL Server. La URL debe apuntar a un blob real, no solo a un contenedor. Si el blob no existe, se crea. Si se especifica un blob que ya existe, BACKUP provoca un error, a menos que se especifique la opción > WITH FORMAT. El siguiente es un ejemplo de la dirección URL que especificaría en el comando BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. Se recomienda HTTPS, pero no es obligatorio. |
| **Credential:** |La información necesaria para conectarse y autenticarse en un servicio de almacenamiento de blobs de Azure se almacena como credencial.  Para que SQL Server escriba copias de seguridad en un blob de Azure o realice la restauración desde él es preciso crear una credencial de SQL Server. Para obtener más información, vea [Credencial de SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Si elige copiar y cargar un archivo de copia de seguridad en un servicio de almacenamiento de blobs de Azure, debe usar blob en páginas como opción de almacenamiento si tiene pensado usar este archivo para las operaciones de restauración. RESTORE desde un tipo de blob en bloques provocará un error.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
1. Cree una cuenta de Azure si aún no tiene una. Si va a evaluar Azure, podría considerar la [evaluación gratuita](https://azure.microsoft.com/free/).
2. A continuación, siga uno de los siguientes tutoriales para crear una cuenta de almacenamiento y realizar una restauración.
   
   * **SQL Server 2014**: [Tutorial: Copias de seguridad y restauración de SQL Server 2014 en el servicio Microsoft Azure Blob Storage](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Tutorial: Archivos de datos de SQL Server en el servicio Almacenamiento de Microsoft Azure](https://msdn.microsoft.com/library/dn466438.aspx)
3. Revise la documentación adicional a partir [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Si tiene algún problema, revise el tema [Prácticas recomendadas y solución de problemas de Copia de seguridad en URL de SQL Server](https://msdn.microsoft.com/library/jj919149.aspx).

Para ver otras opciones de copia de seguridad y restauración de SQL Server, consulte [Copias de seguridad y restauración para SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

