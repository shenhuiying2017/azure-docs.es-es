---
title: Solución de problemas de cambios en una máquina virtual de Azure | Microsoft Docs
description: Use Change Tracking para solucionar problemas de cambios en una máquina virtual de Azure.
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: cambio, seguimiento, automatización
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 62d34f82749900e161bebdb7a1a8d470b2e85bbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>Solución de problemas en el entorno

En este tutorial aprenderá a solucionar problemas de cambios en una máquina virtual de Azure. Si habilita Change Tracking, podrá hacer un seguimiento de los cambios de software, archivos, demonios de Linux, servicios de Windows y claves del Registro de Windows en las máquinas.
Identificar esos cambios de configuración puede ayudarle a localizar problemas operativos de su entorno.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporar una máquina virtual para Change Tracking e Inventario
> * Buscar servicios detenidos en los registros de cambios
> * Configurar el seguimiento de cambios
> * Habilitar la conexión del registro de actividad
> * Desencadenar un evento
> * Ver los cambios

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* Una suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario

En primer lugar, debe habilitar Change Tracking e Inventario para la máquina virtual en este tutorial. Si habilitó previamente otra solución de automatización para una máquina virtual, este paso no es necesario.

1. En el menú de la izquierda, seleccione **Máquinas virtuales** y seleccione una máquina virtual de la lista.
1. En el menú de la izquierda, en la sección **OPERACIONES**, haga clic en **Inventario**. Se abre la página **Change Tracking**.

![Habilitar cambios](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) Se abre la pantalla **Change Tracking**. Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

Un área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Inventario.
El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.

Durante la incorporación la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) e Hybrid Worker.
Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el software instalado.

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador.
Después de habilitar la solución, la información sobre el software instalado y los cambios en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

## <a name="using-change-tracking-in-log-analytics"></a>Uso de Change Tracking en Log Analytics

El seguimiento de cambios genera datos de registro que se envían a Log Analytics. Para buscar los registros mediante la ejecución de consultas, seleccione **Log Analytics** en la parte superior de la ventana **Seguimiento de cambios**.
Los datos de Change Tracking se almacenan con el tipo **ConfigurationChange**. La siguiente consulta de Log Analytics de ejemplo devuelve todos los servicios de Windows que se hayan detenido.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para obtener más información sobre la ejecución y la búsqueda de archivos de registro en Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

## <a name="configure-change-tracking"></a>Configuración de Change Tracking

Change Tracking le ofrece la posibilidad de realizar un seguimiento de los cambios en la configuración de su máquina virtual. En los pasos siguientes se muestra cómo configurar el seguimiento de las claves del Registro y los archivos.
 
Para elegir de qué archivos y claves del Registro se realizará la recopilación y el seguimiento, seleccione **Editar configuración** en la parte superior de la página **Change Tracking**.

> [!NOTE]
> Change Tracking e Inventario usan la misma configuración de recopilación y se configuran en un nivel de área de trabajo.

En la ventana **Configuración del área de trabajo**, agregue las claves del Registro de Windows, los archivos de Windows o los archivos de Linux de los que desea realizar un seguimiento, tal como se describe en las tres secciones siguientes.

### <a name="add-a-windows-registry-key"></a>Agregar una clave del Registro de Windows

1. En la pestaña **Registro de Windows**, seleccione **Agregar**.
    Se abrirá la ventana **Agregar Registro de Windows para el seguimiento de cambios**.

3. En **Agregar Registro de Windows para el seguimiento de cambios**, introduzca la información de la clave cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Clave de Registro de Windows   | La ruta de acceso para buscar el archivo, por ejemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Agregar un archivo de Windows

1. En la pestaña **Archivos de Windows**, seleccione **Agregar**. Se abrirá la ventana **Agregar archivo de Windows para el seguimiento de cambios**.

1. En **Agregar archivo de Windows para el seguimiento de cambios**, introduzca la información del archivo o directorio cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Indicar ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: "c:\temp\myfile.txt"       |

### <a name="add-a-linux-file"></a>Agregar un archivo de Linux

1. En la pestaña **Archivos de Linux**, seleccione **Agregar**. Se abrirá la ventana **Agregar archivo de Linux para el seguimiento de cambios**.

1. En **Agregar archivo de Linux para el seguimiento de cambios**, introduzca la información del archivo o directorio cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos        |
|Indicar ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: "/etc/*.conf"       |
|Tipo de ruta de acceso     | Tipo de elemento cuyo seguimiento se va a realizar; posibles valores son Archivo y Directorio        |
|Recursión     | Determina si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar.        |
|Usar sudo     | Esta configuración determina si se va a utilizar sudo al buscar el elemento.         |
|Vínculos     | Esta configuración determina cómo se tratan los vínculos simbólicos cuando se recorren directorios.<br> **Omitir**: ignora los vínculos simbólicos y no incluye los archivos y directorios de referencia.<br>**Seguir**: sigue los vínculos simbólicos durante la recursión y también incluye los archivos y directorios de referencia.<br>**Administrar**: sigue los vínculos simbólicos y permite modificar el tratamiento del contenido devuelto.      |

   > [!NOTE]   
   > La opción de administración de vínculos no se recomienda. No se admite la recuperación de contenido de los archivos.

## <a name="enable-activity-log-connection"></a>Habilitar la conexión del registro de actividad

Desde la página **Change Tracking** en la máquina virtual, seleccione **Administrar conexión de registro de actividad**. Esta tarea abrirá la página **Registro de actividad de Azure**. Seleccione **Conectar** para conectar Change Tracking al registro de actividad de Azure en la máquina virtual.

Con esta opción habilitada, navegue hasta la página **Información general** en la máquina virtual y seleccione **Detener** para detenerla. Cuando se le solicite, seleccione **Sí** para detener la máquina virtual. Cuando esté desasignada, seleccione **Iniciar** para reiniciar la máquina virtual.

Si se detiene y se inicia una máquina virtual, se registra un evento en el registro de actividad. Vuelva a la página **Change Tracking**. Seleccione la pestaña **Eventos** en la parte inferior de la página. Después de un tiempo, los eventos se mostrarán en el gráfico y la tabla. Al igual que en el paso anterior, cada evento puede seleccionarse para ver información detallada.

![Visualización de los detalles de cambio en el portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Ver los cambios

Una vez habilitadas la solución Change Tracking e Inventario, puede ver los resultados en la página **Change tracking**.

Desde dentro de la máquina virtual, seleccione **Change Tracking** en **OPERACIONES**.

![Captura de pantalla que muestra la lista de cambios de la máquina virtual](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

El gráfico muestra los cambios que se han producido con el tiempo.
Después de agregar una conexión del registro de actividad, el gráfico de líneas en la parte superior muestra los eventos del registro de actividad de Azure.
Cada fila de los gráficos de barras representa un tipo de cambio diferente del cual se puede realizar un seguimiento.
Estos tipos son demonios de Linux, archivos, claves del Registro de Windows, software y servicios de Windows.
En la pestaña de cambios se muestran los detalles de los cambios mostrados en la visualización en orden descendente según la hora en la cual se produjo el cambio (los más recientes se muestran primero).
En la tabla de la pestaña **Eventos** se muestran los eventos del registro de actividad conectados y sus detalles correspondientes, con el más reciente primero.

En los resultados puede ver que se produjeron varios cambios en el sistema, incluidos cambios en el software y los servicios. Puede usar los filtros en la parte superior de la página para filtrar los resultados por **Tipo de cambio** o por el intervalo de tiempo.

Seleccione un cambio de tipo **WindowsServices**. Se abrirá la ventana **Detalles del cambio**. En la ventana de detalles del cambio se muestran los detalles, así como los valores antes y después del cambio. El servicio de protección de software se detuvo en esta instancia.

![Visualización de los detalles de cambio en el portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Incorporar una máquina virtual para Change Tracking e Inventario
> * Buscar servicios detenidos en los registros de cambios
> * Configurar el seguimiento de cambios
> * Habilitar la conexión del registro de actividad
> * Desencadenar un evento
> * Ver los cambios

Continúe hacia la introducción sobre la solución Change Tracking e Inventario para obtener más información.

> [!div class="nextstepaction"]
> [Solución de Inventario y administración de cambios](automation-change-tracking.md)
