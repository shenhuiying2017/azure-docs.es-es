---
title: Uso de Modern Backup Storage con Azure Backup Server v2 | Microsoft Docs
description: "Obtenga información sobre las nuevas características de Azure Backup Server v2. En este artículo se describe cómo actualizar la instalación de la instancia de Backup Server."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Adición de almacenamiento a Azure Backup Server v2

Azure Backup Server v2 incluye Modern Backup Storage de System Center 2016 Data Protection Manager. Modern Backup Storage proporciona ahorros de almacenamiento de hasta el 50 por ciento, copias de seguridad que son tres veces más rápidas y un almacenamiento más eficaz. También ofrece almacenamiento con reconocimiento de la carga de trabajo. 

> [!NOTE]
> Para utilizar Modern Backup Storage, debe ejecutar Backup Server v2 en Windows Server 2016. Si ejecuta Backup Server v2 en una versión anterior de Windows Server, Azure Backup Server no puede sacar partido de Modern Backup Storage. En su lugar, protege las cargas de trabajo como lo hace con Backup Server v1. Para más información, consulte la [matriz de protección](backup-mabs-protection-matrix.md) de la versión de Backup Server.

## <a name="volumes-in-backup-server-v2"></a>Volúmenes en Backup Server v2

Backup Server v2 acepta volúmenes de almacenamiento. Al agregar un volumen, Backup Server da formato al volumen en Sistema de archivos resistente (ReFS), que requiere Modern Backup Storage. Para agregar un volumen y expandirlo más adelante si es necesario, se recomienda utilizar este flujo de trabajo:

1.  Configure Backup Server v2 en una máquina virtual.
2.  Cree un volumen en un disco virtual en un grupo de almacenamiento:
    1.  Agregue un disco a un grupo de almacenamiento y cree un disco virtual con la distribución simple.
    2.  Agregue discos adicionales y extienda el disco virtual.
    3.  Cree volúmenes en el disco virtual.
3.  Agregue los volúmenes a Backup Server.
4.  Configure el almacenamiento con reconocimiento de la carga de trabajo.

## <a name="create-a-volume-for-modern-backup-storage"></a>Creación de un volumen para Modern Backup Storage

El uso de Backup Server v2 con volúmenes como almacenamiento en disco puede ayudarle a mantener el control sobre el almacenamiento. Un volumen puede ser un único disco. Sin embargo, si desea extender el almacenamiento en el futuro, cree un volumen de un disco creado mediante el uso de espacios de almacenamiento. Esto puede ayudarle si desea ampliar el volumen para el almacenamiento de copias de seguridad. En esta sección se ofrecen procedimientos recomendados para crear un volumen con esta configuración.

1. En Administrador del servidor, seleccione **Servicios de archivos y almacenamiento** > **Volúmenes** > **Grupos de almacenamiento**. En **DISCOS FÍSICOS**, seleccione **Nuevo grupo de almacenamiento**. 

    ![Creación de un nuevo grupo de almacenamiento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. En el cuadro desplegable **TAREAS**, seleccione **Nuevo disco virtual**.

    ![Adición de un disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Seleccione el grupo de almacenamiento y, después, seleccione **Agregar disco físico**.

    ![Adición de un disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Seleccione el disco físico y, después, seleccione **Extender disco virtual**.

    ![Extensión del disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Seleccione el disco virtual y, después, seleccione **Nuevo volumen**.

    ![Creación de un nuevo volumen](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. En el cuadro de diálogo **Seleccionar el servidor y el disco**, seleccione el servidor y el nuevo disco. A continuación, seleccione **Siguiente**.

    ![Selección del servidor y disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adición de volúmenes al almacenamiento de disco de Backup Server

Para agregar un volumen a Backup Server, en el panel **Administración**, vuelva a examinar el almacenamiento y, después, seleccione **Agregar**. Aparece una lista de todos los volúmenes disponibles para agregarse al almacenamiento de Backup Server. Después de que se hayan agregado los volúmenes disponibles a la lista de volúmenes seleccionados, puede asignarles un nombre descriptivo para facilitar su administración. Para dar formato a estos volúmenes en ReFS para que Backup Server pueda usar las ventajas de Modern Backup Storage, seleccione **Aceptar**.

![Adición de volúmenes disponibles](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configuración del almacenamiento con reconocimiento de la carga de trabajo

Con el almacenamiento con reconocimiento de la carga de trabajo, puede seleccionar los volúmenes que almacenen de forma preferente ciertos tipos de cargas de trabajo. Por ejemplo, puede establecer volúmenes costosos que admiten un gran número de operaciones de entrada/salida por segundo (IOPS) para almacenar solo las cargas de trabajo que requieren copias de seguridad frecuentes y de gran volumen. Un ejemplo es SQL Server con registros de transacciones. Otras cargas de trabajo que se almacenan en copias de seguridad con menos frecuencia, como máquinas virtuales, pueden ser guardadas en volúmenes de bajo coste.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Puede configurar el almacenamiento con reconocimiento de la carga de trabajo mediante el cmdlet de PowerShell Update-DPMDiskStorage, que actualiza las propiedades de un volumen en el bloque de almacenamiento en un servidor de Data Protection Manager.

Sintaxis:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
En la captura de pantalla siguiente se muestra el cmdlet Update-DPMDiskStorage en la ventana de PowerShell.

![El comando Update-DPMDiskStorage en la ventana de PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Los cambios que realice con PowerShell se reflejan en la consola de administrador de Backup Server.

![Discos y volúmenes en la consola de administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Pasos siguientes
Después de instalar Backup Server, sepa cómo preparar el servidor o empezar a proteger la carga de trabajo.

- [Preparar cargas de trabajo de Backup Server](backup-azure-microsoft-azure-backup.md)
- [Usar Backup Server para hacer una copia de seguridad de un servidor de VMware](backup-azure-backup-server-vmware.md)
- [Uso de Backup Server para hacer una copia de seguridad de SQL Server](backup-azure-sql-mabs.md)

