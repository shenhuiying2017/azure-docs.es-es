<properties
   pageTitle="Solución de problemas de lentitud de copia de seguridad de archivos y carpetas en Copia de seguridad de Azure | Microsoft Azure"
   description="Orientación de solución de problemas que le ayudará a diagnosticar y aislar la causa de un problema de rendimiento lento en Copia de seguridad de Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="markgal"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/20/2016"
    ms.author="genli"/>

# Solución de problemas de lentitud de copia de seguridad de archivos y carpetas en Copia de seguridad de Azure

Este artículo proporciona orientación de solución de problemas que le ayudará a diagnosticar y aislar la causa de un rendimiento lento en la copia de seguridad de archivos y carpetas mediante Copia de seguridad de Azure. Cuando utiliza el agente de Copia de seguridad de Azure para hacer copia de seguridad de los archivos, el proceso tarda más de lo esperado. Este problema puede deberse a uno o a varios de los siguientes motivos:

-	[Cuellos de botella que afectan al rendimiento del equipo en el que se realiza la copia de seguridad](#cause1).
-	[Otro proceso o software antivirus interfiere con Copia de seguridad de Azure](#cause2).
-	[El agente de copia de seguridad se ejecuta en una máquina virtual (VM) de Azure](#cause3).
-	[Se está intentando realizar la copia de seguridad de un gran número de archivos (varios millones)](#cause4).

La siguiente sección le ayudará a determinar la causa específica del problema.

Antes de empezar a solucionar el problema, le recomendamos que descargue e instale el [agente de Copia de seguridad de Azure más reciente](http://aka.ms/azurebackup_agent). Se realizan actualizaciones frecuentes en el agente de copia de seguridad para corregir varios problemas, agregar características y mejorar el rendimiento.

También recomendamos encarecidamente que revise el artículo [P+F de servicio de Copia de seguridad de Azure](backup-azure-backup-faq.md) para asegurarse de que no experimenta alguno de los problemas habituales de configuración.

## Pasos para solucionar problemas
<a id="cause1"></a>
## Causa 1: Copia de seguridad lenta debido a cuellos de botella que afectan al rendimiento del equipo en el que se realiza la copia de seguridad

### Solución

Puede haber algunos cuellos de botella en el equipo en el que se realiza la copia de seguridad que pueden causar retrasos. Por ejemplo, pueden afectar a la capacidad del equipo para leer o escribir en el disco, a los anchos de banda para enviar datos a través de la red, etc.

Windows proporciona una herramienta integrada que se denomina [Monitor de rendimiento](https://technet.microsoft.com/magazine/2008.08.pulse.aspx)(Perfmon) para detectar estos cuellos de botella. La siguiente tabla resume los contadores de rendimiento y los intervalos para que las copias de seguridad sean óptimas.

Estos son algunos contadores de rendimiento e intervalos que pueden resultar útiles para diagnosticar cuellos de botella.

| Contador | Status |
|---|---|
|Logical Disk(Physical Disk) [Disco lógico (disco físico)]--% de inactividad | • Entre 100 % y 50 % de inactividad = Correcto</br> • Del 49 % al 20 % de inactividad = Advertencia o supervisión</br>• 19 % al 0 % de inactividad = Situación crítica o fuera de la especificación|
| Logical Disk(Physical Disk) [Disco lógico (disco físico)]--% promedio Disk Sec Read or Write (Segundos de disco de lectura o escritura) | • 0,001 ms a 0,015 ms = Correcto</br>• 0,015 ms a 0,025 = Advertencia o supervisión</br>• 0,026 ms o superior = Situación crítica o fuera de la especificación|
| Logical Disk(Physical Disk) [Disco lógico (disco físico)] -- Longitud actual de la cola de disco (para todas las instancias) | 80 solicitudes durante más de 6 minutos |
| Memoria: Pool Non Paged Bytes (Bytes de bloque no paginado)|• Inferior al 60 % del bloque consumido = Correcto<br>• 61 % al 80 % del bloque consumido = Advertencia o supervisión</br>• Superior al 80 % del bloque consumido = Situación crítica o fuera de la especificación|
| Memoria: Bytes de bloque paginado |• Inferior al 60 % del bloque consumido = Correcto</br>• 61 % al 80 % del bloque consumido = Advertencia o supervisión</br>• Superior al 80 % del bloque consumido = Situación crítica o fuera de la especificación|
| Memoria: Megabytes disponibles| • 50 % de memoria libre disponible o más = correcto</br>• 25 % de memoria libre disponible = Supervisión.</br>• 10 % de memoria libre disponible = Advertencia.</br>• Menos de 100 MB o el 5% de memoria libre disponible = Situación crítica o fuera de la especificación.|
|Procesador: \\% de tiempo de procesador (todas las instancias)|• Inferior al 60 % consumido = Correcto</br>• Del 61 al 90 % consumido = Supervisión o precaución</br>• Del 91 % al 100 % consumido = Situación crítica|


> [AZURE.NOTE] Si ya se ha aislado la infraestructura causante, se aconseja desfragmentar los discos que desea proteger de forma regular para mejorar el rendimiento.

<a id="cause2"></a>
## Causa 2: Otro proceso o software antivirus interfiere con el proceso de Copia de seguridad de Azure

Se han detectado varios ejemplos en los que otros procesos dentro del sistema de Windows han afectado negativamente al rendimiento del proceso del agente de Copia de seguridad de Azure. Por ejemplo, si utiliza el agente de Copia de seguridad de Azure y se está ejecutando otro programa de copia de seguridad de datos o software antivirus que tiene bloqueados los archivos de los que se va a realizar la copia de seguridad, estos bloqueos de varios archivos pueden causar un bajo rendimiento. En esta situación, se puede producir un error en la copia de seguridad o el trabajo puede tardar más de lo esperado.

### Solución

La mejor recomendación en este escenario consiste en desactivar el otro programa de copia de seguridad para ver si cambia el tiempo de copia de seguridad del agente de Copia de seguridad de Azure. Normalmente, es suficiente con asegurarse de que varios trabajos de copia de seguridad no se están ejecutando al mismo tiempo para impedir que se estorben entre sí.

En el caso de los programas antivirus, se recomienda que excluya los siguientes archivos y ubicaciones:

- Excluya C:\\Archivos de programa\\Agente de Servicios de recuperación de Microsoft Azure\\bin\\cbengine.exe como proceso.
- Excluya las carpetas de C:\\Archivos de programa\\Agente de Servicios de recuperación de Microsoft Azure\\.
- Excluya la ubicación del borrador (si no utiliza la ubicación estándar mencionada anteriormente).

<a id="cause3"></a>
## Causa 3: El agente de copia de seguridad se ejecuta en una máquina virtual (VM) de Azure

### Solución

Si está ejecutando el agente de Copia de seguridad en una máquina virtual, el rendimiento será más lento que cuando se ejecuta en una máquina física. Esto se debe a limitaciones de IOPS. Sin embargo, puede optimizar el rendimiento mediante el cambio de las unidades de datos de las que está realizando la copia de seguridad al almacenamiento premium. Estamos trabajando para solucionar este problema y la solución estará disponible en futuras versiones.

<a id="cause4"></a>
## Causa 4: Copia de seguridad de un número grande (varios millones) de archivos

### Solución

Es comprensible que mover un volumen grande de datos tarde más tiempo que mover uno más pequeño. Pero en algunos casos el tiempo de copia de seguridad no está relacionado solo con el tamaño de los datos, sino también con el número de archivos o carpetas, especialmente en un escenario en el que haya que hacer copia de seguridad de millones de archivos pequeños (desde pocos bytes a pocos kilobytes).

Este comportamiento se produce porque mientras se realiza la copia de seguridad de los datos y esta se mueve a Azure, al mismo tiempo estamos catalogando los archivos y, en ocasiones poco frecuentes, la operación de catálogo podría llevar más tiempo.

Siga los pasos que se describen a continuación para entender el cuello de botella y trabajar según corresponda en los pasos siguientes:

a. **La interfaz de usuario muestra el progreso de la cantidad de datos transferidos**: en este caso, los datos se están transfiriendo todavía y el ancho de banda de red o el tamaño de los datos podría provocar retrasos.

b. **La interfaz de usuario no muestra progreso**: en ese caso, abra los registros ubicados en "C:\\Agente de Servicios de recuperación de Microsoft Azure\\Temp" y, a continuación, busque la entrada "FileProvider::EndData" en los registros. Esta entrada indica que se completó la transferencia de datos y que se está realizando la operación de catálogo. No cancele los trabajos de copia de seguridad y, en su lugar, espere algo más de tiempo para que el catálogo finalice. Si el problema persiste, póngase en contacto con el [servicio de soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).

<!---HONumber=AcomDC_0720_2016-->