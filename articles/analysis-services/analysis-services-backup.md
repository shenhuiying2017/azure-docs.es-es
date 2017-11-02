---
title: "Copia de seguridad y restauración de bases de datos de Azure Analysis Services | Microsoft Docs"
description: "Se describe cómo realizar una copia de seguridad de una base de datos de Azure Analysis Services y restaurarla."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: f96e72e4decd475e7859eb7f70046a277b718ac8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="backup-and-restore"></a>Copia de seguridad y restauración

Realizar una copia de seguridad de bases de datos de modelo tabular en Azure Analysis Services es casi lo mismo que en Analysis Services local. La diferencia principal es el lugar donde se almacenan los archivos de copia de seguridad. Los archivos de copia de seguridad se deben guardar en un contenedor de una [cuenta de almacenamiento de Azure](../storage/common/storage-create-storage-account.md). Puede usar una cuenta de almacenamiento y un contenedor que ya tenga o puede crear estos al configurar el almacenamiento para el servidor.

> [!NOTE]
> La creación de una cuenta de almacenamiento puede dar lugar a un nuevo servicio facturable. Para más información, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Las copias de seguridad se guardan con extensión abf. Para los modelos tabulares en memoria, se almacenan los datos y los metadatos del modelo. Para los modelos tabulares de consulta directa, solo se almacenan los metadatos del modelo. Las copias de seguridad se pueden comprimir y cifrar, según las opciones que elija. 



## <a name="configure-storage-settings"></a>Configuración de los valores de almacenamiento
Antes de realizar copias de seguridad, debe configurar los valores de almacenamiento para el servidor.


### <a name="to-configure-storage-settings"></a>Para configurar los valores de almacenamiento:
1.  En el portal de Azure > **Configuración**, haga clic en **Backup**.

    ![Copias de seguridad en Configuración](./media/analysis-services-backup/aas-backup-backups.png)

2.  Haga clic en **Habilitado** y, luego, haga clic en **Configuración de Storage**.

    ![Habilitar](./media/analysis-services-backup/aas-backup-enable.png)

3. Seleccione su cuenta de almacenamiento o cree una nueva.

4. Seleccione un contenedor o cree uno nuevo.

    ![Seleccionar el contenedor](./media/analysis-services-backup/aas-backup-container.png)

5. Guarde la configuración de copia de seguridad.

    ![Guardar la configuración de copia de seguridad](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Para realizar una copia de seguridad mediante SSMS:

1. En SSMS, haga clic con el botón derecho en una base de datos > **Copia de seguridad**.

2. En **Copia de seguridad de la base de datos** > **Archivo de copia de seguridad**, haga clic en **Examinar**.

3. En el cuadro de diálogo **Guardar archivo como**, compruebe la ruta de acceso de la carpeta y luego escriba un nombre para el archivo de copia de seguridad. 

4. En el cuadro de diálogo **Copia de seguridad de la base de datos**, seleccione opciones.

    **Permitir la sobrescritura de archivos**: seleccione esta opción para sobrescribir archivos de copia de seguridad del mismo nombre. Si no se selecciona esta opción, el archivo que se guarde no podrá tener el mismo nombre que un archivo que ya exista en la misma ubicación.

    **Aplicar compresión**: seleccione esta opción para comprimir el archivo de copia de seguridad. Los archivos de copia de seguridad comprimidos ahorran espacio en disco, pero requieren un uso de CPU algo mayor. 

    **Cifrar archivo de copia de seguridad**: seleccione esta opción para cifrar el archivo de copia de seguridad. Esta opción requiere una contraseña suministrada por el usuario para proteger el archivo de copia de seguridad. La contraseña impide la lectura de los datos de copia de seguridad por cualquier medio que no sea una operación de restauración. Si elige cifrar las copias de seguridad, almacene la contraseña en una ubicación segura.

5. Haga clic en **Aceptar** para crear y guardar el archivo de copia de seguridad.


### <a name="powershell"></a>PowerShell
Use el cmdlet [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet).

## <a name="restore"></a>Restauración
Para la restauración, el archivo de copia de seguridad debe estar en la cuenta de almacenamiento que configurada para el servidor. Si debe mover un archivo de copia de seguridad de una ubicación local a la cuenta de almacenamiento, utilice [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) o la utilidad de línea de comandos [AzCopy](../storage/common/storage-use-azcopy.md). 



> [!NOTE]
> Si va a restaurar desde un servidor local, debe quitar todos los usuarios del dominio de los roles del modelo y agregarlos de nuevo a los roles como usuarios de Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para realizar la restauración con SSMS:

1. En SSMS, haga clic con el botón derecho en una base de datos > **Restaurar**.

2. En el cuadro de diálogo **Copia de seguridad de la base de datos**, en **Archivo de copia de seguridad**, haga clic en **Examinar**.

3. En el cuadro de diálogo **Buscar archivos de base de datos**, seleccione el archivo que quiere restaurar.

4. En **Restaurar base de datos**, seleccione la base de datos.

5. Especifique las opciones. Las opciones de seguridad deben coincidir con las opciones de copia de seguridad que usó al realizar copias de seguridad.


### <a name="powershell"></a>PowerShell

Use el cmdlet [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet).


## <a name="related-information"></a>Información relacionada

[Cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md)  
[Alta disponibilidad](analysis-services-bcdr.md)     
[Administración de Azure Analysis Services](analysis-services-manage.md)
