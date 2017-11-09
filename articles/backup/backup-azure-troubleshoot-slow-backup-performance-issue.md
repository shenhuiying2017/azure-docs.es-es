---
title: "Solución del problema de la lentitud de copias de seguridad de archivos y carpetas en Azure Backup | Microsoft Docs"
description: "Le proporciona una guía para solucionar problemas que le ayudará a diagnosticar la causa de los problemas de rendimiento de Azure Backup"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 373a98855886cc7be7518c664f82bb6f92ca86f3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Solución de problemas de lentitud en la copia de seguridad de archivos y carpetas en Azure Backup
Este artículo proporciona una guía para la solución de problemas que le ayudará a diagnosticar la causa de un rendimiento lento en la copia de seguridad de archivos y carpetas cuando se usa Azure Backup. Si se utiliza el agente de Azure Backup para hacer copia de seguridad de los archivos, el proceso puede tardar más de lo esperado. Este problema puede deberse a uno o a varios de los siguientes motivos:

* [Hay cuellos de botella que afectan al rendimiento del equipo en el que se realiza la copia de seguridad](#cause1)
* [Otro proceso o software antivirus que interfiere en el proceso de Azure Backup](#cause2)
* [El agente de Copia de seguridad se ejecuta en una máquina virtual (VM) de Azure.](#cause3)  
* [Se realiza una copia de seguridad de un número elevado (varios millones) de archivos.](#cause4)

Antes de empezar a solucionar problemas, se recomienda descargar e instalar el [agente de Azure Backup más reciente](http://aka.ms/azurebackup_agent). Se realizan actualizaciones frecuentes en el agente de copia de seguridad para corregir varios problemas, agregar características y mejorar el rendimiento.

También recomendamos encarecidamente que revise el artículo [P+F de servicio de Azure Backup](backup-azure-backup-faq.md) para asegurarse de que no experimenta alguno de los problemas habituales de la configuración.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: cuellos de botella que afectan al rendimiento del equipo.
Los cuellos de botella del equipo en el que se realiza la copia de seguridad pueden provocar retrasos. Por ejemplo, la capacidad del equipo para leer o escribir en el disco, o el ancho de banda disponible para enviar datos a través de la red pueden provocar cuellos de botella.

Windows proporciona una herramienta integrada que se denomina [Monitor de rendimiento de](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) para detectar cuellos de botella.

Estos son algunos contadores de rendimiento e intervalos que pueden resultar útiles para diagnosticar cuellos de botella, con el fin de que las copias de seguridad sean óptimas.

| Contador | Estado |
| --- | --- |
| Logical Disk(Physical Disk) [Disco lógico (disco físico)]--% de inactividad |• Entre 100 % y 50 % de inactividad = Correcto</br>• Entre 49 % y 20 % de inactividad = Advertencia o supervisión</br>• Del 19 % al 0 % de inactividad = Situación crítica o fuera de la especificación |
| Logical Disk(Physical Disk) [Disco lógico (disco físico)]--% promedio Disk Sec Read or Write (Segundos de disco de lectura o escritura) |• De 0,001 ms a 0,015 ms = Correcto</br>• De 0,015 ms a 0,025 ms = Advertencia o supervisión</br>• 0.026 o más = Situación crítica o fuera de la especificación |
| Logical Disk(Physical Disk) [Disco lógico (disco físico)] -- Longitud actual de la cola de disco (para todas las instancias) |80 solicitudes durante más de 6 minutos |
| Memoria: Pool Non Paged Bytes (Bytes de bloque no paginado) |• Menos del 60 % del grupo consumido = Correcto<br>• Del 61 % al 80 % del grupo consumido = Advertencia o supervisión</br>• Más del 80 % del grupo consumido = Situación crítica o fuera de la especificación |
| Memoria: Bytes de bloque paginado |• Menos del 60 % del grupo consumido = Correcto</br>• Del 61 % al 80 % del grupo consumido = Advertencia o supervisión</br>• Más del 80 % del grupo consumido = Situación crítica o fuera de la especificación |
| Memoria: Megabytes disponibles |• El 50 % de la memoria libre, o más, está disponible = Correcto</br>• El 25 % de la memoria libre está disponible = Supervisión</br>• El 10 % de la memoria libre está disponible = Advertencia</br>• Menos de 100 MB o del 5 % de memoria libre está disponible = Situación crítica o fuera de la especificación. |
| Procesador: \%Tiempo de procesador (todas las instancias) |• Menos del 60 % consumido = Correcto</br>• Del 61 % al 90 % consumido = Supervisión o precaución</br>• Del 91 % al 100 % consumido = Situación crítica |

> [!NOTE]
> Si determina que la causa es la infraestructura, se recomienda desfragmentar los discos periódicamente para mejorar su rendimiento.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: otro proceso o software antivirus interfiere con Azure Backup.
Se han detectado varios ejemplos en los que otros procesos del sistema de Windows han afectado negativamente al rendimiento del proceso del agente de Azure Backup. Por ejemplo, si utiliza el agente de Azure Backup y otro programa para realizar copias de seguridad de los datos o si se ejecuta un software antivirus que tiene bloqueados los archivos de los que se va a realizar la copia de seguridad, estos bloqueos de varios archivos podrían causar una contención. En esta situación, se podría producir un error en la copia de seguridad o el trabajo podría tardar más de lo esperado.

La mejor recomendación en este escenario consiste en desactivar el otro programa de copia de seguridad para ver si cambia el tiempo de copia de seguridad del agente de Azure Backup. Normalmente, para evitar que unos trabajos de copia de seguridad afecten a otros es suficiente asegurarse de que no se ejecuta varios trabajos al mismo tiempo.

En el caso de los programas antivirus, se recomienda que excluya los siguientes archivos y ubicaciones:

* C:\Archivos de programa\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como proceso.
* Carpetas de C:\Archivos de programa\Microsoft Azure Recovery Services Agent\.
* La ubicación de la carpeta temporal (si no utiliza la ubicación estándar).

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: el agente de Copia de seguridad se ejecuta en una máquina virtual de Azure.
Si el agente de Copia de seguridad se ejecuta en una máquina virtual, el rendimiento será peor que si lo hace en una máquina física. Esto se debe a limitaciones de IOPS.  Sin embargo, el rendimiento se puede optimizar mediante el cambio de las unidades de datos de las que se realiza la copia de seguridad a Azure Premium Storage. Estamos trabajando para solucionar este problema y dicha solución estará disponible en futuras versiones.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: se realiza una copia de seguridad de un número elevado (varios millones) de archivos.
El movimiento de un gran volumen de datos tarda más tiempo en realizarse el de un volumen más pequeño. En algunos casos, el tiempo que tarda en realizarse una copia de seguridad está relacionado no solo con el tamaño de los datos, sino también con el número de archivos o carpetas. Esto sucede especialmente cuando se realizan copias de seguridad de millones de archivos pequeños (que tienen entre unos pocos bytes y unos pocos kilobytes).

Este comportamiento se produce porque mientras se realiza la copia de seguridad de los datos y su movimiento a Azure, Azure está catalogando los archivos. En algunos casos poco frecuentes, la operación de catálogo puede tardar más tiempo del esperado.

Los siguientes indicadores pueden ayudarle a entender el cuello de botella y trabajar según corresponda en los pasos siguientes:

* **La interfaz de usuario muestra el progreso de la transferencia de datos**. La transferencia de datos no ha finalizado. El ancho de banda de la red o el tamaño de datos pueden estar causando retrasos.
* **La interfaz de usuario no muestra el progreso de la transferencia de datos**. Abra los registros ubicados en "C:\Microsoft Azure Recovery Services Agent\Temp" y busque en ellos la entrada FileProvider::EndData. Esta entrada indica que la transferencia de datos ha finalizado y que se está realizando la operación de catálogo. No cancele los trabajos de copia de seguridad. Es preferible que espere hasta que finalice la operación de catálogo. Si el problema persiste, póngase en contacto con el [servicio de soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).
