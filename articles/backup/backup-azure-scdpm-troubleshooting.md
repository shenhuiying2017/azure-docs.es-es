---
title: "Solución de problemas de System Center Data Protection Manager con Azure Backup | Microsoft Docs"
description: "Solución de problemas de System Center Data Protection Manager."
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: bf4ea676c5309bb732f6a4ce71849606b4d2e4df
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Solución de problemas de System Center Data Protection Manager

En este artículo se describen soluciones para problemas que puede encontrar al usar Data Protection Manager.

Para obtener las notas de versión más recientes de System Center Data Protection Manager, consulte la [documentación de System Center](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Puede aprender más sobre la compatibilidad para Data Protection Manager en [esta matriz](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Error: La réplica no es coherente

Una réplica puede ser incoherente por las razones siguientes:
- El trabajo de creación de réplica da error.
- Hay problemas con el diario de cambios.
- El mapa de bits del filtro de nivel de volumen contiene errores.
- La máquina de origen se apaga inesperadamente.
- El registro de sincronización se desborda.
- La réplica es realmente incoherente.

Para resolver este problema, realice las acciones siguientes:
- Para quitar el estado incoherente, ejecute la comprobación de coherencia manual o programe una comprobación de coherencia diaria.
- Asegúrese de que está usando la versión más reciente de Microsoft Azure Backup Server y Data Protection Manager.
- Asegúrese de que la configuración de **coherencia automática** está habilitada.
- Pruebe a reiniciar los servicios desde el símbolo del sistema. Use el comando `net stop dpmra` seguido de `net start dpmra`.
- Asegúrese de que se cumplen los requisitos de conectividad y ancho de banda de red.
- Compruebe si la máquina de origen se apagó de forma inesperada.
- Asegúrese de que el disco es correcto y que hay suficiente espacio para la réplica.
- Asegúrese de que no se ejecutan trabajos de copia de seguridad duplicados simultáneamente.

## <a name="error-online-recovery-point-creation-failed"></a>Error: Error en la creación de punto de recuperación en línea

Para resolver este problema, realice las acciones siguientes:
- Asegúrese de que usa la versión más reciente del agente de Azure Backup.
- Pruebe a crear manualmente un punto de recuperación en el área de tareas de protección.
- Asegúrese de que ejecuta una comprobación de coherencia en el origen de datos.
- Asegúrese de que se cumplen los requisitos de conectividad y ancho de banda de red.
- Cuando los datos de réplica estén en un estado incoherente, cree un punto de recuperación de disco de este origen de datos.
- Asegúrese de que la réplica está presente, de que no falta.
- Asegúrese de que la réplica tiene espacio suficiente para crear el diario de números de secuencias actualizadas (USN).

## <a name="error-unable-to-configure-protection"></a>Error: No se pudo configurar la protección

Este error se produce cuando el servidor Data Protection Manager no puede ponerse en contacto con el servidor protegido. 

Para resolver este problema, realice las acciones siguientes:
- Asegúrese de que usa la versión más reciente del agente de Azure Backup.
- Asegúrese de que hay conectividad (red, firewall y proxy) entre su servidor Data Protection Manager y el servidor protegido.
- Si va a proteger un servidor SQL Server, asegúrese de que la propiedad **Propiedades de inicio de sesión** > **AUTORIDAD DE NT\SISTEMA** muestra la configuración **sysadmin** habilitada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Error: Servidor no registrado tal como se especifica en el archivo de credenciales de almacén

Este error se produce durante el proceso de recuperación de datos de servidor Data Protection Manager o Azure Backup Server. El archivo de credenciales de almacén que se usa en el proceso de recuperación no pertenece al almacén de Recovery Services para el servidor Data Protection Manager o Azure Backup Server.

Para resolver este problema, realice estos pasos:
1. Descargue el archivo de credenciales del almacén de Recovery Services en el que está registrado el servidor Data Protection Manager o Azure Backup Server.
2. Pruebe a registrar el servidor en el almacén mediante el archivo de credenciales de almacén descargado más recientemente.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Error: No hay datos recuperables o el servidor seleccionado no es un servidor Data Protection Manager

Este error se produce por las siguientes razones:
- No hay otros servidores Data Protection Manager o Azure Backup Server registrados en el almacén de Recovery Services.
- Los servidores aún no han cargado los metadatos.
- El servidor seleccionado no es un servidor Data Protection Manager o Azure Backup Server.

Cuando otros servidores Data Protection Manager o Azure Backup Server no estén registrados en el almacén de Recovery Services, realice estos pasos para resolver el problema:
1. Asegúrese de que el agente de Azure Backup está instalado.
2. Después de asegurarse de que el agente más reciente está instalado, espere un día antes de empezar el proceso de recuperación. El trabajo de copia de seguridad nocturno carga los metadatos de todas las copias de seguridad protegidas en la nube. Después, los datos de copia de seguridad estarán disponibles para la recuperación.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Error: La frase de contraseña proporcionada no coincide con la frase de contraseña para el servidor

Este error se produce durante el proceso de cifrado cuando se recuperan datos de servidor Data Protection Manager o Azure Backup Server. La frase de contraseña de cifrado que se utiliza en el proceso de recuperación no coincide con la frase de contraseña de cifrado del servidor. Como resultado, el agente no puede descifrar los datos y se produce un error en la recuperación.

> [!IMPORTANT]
> Si olvida o pierde la frase de contraseña de cifrado, no hay ningún otro método para recuperar los datos. La única opción consiste en volver a generar la frase de contraseña. Use la nueva frase de contraseña para cifrar los datos de copia de seguridad futuros.
>
> Cuando esté recuperando datos, proporcione siempre la misma frase de contraseña de cifrado que esté asociada al servidor Data Protection Manager o Azure Backup Server. 
>
