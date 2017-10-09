---
title: "Administración de una máquina virtual de Azure con la recopilación de inventario | Microsoft Docs"
description: "Administración de una máquina virtual con la recopilación de inventario"
services: automation
keywords: "inventario, automatización, cambio, seguimiento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Administración de una máquina virtual de Azure con la recopilación de inventario

El seguimiento del inventario puede habilitarse para una máquina virtual de Azure desde la página de recursos de esta. Este método proporciona una interfaz de usuario basada en explorador para instalar y configurar la recopilación de inventario.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
Si no la tiene, cree una [máquina virtual](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) de Azure antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Habilitación de la recopilación de inventario desde la página de recursos de la máquina virtual

1. En la pantalla de la izquierda, haga clic en **Máquinas virtuales**.
1. Seleccione una máquina virtual de la lista.
1. Seleccione **Inventario (versión preliminar)** del menú de recursos de **Operaciones**.
1. Aparecerá un banner en la parte superior de la página que le notificará que la solución no está habilitada. Haga clic en el banner para habilitar la solución.
1. Seleccione un área de trabajo de Log Analytics para almacenar los registros de datos. Si no hay áreas de trabajo disponibles para esa región, deberá crear una predeterminada y la cuenta de Automation. Haga clic en **Habilitar** para iniciar la incorporación al equipo.

   ![Vista de las opciones de incorporación](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Una barra de estado le notifica que la solución se está habilitando. Este proceso puede tardar hasta 15 minutos. Durante este tiempo, puede cerrar la hoja o mantenerla abierta, se le notificará se haya habilitado la solución. Puede supervisar el estado de la implementación desde el panel de notificaciones.

   ![Vista de la solución de inventario inmediatamente después de la incorporación](./media/automation-vm-inventory/inventory-onboarded.png)

1. Una vez completada la implementación, la barra de estado desaparece. En este momento, el sistema sigue recopilando datos de inventario y puede que estos aún no se vean. La recopilación de datos puede tardar 24 horas.

## <a name="configure-your-inventory-settings"></a>Configuración del inventario

De forma predeterminada, el software, los servicios de Windows y los demonios de Linux están configurados para la recopilación. Para recopilar el registro de Windows y el inventario de archivos, configure las opciones de recopilación de inventario.

1. Desde la vista **Inventario (versión preliminar)**, seleccione el botón **Editar configuración** de la parte superior de la página.
2. Para agregar una nueva configuración de recopilación, vaya a la categoría de configuración que desee agregar mediante las pestañas denominadas **Registro de Windows**, **Archivos de Windows**, y **Archivos de Linux**. Haga clic en **Agregar** en la parte superior de la página.
3. Para ver los detalles y las descripciones de cada propiedad de configuración, visite la [página de documentación del inventario](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Interrupción de la administración de la máquina virtual

Para dejar de administrar el inventario de la máquina:

1. En el menú izquierdo de Azure Portal, haga clic en **Log Analytics** y seleccione el área de trabajo que usó durante la incorporación a la máquina virtual.
1. En la página de Log Analytics, seleccione **Máquinas virtuales** en la categoría **Orígenes de datos del área de trabajo** del menú de recursos. 
1. Seleccione la máquina virtual que quiera desconectar de la lista. Tendrá una marca de verificación verde junto al texto que dice "Esta área de trabajo" en la columna **Conexión a OMS**. Haga clic en **Desconectar** en la parte superior de la página siguiente y en **Sí** en el cuadro de diálogo de confirmación para dejar de administrar la máquina.

## <a name="next-steps"></a>Pasos siguientes

* Más información acerca de cómo administrar los cambios de configuración en los archivos y el registro de las máquinas virtuales, consulte el artículo de [seguimiento de los cambios](../log-analytics/log-analytics-change-tracking.md).
* Para más información sobre cómo administrar Windows y las actualizaciones de los paquetes en las máquinas virtuales, consulte el artículo de [administración de las actualizaciones](../operations-management-suite/oms-solution-update-management.md).

