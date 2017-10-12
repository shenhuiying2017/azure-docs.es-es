---
title: "Uso de Azure Backup Server para realizar una copia de seguridad de cargas de trabajo en el Portal de Azure clásico | Microsoft Docs"
description: "Asegúrese de que el entorno está preparado correctamente para hacer copias de seguridad de las cargas de trabajo con Azure Backup Server"
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
keywords: "azure backup server; almacén de copia de seguridad"
ms.assetid: d86450e8-da63-4283-8fd7-2ee629fa14ab
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: ffef289e154986e4b08a072d3a95f77818fb9c35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (clásico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clásico)](backup-azure-dpm-introduction-classic.md)
>
>

En este artículo, se trata cómo preparar el entorno para hacer copias de seguridad de las cargas de trabajo con Azure Backup Server. Con Azure Backup Server, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola:

> [!WARNING]
> Azure Backup Server hereda la funcionalidad de Data Protection Manager (DPM) para copias de seguridad de cargas de trabajo. Encontrará punteros para la documentación de DPM para algunas de estas capacidades. Sin embargo, Azure Backup Server no ofrece protección en cinta o integración con System Center.
>
>

## <a name="1-windows-server-machine"></a>1. Máquina de Windows Server
![step1](./media/backup-azure-microsoft-azure-backup/step1.png)

El primer paso para que Azure Backup Server funcione es tener una máquina Windows Server.

| Ubicación | Requisitos mínimos | Instrucciones adicionales |
| --- | --- | --- |
| Azure |Máquina virtual de Azure IaaS<br><br>A2 Standard: 2 núcleos, 3,5 GB de RAM |Puede comenzar por una galería de imágenes sencilla de Windows Server 2012 R2 Datacenter. [La protección de cargas de trabajo de IaaS con Azure Backup Server (DPM)](https://technet.microsoft.com/library/jj852163.aspx) presenta numerosos matices. Asegúrese de leer el artículo completo antes de implementar la máquina. |
| Local |Máquina virtual de Hyper-V,<br> Máquina virtual de VMware<br> o un host físico<br><br>2 núcleos y 4 GB de RAM |Puede desduplicar el almacenamiento de DPM con la desduplicación de Windows Server. Más información sobre cómo funcionan juntos [DPM y la desduplicación](https://technet.microsoft.com/library/dn891438.aspx) al implementarlos en máquinas virtuales de Hyper-V. |

> [!NOTE]
> Se recomienda instalar el Azure Backup Server en una máquina con Windows Server 2012 R2 Datacenter. Muchos de los requisitos previos quedan resueltos automáticamente con la versión más reciente del sistema operativo Windows.
>
>

Si planea unir la instancia de Azure Backup Server a un dominio, se recomienda que una el servidor físico o la máquina virtual al dominio antes de instalar el software de Azure Backup Server. *No se permite* mover una instancia de Azure Backup Server a un dominio nuevo después de la implementación.

## <a name="2-backup-vault"></a>2. Almacén de copia de seguridad
![step2](./media/backup-azure-microsoft-azure-backup/step2.png)

Si envía datos de copia de seguridad a Azure o los mantiene localmente, Azure Backup Server debe registrarse en un almacén. Si es un usuario nuevo de Azure Backup y desea usar Azure Backup Server, consulte la versión de Azure Portal del artículo [Preparación para la copia de seguridad de cargas de trabajo en Microsoft Azure](backup-azure-microsoft-azure-backup.md).

> [!IMPORTANT]
> A partir de marzo de 2017, ya no podrá usar el portal clásico para crear almacenes de Backup.
> Ahora puede actualizar los almacenes de Backup a almacenes de Recovery Services. Para más información, consulte el artículo [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft anima a actualizar los almacenes de Backup a almacenes de Recovery Services.<br/> A partir del 15 de octubre de 2017, no podrá usar PowerShell para crear almacenes de Backup. **El 1 de noviembre de 2017**:
>- Todos los almacenes de Backup restantes se actualizarán automáticamente a almacenes de Recovery Services.
>- No podrá acceder a los datos de copia de seguridad en el portal clásico. En su lugar, utilice Azure Portal para tener acceso a los datos de copia de seguridad en los almacenes de Recovery Services.
>



## <a name="3-software-package"></a>3. Paquete de software
![step3](./media/backup-azure-microsoft-azure-backup/step3.png)

### <a name="downloading-the-software-package"></a>Descarga del paquete de software
De forma similar a las credenciales del almacén, puede descargar Microsoft Azure Backup para cargas de trabajo de aplicaciones desde la **página de inicio rápido** del almacén de copia de seguridad.

1. Haga clic en **For Application Workloads (Disk to Disk to Cloud)**(Para cargas de trabajo de aplicaciones (de disco a disco y a nube)). Esto le llevará a la página del Centro de descargas donde podrá descargar el paquete de software.

    ![Pantalla de bienvenida de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)
2. Haga clic en **Descargar**.

    ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)
3. Seleccione todos los archivos y haga clic en **Siguiente**. Descargue todos los archivos procedentes de la página de descarga de Microsoft Azure Backup y colóquelos en la misma carpeta.
   ![Centro de descarga 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Puesto que el tamaño de descarga de todos los archivos juntos es de más de 3 GB, con un vínculo de descarga a 10 Mbps, se puede tardar hasta 60 minutos en completarla.

### <a name="extracting-the-software-package"></a>Extracción del paquete de software
Después de descargar todos los archivos, haga clic en **MicrosoftAzureBackupInstaller.exe**. Se inicia el **Asistente para instalación de Microsoft Azure Backup**, que extraerá los archivos de instalación en una ubicación especificada por el usuario. Siga con el asistente y haga clic en el botón **Extraer** para comenzar el proceso de extracción.

> [!WARNING]
> Se requieren al menos 4 GB de espacio libre para extraer los archivos de instalación.
>
>

![Asistente para instalación de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Después de completar el proceso de extracción, active la casilla para iniciar el archivo *setup.exe* recién extraído y empezar a instalar el servidor de Microsoft Azure Backup. Luego, haga clic en el botón **Finalizar**.

### <a name="installing-the-software-package"></a>Instalación del paquete de software
1. Haga clic en **Microsoft Azure Backup** para iniciar el asistente para la instalación.

    ![Asistente para instalación de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. En la pantalla de bienvenida, haga clic en **Siguiente** . Irá a la sección *Prerequisite Checks* (Comprobaciones de requisitos previos). En esta pantalla, haga clic en el botón **Comprobar** para determinar si se cumplieron los requisitos previos de hardware y software para Azure Backup Server. Si se cumplieron todos los requisitos previos, verá un mensaje que indica que la máquina los cumple. Haga clic en el botón **Siguiente** .

    ![Azure Backup Server - Bienvenida y requisitos previos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server requiere SQL Server Standard y en el paquete de instalación de Azure Backup Server se incluyen los correspondientes archivos binarios de SQL Server necesarios. Cuando comience una nueva instalación de Azure Backup Server, debe elegir la opción **Install new Instance of SQL Server with this Setup** (Instalar nueva instancia de SQL Server con esta configuración) y hacer clic en el botón **Comprobar e instalar**. Una vez que los requisitos previos se instalen correctamente, haga clic en **Next**(Siguiente).

    ![Azure Backup Server - Comprobación de SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Si se produce un error con una recomendación para reiniciar el equipo, proceda a reiniciar y haga clic en **Check Again**(Comprobar de nuevo).

   > [!NOTE]
   > Azure Backup Server no funcionará con una instancia remota de SQL Server. La instancia que se utiliza en Azure Backup Server debe ser local.
   >
   >

4. Proporcione una ubicación donde instalar los archivos del servidor de Microsoft Azure Backup y haga clic en **Next**(Siguiente).

    ![Requisitos previos de Microsoft Azure Backup2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La ubicación temporal es un requisito para hacer copias de seguridad en Azure. Asegúrese de que la ubicación temporal sea al menos el 5% de los datos cuya copia de seguridad se planea hacer en la nube. Para la protección de disco, deben configurarse discos independientes una vez completada la instalación. Para obtener más información acerca de los grupos de almacenamiento, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Proporcione una contraseña segura para las cuentas de usuario locales con permisos restringidos y haga clic en **Next**(Siguiente).

    ![Requisitos previos de Microsoft Azure Backup2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Seleccione si desea usar *Microsoft Update* para comprobar si hay actualizaciones y haga clic en **Next**(Siguiente).

   > [!NOTE]
   > Se recomienda que Windows Update se redirija a Microsoft Update, que ofrece actualizaciones importantes y de seguridad para Windows y otros productos como Microsoft Azure Backup Server.
   >
   >

    ![Requisitos previos de Microsoft Azure Backup2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Revise *Summary of Settings* (Resumen de la configuración) y haga clic en **Install**(Instalar).

    ![Requisitos previos de Microsoft Azure Backup2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. La instalación se realiza en fases. En la primera fase, se instala el agente de Servicios de recuperación de Microsoft Azure en el servidor. El asistente comprueba igualmente la conectividad a Internet. Si la conectividad a Internet está disponible, puede continuar con la instalación; de lo contrario, debe proporcionar los detalles del proxy para conectarse a Internet.

    El siguiente paso es configurar el agente de Servicios de recuperación de Microsoft Azure. Como parte de la configuración, tendrá que proporcionar las credenciales del almacén para registrar la máquina en el almacén de copia de seguridad. También proporcionará una frase de contraseña para cifrar y descifrar los datos enviados entre Azure y sus instalaciones. Puede generar una frase de contraseña automáticamente o proporcionar la suya propia, con un mínimo de 16 caracteres. Continúe con el asistente hasta que se haya configurado el agente.

    ![Requisitos previos de Azure Backup Server2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Una vez que se complete correctamente el registro de Microsoft Azure Backup Server, el asistente para instalación global prosigue con la instalación y configuración de los componentes de SQL Server y de Microsoft Azure Backup Server. Tras completarse la instalación de los componentes de SQL Server, se instalan los componentes de Azure Backup Server.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Cuando el paso de instalación haya finalizado, se habrán creado también los iconos de escritorio del producto. Haga doble clic en el icono para iniciar el producto.

### <a name="add-backup-storage"></a>Incorporación de almacenamiento de copia de seguridad
La primera copia de seguridad se mantiene en el almacenamiento conectado a la máquina de Azure Backup Server. Para obtener más información acerca de los discos, consulte [Configuración de bloques de almacenamiento y almacenamiento en disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Debe agregar el almacenamiento de copia de seguridad incluso si tiene pensado enviar los datos a Azure. En la arquitectura actual de Azure Backup Server, el almacén de Azure Backup contiene la *segunda* copia de los datos, mientras que el almacenamiento local contiene la primera (y obligatoria) copia de seguridad.  
>
>

## <a name="4-network-connectivity"></a>4. Conectividad de red
![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

Azure Backup Server requiere conectividad al servicio Azure Backup para que el producto funcione correctamente. Para validar si la máquina tiene conectividad a Azure, use el cmdlet ```Get-DPMCloudConnection``` en la consola de PowerShell de Azure Backup Server. Si la salida del commandlet es TRUE, entonces existe conectividad, de lo contrario, no hay conectividad.

Además, la suscripción de Azure debe encontrarse en un estado correcto. Para averiguar el estado de la suscripción y administrarla, inicie sesión en el [portal de suscripción](https://account.windowsazure.com/Subscriptions).

Una vez que conozca el estado de la conectividad y suscripción de Azure, puede usar la tabla siguiente para saber el impacto en la funcionalidad de copia de seguridad y restauración que se ofrece.

| Estado de conectividad | Suscripción de Azure | Copia de seguridad en Azure | Copia de seguridad en disco | Restauración desde Azure | Restauración desde disco |
| --- | --- | --- | --- | --- | --- |
| Conectado |Active |Permitida |Permitida |Permitida |Permitida |
| Conectado |Expirada |Stopped |Stopped |Permitida |Permitida |
| Conectado |Desaprovisionada |Stopped |Stopped |Detenida y puntos de recuperación de Azure eliminados |Stopped |
| Pérdida de conectividad > 15 días |Active |Stopped |Stopped |Permitida |Permitida |
| Pérdida de conectividad > 15 días |Expirada |Stopped |Stopped |Permitida |Permitida |
| Pérdida de conectividad > 15 días |Desaprovisionada |Stopped |Stopped |Detenida y puntos de recuperación de Azure eliminados |Stopped |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperación de una pérdida de conectividad
Si tiene un firewall o un proxy que impide el acceso a Azure, deberá permitir primero las siguientes direcciones de dominio en el perfil del firewall/proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Una vez restaurada la conectividad a Azure en la máquina de Azure Backup Server, las operaciones que pueden realizarse dependen del estado de la suscripción de Azure. La tabla anterior incluye detalles acerca de las operaciones permitidas una vez que la máquina esté "Conectada".

### <a name="handling-subscription-states"></a>Control de los estados de la suscripción
Es posible llevar una suscripción de Azure desde un estado *Expirado* o *Desaprovisionado* hasta un estado *Activo*. Sin embargo, esto tiene algunos efectos sobre el comportamiento del producto mientras el estado no sea *Activo*:

* Una suscripción con estado *Desaprovisionado* pierde la funcionalidad durante el período en que está desaprovisionada. Al pasar a *Activo*, se reactiva la funcionalidad del producto de copia de seguridad y restauración. Los datos de copia de seguridad del disco local también pueden recuperarse en caso de que se haya mantenido con un período de retención lo suficientemente amplio. No obstante, los datos de copia de seguridad de Azure se pierden irremediablemente una vez que la suscripción pasa al estado *Desaprovisionado* .
* Una suscripción con estado *Expirado* solo pierde la funcionalidad hasta que pase de nuevo al estado *Activo*. Las copias de seguridad programadas para el período en el que la suscripción tenía el estado *Expirado* no se ejecutarán.

## <a name="troubleshooting"></a>Solución de problemas
Si el servidor de Microsoft Azure Backup produce un error durante la fase de instalación (o copia de seguridad o restauración), consulte este [documento de códigos de error](https://support.microsoft.com/kb/3041338) para más información.
También puede consultar [Azure Backup - Preguntas más frecuentes](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la [preparación del entorno para DPM](https://technet.microsoft.com/library/hh758176.aspx) , visite el sitio de Microsoft TechNet. También contiene información sobre las configuraciones admitidas en las que se puede implementar y usar Azure Backup Server.

Puede usar estos artículos para mejorar la comprensión sobre la protección de cargas de trabajo mediante el servidor Microsoft Azure Backup Server.

* [Copia de seguridad de SQL Server](backup-azure-backup-sql.md)
* [Copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)
* [Copia de seguridad de otro servidor](backup-azure-alternate-dpm-server.md)
