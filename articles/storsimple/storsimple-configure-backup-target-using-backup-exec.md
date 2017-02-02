---
title: "Configuración de StorSimple con Veritas Backup Exec | Microsoft Docs"
description: "Describe la configuración del destino de copia de seguridad de StorSimple con Veritas Backup Exec."
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>Configuración de StorSimple con Veritas Backup Exec&#8482;

## <a name="overview"></a>Información general

Microsoft Azure StorSimple es una solución híbrida de almacenamiento en la nube que aborda las complejidades del crecimiento exponencial de datos. Esta solución usa una cuenta de Azure Storage como extensión de la solución local y organiza automáticamente los datos en niveles entre el almacenamiento local y el almacenamiento en la nube.

En este artículo se describen la integración de StorSimple con Veritas Backup Exec y los procedimientos recomendadas para la integración de ambas soluciones. También se ofrecen recomendaciones sobre cómo configurar Veritas Backup Exec para que se integre perfectamente con StorSimple. Nos referimos a los procedimientos recomendados de Veritas, a los arquitectos de copia de seguridad y a los administradores sobre cómo configurar mejor Veritas Backup Exec para cumplir los requisitos de copia de seguridad y los SLA.

En este artículo se muestran tanto los pasos de configuración como los conceptos clave, pero no es una guía detallada de configuración o instalación. En el artículo se supone que la infraestructura y los componentes básicos funcionan perfectamente y están listos para admitir los conceptos que se describen.

## <a name="why-storsimple-as-a-backup-target"></a>¿Por qué StorSimple como destino de copia de seguridad?

StorSimple es un excelente destino de copia de seguridad por las razones siguientes:

-   Proporciona almacenamiento local estándar que las aplicaciones de copia de seguridad pueden usar sin realizar ningún cambio, con el fin de proporcionar un destino de copias de seguridad rápidas. StorSimple también está disponible para la restauración rápida de copias de seguridad recientes.

-   Sus niveles en la nube se integran perfectamente con el económico almacenamiento en la nube de Microsoft Azure.

-   Proporciona automáticamente almacenamiento externo para la recuperación ante desastres.


## <a name="target-audience"></a>Audiencia de destino

La audiencia este documento incluye administradores de copia de seguridad, administradores de almacenamiento y arquitectos de almacenamiento con conocimientos de almacenamiento, Windows Server 2012 R2, Ethernet, servicios en la nube y Veritas Backup Exec.

## <a name="supported-versions"></a>Versiones compatibles

Para obtener una lista completa de las versiones compatibles, vaya a:

-   [Backup Exec, versiones 16 y superiores](http://backupexec.com/compatibility).

-   [StorSimple Update 3 y versiones posteriores](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Conceptos clave

Al igual que con cualquier otra solución de almacenamiento, para que sea un éxito son fundamentales una evaluación minuciosa del rendimiento del almacenamiento de la solución, los SLA, la tasa de cambio y las necesidades de aumento de capacidad. La idea principal es que mediante la introducción de un nivel de nube, los tiempos de acceso y el rendimiento en la nube juegan un papel fundamental en la capacidad de StorSimple para hacer su trabajo.

StorSimple está diseñado para proporcionar almacenamiento a las aplicaciones que funcionan en un conjunto de datos activo bien definido (datos activos). En este modelo, el conjunto de datos activo se almacena en las capas locales y el conjunto de datos no activos o archivados restantes está en capas en la nube. Este modelo se representa en la siguiente ilustración. La línea verde casi plana representa los datos almacenados en los niveles locales del dispositivo de StorSimple. La línea roja representa la cantidad total de datos almacenados en la solución de StorSimple a través de todos los niveles. El espacio entre la línea verde plana y la curva exponencial roja representa la cantidad total de datos almacenados en la nube.

**Niveles de StorSimple**
![Diagrama de niveles de Storsimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Con esta arquitectura en mente, StorSimple es ideal para operar como destino de copia de seguridad. StorSimple permite:

-   Realizar las restauraciones más frecuentes desde el conjunto de datos activo local.

-   Usar la nube para la recuperación ante desastres fuera del sitio y para datos antiguos, donde las restauraciones son menos frecuentes.

## <a name="storsimple-benefits"></a>Ventajas de StorSimple

StorSimple ofrece una solución local que se integra perfectamente con Microsoft Azure, para lo que saca provecho del acceso ininterrumpido al almacenamiento local y en la nube.

StorSimple usa niveles automáticos entre el dispositivo local, que contiene almacenamiento SCSI conectado en serie (SAS) y en dispositivo en estado sólido (SSD), y Azure Storage. Los niveles automáticos mantienen en un entorno local los datos de acceso frecuente, en los niveles de SSD y SAS, y mueven a Azure Storage los datos a los que se accede con poca frecuencia.

StorSimple ofrece las siguientes ventajas:

-   Algoritmos de desduplicación y compresión únicos que usan la nube para lograr unos niveles de desduplicación sin precedentes

-   Alta disponibilidad

-   Replicación geográfica mediante el uso de la replicación geográfica de Azure

-   Integración de Azure

-   Cifrado de datos en la nube

-   Mejor recuperación ante desastres y cumplimiento normativo

Aunque StorSimple presenta dos escenarios de implementación principales (destino de copia de seguridad principal y secundario), tenga en cuenta que es fundamentalmente un dispositivo de almacenamiento de bloques sin formato. StorSimple realiza toda la compresión y desduplicación, y envía y recupera datos de la nube sin problemas tanto en la aplicación como en el sistema de archivos.

Para más información sobre StorSimple, consulte [Serie StorSimple 8000: una solución de almacenamiento en la nube híbrida](storsimple-overview.md) y [Especificaciones técnicas y cumplimiento normativo para el dispositivo StorSimple](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> El uso de un dispositivo de StorSimple como destino de copia de seguridad solo se admite en StorSimple 8000 Update 3, o en cualquier versión posterior.

## <a name="architecture-overview"></a>Introducción a la arquitectura

En las tablas siguientes se muestran la guía inicial de la relación entre el modelo del dispositivo y la arquitectura.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacidades de StorSimple para el almacenamiento local y en la nube


| Capacidad de almacenamiento       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacidad de almacenamiento local | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacidad de almacenamiento en la nube | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* En el tamaño de almacenamiento, se asume que no hay desduplicación ni compresión.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacidades de StorSimple para copias de seguridad principales y secundarias


| Escenario de copia de seguridad  | Capacidad de almacenamiento local                                         | Capacidad de almacenamiento en la nube                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Copia de seguridad principal   | Copias de seguridad recientes almacenadas localmente para una recuperación rápida (RPO) | El historial de copias de seguridad (RPO) se ajusta a la capacidad de la nube |
| Copia de seguridad secundaria | La copia secundaria de los datos de las copias de seguridad se puede almacenar en la capacidad de la nube  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de copia de seguridad principal

En este escenario, los volúmenes de StorSimple se presentan a la aplicación de copia de seguridad como el único repositorio para copias de seguridad. La siguiente ilustración muestra la arquitectura de una solución en la que todas las copias de seguridad usan los volúmenes en capas de StorSimple tanto para las copias de seguridad como para las restauraciones.

![Diagrama lógico de StorSimple como destino de copia de seguridad principal](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Pasos lógicos de copias de seguridad en el destino principal

1.  El servidor de copia de seguridad se pone en contacto con el agente de copia de seguridad de destino y este transmite los datos al primero.

2.  El servidor de copia de seguridad escribe los datos en los volúmenes en capas de StorSimple.

3.  El servidor de copia de seguridad actualiza la base de datos del catálogo y completa el trabajo de copia de seguridad.

4.  El script de la instantánea desencadena la administración de instantáneas en la nube de StorSimple (start-delete).

5.  En función de lo que establezca una directiva de retención, el servidor de copia de seguridad elimina las copias de seguridad que han expirado.

###  <a name="primary-target-restore-logical-steps"></a>Pasos lógicos de restauración del destino principal

1.  El servidor de copia de seguridad empieza a restaurar los datos apropiados del repositorio de almacenamiento.

2.  El agente de copia de seguridad recibe los datos del servidor de copia de seguridad.

3.  El servidor de copia de seguridad completa el trabajo de restauración.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de copia de seguridad secundario

En este escenario, los volúmenes de StorSimple se utilizan principalmente para la retención o el archivado a largo plazo.

En la siguiente ilustración se muestra la arquitectura en la que las copias de seguridad y restauraciones iniciales tienen como destino un volumen de alto rendimiento. Estas copias de seguridad se realizan y se archivan en un volumen en capas de StorSimple con una programación determinada.

Es importante dotar al volumen de alto rendimiento con suficiente espacio y ajustar su rendimiento para controlar la directiva de retención, la capacidad y los requisitos de rendimiento.

![Diagrama lógico de StorSimple como destino de copia de seguridad secundario](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Pasos lógicos de copias de seguridad en el destino secundario

1.  El servidor de copia de seguridad se pone en contacto con el agente de copia de seguridad de destino y este transmite los datos al primero.

2.  El servidor de copia de seguridad escribe datos en el almacenamiento de alto rendimiento.

3.  El servidor de copia de seguridad actualiza la base de datos del catálogo y completa el trabajo de copia de seguridad.

4.  En función de lo que establezca una directiva de retención, el servidor de copia de seguridad copia las copias de seguridad en StorSimple.

5.  El script de la instantánea desecandena la administración de instantáneas en la nube de StorSimple (start-delete).

6.  En función de lo que establezca una directiva de retención, el servidor de copia de seguridad elimina las copias de seguridad que han expirado.

### <a name="secondary-target-restore-logical-steps"></a>Pasos lógicos de restauración del destino secundario

1.  El servidor de copia de seguridad empieza a restaurar los datos apropiados del repositorio de almacenamiento.

2.  El agente de copia de seguridad recibe los datos del servidor de copia de seguridad.

3.  El servidor de copia de seguridad completa el trabajo de restauración.

## <a name="deploy-the-solution"></a>Implementación de la solución

La implementación de esta solución consta de tres pasos: preparar la infraestructura de red, implementar el dispositivo de StorSimple como destino de copia de seguridad y, finalmente, implementar Veritas Backup Exec. En las siguientes secciones se detallan cada uno de estos pasos.

### <a name="configure-the-network"></a>Configuración de la red

StorSimple como solución integrada con la nube de Azure requiere una conexión activa con la nube de Azure. Esta conexión se utiliza para operaciones tales como instantáneas en la nube, administración, transferencia de metadatos y al nivel anterior, datos con menos acceso al almacenamiento en la nube de Azure.

Para que la solución funcione de manera óptima, se recomienda adherirse a los siguientes procedimientos de red recomendados:

-   El vínculo que conecta los niveles de StorSimple con Azure debe cumplir los requisitos de ancho de banda, para lo que se aplica la calidad de servicio (QoS) adecuada a los conmutadores de infraestructura para que se ajusten a los SLA de RPO/RTO.

-   Las latencias de acceso máximas de Azure Blob Storage deben estar en el intervalo de 80 ms.

### <a name="deploy-storsimple"></a>Implementación de StorSimple

Para ver instrucciones detalladas para la implementación de StorSimple, vaya a [Implementación de un dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veritas-backup-exec"></a>Implementación de Veritas Backup Exec

Para conocer los procedimientos recomendados para la instalación de Veritas Backup Exec, vaya a [Best practices for Backup Exec installation](https://www.veritas.com/support/en_US/article.000068207) (Procedimientos recomendados para la instalación de Backup Exec).

## <a name="configure-the-solution"></a>Configuración de la solución

En esta sección se muestran algunos ejemplos de configuración. Las siguientes recomendaciones o ejemplos ilustran la implementación más básica y fundamental. Es posible que esta implementación no se aplique directamente a sus requisitos de copia de seguridad específicos.

### <a name="configure-storsimple"></a>Configuración de StorSimple

| Tareas de implementación de StorSimple                                                                                                                 | Comentarios adicionales                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Implementar un dispositivo de StorSimple local.                                                                                                | Versión compatible: Update 3 y versiones superiores.                                                                                                                                                                                                                                                                 |
| Habilite el modo de destino de copia de seguridad.                                                                                                                   | Utilice los siguientes comandos tanto para habilitar o deshabilitar un estado como para obtenerlo. Para más información, vaya a [Conectarse de forma remota al dispositivo StorSimple](storsimple-remote-connect.md).</br> Habilitar modo de copia de seguridad:`Set-HCSBackupApplianceMode -enable`</br>  Deshabilitar modo de copia de seguridad:`Set-HCSBackupApplianceMode -disable`</br> Estado actual de la configuración del modo de copia de seguridad`Get-HCSBackupApplianceMode` |
| Cree un contenedor de volúmenes común para el volumen que almacena los datos de copia de seguridad.   Todos los datos de un contenedor de volumen se desduplican. | Los contenedores de volúmenes de StorSimple definen dominios de desduplicación.                                                                                                                                                                                                                                             |
| Creación de volúmenes de StorSimple                                                                                                                 | Cree volúmenes cuyos tamaños se ajusten lo máximo posible al uso previsto, ya que el tamaño del volumen afecta a la duración de la instantánea de la nube. Para más información sobre cómo cambiar el tamaño de un volumen, vaya a [Directivas de retención](#retention-policies).</br> </br> Use los volúmenes en capas de StorSimple y active la casilla **Use the following commands to enable/disable and get status** (Usar este volumen para los datos de archivo a los que accede con menos frecuencia). </br> No se admite que solo se usen volúmenes anclados localmente.|
| Cree una directiva de copia de seguridad de StorSimple única para todos los volúmenes de destino de copia de seguridad.                                                               | Una directiva de copia de seguridad de StorSimple define el grupo de coherencia del volumen.                                                                                                                                                                                                                                       |
| Deshabilite la programación a medida que las instantáneas expiran.                                                                                                    | Las instantáneas se desencadenan como operación posterior al procesamiento.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>Configuración del almacenamiento del servidor de copia de seguridad de host

Asegúrese de que para configurar el almacenamiento del servidor de copia de seguridad de host se han seguido las instrucciones siguientes.  

- No se admiten volúmenes distribuidos (creados por el administrador de discos de Windows).
- Dé formato a los volúmenes mediante NTFS con un tamaño de asignación de 64 KB.
- Asigne los volúmenes de StorSimple directamente en el servidor de "Veeam".
    - Utilice iSCSI en el caso de los servidores físicos.
    - Use discos de acceso directo para servidores virtuales.


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>Procedimientos recomendados para StorSimple y Veritas Backup Exec

Configure su solución según las instrucciones siguientes.


### <a name="operating-system"></a>Sistema operativos

-   Deshabilite el cifrado y la desduplicación de Windows Server para el sistema de archivos NTFS.

-   Deshabilite la desfragmentación de Windows Server en los volúmenes de StorSimple.

-   Deshabilite la indexación de Windows Server en los volúmenes de StorSimple.

-   Realice una detección de virus en el host de origen (no en los volúmenes de StorSimple).

-   Deshabilite el [mantenimiento de Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) predeterminado en el Administrador de tareas.

    - Deshabilite el configurador de mantenimiento en el Programador de tareas de Windows.

        O

    - Descargue: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx).

      - Después de descargar PSEXEC, ejecute Windows PowerShell como administrador y escriba:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Asegúrese de que el dispositivo de StorSimple se ha actualizado a [Update 3, o cualquier versión posterior](storsimple-install-update-3.md).

-   Aísle tráfico de iSCSI y de la nube. Use conexiones iSCSI dedicadas para el tráfico entre StorSimple y el servidor de copia de seguridad.

-   Asegúrese de que el dispositivo de StorSimple sea un destino de copia de seguridad dedicado. No se admiten cargas de trabajo mixtas, ya que afectan a RTO/RPO.

### <a name="veritas-backup-exec"></a>Veritas Backup Exec

-   Veritas Backup Exec debe instalarse en una unidad local del servidor, no en un volumen de StorSimple.

-   Establezca el valor del número de**operaciones de escritura simultáneas** en el almacenamiento de Veritas Backup Exec al máximo permitido.

    -   El **tamaño de bloque y del búfer** del almacenamiento de Veritas Backup Exec debe establecerse en 512 KB.

    -   Deben habilitarse la **lectura y escritura del búfer** del almacenamiento de Veritas Backup Exec.

-   Se admiten las copias de seguridad completas e incrementales de Veritas Backup Exec, mientras que las sintéticas y diferenciales no se recomiendan.

-   Los archivos de datos de copia de seguridad solo deben contener los datos de un trabajo concreto. Por ejemplo, no se permiten anexos de medios entre distintos trabajos.

-   Deshabilite la comprobación de trabajos. Si es necesaria, se debe programar que se realice una comprobación después del último trabajo de copia de seguridad. Es importante saber que este trabajo afecta a la ventana de copia de seguridad.

-   En **Storage > Your disk > Details > Properties** (Almacenamiento > Su disco > Detalles > Propiedades), deshabilite **Pre-allocate disk space** (Preasignar espacio en disco).

Para conocer la configuración más reciente de Veritas Backup Exec y los procedimientos recomendados para implementar los requisitos, consulte la documentación de Veritas Backup Exec, que se puede encontrar en [www.veritas.com](https://www.veritas.com).

## <a name="retention-policies"></a>Directivas de retención

Una de las directivas de retención de copias de seguridad más usadas es Grandfather, Father, and Son (GFS). En esta directiva, se realiza una copia de seguridad incremental diaria. Las copias de seguridad completas se realizan semanal y mensualmente. Esta directiva genera seis volúmenes en capas de StorSimple.

-   Un volumen contiene las copias de seguridad completas semanales, mensuales y anuales.

-   Los cinco volúmenes restantes almacenan las copias de seguridad incrementales diarias.

En el siguiente ejemplo se usa una rotación de GFS. En dicho ejemplo se dan por supuestos los siguientes hechos:

-   Se usan datos no desduplicados o comprimidos.

-   Cada copia de seguridad completa ocupa 1 TiB.

-   Las copias de seguridad incrementales diarias ocupan 500 GiB.

-   Se conservan cuatro copias de seguridad semanales durante un mes.

-   Se conservan doce copias de seguridad mensuales durante un año.

-   Se conserva una copia de seguridad anual durante diez años.

De acuerdo con los supuestos anteriores, cree un volumen en capas de StorSimple de 26 TiB para las copias de seguridad completas mensuales y anuales. Cree un volumen en capas de StorSimple de 5 TiB para cada una de las copias de seguridad incrementales diarias.

| Retención de tipo de copia de seguridad | Tamaño (TiB) | Multiplicador de GFS\*                                       | Capacidad total (TiB)          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completa semanal           | 1        | 4                                                      | 4                           |
| Incremental diaria     | 0,5      | 20 (los ciclos equivalen al número de semanas al mes) | 12 (2 para cuota adicional) |
| Completa mensual          | 1        | 12                                                     | 12                          |
| Completa anual           | 1        | 10                                                     | 10                          |
| Requisito de GFS       |          |                                                        | 38                          |
| Cuota adicional      | 4        |                                                        | Requisito de GFS, un total de 42   |

\*El multiplicador de GFS es el número de copias que es preciso proteger y mantener para cumplir las directivas de copia de seguridad.

## <a name="configure-veritas-backup-exec-storage"></a>Configuración del almacenamiento de Veritas Backup Exec

1.  En la consola de administración de Veritas Backup Exec, seleccione **Storage (Almacenamiento) &gt; Configure Storage (Configurar almacenamiento) &gt; Disk-Based Storage (Almacenamiento en disco)**. Haga clic en **Next&gt; (Siguiente)**.

    ![Consola de administración de Veritas Backup Exec, pantalla de configuración de almacenamiento](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  Haga clic en **Disk Storage** (Almacenamiento en disco) y, después, vaya a la página siguiente.

    ![Consola de administración de Veritas Backup Exec, pantalla de selección de almacenamiento](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  Escriba un nombre representativo, como por ejemplo, Saturday Full (Completa sábado) y una descripción. Pase a la página siguiente.

    ![Consola de administración de Veritas Backup Exec, pantalla de nombre y descripción](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  Seleccione el disco pertinente y haga clic en **Next&gt; (Siguiente)**. Por ejemplo, hemos seleccionado D:.

    ![Consola de administración de Veritas Backup Exec, pantalla de selección de disco de almacenamiento](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  Aumente el número de operaciones de escritura a 16 y haga clic en **Next&gt; (Siguiente)**.

    ![Consola de administración de Veritas Backup Exec, pantalla de configuración de operaciones de escritura simultáneas ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  Revise la configuración y haga clic en **Finish** (Finalizar).

    ![Consola de administración de Veritas Backup Exec, pantalla resumen de configuración de almacenamiento](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  Al final de cada asignación, cambie la configuración del dispositivo de almacenamiento para que coincida con la configuración recomendada en la lista de procedimientos recomendados.

    ![Consola de administración de Veritas Backup Exec, pantalla de configuración de dispositivo de almacenamiento](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  Repita todos los pasos anteriores hasta que haya terminado de asignar los volúmenes de StorSimple a Veritas Backup Exec.

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple como destino de copia de seguridad principal

> [!NOTE]
> Tenga en cuenta que si tiene que restaurar los datos de una copia de seguridad que ha sido organizada en niveles en la nube, la restauración se realizará a velocidades de la nube.

En la ilustración siguiente, se muestra la asignación de un volumen normal a un trabajo de copia de seguridad. En este caso, todas las copias de seguridad semanales se asignan al disco Saturday Full (Completa sábado) y las copias de seguridad incrementales se asignan a los discos Monday-Friday Incremental (Incremental lunes a viernes). Todas las copias de seguridad y restauraciones se producen desde un volumen en capas de StorSimple.

![Diagrama lógico de la configuración de destino de copia de seguridad principal ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Ejemplo de programación de Grandfather, Father, and Son (GFS) de destino de copia de seguridad principal de StorSimple

| Programación de rotación de GFS durante 4 semanas, mensual y anual |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Frecuencia o tipo de copia de seguridad   | Completo          | Incremental (día 1 - 5)  |
| Semanal (semana 1 - 4)    | Sábado | Lunes a viernes | 
| Mensual     | Sábado  |             |
| Anual      | Sábado  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>Asignación de volúmenes de StorSimple a un trabajo de copia de seguridad de Veritas Backup Exec.

En la secuencia siguiente se asume que tanto Backup Exec como el host de destino están configurados según la guía del de agente de Backup Exec.

1.  En la consola de administración de Veritas Backup Exec, vaya a **Host &gt; Backup (Copia de seguridad) &gt; Backup to Disk (Copia de seguridad en disco) &gt;**.

    ![Consola de administración de Veritas Backup Exec, selección de host, backup (copia de seguridad), Backup to disk (Copia de seguridad en disco)](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  En la ventana Backup Definition Properties (Propiedades de definición de copia de seguridad), seleccione **Edit** (Editar) [en Backup (Copia de seguridad)].

    ![Ventana Backup Definition Properties (Propiedades de definición de copia de seguridad), selección de Edit (Editar)](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  Configure las copias de seguridad completas e incrementales para que cumplan los requisitos de RPO/RTO y se ajusten a los procedimientos recomendados de Veritas.

2.  En la ventana Backup Options (Opciones de copia de seguridad), seleccione **Storage** (Almacenamiento).

    ![Pestaña Storage (Almacenamiento) de Backup Options (Opciones de copia de seguridad)](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  Asigne los volúmenes de StorSimple correspondientes a una programación de copia de seguridad.

    > [!NOTE]
    > En **Compression** (Compresión) y **Encryption type** (Tipo de cifrado) se selecciona **None** (No).

2.  En **Verify** (Comprobar), active **Do not verify data for this job** (No comprobar los datos de este trabajo), ya que esto podría afectar a la organización en niveles de StorSimple.

    > [!NOTE]
    > La desfragmentación, la indexación y la comprobación en segundo plano afectan negativamente a la organización en niveles de StorSimple.

    ![Configuración de comprobaciones en Backup Options (Opciones de copia de seguridad)](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  Una vez que haya configurado el resto de las opciones de copia de seguridad para que se ajusten a sus requisitos, seleccione **Ok** (Aceptar) para finalizar.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple como destino de copia de seguridad secundario

> [!NOTE]
> Tenga en cuenta que si tiene que restaurar los datos de una copia de seguridad que ha sido organizada en niveles en la nube, la restauración se realizará a velocidades de la nube.

En este modelo, es preciso un soporte físico de almacenamiento (que no sea StorSimple) que actúe como una memoria caché temporal. Por ejemplo, se puede utilizar un volumen RAID para dar cabida al espacio, la E/S y el ancho de banda. Se recomienda usar RAID 5, 50 y 10.

En la ilustración siguiente aparecen tanto volúmenes locales (en el servidor) para la retención a corto plazo como volúmenes de archivo a largo plazo. En este caso, todas las copias de seguridad se ejecutan en el volumen RAID local (en el servidor). Estas copias de seguridad se duplican periódicamente y se archivan en un volumen de archivo. Es importante ajustar el tamaño del volumen RAID local (en el servidor) para controlar los requisitos de rendimiento y capacidad de retención a corto plazo.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Ejemplo de GFS de StorSimple como destino de copia de seguridad secundario

![Diagrama lógico de StorSimple como destino de copia de seguridad secundario ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

En la tabla siguiente se muestra cómo deben configurarse las copias de seguridad para que se ejecuten en los discos locales y de StorSimple, e incluye los requisitos de capacidad individuales y totales.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configuración de copia de seguridad y requisitos de capacidad

| Tipo de copia de seguridad y retención                    |Almacenamiento configurado| Tamaño (TiB) | Multiplicador de GFS | Capacidad total (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Semana 1 (completa e incremental) |Disco local (a corto plazo)| 1        | 1              | 1           |
| StorSimple semana 2-4           |Disco de StorSimple (largo plazo) | 1        | 4              | 4                   |
| Completa mensual                                 |Disco de StorSimple (largo plazo) | 1        | 12             | 12                   |
| Completa anual                               |Disco de StorSimple (largo plazo) | 1        | 1              | 1                   |
|Requisito de tamaño de volúmenes de GFS | |          |                | 18*|

\* la capacidad total incluye 17 TiB de discos de StorSimple y 1 TiB de volumen RAID local.


#### <a name="gfs-example-schedule"></a>Programación de ejemplo de GFS

|Programación semanal, mensual y anual de rotación de GFS|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Frecuencia                                                                     | Completo               | Incremental día 1        | Incremental día 2        | Incremental día 3        | Incremental día 4        | Incremental día 5        |
| Semana 1                                                                   | Volumen RAID local  | Volumen RAID local | Volumen RAID local | Volumen RAID local | Volumen RAID local | Volumen RAID local |
| Semana 2                                                                   | StorSimple semana 2-4 |                   |                   |                   |                   |                   |
| Semana 3                                                                   | StorSimple semana 2-4 |                   |                   |                   |                   |                   |
| Semana 4                                                                   | StorSimple semana 2-4 |                   |                   |                   |                   |                   |
| Mensual                                                                  | StorSimple mensual |                   |                   |                   |                   |                   |
| Anual                                                                   | StorSimple anual  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>Asignación de volúmenes de StorSimple a un trabajo de archivo/desduplicación de Backup Exec

1.  Una vez que el trabajo de copia de seguridad inicial se ha configurado para que use el volumen RAID como destino de copia de seguridad principal, vaya a la consola de administración de Veritas Backup Exec. Seleccione el trabajo que desea archivar en un volumen de StorSimple, haga clic con el botón derecho y seleccione **Backup Definition Properties** (Propiedades de definición de copia de seguridad). Haga clic en **Edit** (Editar).

    ![Consola de Backup Exec, pestaña de propiedades de las definiciones de copia de seguridad](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  Seleccione **Add Stage** (Agregar fase) y en la lista desplegable, seleccione **Duplicate to Disk** (Duplicar en disco). Haga clic en **Editar**.

    ![Consola de Backup Exec, agregar fase de definiciones de copia de seguridad](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  En **Duplicate Options** (Opciones de duplicación), seleccione el valor apropiado de **Source** (Origen) y de **Schedule** (Programación).

    ![Consola de Backup Exec, propiedades de las definiciones de copia de seguridad, Duplicate Options (Opciones de duplicación)](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  En la .lista desplegable **Storage** (Almacenamiento), seleccione el volumen de StorSimple en el que desea que el trabajo de archivo almacene los datos. En este caso, en el campo de origen se elige **Full** (Completa) y como destino se establece el volumen de archivo completo de StorSimple.

    ![Consola de Backup Exec, propiedades de las definiciones de copia de seguridad, Duplicate Options (Opciones de duplicación)](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  Vaya a **Verify** (Comprobar) y active **Do not verify data for this job** (No comprobar los datos de este trabajo).

    ![Consola de Backup Exec, propiedades de las definiciones de copia de seguridad, Duplicate Options (Opciones de duplicación)](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  Haga clic en **OK**.

    ![Consola de Backup Exec, propiedades de las definiciones de copia de seguridad, Duplicate Options (Opciones de duplicación)](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  En la columna **Backup** (Copia de seguridad), agregue una nueva fase. Elija un origen incremental y que el destino sea el volumen de StorSimple en el que se archiva el trabajo de copia de seguridad incremental. Repita los pasos anteriores.

## <a name="storsimple-cloud-snapshots"></a>Instantáneas de nube de StorSimple

Las instantáneas de nube de StorSimple protegen los datos que residen en el dispositivo de StorSimple. Es como enviar cintas a una instalación fuera del sitio y si se utiliza el almacenamiento con redundancia geográfica (GRS) de Azure, como enviar cintas a varios sitios. Si se necesita que se restaure un dispositivo en caso de desastre, puede poner en línea otro dispositivo de StorSimple y realizar una conmutación por error. Después de dicha conmutación, se podría acceder a los datos (a velocidades de nube) desde la instantánea en la nube más reciente.

En la sección siguiente se muestra cómo crear un script breve para desencadenar y eliminar instantáneas en la nube de StorSimple durante el procesamiento posterior a la copia de seguridad.

> [!NOTE]
> Las instantáneas que se crean manualmente o mediante programación no siguen la directiva de expiración de instantáneas de StorSimple. En otras palabras, se deben eliminar manualmente o mediante programación.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Inicio y eliminación de instantáneas en la nube con un script

> [!NOTE]
> Evalúe cuidadosamente las repercusiones tanto en lo relativo al cumplimiento como a la retención de datos antes de eliminar una instantánea de StorSimple. Para más información acerca de cómo ejecutar un script posterior a la copia de seguridad, consulte la documentación de Veritas Backup Exec.

#### <a name="backup-lifecycle"></a>Ciclo de vida de copia de seguridad

![Diagrama del ciclo de vida de copia de seguridad](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

#### <a name="requirements"></a>Requisitos:

-   El servidor que ejecute el script debe tener acceso a la nube de Azure.

-   La cuenta de usuario debe tener los permisos necesarios.

-   Una directiva de copia de seguridad de StorSimple con los volúmenes de StorSimple asociados configurada, pero no habilitada.

-   Nombre del recurso de StorSimple, clave de registro, nombre de dispositivo e identificador de directiva de copia de seguridad.

#### <a name="steps"></a>Pasos:

1.  [Instale Azure PowerShell](/powershell-install-configure/).

2.  [Descargue e importe la configuración de publicación y la información de suscripción](https://msdn.microsoft.com/library/dn385850.aspx).

3.  En el Portal de Azure clásico, obtenga el nombre del recurso y la [clave de registro del servicio StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  En el servidor que ejecuta el script, ejecute Windows PowerShell como administrador. Escriba:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Tome nota del identificador de la directiva de copia de seguridad.

5.  En el Bloc de notas, cree un nuevo script de Windows PowerShell y guárdelo en la misma ubicación en la que guardó la configuración de publicación de Azure. Por ejemplo: `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`.

    Copie y pegue el siguiente fragmento de código:

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
    $SnapshotStartTimeStamp = $Snapshot.CreatedOn
    if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
        $SnapShotInstanceID = $SnapShot.InstanceId
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  Agregue el script al trabajo de copia de seguridad en Veritas Backup Exec, para lo que debe editar los comandos previos y posteriores de las opciones del trabajo de Veritas Backup Exec.

    ![Consola de Backup Exec, opciones de copia de seguridad, pestaña de comandos previos o posteriores](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Se recomienda ejecutar la directiva de copia de seguridad de instantáneas en la nube de StorSimple al final del trabajo de copia de seguridad diaria como script posterior al proceso. Para más información acerca de cómo realizar copias de seguridad y restaurar el entorno de una aplicación de copia de seguridad para que cumpla el RPO/RTO, consulte al arquitecto de copia de seguridad.

## <a name="storsimple-as-a-restore-source"></a>StorSimple como origen de restauración

Las restauraciones de StorSimple funcionan de la misma forma que las de cualquier dispositivo de almacenamiento en bloque. Las restauraciones de los datos que están en capas en la nube se producen a velocidades de nube. En el caso de los datos locales, las restauraciones se producen a la velocidad del disco local del dispositivo. Para obtener información acerca de cómo realizar una restauración, consulte la documentación de Veritas Backup Exec y realice los procedimientos recomendados para la restauración en Veritas Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Conmutación por error y recuperación ante desastres de StorSimple

> [!NOTE]
> En los escenarios de destino de copia de seguridad, StorSimple Cloud Appliance no se admite como destino de restauración.

Hay varios factores que podrían provocar un desastre. En la tabla siguiente encontrará escenarios comunes de recuperación ante desastres (DR).

| Escenario                                                                    | Impacto                                             | Cómo realizar la recuperación                                                                                                                                                                               | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Error de dispositivo StorSimple.                                                | Se interrumpen las operaciones de copia de seguridad y restauración. | Reemplace el dispositivo con error y realice las operaciones de [conmutación por error y recuperación ante desastres de StorSimple](storsimple-device-failover-disaster-recovery.md). | Si se realiza una operación de restauración inmediatamente después de la recuperación del dispositivo, los espacios de trabajo completos se recuperan de la nube al nuevo dispositivo. Como consecuencia, todas las operaciones se realizan a velocidades de la nube. Además, un segundo examen del índice y del catálogo puede provocar que todos los conjuntos de copia de seguridad se examinen y se extraigan del nivel de la nube al nivel local del dispositivo. Esto hace que requiera mucho tiempo.                                                               |
| Error del servidor de Veritas Backup Exec.                                       | Se interrumpen las operaciones de copia de seguridad y restauración. | Vuelva a compilar el servidor de copia de seguridad y realice la restauración de base de datos como se detalla en [How to do a manual Backup and Restore of Backup Exec (BEDB) database](http://www.veritas.com/docs/000041083) [Cómo realizar una copia de seguridad y una restauración de una base de datos de Backup Exec (BEDB)].            | Es preciso que el servidor de Veritas Backup Exec se vuelva a generar o restaurar en el sitio de recuperación ante desastres. La base de datos debe restaurarse al punto más reciente. Si la base de datos de Veritas Backup Exec restaurada no está sincronizada con los trabajos de copia de seguridad más recientes, se requieren la indexación y la catalogación. El segundo examen del índice y del catálogo puede provocar que los conjuntos de copia de seguridad se examinen y se extraigan del nivel de la nube al nivel del dispositivo local. Esto hace que requiera mucho tiempo. |
| Error del sitio que da lugar a la pérdida del servidor de copia de seguridad y de StorSimple. | Se interrumpen las operaciones de copia de seguridad y restauración. | Restaure primero StorSimple y, después, Veritas Backup Exec.                                                                                                                                   | Restaure primero StorSimple y, después, Veritas Backup Exec.                                                                Si es preciso realizar una operación de restauración inmediatamente después de la recuperación del dispositivo, los espacios de trabajo completos se recuperan de la nube al nuevo dispositivo. Como consecuencia, todas las operaciones se realizan a velocidad de la nube.|


## <a name="references"></a>Referencias

En este artículo se ha hecho referencia a los siguientes documentos:

- [Configurar E/S de múltiples rutas para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md)

- [Escenarios de almacenamiento: el aprovisionamiento fino](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Using GPT drives](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD) (Uso de unidades de GPT)

- [Habilitar y configurar las instantáneas de carpetas compartidas](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Pasos siguientes

Más información:

- Acerca de cómo [restaurar a partir de un conjunto de copia de seguridad](storsimple-restore-from-backup-set-u2.md).
- Acerca de cómo realizar [la conmutación por error y la recuperación ante desastres en los dispositivos](storsimple-device-failover-disaster-recovery.md).


<!--HONumber=Dec16_HO2-->


