---
title: "Copia de seguridad y restauración del sistema operativo de SAP HANA en SKU de tipo II de Azure (instancias grandes)| Microsoft Docs"
description: "Realización de copia de seguridad y restauración del sistema operativo de SAP HANA en SKU de tipo II de Azure (instancias grandes)"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41349cd7fe3bf39b5b42c44ba47acf980d15ebe7
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Copia de seguridad y restauración del sistema operativo para SKU de tipo II

En este documento se describen los pasos para realizar una copia de seguridad y una restauración del sistema operativo para las **SKU de tipo II** de instancias grandes de HANA. 

>[!NOTE]
>En los scripts de copia de seguridad del SO se usa el software ReaR, que viene preinstalado en el servidor.  

Después de que el equipo de Microsoft Service Management ha realizado el aprovisionamiento, el servidor se configura de forma predeterminada con dos programaciones de copia de seguridad para realizar la copia de seguridad del sistema operativo completo. Puede comprobar la programación del trabajo de copia de seguridad mediante el comando siguiente:
```
#crontab –l
```
En cualquier momento se puede cambiar la programación de la copia de seguridad mediante el siguiente comando:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Creación de una copia de seguridad manual

La copia de seguridad del sistema operativo ya está programada mediante un **trabajo de cron**. Sin embargo, también se puede realizar manualmente la copia de seguridad del sistema operativo. Para realizar una copia de seguridad manual, ejecute el siguiente comando:

```
#rear -v mkbackup
```
La siguiente pantalla muestra la copia de seguridad manual de ejemplo:

![¿Cómo?](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Restauración de una copia de seguridad

Puede restaurar una copia de seguridad completa o archivos individuales de una copia de seguridad. Para ello, use el siguiente comando:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Después de la restauración, el archivo se recupera en el directorio de trabajo actual.

El comando siguiente muestra la restauración de un archivo */etc/fstabfrom* del archivo de copia de seguridad *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Deberá copiar el archivo en la ubicación deseada después de restaurarlo de la copia de seguridad.

En la captura de pantalla siguiente se muestra la restauración de una copia de seguridad completa:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Instalación de la herramienta ReaR y cambio de la configuración 

Los paquetes Relax-y Recover (ReaR) se encuentra **preinstalados** en las **SKU de tipo II** de las instancias grandes de HANA, y no es necesaria ninguna acción por parte del usuario. Puede comenzar a usar directamente ReaR para la copia de seguridad del sistema operativo.
Sin embargo, en aquellos casos en que necesite instalar los paquetes por su cuenta, puede seguir los pasos que se indican para instalar y configurar esta herramienta.

Para instalar los paquetes de copia de seguridad de **ReaR**, use los siguientes comandos:

Para el sistema operativo **SLES**, use el siguiente comando:
```
#zypper install <rear rpm package>
```
Para el sistema operativo **RHEL**, use el siguiente comando: 
```
#yum install rear -y
```
Para configurar la herramienta ReaR, debe actualizar los parámetros **OUTPUT_URL** y **BACKUP_URL** del *archivo /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

En la captura de pantalla siguiente se muestra la restauración de una copia de seguridad completa: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
