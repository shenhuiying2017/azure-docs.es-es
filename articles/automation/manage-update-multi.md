---
title: "Administración de actualizaciones para varias máquinas virtuales de Azure | Documentos de Microsoft"
description: "Incorporación de máquinas virtuales de Azure para administrar actualizaciones."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Administración de actualizaciones para varias máquinas virtuales de Azure

Update Management permite administrar las actualizaciones y las revisiones para las máquinas virtuales de Azure.
Desde la cuenta de [Azure Automation](automation-offering-get-started.md), puede incorporar rápidamente máquinas virtuales, evaluar el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en todas las máquinas virtuales para las que se ha habilitado Update Management.

## <a name="prerequisites"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de esta guía:

* Una cuenta de Automatización de Azure Para obtener instrucciones sobre cómo crear una cuenta de ejecución de Azure Automation, consulte el artículo sobre las [cuentas de ejecución de Azure](automation-sec-configure-azure-runas-account.md).
* Una máquina virtual de Azure Resource Manager (no clásico). Para obtener instrucciones sobre la creación de una máquina virtual, consulte [Creación de la primera máquina virtual de Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitación de la administración de actualizaciones de máquinas virtuales de Azure

1. Abra la cuenta de Automation en Azure Portal.
2. En el lado izquierdo de la pantalla, seleccione **Administración de actualizaciones**.
3. En la parte superior de la pantalla, haga clic en **Agregar máquina virtual de Azure**.
    ![Incorporación de máquinas virtuales](./media/manage-update-multi/update-onboard-vm.png)
4. Seleccione la máquina virtual que desea incorporar. Aparece la pantalla **Habilitar la administración de actualizaciones**.
5. Hacer clic en **Habilitar**.

   ![Habilitar la administración de actualizaciones](./media/manage-update-multi/update-enable.png)

Update Management está habilitado para su máquina virtual.

## <a name="view-update-assessment"></a>Visualización de la evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Puede ver una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

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

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Update Management, consulte [Update Management](../operations-management-suite/oms-solution-update-management.md).