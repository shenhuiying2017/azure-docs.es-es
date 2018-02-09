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
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Solución de problemas de System Center Data Protection Manager

[Aquí](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016) puede encontrar las notas de la versión más recientes de SC DPM.
[Aquí](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016) se puede encontrar la Matriz de compatibilidad de protección.

## <a name="replica-is-inconsistent"></a>La réplica es incoherente

Este error puede suceder por diversos motivos como: error en el trabajo de creación de réplicas, problemas con el diario de cambios, errores de mapa de bits de filtro de nivel de volumen, la máquina de origen se cerró inesperadamente, el desbordamiento del registro de sincronización o la réplica son realmente incoherentes. Para resolver el problema, siga estos pasos:
- Para eliminar el estado incoherente, ejecute una comprobación de coherencia manual o programe una comprobación de coherencia diaria.
- Asegúrese de que se encuentra en la versión más reciente del servidor de MAB o System Center DPM
- Asegúrese de que está habilitada la comprobación de coherencia automática
- Pruebe a reiniciar los servicios desde el símbolo del sistema ("net stop dpmra", seguido por "net start dpmra")
- Asegúrese de que se cumplen los requisitos de conectividad y ancho de banda de red
- Compruebe si la máquina de origen se apagó de forma inesperada
- Asegúrese de que el disco es correcto y de que hay suficiente espacio para la réplica
- Asegúrese de que no se ejecutan trabajos de copia de seguridad duplicados simultáneamente

## <a name="online-recovery-point-creation-failed"></a>No se pudo crear el punto de recuperación en línea

Para resolver el problema, siga estos pasos:
- Asegúrese de que se encuentra en la versión más reciente de Azure Backup Agent
- Pruebe a crear manualmente un punto de recuperación en el área de tareas protección
- Asegurarse de que ejecuta la comprobación de coherencia en el origen de datos
- Asegúrese de que se cumplen los requisitos de conectividad y ancho de banda de red
- Los datos de la réplica está en un estado incoherente. Cree un punto de recuperación de disco de este origen de datos
- Asegúrese de que la réplica está presente, de que no falta
- La réplica tiene espacio suficiente para crear un diario USN

## <a name="unable-to-configure-protection"></a>No se puede configurar la protección.

Este error aparece cuando el servidor DPM no puede ponerse en contacto con el servidor protegido. Para resolver el problema, siga estos pasos:
- Asegúrese de que se encuentra en la versión más reciente de Azure Backup Agent
- Asegúrese de que hay conectividad (red/firewall/proxy) entre su servidor DPM y el servidor protegido
- Si va a proteger SQL Server, asegúrese de que sysadmin se habilita en NT AUTHORITY\SYSTEM en Propiedades de inicio de sesión

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Este servidor no está registrado en el almacén especificado por la credencial del almacén

Este error aparece cuando el archivo de credenciales del almacén seleccionado no pertenece al almacén de Recovery Services asociado a la instancia de System Center DPM o Azure Backup Server en la que se intenta la recuperación. Para resolver el problema, siga estos pasos:
- Descargue el archivo de credenciales del almacén de Recovery Services en el que está registrada la instancia de System Center DPM o Azure Backup Server.
- Pruebe a registrar el servidor en el almacén mediante el archivo de credenciales de almacén descargado más reciente.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>Los datos recuperables no están disponibles o el servidor seleccionado no es un servidor DPM
Este error aparece cuando no hay otros no hay ningún otras instancias de System Center DPM o Azure Backup Server registradas en el almacén de Recovery Services, o bien los servidores aún no han cargado los metadatos o el servidor seleccionado no es una instancia de System Center DPM o Azure Backup Server.
- Si hay otras instancias de System Center DPM o Azure Backup Server registradas en el almacén de Recovery Services, asegúrese de que está instalado el agente de Azure Backup más reciente.
- Si hay otras instancias de System Center DPM o Azure Backup Server registradas en el almacén de Recovery Services, espere un día después de la instalación para iniciar el proceso de recuperación. El trabajo nocturno carga los metadatos de todas las copias de seguridad protegidas en la nube. Los datos están disponibles para su recuperación.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>La frase de contraseña de cifrado especificada no coincide con la asociada al siguiente servidor

> [!NOTE]
>Si ha olvidado o perdido la frase de contraseña de cifrado, no hay posibilidad alguna de recuperar los datos. La única opción es volver a generar la frase de contraseña y usarla para cifrar los datos de las siguientes copias de seguridad.
>
>

Este error se produce cuando la frase de contraseña de cifrado que se usa en el proceso de cifrado de los datos de System Center DPM o Azure Backup Server que se recuperan no coincide con la especificada. El agente no puede descifrar los datos. Por lo tanto, se produce un error en la recuperación. Para resolver el problema, siga estos pasos:
- Especifique la misma frase de contraseña de cifrado que está asociada al System Center DPM o Azure Backup Server cuyos datos se recuperan. 
