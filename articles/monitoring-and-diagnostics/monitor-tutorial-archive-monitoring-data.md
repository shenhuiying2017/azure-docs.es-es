---
title: "Archivado de datos de supervisión de Azure | Microsoft Docs"
description: "Archivar datos de registros y de métricas que se producen dentro de Azure para una cuenta de almacenamiento."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: a3ab4713861d4d9681ad2ac5f084255fc29462ce
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="archive-azure-monitoring-data"></a>Archivado de datos de supervisión de Azure

Varias capas de su entorno de Azure generan datos de registros y de métricas que pueden archivarse en una cuenta de Azure Storage. Puede que desee hacer esto para conservar un historial de los datos de supervisión a lo largo del tiempo en un almacén económico y que no admite búsquedas, una vez que haya transcurrido el período de retención en Log Analytics o Azure Monitor. En este tutorial, se recorre el proceso de configuración del entorno de Azure para archivar datos en una cuenta de almacenamiento.

> [!div class="checklist"]
> * Creación de una cuenta de almacenamiento para conservar los datos de supervisión
> * Enrutamiento de los registros de suscripción a la cuenta de almacenamiento 
> * Enrutamiento de los datos de recursos a la cuenta de almacenamiento 
> * Enrutamiento de los datos de máquinas virtuales (SO invitado) a la cuenta de almacenamiento 
> * Visualización de los datos de supervisión en la cuenta de almacenamiento 
> * Limpiar los recursos 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

En primer lugar, debe configurar una cuenta de almacenamiento en la que se archivarán los datos de supervisión. Para ello, [siga estos pasos](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Enrutamiento de registros de suscripción a la cuenta de almacenamiento

Ahora está listo para empezar a configurar el entorno de Azure para enrutar los datos de supervisión para una cuenta de almacenamiento. En primer lugar, configure los datos en el nivel de suscripción (contenidos en el registro de actividad de Azure) que se enrutarán a la cuenta de almacenamiento. El [**Registro de actividad de Azure**](monitoring-overview-activity-logs.md) proporciona un historial de eventos de nivel de suscripción de Azure. Puede examinarlo en Azure Portal para determinar *quién* creó, actualizó o eliminó *qué* recursos y *cuándo* lo hicieron.

1. Haga clic en el botón **Monitor** que se encuentra en la lista de navegación de la izquierda y luego en **Registro de actividad**.

   ![Sección Registro de actividades](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. En la sección Registro de actividad que se muestra, haga clic en el botón **Exportar**.

3. En la sección **Exportar registros de actividad** que aparece, marque la casilla para **Exportar en una cuenta de almacenamiento** y haga clic en **Seleccionar una cuenta de almacenamiento**.

   ![Exportación del Registro de actividad](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. En la sección que aparece, use la lista desplegable **Cuenta de almacenamiento** para seleccionar el nombre de la cuenta de almacenamiento que creó en el paso **Crear una cuenta de almacenamiento** anterior y luego haga clic en **Aceptar**.

   ![Selección de una cuenta de almacenamiento](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Establezca el control deslizante **Retención (días)** en 30. Este control deslizante establece un número de días para retener los datos de supervisión en la cuenta de almacenamiento. Azure Monitor elimina automáticamente los datos anteriores al número de días especificado. Con una retención de cero días, los datos se almacenan indefinidamente.

6. Haga clic en **Guardar** y cierre esta sección.

Los datos de supervisión de la suscripción ahora fluyen a la cuenta de almacenamiento.

## <a name="route-resource-data-to-the-storage-account"></a>Enrutamiento de datos de recursos a la cuenta de almacenamiento

Ahora configure los datos de nivel de recurso (métricas de recursos y registros de diagnóstico) que se enrutarán a la cuenta de almacenamiento mediante las opciones de **configuración de diagnóstico de recurso**.

1. Haga clic en el botón **Monitor** que se encuentra en la lista de navegación de la izquierda y luego en **Configuración de diagnóstico**. Aquí verá una lista de todos los recursos de la suscripción que generan datos de supervisión a través de Azure Monitor. Si no dispone de ningún recurso en esta lista, puede [crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) antes de continuar para tener un recurso en el que pueda definir una configuración de diagnóstico.

2. Haga clic en un recurso de la lista y luego en **Activar diagnóstico**.
   
   ![Activación del diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Si ya existe una configuración definida, verá en su lugar la configuración existente y un botón para **Agregar configuración de diagnóstico**. Haga clic en este botón.

   Una configuración de diagnóstico de recurso es una definición de *qué* datos de supervisión deberían enrutarse desde un recurso en particular y *adónde* deberían ir esos datos de supervisión.

3. En la sección que aparece, otorgue un **nombre** a la configuración y marque la casilla **Archivar en una cuenta de almacenamiento**.

   ![Sección Configuración de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Haga clic en el botón **Configurar** debajo de **Archivar en una cuenta de almacenamiento** y seleccione la cuenta de almacenamiento que creó en la sección anterior. Haga clic en **OK**.

   ![Cuenta de almacenamiento de configuración de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Marque todas las casillas debajo de **Registro** y **Métrica**. Según el tipo de recurso, puede que solo tenga una de estas opciones. Estas casillas controlan qué categorías de datos de registros y de métricas disponibles para ese tipo de recurso se envían al destino que ha seleccionado, en este caso, una cuenta de almacenamiento.

   ![Categorías de configuración de diagnóstico](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Establezca el control deslizante **Retención (días)** en 30. Este control deslizante establece un número de días para retener los datos de supervisión en la cuenta de almacenamiento. Azure Monitor elimina automáticamente los datos anteriores al número de días especificado. Con una retención de cero días, los datos se almacenan indefinidamente.

7. Haga clic en **Save**(Guardar).

Los datos de supervisión del recurso ahora fluyen a la cuenta de almacenamiento.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Enrutamiento de los datos de máquinas virtuales (SO invitado) a la cuenta de almacenamiento

1. [Cree una máquina virtual](../virtual-machines/windows/quick-create-portal.md) si aún no tiene ninguna en su suscripción.

2. En la lista de navegación de la izquierda en el portal, haga clic en **Máquinas virtuales**.

3. En la lista de máquinas virtuales que aparece, haga clic en la máquina virtual que creó.

4. En la sección que aparece, haga clic en **Configuración de diagnóstico** en el panel de navegación izquierdo. Esta sección le permite configurar la extensión de supervisión lista para usar desde Azure Monitor en la máquina virtual, y enrutar los datos generados por Windows o Linux a una cuenta de almacenamiento.

   ![Navegación a la configuración de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Haga clic en **Habilitar supervisión a nivel de invitado** en la sección que aparece.

   ![Habilitación de la configuración de diagnóstico](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. Una vez que la configuración de diagnóstico se haya guardado correctamente, la pestaña **Información general** mostrará una lista de los datos que se recopilaron y dónde se almacenaron. Haga clic en la sección **Contadores de rendimiento** para revisar el conjunto de contadores de rendimiento de Windows que se recopilan.

   ![Configuración de contadores de rendimiento](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Haga clic en la pestaña **Registros** y marque las casillas para los registros de nivel de **Información** en los registros Aplicación y Sistema.

   ![Configuración de registros](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Haga clic en la pestaña **Agente** y, en **Cuenta de almacenamiento**, haga clic en el nombre de la cuenta de almacenamiento que se muestra.

   ![Actualización de la cuenta de almacenamiento](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. En la sección que aparece, seleccione la cuenta de almacenamiento que creó en el paso **Crear una cuenta de almacenamiento** anterior.

10. Haga clic en **Save**(Guardar).

Los datos de supervisión de las máquinas virtuales ahora fluyen a la cuenta de almacenamiento.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Visualización de los datos de supervisión en la cuenta de almacenamiento

Si ha seguido los pasos anteriores, los datos han comenzado a fluir a la cuenta de almacenamiento.

1. Para algunos tipos de datos (por ejemplo, el Registro de actividad), debe haber alguna actividad que genere un evento en la cuenta de almacenamiento. Para generar actividad en el Registro de actividad, siga [estas instrucciones](./monitor-quick-audit-notify-action-in-subscription.md). Puede que tenga que esperar hasta cinco minutos antes de que el evento aparezca en la cuenta de almacenamiento.

2. En el portal, vaya a la sección **Cuentas de almacenamiento** desde la barra de navegación a la izquierda.

3. Identifique la cuenta de almacenamiento que creó en la sección anterior y haga clic en ella.

4. Haga clic en **Blobs**, luego en el contenedor con la etiqueta **insights-operational-logs** y, por último, en el contenedor con la etiqueta **name=default**. Este es el contenedor que tiene el Registro de actividad. Los datos de supervisión se desglosan en contenedores por Id. de recurso (solo el identificador de suscripción para el Registro de actividad), después por fecha y hora. El formato completo de estos blobs es:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={año con cuatro dígitos}/m={mes con dos dígitos}/d={día con dos dígitos}/h={hora en formato de 24 horas con dos dígitos}/m=00/PT1H.json

5. Haga clic en los contenedores de ld. de recurso, fecha y hora para navegar hasta el archivo PT1H.json. Haga clic en el archivo PT1H.json y haga clic en **Descargar**. Cada blob PT1H.json contiene un blob JSON de eventos que se produjeron en la hora especificada en la dirección URL del blob (por ejemplo h=12). Durante la hora en cuestión, los eventos se anexan al archivo PT1H.json a medida que se producen. El valor de los minutos (m = 00) siempre es 00, ya que los eventos del registro se dividen en blobs individuales por hora.

   Ahora puede ver el evento JSON que se almacenó en la cuenta de almacenamiento. Para los registros de diagnóstico de recursos, el formato para los blobs es:

   insights-logs-{nombre de categoría de registro}/resourceId=/{id. de recurso}/y={año con cuatro dígitos}/m={mes con dos dígitos}/d={día con dos dígitos}/h={hora en formato de 24 horas con dos dígitos}/m=00/PT1H.json

6. Los datos de supervisión del SO invitado se almacenan en tablas. Vuelva al inicio de la cuenta de almacenamiento y haga clic en **Tablas**. Hay tablas para métricas, contadores de rendimiento y registros de eventos.

Ya ha configurado correctamente que los datos de supervisión se archiven en una cuenta de almacenamiento.

## <a name="clean-up-resources"></a>Limpieza de recursos

1. Vuelva a la sección **Exportar registros de actividad** del paso **Enrutamiento de registros de suscripción a la cuenta de almacenamiento** anterior y haga clic en **Restablecer**.

2. Navegue a la sección **Configuración de diagnóstico**, haga clic en el recurso en el que creó una configuración de diagnóstico en el paso **Enrutamiento de datos de recursos a la cuenta de almacenamiento** anterior, a continuación, busque la configuración que creó, haga clic en el botón **Editar configuración** y haga clic en **Eliminar**.

3. Navegue a la sección **Configuración de diagnóstico** en la máquina virtual que configuró en el paso **Enrutamiento de los datos de máquinas virtuales (SO invitado) a la cuenta de almacenamiento** anterior y, en la pestaña  **Agente**, haga clic en **Quitar** (bajo la sección **Quitar agente de Azure Diagnostics**).

4. Vaya a la cuenta de almacenamiento que creó en el paso **Crear una cuenta de almacenamiento** anterior y haga clic en **Eliminar cuenta de almacenamiento**. Escriba el nombre de la cuenta de almacenamiento y después haga clic en **Eliminar**.

5. Si ha creado una máquina virtual o aplicación lógica para los pasos anteriores, elimínelas también.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha visto cómo configurar los datos de supervisión desde el entorno de Azure (suscripción, recursos y SO invitado) para archivarlos en una cuenta de almacenamiento. 


> [!div class="checklist"]
> * Creación de una cuenta de almacenamiento para conservar los datos de supervisión
> * Enrutamiento de los registros de suscripción a la cuenta de almacenamiento 
> * Enrutamiento de los datos de recursos a la cuenta de almacenamiento 
> * Enrutamiento de los datos de máquinas virtuales (SO invitado) a la cuenta de almacenamiento 
> * Visualización de los datos de supervisión en la cuenta de almacenamiento 
> * Limpiar los recursos 

Para obtener el máximo partido de los datos y derivar información adicional, envíe también los datos en Log Analytics.

> [!div class="nextstepaction"]
> [Introducción a Log Analytics](../log-analytics/log-analytics-get-started.md)
