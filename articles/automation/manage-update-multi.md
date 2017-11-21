---
title: "Administración de actualizaciones para varias máquinas virtuales de Azure | Documentos de Microsoft"
description: "En este tema se describe cómo administrar las actualizaciones de máquinas virtuales de Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: f97b28d1588e959728163f7ab16d2550a79f610e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Administración de actualizaciones para varias máquinas

Update Management permite administrar las actualizaciones y las revisiones para las máquinas Windows y Linux.
Desde la cuenta de [Azure Automation](automation-offering-get-started.md), puede incorporar máquinas rápidamente, evaluar el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en todas las máquinas virtuales en las que se habilitó Update Management.

## <a name="prerequisites"></a>Requisitos previos

Para usar Update Management, necesita:

* Una cuenta de Azure Automation Para obtener instrucciones sobre cómo crear una cuenta de ejecución de Azure Automation, consulte el artículo [Introducción a Azure Automation](automation-offering-get-started.md).

* Una máquina virtual o un equipo con uno de los sistemas operativos admitidos instalado.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles

Update Management es compatible con los sistemas operativos siguientes.

### <a name="windows"></a>Windows

* Windows Server 2008 y versiones posterior, y las implementaciones de actualizaciones en Windows Server 2008 R2 SP1 y versiones posteriores.  No se admiten las opciones de instalación de Server Core y Nano Server.

    > [!NOTE]
    > Para poder implementar las actualizaciones en Windows Server 2008 R2 SP1 se necesita .NET Framework 4.5 y WMF 5.0 o versiones posteriores.
    > 
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
> Para evitar que las actualizaciones se apliquen fuera de una ventana de mantenimiento en Ubuntu, vuelva a configurar el paquete de actualizaciones desatendidas para deshabilitar las actualizaciones automáticas. Para más información sobre cómo configurarlo, consulte el [tema sobre actualizaciones automáticas en la Guía de Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Los agentes de Linux deben tener acceso a un repositorio de actualización.

> [!NOTE]
> La solución no admite un agente de OMS para Linux configurado para informar a varias áreas de trabajo OMS.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitación de la administración de actualizaciones de máquinas virtuales de Azure

1. Abra la cuenta de Automation en Azure Portal.
2. En el lado izquierdo de la pantalla, seleccione **Administración de actualizaciones**.
3. En la parte superior de la pantalla, haga clic en **Agregar máquina virtual de Azure**.
    ![Incorporación de máquinas virtuales](./media/manage-update-multi/update-onboard-vm.png)
4. Seleccione la máquina virtual que desea incorporar. Aparece la pantalla **Habilitar la administración de actualizaciones**.
5. Hacer clic en **Habilitar**.

   ![Habilitar la administración de actualizaciones](./media/manage-update-multi/update-enable.png)

Update Management está habilitado para su máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitación de Update Management en equipos y máquinas virtuales que no son de Azure

Para obtener instrucciones sobre cómo habilitar Update Management en equipos y máquinas virtuales Windows que no sean de Azure, consulte [Conexión de equipos Windows al servicio Log Analytics de Azure](../log-analytics/log-analytics-windows-agents.md).

Para obtener instrucciones acerca de cómo habilitar Update Management en equipos y máquinas virtuales Linux que no sean de Azure, consulte [Conexión de equipos Linux a Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-update-assessment"></a>Visualización de la evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Puede ver una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

## <a name="data-collection"></a>Colección de datos

Tanto los agentes instalados en las máquinas virtuales como los equipos recopilan datos acerca de las actualizaciones y los envían a Azure Update Management.

### <a name="supported-agents"></a>Agentes admitidos

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Compatible | Description |
| --- | --- | --- |
| Agentes de Windows |Sí |Update Management recopila información acerca de las actualizaciones del sistema de los agentes de Windows e inicia la instalación de las actualizaciones necesarias. |
| Agentes de Linux |Sí |Update Management recopila información acerca de las actualizaciones del sistema de los agentes de Linux e inicia la instalación de las actualizaciones necesarias en las distribuciones admitidas. |
| Grupo de administración de Operations Manager |Sí |Update Management recopila información acerca de las actualizaciones del sistema de agentes en un grupo de administración conectado. |
| Cuenta de almacenamiento de Azure |No |Azure Storage no incluye información acerca de las actualizaciones del sistema. |

### <a name="collection-frequency"></a>Frecuencia de recopilación

Para cada equipo Windows administrado, se realiza un examen dos veces al día. Cada 15 minutos, se llama a la API de Windows para consultar la hora de la última actualización y determinar si ha cambiado el estado y, de ser así, se inicia un examen de cumplimiento.  Para cada equipo Linux administrado, se realiza un examen cada tres horas.

Puede tardar entre 30 minutos y 6 horas mostrar en el panel los datos actualizados de los equipos administrados.

## <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones.
Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

Programe una nueva implementación de actualizaciones para una o varias máquinas virtuales. Para ello, haga clic en **Programar implementación de actualizaciones** en la parte superior de la pantalla **Administración de actualizaciones**. En la pantalla **Nueva implementación de actualización**, especifique lo siguiente:

* **Nombre**: proporcione un nombre único para identificar la implementación de actualizaciones.
* **Tipo de SO**: seleccione Windows o Linux.
* **Equipos para actualizar**: seleccione las máquinas virtuales que desea actualizar.

  ![Selección de máquinas virtuales para actualizar](./media/manage-update-multi/update-select-computers.png)

* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones va a incluir en la implementación. Los tipos de clasificación son:
  * Actualizaciones críticas
  * Actualizaciones de seguridad
  * Paquetes acumulativos de actualizaciones
  * Feature Packs
  * Service Packs
  * Actualizaciones de definiciones
  * Herramientas
  * Actualizaciones
* **Configuración de la programación**: puede aceptar la fecha y hora predeterminadas, es decir, 30 minutos después de la hora actual, o bien especificar una hora distinta.
   También puede especificar si la implementación se produce una vez o configurar una programación periódica. Haga clic en la opción Periódica en Periodicidad para configurar una programación periódica.

   ![Pantalla de configuración de la programación de actualizaciones](./media/manage-update-multi/update-set-schedule.png)

* **Ventana de mantenimiento (minutos)**: especifique el período de tiempo en el que desea que se produzca la implementación de actualizaciones.  Esto ayuda a garantizar que los cambios se realizan en las ventanas de servicio definidas.

Después de completar la configuración de la programación, haga clic en el botón **Crear** para volver al panel de estado.
Tenga en cuenta que la tabla **Programada** muestra la programación de implementación que ha creado.

> [!WARNING]
> Para las actualizaciones que requieren un reinicio, la máquina virtual se reiniciará automáticamente.

## <a name="view-results-of-an-update-deployment"></a>Visualización de los resultados de una implementación de actualizaciones

Después de iniciar la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Administración de actualizaciones**.
Si se está ejecutando actualmente, su estado se muestra como **En curso**. Cuando se completa, si se realiza correctamente, cambia a **Correcto**.
Si se produce un error con una o varias actualizaciones en la implementación, el estado es **Error parcial**.

![Estado de implementación de actualizaciones ](./media/manage-update-multi/update-view-results.png)

Haga clic en la implementación de actualizaciones completada para ver el panel correspondiente.

En el icono **Resultados de actualización** encontrará un resumen del número total de actualizaciones y los resultados de la implementación en la máquina virtual.
En la tabla de la derecha encontrará un análisis detallado de cada actualización y los resultados de la instalación, que podrían ser uno de los valores siguientes:

* No intentado: la actualización no se instaló porque no había tiempo disponible suficiente de acuerdo con la duración definida para la ventana de mantenimiento.
* Correcto: la actualización se realizó correctamente.
* Error: se produjo un error en la actualización.

Haga clic en **Todos los registros** para ver todas las entradas de registro que creó la implementación.

Haga clic en el icono **Salida** para ver el flujo de trabajo del runbook responsable de administrar la implementación de actualizaciones en la máquina virtual de destino.

Haga clic en **Errores** para ver información detallada sobre los errores de la implementación.

Para obtener información detallada acerca de los registros, la salida y la información de error, consulte [Update Management](../operations-management-suite/oms-solution-update-management.md).

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Update Management, consulte [Update Management](../operations-management-suite/oms-solution-update-management.md).
