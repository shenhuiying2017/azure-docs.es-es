---
title: "Seguimiento de cambios en máquinas virtuales de Azure | Microsoft Docs"
description: "Use el seguimiento de cambios para controlar los cambios que se realizan en los archivos y el Registro de las máquinas virtuales."
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Seguimiento de cambios en máquinas virtuales de Azure

Al habilitar el seguimiento de cambios, puede controlar los cambios que se realizan en los archivos y las claves del Registro de Windows de las máquinas virtuales. . Identificar los cambios de configuración puede ayudarle a localizar problemas operativos.

Puede habilitar el seguimiento de cambios directamente desde la máquina virtual de Azure.

Si no tiene una máquina virtual de Azure, puede crear una mediante las instrucciones de la [guía de inicio rápido de Windows](../virtual-machines/windows/quick-create-portal.md) o la [guía de inicio rápido de Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Habilitar el seguimiento de cambios para una máquina virtual de Azure

1. En el lado izquierdo de la pantalla, seleccione **Máquinas virtuales**.
1. Seleccione una máquina virtual de la lista.
1. En la pantalla de la máquina virtual, en la sección **Operaciones**, haga clic en **Seguimiento de cambios**. Se abre la pantalla **Habilitar la administración de actualizaciones**.

   ![Incorporación de máquina virtual al seguimiento de cambios](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

Se realiza la validación para determinar si el seguimiento de cambios está habilitado para esta máquina virtual. Si no lo está, aparece un banner que le ofrece la opción de habilitar la solución.

   ![Banner de configuración de la incorporación del seguimiento de cambios](./media/automation-vm-change-tracking/change-onboard-banner.png)

Haga clic en el banner para habilitar la solución. Si no tiene lo siguiente, se agregará automáticamente:

* Área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md)
* Cuenta de [Automation](../automation/automation-offering-get-started.md)

Seleccione un área de trabajo de Log Analytics para almacenar los registros de datos del seguimiento de cambios y una cuenta de Automation para realizar el seguimiento de cambios. Después, haga clic en **Habilitar**.

Una barra de estado le notifica que la solución se está habilitando. Este proceso puede tardar hasta 15 minutos.

## <a name="configure-change-tracking"></a>Configurar el seguimiento de cambios

Una vez que se ha habilitado el seguimiento de cambios, aparece la pantalla **Seguimiento de cambios**. Para elegir los archivos y las claves del Registro de los que quiere realizar un seguimiento, haga clic en **Editar configuración**.

![Editar configuración del seguimiento de cambios](./media/automation-vm-change-tracking/change-edit-settings.png)

Se abre la pantalla **Configuración del área de trabajo**. Haga clic en **Agregar** en la pestaña correspondiente para agregar las claves del Registro de Windows, los archivos de Windows o los archivos de Linux de los que quiere realizar un seguimiento.

## <a name="add-a-windows-registry-key"></a>Agregar una clave del Registro de Windows

1. En la pestaña **Registro de Windows** de la pantalla **Configuración del área de trabajo**, haga clic en **Agregar**. Se abre la pantalla **Agregar Registro de Windows para el seguimiento de cambios**.

   ![Agregar Registro para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-registry.png)

1. En **Habilitado**, seleccione **True**.
1. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
1. En el campo **Grupo**, escriba un nombre de grupo (opcional).
1. En el campo **Clave del Registro de Windows**, agregue el nombre de la clave del Registro de la que quiere realizar un seguimiento.
1. Haga clic en **Guardar**.

## <a name="add-a-windows-file"></a>Agregar un archivo de Windows

1. En la pestaña **Archivos de Windows** de la pantalla **Configuración del área de trabajo**, haga clic en **Agregar**. Se abre la pantalla **Agregar archivo de Windows para el seguimiento de cambios**.

   ![Agregar archivo de Windows para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-win-file.png)

1. En **Habilitado**, seleccione **True**.
1. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
1. En el campo **Grupo**, escriba un nombre de grupo (opcional).
1. En el campo **Indicar ruta de acceso**, agregue la ruta de acceso completa y el nombre del archivo del que quiere realizar un seguimiento.
1. Haga clic en **Guardar**.

## <a name="add-a-linux-file"></a>Agregar un archivo de Linux

1. En la pestaña **Archivos de Linux** de la pantalla **Configuración del área de trabajo**, haga clic en **Agregar**. Se abre la pantalla **Agregar archivo de Linux para el seguimiento de cambios**.

   ![Agregar archivo de Linux para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. En **Habilitado**, seleccione **True**.
1. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
1. En el campo **Grupo**, escriba un nombre de grupo (opcional).
1. En el campo **Indicar ruta de acceso**, agregue la ruta de acceso completa y el nombre del archivo del que quiere realizar un seguimiento.
1. En el campo **Tipo de ruta de acceso**, seleccione **Archivo** o **Directorio**.
1. En **Recursión**, seleccione **Activado** para realizar el seguimiento de cambios para la ruta de acceso especificada y para todos los archivos y las rutas de acceso que se encuentran bajo ella. Para realizar el seguimiento únicamente de la ruta de acceso o el archivo especificados, seleccione **Desactivar**.
1. En **Sudo**, seleccione **Activado** para realizar un seguimiento de los archivos que requieren el comando `sudo` para el acceso. De lo contrario, seleccione **Desactivado**.
1. Haga clic en **Guardar**.

## <a name="view-changes"></a>Ver los cambios

En la pantalla **Seguimiento de cambios**, puede ver los cambios en cada una de las distintas categorías para la máquina virtual a lo largo del tiempo.

   ![Agregar la visualización del seguimiento de cambios](./media/automation-vm-change-tracking/change-view-changes.png)

Puede seleccionar las categorías y el intervalo de tiempo de los cambios que se van a visualizar. En la parte superior de la pantalla, verá una vista gráfica de los cambios a lo largo del tiempo.
En la parte inferior de la pantalla, verá una lista de los cambios recientes.

## <a name="view-change-tracking-log-data"></a>Datos de registro de la visualización del seguimiento de cambios

El seguimiento de cambios genera datos de registro que se envían a Log Analytics. Para buscar los registros mediante la ejecución de consultas, haga clic en **Log Analytics** en la parte superior de la pantalla **Seguimiento de cambios**.

   ![Log Analytics para el seguimiento de cambios](./media/automation-vm-change-tracking/change-log-analytics.png)

Para obtener más información sobre la ejecución de archivos de registro de búsqueda en Log Analytics, vea [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre el seguimiento de cambios, vea [Seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md).
* Para obtener más información sobre cómo administrar las actualizaciones de las máquinas virtuales, vea [Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md).

