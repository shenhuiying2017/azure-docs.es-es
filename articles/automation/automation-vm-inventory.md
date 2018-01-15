---
title: "Administración de una máquina virtual de Azure con la recopilación de inventario | Microsoft Docs"
description: "Administración de una máquina virtual con la recopilación de inventario"
services: automation
keywords: "inventario, automatización, cambio, seguimiento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7b0e39e98a81231b68414f36ac5c1fc0897304a1
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Administración de una máquina virtual de Azure con la recopilación de inventario

Puede habilitar el seguimiento del inventario para una máquina virtual de Azure desde la página de recursos de esta. Este método proporciona una interfaz de usuario basada en explorador para instalar y configurar la recopilación de inventario.

## <a name="before-you-begin"></a>Antes de empezar
Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/).
Si no tiene una máquina virtual de Azure, cree una [máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.
Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitación de la recopilación de inventario desde la página de recursos de la máquina virtual

1. En el panel izquierdo de Azure Portal, seleccione **Máquinas virtuales**.
2. En la lista de máquinas virtuales, seleccione una máquina virtual.
3. En el menú **Recurso**, en **Operaciones**, seleccione **Inventario (versión preliminar)**.  
    Aparecerá un banner en la parte superior de la ventana que le notificará que la solución no está habilitada. 
4. Seleccione el banner para habilitar la solución.
5. Seleccione un área de trabajo de Log Analytics para almacenar los registros de datos.  
    Si no hay áreas de trabajo disponibles para esa región, deberá crear una predeterminada y una cuenta de Automation. 
6. Seleccione **Habilitar** para iniciar la incorporación al equipo.

   ![Vista de las opciones de incorporación](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Una barra de estado le notifica que la solución se está habilitando. Este proceso puede tardar hasta 15 minutos. Durante este tiempo, puede cerrar la ventana o, para que le notifique cuando se haya habilitado la solución, puede mantenerla abierta. Puede supervisar el estado de la implementación desde el panel de notificaciones.

   ![Vista de la solución de inventario inmediatamente después de la incorporación](./media/automation-vm-inventory/inventory-onboarded.png)

Una vez completada la implementación, la barra de estado desaparece. El sistema sigue recopilando datos de inventario y puede que estos aún no se vean. La recopilación de datos puede tardar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configuración del inventario

De forma predeterminada, el software, los servicios de Windows y los demonios de Linux están configurados para la recopilación. Para recopilar el registro de Windows y el inventario de archivos, configure las opciones de recopilación de inventario.

1. En la vista **Inventario (versión preliminar)**, seleccione el botón **Editar configuración** de la parte superior de la ventana.
2. Para agregar una nueva configuración de recopilación, vaya a la categoría de configuración que desee agregar seleccionando las pestañas **Registro de Windows**, **Archivos de Windows** y **Archivos de Linux**. 
3. Seleccione **Agregar** en la parte superior de la ventana.
4. Para ver los detalles y las descripciones de cada propiedad de configuración, visite la [página de documentación del inventario](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Interrupción de la administración de la máquina virtual

Para dejar de administrar el inventario de la máquina virtual:

1. En el panel izquierdo de Azure Portal, seleccione **Log Analytics** y, a continuación, el área de trabajo que usó durante la incorporación a la máquina virtual.
2. En la ventana **Log Analytics**, en la categoría **Orígenes de datos del área de trabajo** del menú **Recurso**, seleccione **Máquinas virtuales**. 
3. En la lista, seleccione la máquina virtual que desee desconectar. La máquina virtual tiene una marca de verificación verde junto a **Esta área de trabajo** en la columna **Conexión a OMS**. 
4. En la parte superior de la página siguiente, seleccione **Desconectar**.
5. En la ventana de confirmación, seleccione **Sí**.  
    Esta acción interrumpe la administración de la máquina.

## <a name="next-steps"></a>pasos siguientes

* Para más información acerca de cómo administrar los cambios de configuración en los archivos y el registro de las máquinas virtuales, consulte [Seguimiento de cambios de software en su entorno con la solución de seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md).
* Para más información sobre cómo administrar Windows y las actualizaciones de los paquetes en las máquinas virtuales, consulte [Solución Administración de actualizaciones de OMS](../operations-management-suite/oms-solution-update-management.md).
