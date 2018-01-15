---
title: "Administración de actualizaciones para varias máquinas virtuales de Azure | Documentos de Microsoft"
description: "En este tema se describe cómo administrar las actualizaciones de máquinas virtuales de Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: magoedte;gwallace
ms.openlocfilehash: 1763077aa733fc93dd59147405db9942c6c98960
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="manage-updates-for-multiple-machines"></a>Administración de actualizaciones para varias máquinas

Puede usar Update Management para administrar las actualizaciones y las revisiones para las máquinas Windows y Linux. Desde la cuenta de [Azure Automation](automation-offering-get-started.md), puede:

- Incorporar máquinas virtuales.
- Evaluar el estado de las actualizaciones disponibles.
- Programar la instalación de las actualizaciones necesarias.
- Revisar los resultados de la implementación para comprobar que las actualizaciones se han aplicado correctamente en todas las máquinas virtuales para las cuales se ha habilitado la administración de actualizaciones.

## <a name="prerequisites"></a>Requisitos previos

Para usar Update Management, necesita:

* Una cuenta de ejecución de Azure Automation Para obtener instrucciones sobre cómo crear una, consulte [introducción a Azure Automation](automation-offering-get-started.md).

* Una máquina virtual o un equipo con uno de los sistemas operativos admitidos instalado.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles

Update Management es compatible con los sistemas operativos siguientes.

### <a name="windows"></a>Windows

* Windows Server 2008 y versiones posterior, y las implementaciones de actualizaciones en Windows Server 2008 R2 SP1 y versiones posteriores. Nano Server no se admite.

  Para poder implementar las actualizaciones en Windows Server 2008 R2 SP1, se necesita .NET Framework 4.5 y Windows Management Framework 5.0 o versiones posteriores.

* No se admiten los sistemas operativos cliente Windows.

Los agentes de Windows deben estar configurados para comunicarse con un servidor de Windows Server Update Services (WSUS) o tener acceso a Microsoft Update.

> [!NOTE]
> System Center Configuration Manager no puede administrar simultáneamente el agente de Windows.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) y 7 (x64)  
* Red Hat Enterprise (x86/x64) 6 y 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) y 12 (x64)  
* Ubuntu 12.04 LTS y versiones más recientes (x86/x64)   

> [!NOTE]  
> Para evitar que las actualizaciones se apliquen fuera de una ventana de mantenimiento en Ubuntu, vuelva a configurar el paquete de actualizaciones desatendidas para deshabilitar las actualizaciones automáticas. Para más información, consulte el [tema sobre actualizaciones automáticas en la guía de Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Los agentes de Linux deben tener acceso a un repositorio de actualización.

Esta solución no es compatible con un agente de OMS para Linux que se configura para informar a varias áreas de trabajo de Operations Management Suite.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitación de la administración de actualizaciones de máquinas virtuales de Azure

1. Abra la cuenta de Automation en Azure Portal.
2. En el panel izquierdo, seleccione **Update Management**.
3. En la parte superior de la ventana, seleccione **Agregar máquina virtual de Azure**.
   ![Pestaña Agregar máquina virtual de Azure](./media/manage-update-multi/update-onboard-vm.png)
4. Seleccione la máquina virtual que desea incorporar. Aparece el cuadro de diálogo **Habilitar la administración de actualizaciones**.
5. Seleccione **Habilitar**.

   ![Cuadro de diálogo Habilitar la administración de actualizaciones](./media/manage-update-multi/update-enable.png)

Update Management está habilitado para su máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitación de Update Management en equipos y máquinas virtuales que no son de Azure

Para obtener instrucciones sobre cómo habilitar Update Management en equipos y máquinas virtuales Windows que no sean de Azure, consulte [Conexión de equipos Windows al servicio Log Analytics de Azure](../log-analytics/log-analytics-windows-agent.md).

Para obtener instrucciones acerca de cómo habilitar Update Management en equipos y máquinas virtuales Linux que no sean de Azure, consulte [Conexión de equipos con Linux a Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visualización de equipos conectados a su cuenta de Automation
Tras habilitar la administración de actualizaciones en las máquinas, para su información, haga clic en **Equipos**. Verá la información del equipo, como su *nombre*, *cumplimiento*, *entorno*, *tipo de SO*, *actualizaciones críticas y de seguridad*  y *otras actualizaciones*. 

  ![Pestaña en la que se ven los equipos](./media/manage-update-multi/update-computers-tab.png)

Los equipos en los que se haya habilitado recientemente la administración de actualizaciones puede que no se hayan evaluado aún. El estado de cumplimiento de esos equipos sería *No evaluado*.  Esta es una lista de valores del estado de cumplimiento:
* Compatible: equipos que no tienen actualizaciones de seguridad o críticas pendientes.
* No compatible: equipos que no tienen al menos una actualización de seguridad o crítica pendiente.
* No evaluado: los datos de evaluación de las actualizaciones no se han recibido del equipo en el período esperado.  En los equipos con Linux, dicho periodo es las últimas tres horas, mientras que en los equipos con Windows, las últimas 12 horas.  

## <a name="view-an-update-assessment"></a>Visualización de la evaluación de la actualización

Una vez habilitado Update Management, se muestra el cuadro de diálogo **Administración de actualizaciones**. Puede ver una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

## <a name="collect-data"></a>Recopilación de datos

Tanto los agentes instalados en las máquinas virtuales como los equipos recopilan datos acerca de las actualizaciones y los envían a Azure Update Management.

### <a name="supported-agents"></a>Agentes admitidos

En la tabla siguiente se describen los orígenes conectados compatibles con esta solución:

| Origen conectado | Compatible | DESCRIPCIÓN |
| --- | --- | --- |
| Agentes de Windows |Sí |Update Management recopila información acerca de las actualizaciones del sistema de los agentes de Windows e inicia la instalación de las actualizaciones necesarias. |
| Agentes de Linux |Sí |Update Management recopila información acerca de las actualizaciones del sistema de los agentes de Linux e inicia la instalación de las actualizaciones necesarias en las distribuciones admitidas. |
| Grupo de administración de Operations Manager |Sí |Update Management recopila información acerca de las actualizaciones del sistema de agentes en un grupo de administración conectado. |
| Cuenta de almacenamiento de Azure |Sin  |Azure Storage no incluye información acerca de las actualizaciones del sistema. |

### <a name="collection-frequency"></a>Frecuencia de recopilación

Para cada equipo Windows administrado, se ejecuta un examen dos veces al día. Cada 15 minutos, se llama a la API de Windows para consultar la hora de la última actualización y determinar si ha cambiado el estado. Si es así, se inicia un examen de cumplimiento. Para cada equipo Linux administrado, se ejecuta un examen cada tres horas.

Puede tardar entre 30 minutos y 6 horas mostrar en el panel los datos actualizados de los equipos administrados.

## <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones.
Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

Programe una nueva implementación de actualizaciones para una o varias máquinas virtuales. Para ello, seleccione **Programar implementación de actualizaciones** en la parte superior del cuadro de diálogo **Administración de actualizaciones**. En el panel **Nueva implementación de actualización**, especifique lo siguiente:

* **Nombre**: proporcione un nombre único para identificar la implementación de actualizaciones.
* **Tipo de SO**: seleccione Windows o Linux.
* **Equipos para actualizar**: seleccione las máquinas virtuales que desea actualizar.

  ![Panel Nueva implementación de actualización](./media/manage-update-multi/update-select-computers.png)

* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones va a incluir. Los tipos de clasificación son:
  * Actualizaciones críticas
  * Actualizaciones de seguridad
  * Paquetes acumulativos de actualizaciones
  * Feature Packs
  * Service Packs
  * Actualizaciones de definiciones
  * Herramientas
  * Actualizaciones
* **Configuración de la programación**: puede aceptar la fecha y hora predeterminadas, que son 30 minutos después de la hora actual. O bien, especificar una hora distinta.
   También puede especificar si la implementación se produce una vez o configurar una programación periódica. Para configurar una programación periódica, haga clic en la opción **Periódica** en **Periodicidad**.

   ![Cuadro de diálogo Configuración de programación](./media/manage-update-multi/update-set-schedule.png)

* **Ventana de mantenimiento (minutos)**: especifique el período de tiempo en el que desea que se produzca la implementación de actualizaciones. Esta configuración ayuda a garantizar que los cambios se realizan en las ventanas de servicio definidas.

Después de finalizar la configuración de la programación, vuelva al panel de estado mediante la selección del botón **Crear**. La tabla **Programada** muestra la programación de implementación que ha creado.

> [!WARNING]
> Para las actualizaciones que requieren un reinicio, la máquina virtual se reiniciará automáticamente.

## <a name="view-results-of-an-update-deployment"></a>Visualización de los resultados de una implementación de actualizaciones

Después de iniciar la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en el cuadro de diálogo **Administración de actualizaciones**.
Si la implementación se está ejecutando, su estado es **En curso**. Cuando la implementación finaliza correctamente, cambia a **Correcto**.
Si se produce un error en una o varias actualizaciones de la implementación, el estado es **Error parcial**.

![Estado de la implementación de actualizaciones](./media/manage-update-multi/update-view-results.png)

Para establecer el panel para una implementación de actualizaciones, seleccione la implementación completada.

En el panel **Resultados de actualización** encontrará el número total de actualizaciones y los resultados de la implementación en la máquina virtual.
En la tabla de la derecha se muestra un análisis detallado de cada actualización y los resultados de la instalación. Los resultados de la instalación puede ser uno de los siguientes valores:

* No intentado: la actualización no se instaló porque no había tiempo disponible suficiente de acuerdo con la ventana de mantenimiento definida.
* Correcto: la actualización se realizó correctamente.
* Error: se produjo un error en la actualización.

Seleccione **Todos los registros** para ver todas las entradas de registro que creó la implementación.

Seleccione el icono **Salida** para ver el flujo de trabajo del runbook que administra la implementación de actualizaciones en la máquina virtual de destino.

Seleccione **Errores** para ver información detallada sobre los errores de la implementación.

## <a name="next-steps"></a>pasos siguientes

* Para más información sobre la administración de actualizaciones, incluso registros, salida y errores, consulte [Solución Administración de actualizaciones de OMS](../operations-management-suite/oms-solution-update-management.md).

