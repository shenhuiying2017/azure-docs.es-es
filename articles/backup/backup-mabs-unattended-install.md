---
title: "Instalación silenciosa de Azure Backup Server v2 | Microsoft Docs"
description: "Use un script de PowerShell para realizar una instalación silenciosa de Azure Backup Server v2. Este tipo de instalación también se denomina instalación desatendida."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Ejecutar una instalación desatendida de Azure Backup Server v2

Obtenga información sobre cómo ejecutar una instalación desatendida de Azure Backup Server v2. 

Estos pasos no se aplican si va a instalar Azure Backup Server v1.

## <a name="install-backup-server-v2"></a>Instalar Backup Server v2

1. En el servidor que hospeda Azure Backup Server v2, cree un archivo de texto. (Puede crear el archivo en el Bloc de notas o en otro editor de texto). Guarde el archivo como MABSSetup.ini. 

2. Pegue el código siguiente en el archivo MABSSetup.ini. Reemplace el texto entre corchetes (\< \>) por los valores de su entorno. A continuación se muestra un texto de ejemplo:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Guarde el archivo . Después, en un símbolo del sistema con privilegios elevados en el servidor de instalación, escriba este comando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Puede usar estas marcas para la instalación:</br>
**/f**: ruta de acceso del archivo .ini</br>
**/l**: ruta de acceso del registro</br>
**/i**: ruta de acceso de instalación</br>
**/x**: ruta de acceso de desinstalación</br>

## <a name="next-steps"></a>Pasos siguientes
Después de instalar Backup Server, sepa cómo preparar el servidor o empezar a proteger la carga de trabajo.

- [Preparar cargas de trabajo de Backup Server](backup-azure-microsoft-azure-backup.md)
- [Usar Backup Server para hacer una copia de seguridad de un servidor de VMware](backup-azure-backup-server-vmware.md)
- [Usar Backup Server para hacer una copia de seguridad de SQL Server](backup-azure-sql-mabs.md)
- [Agregar Modern Backup Storage a Backup Server](backup-mabs-add-storage.md)
