---
title: "Planeamiento de la capacidad y el escalado de la replicación de máquinas virtuales de Hyper-V (con VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Use este artículo para planear la capacidad y la escala al replicar en Azure máquinas virtuales de Hyper-V en nubes de VMM con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>Paso 3: Planeamiento de la capacidad y el escalado para la replicación de Hyper-V (con VMM) en Azure

Después de revisar los [requisitos previos de implementación](vmm-to-azure-walkthrough-prerequisites.md), use este artículo para averiguar cómo planear la capacidad y el escalado al replicar en Azure máquinas virtuales locales de Hyper-V en nubes de System Center Virtual Machine Manager (VMM) con [Azure Site Recovery](site-recovery-overview.md).

Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="how-do-i-start-capacity-planning"></a>¿Cómo se puede iniciar el planeamiento de la capacidad?


Recopile información sobre el entorno de replicación y luego planee la capacidad con estos datos y las consideraciones resaltadas en este artículo.


## <a name="gather-information"></a>Recopilación de información

1. Recopilar información sobre su entorno de replicación, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
2. Identifique la tasa de cambio (renovación) diaria para los datos replicados. Descargue la [herramienta de planeamiento de la capacidad de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obtener la tasa de cambio. Se recomienda ejecutar esta herramienta durante una semana para capturar los promedios.
 

## <a name="figure-out-capacity"></a>Averiguar la capacidad

En función de la información que ha recopilado, ejecute la herramienta [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) para analizar el entorno de origen y las cargas de trabajo, calcular las necesidades de ancho de banda, los recursos de servidor que necesita en la ubicación de origen y los recursos (máquinas virtuales, almacenamiento, etc.) que necesita en la ubicación de destino. Puede ejecutar la herramienta de dos modos distintos:

- Planeamiento rápido: ejecute la herramienta en este modo para obtener las proyecciones de la red y del servidor según el promedio de las máquinas virtuales, discos, almacenamiento y tasa de cambio.
- Planeamiento detallado: ejecute la herramienta en este modo y proporcione detalles de cada carga de trabajo a nivel de máquina virtual. Analice la compatibilidad de la máquina virtual y obtenga proyecciones de red y del servidor.

Ahora, ejecute la herramienta:

1. Descargue la [herramienta](http://aka.ms/asr-capacity-planner-excel)
2. Para ejecutar el planeamiento rápido, siga [estas instrucciones](site-recovery-capacity-planner.md#run-the-quick-planner) y seleccione el escenario **Hyper-V a Azure**.
3. Para ejecutar el planeamiento detallado, siga [estas instrucciones](site-recovery-capacity-planner.md#run-the-detailed-planner) y seleccione el escenario **Hyper-V a Azure**.

## <a name="control-network-bandwidth"></a>Ancho de banda de red de control

Después de que ha calculado el ancho de banda que necesita, tiene dos opciones para controlar la cantidad de ancho de banda utilizado para la replicación:

* **Limitar ancho de banda**: el tráfico de Hyper-V que se replica en Azure pasa a través de un host de Hyper-V específico. Puede limitar el ancho de banda en el servidor host.
* **Influir en el ancho de banda**: puede influir en el ancho de banda utilizado para la replicación mediante un par de claves del registro.

### <a name="throttle-bandwidth"></a>Limitar el ancho de banda
1. Abra el complemento Microsoft Azure Backup en el servidor host de Hyper-V. De manera predeterminada, hay disponible un acceso directo a Microsoft Azure Backup en el escritorio o en la siguiente ruta de acceso: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Limitación**, seleccione **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** y defina los límites durante las horas de trabajo y fuera de las horas de trabajo. Los intervalos válidos van de 512 Kbps a 102 Mbps por segundo.

    ![Limitar el ancho de banda](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

También puede utilizar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer la limitación. Aquí tiene un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que no se requiere ninguna limitación.

### <a name="influence-network-bandwidth"></a>Control del uso de ancho de banda de red
1. En el Registro, vaya a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influir en el tráfico de ancho de banda en un disco de replicación, modifique el valor de **UploadThreadsPerVM**o cree la clave en caso de que no exista.
   * Para influir en el ancho de banda para el tráfico de conmutación por recuperación de Azure, modifique el valor **DownloadThreadsPerVM**.
2. El valor predeterminado es 4. En una red "sobreaprovisionada", se deben cambiar los valores predeterminados de estas claves de registro. El valor máximo es 32. Supervise el tráfico para optimizar el valor.

## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 4: Planeamiento de las redes](vmm-to-azure-walkthrough-network.md).
