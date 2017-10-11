---
title: Uso de Azure Backup para reemplazar la infraestructura de cintas | Microsoft Docs
description: "Aprenda cómo la Copia de seguridad de Microsoft Azure proporciona semántica similar a la cinta que le permite hacer copias de seguridad y restaurar datos en Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
ms.assetid: 2e1bb67d-986c-4437-8056-3a63169b4214
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/10/2017
ms.author: saurse;trinadhk;markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0f3152daf5f91f7c9e540797bf09b21969d2d33
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Traslado del almacenamiento a largo plazo de la cinta a la nube de Azure
Los clientes de Copia de seguridad de Microsoft Azure y System Center Data Protection Manager pueden:

* Realizar copias de seguridad de datos en las programaciones que mejor se adapten a las necesidades de su organización.
* Conservar los datos de copia de seguridad durante períodos más largos.
* Hacer a Azure partícipe de sus necesidades de retención a largo plazo (en lugar de la cinta).

Este artículo explica cómo los clientes pueden habilitar las directivas de copia de seguridad y retención. Los clientes que utilicen las cintas para abordar sus necesidades de retención a largo plazo ahora tienen una alternativa viable y eficaz con la disponibilidad de esta característica. La característica está habilitada en la versión más reciente de la copia de seguridad de Azure (que está disponible [aquí](http://aka.ms/azurebackup_agent)). Debe actualizarse a los clientes System Center DPM, como mínimo, DPM 2012 R2 UR5 para usar DPM con el servicio de Azure Backup.

## <a name="what-is-the-backup-schedule"></a>¿Cuál es la programación de copia de seguridad?
La programación de copia de seguridad indica la frecuencia de la operación de copia de seguridad. Por ejemplo, la configuración de la pantalla siguiente indica que las copias de seguridad se tomarán diariamente a las 18:00 h y a medianoche.

![Programación diaria](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Los clientes también pueden programar una copia de seguridad semanal. Por ejemplo, la configuración de la pantalla siguiente indica que las copias de seguridad se realizarán alternando entre cada domingo y miércoles a las 09:30 h y a la 01: 00 h.

![Programación semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>¿Qué es la directa de retención?
La directiva de retención especifica el tiempo durante el que debe almacenarse la copia de seguridad. En vez de especificar solo una "directiva" para todos los puntos de copia de seguridad, los clientes pueden especificar directivas de retención diferentes en función de cuándo se realizó la copia de seguridad. Por ejemplo, un punto de copia de seguridad diario (que sirve como punto de recuperación operativo) podría conservarse durante 90 días. El punto de copia de seguridad realizado al final de cada trimestre con fines de auditoría se conserva durante un período prolongado.

![Directiva de retención](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

El número total de "puntos de retención" especificado en esta directiva es de 90 (puntos diarios) + 40 (uno cada trimestre durante 10 años) = 130.

## <a name="example--putting-both-together"></a>Ejemplo: reunir ambos
![Pantalla de ejemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Directiva de retención diaria**: las copias de seguridad realizadas diariamente se almacenan durante 7 días.
2. **Directiva de retención semanal**: las copias de seguridad realizadas todos los días a medianoche y el sábado a las 18:00 h se conservan durante 4 semanas.
3. **Directiva de retención mensual**: las copias de seguridad realizadas a medianoche y a las 18:00 h del último sábado de cada mes se conservarán durante 12 meses.
4. **Directiva de retención anual**: las copias de seguridad realizadas del último sábado de cada mes de marzo se conservarán durante 10 años.

El número total de "puntos de retención" (desde los que un cliente puede restaurar datos) en el diagrama anterior se calcula de la forma siguiente:

* 2 puntos por día durante 7 días = 14 puntos de recuperación
* 2 puntos por día durante 4 semanas = 8 puntos de recuperación
* 2 puntos por mes durante 12 meses = 24 puntos de recuperación
* 1 punto por año durante 10 años = 10 puntos de recuperación

El número total de puntos de recuperación es 56.

> [!NOTE]
> La copia de seguridad de Azure no tiene una restricción sobre el número de puntos de recuperación.
>
>

## <a name="advanced-configuration"></a>Configuración avanzada
Al hacer clic en **Modificar** en la pantalla anterior, los clientes tienen más flexibilidad para especificar programaciones de retención.

![Modificar](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Azure Backup, vea

* [Introducción a la Copia de seguridad de Azure](backup-introduction-to-azure-backup.md)
* [Probar Copia de seguridad de Azure](backup-try-azure-backup-in-10-mins.md)
