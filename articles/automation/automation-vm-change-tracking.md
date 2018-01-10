---
title: "Seguimiento de cambios en máquinas virtuales de Azure | Microsoft Docs"
description: "Use el seguimiento de cambios para controlar los cambios que se realizan en los archivos y el Registro de las máquinas virtuales."
services: automation
documentationcenter: automation
author: georgewallace
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
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: 3a661fada2e768c2206183c125593d019b557c1d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Seguimiento de cambios en máquinas virtuales de Azure

Al habilitar el seguimiento de cambios, puede controlar los cambios que se realizan en los archivos y las claves del Registro de Windows de las máquinas virtuales. Identificar los cambios de configuración puede ayudarle a localizar problemas operativos.

Puede habilitar el seguimiento de cambios directamente desde la máquina virtual de Azure.

Si no tiene una máquina virtual de Azure, puede crear una mediante las instrucciones de los artículos de la [guía de inicio rápido de Windows](../virtual-machines/windows/quick-create-portal.md) o la [guía de inicio rápido de Linux](../virtual-machines/linux/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.
Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Habilitar el seguimiento de cambios para una máquina virtual de Azure

1. En el panel izquierdo de Azure Portal, seleccione **Máquinas virtuales**.
2. Seleccione una máquina virtual de la lista.
3. En la ventana de la máquina virtual, en **Operaciones**, seleccione **Seguimiento de cambios**. 

   ![Incorporación de máquina virtual al seguimiento de cambios](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    Se abre la ventana **Habilitar la administración de actualizaciones**.

    Se realiza la validación para determinar si el seguimiento de cambios está habilitado para esta máquina virtual. Si no lo está, aparece un banner que le ofrece la opción de habilitar la solución.

   ![Banner de configuración de la incorporación del seguimiento de cambios](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Seleccione el banner para habilitar la solución. Si no tiene los siguientes elementos, se agregarán automáticamente:

   * Área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md)
   * Cuenta de [Automation](../automation/automation-offering-get-started.md)

5. Seleccione un área de trabajo de Log Analytics para almacenar los registros de datos del seguimiento de cambios y una cuenta de Automation para realizar el seguimiento de cambios. Después, haga clic en **Habilitar**.  
    Una barra de estado le notifica que la solución se está habilitando. Este proceso puede tardar hasta 15 minutos.

## <a name="configure-change-tracking"></a>Configurar el seguimiento de cambios

Una vez que se ha habilitado el seguimiento de cambios, aparece la ventana **Seguimiento de cambios**. 

Para elegir los archivos y las claves del Registro de los que quiere realizar un seguimiento, seleccione **Editar configuración**.

   ![Editar configuración del seguimiento de cambios](./media/automation-vm-change-tracking/change-edit-settings.png)

   Se abre la ventana **Configuración del área de trabajo**. 

En la ventana **Configuración del área de trabajo**, agregue las claves del Registro de Windows, los archivos de Windows o los archivos de Linux de los que desea realizar un seguimiento, tal como se describe en las tres secciones siguientes.

### <a name="add-a-windows-registry-key"></a>Agregar una clave del Registro de Windows

1. En la pestaña **Registro de Windows**, seleccione **Agregar**.  
    Se abrirá la ventana **Agregar Registro de Windows para el seguimiento de cambios**.

   ![Agregar Registro para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-registry.png)

2. En **Habilitado**, seleccione **True**.
3. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
4. En el cuadro **Grupo**, escriba un nombre de grupo (opcional).
5. En el cuadro **Clave del Registro de Windows**, agregue el nombre de la clave del Registro de la que quiere realizar un seguimiento.
6. Seleccione **Guardar**.

### <a name="add-a-windows-file"></a>Agregar un archivo de Windows

1. En la pestaña **Archivos de Windows**, seleccione **Agregar**.  
    Se abrirá la ventana **Agregar archivo de Windows para el seguimiento de cambios**.

   ![Agregar archivo de Windows para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-win-file.png)

2. En **Habilitado**, seleccione **True**.
3. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
4. En el cuadro **Grupo**, escriba un nombre de grupo (opcional).
5. En el cuadro **Indicar ruta de acceso**, escriba la ruta de acceso completa y el nombre del archivo del que quiere realizar un seguimiento.
6. Seleccione **Guardar**.

### <a name="add-a-linux-file"></a>Agregar un archivo de Linux

1. En la pestaña **Archivos de Linux**, seleccione **Agregar**.  
    Se abrirá la ventana **Agregar archivo de Linux para el seguimiento de cambios**.

   ![Agregar archivo de Linux para el seguimiento de cambios](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. En **Habilitado**, seleccione **True**.
3. Agregue un nombre descriptivo en el campo **Nombre del elemento**.
4. En el cuadro **Grupo**, escriba un nombre de grupo (opcional).
5. En el cuadro **Indicar ruta de acceso**, escriba la ruta de acceso completa y el nombre del archivo del que quiere realizar un seguimiento.
6. En el cuadro **Tipo de ruta de acceso**, seleccione **Archivo** o **Directorio**.
7. En **Recursión**, seleccione **Activado** para realizar el seguimiento de cambios para la ruta de acceso especificada y para todos los archivos y las rutas de acceso que se encuentran bajo ella. Para realizar el seguimiento únicamente de la ruta de acceso o el archivo especificados, seleccione **Desactivar**.
8. En **Usar sudo**, para realizar un seguimiento de los archivos que requieren el comando `sudo` para el acceso, seleccione **Activado**. De lo contrario, seleccione **Desactivado**.
9. Seleccione **Guardar**.

## <a name="view-changes"></a>Ver los cambios

En la ventana **Seguimiento de cambios**, puede ver los cambios en cada una de las distintas categorías para la máquina virtual a lo largo del tiempo.

   ![Agregar la visualización del seguimiento de cambios](./media/automation-vm-change-tracking/change-view-changes.png)

Puede seleccionar las categorías y el intervalo de tiempo de los cambios que se van a visualizar. En la parte superior de la ventana, puede ver una representación gráfica de los cambios en el tiempo. En la parte inferior de la pantalla, puede ver una lista de los cambios recientes.

## <a name="view-change-tracking-log-data"></a>Visualización de los datos de registro del seguimiento de cambios

El seguimiento de cambios genera datos de registro que se envían a Log Analytics. Para buscar los registros mediante la ejecución de consultas, seleccione **Log Analytics** en la parte superior de la ventana **Seguimiento de cambios**.

   ![Log Analytics para el seguimiento de cambios](./media/automation-vm-change-tracking/change-log-analytics.png)

Para más información sobre la ejecución y la búsqueda de archivos de registro en Log Analytics, consulte [Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>pasos siguientes

* Para más información sobre el seguimiento de cambios, consulte [Seguimiento de cambios de software en su entorno con la solución de seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md).
* Para más información sobre cómo administrar las actualizaciones de las máquinas virtuales, vea [Solución Administración de actualizaciones de OMS](../operations-management-suite/oms-solution-update-management.md).
