---
title: "Creación, visualización y administración de alertas en la sección Alertas (versión preliminar) de Azure Monitor | Microsoft Docs"
description: "Use la nueva experiencia de alertas unificadas de Azure para crear, ver y administrar reglas de alertas de métricas y registros desde un solo lugar."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: e3902ec5fc27c829fa97042d15552c8c895c6408
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Creación, visualización y administración de alertas en la sección Alertas (versión preliminar) de Azure Monitor

## <a name="overview"></a>Información general
En este artículo se explica cómo configurar las alertas en la nueva interfaz de Alertas (versión preliminar) en Azure Portal. La definición de una regla de alertas consta de tres partes:
- Destino: recurso de Azure específico que se va a supervisar.
- Criterios: condición o lógica específicas que, cuando se señaliza la alerta, deben desencadenar una acción.
- Acción: llamada específica enviada a un receptor de una notificación (correo electrónico, SMS, webhook, etc.).

En Alertas (versión preliminar), se usa el término **Alertas de registro** para describir aquellas alertas cuya señal es una consulta personalizada basada en [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md). La funcionalidad de alertas de métricas llamada [Alerta de métricas de Near Real Time](monitoring-near-real-time-metric-alerts.md) en la experiencia de alertas existente se denomina **Alertas de métrica** en Alertas (versión preliminar). En *Alertas de métrica*, algunos tipos de recursos proporcionan [métricas multidimensionales](monitoring-metric-charts.md) para un recurso específico de Azure y, por tanto, las alertas de dicho recurso pueden hacerse más específicas con filtros adicionales sobre las dimensiones; tales alertas se conocen como **Alertas de métricas multidimensionales**. Alertas de Azure (versión preliminar) también ofrece una vista unificada de todas las reglas de alertas y la posibilidad de administrarlas desde un único lugar, incluida la visualización de cualquier alerta sin resolver. Obtenga más información sobre la funcionalidad en la [introducción sobre Alertas de Azure (versión preliminar)](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Alertas de Azure (versión preliminar) ofrece una experiencia nueva y mejorada para la creación de alertas en Azure. La experiencia existente de [Alertas de Azure](monitoring-overview-alerts.md) ya no se puede utilizar.
>

A continuación, se ofrecen instrucciones detalladas sobre cómo usar Alertas de Azure (versión preliminar).

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Creación de una regla de alertas en Azure Portal
1. En [Azure Portal](https://portal.azure.com/), seleccione **Supervisar** y, en la sección SUPERVISAR, elija **Alertas (versión preliminar)**.  
    ![Supervisión](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Seleccione el botón **Nueva regla de alertas** para crear una alerta en Azure.
    ![Adición de una alerta](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. La sección Crear alerta se divide en tres partes: *Definir condición de la alerta*, *Definir detalles de la alerta* y *Definir grupo de acciones*.
    ![Creación de una regla](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Defina la condición de la alerta; para ello, use primero el vínculo **Seleccionar recurso** y especifique el destino mediante la selección de un recurso. Aplique el filtro adecuado con la elección de la *suscripción* y el *tipo de recurso* apropiados y, por último, seleccione el *recurso* necesario. Como Alertas de Azure (versión preliminar) permite crear distintos tipos de alertas desde una interfaz singular; según el tipo de alerta deseada, elija el paso siguiente según el caso:
    - Para **alertas de métrica**: siga los pasos 5 a 7; a continuación, vaya directamente al paso 11.
    - Para **alertas de registro**, empiece a partir del paso 8.

5. *Alertas de métrica*: asegúrese de que el campo **Tipo de recurso** sea el servicio de plataforma o de supervisión seleccionado (que no sea *Log Analytics*); una vez elegido el **recurso** apropiado, haga clic en el botón *Listo* para volver a Crear alerta. Después, use el botón **Agregar criterios** para elegir la señal específica de la lista de opciones de señal, su servicio de supervisión y el tipo indicado, que están disponibles para el recurso seleccionado anteriormente.
    ![Selección de un recurso](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]
    > Las nuevas funcionalidades de métricas presentadas para una creación de alertas rápida solo están incluidas en tipos de señal como métricas del servicio de plataforma.
  

6. *Alertas de métrica*: una vez seleccionada la señal, se puede indicar la lógica para la creación de alertas. Como referencia, se muestran los datos de señal históricos con la opción de retocar la ventana de tiempo con **Mostrar historial**, desde las seis últimas horas hasta la última semana. Con la visualización activada, se puede seleccionar la **Lógica de alerta** de entre las opciones mostradas de Condición, Agregación y, por último, Umbral. Como vista previa de la lógica proporcionada, la condición se muestra en la visualización junto con el historial de señales, a fin de indicar cuándo debería haberse desencadenado la alerta. Por último, en la opción **Periodo** elija el tiempo durante el cual la alerta debe buscar la condición especificada y seleccione la **Frecuencia** con que debe ejecutarse la alerta.
    ![Configuración de la lógica de señal de la métrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alertas de métrica*: si la señal es una métrica, se puede filtrar la ventana de alerta con la utilización de varios puntos de datos o dimensiones para el recurso de Azure especificado. De forma similar a las alertas de métrica, se puede elegir el historial de señales especificando la duración en el menú desplegable **Mostrar historial**. Asimismo, se pueden seleccionar las dimensiones de la métrica elegida para filtrar según las series temporales necesarias; la elección de las dimensiones es opcional y, en su caso, se pueden usar hasta cinco dimensiones. Se puede seleccionar la **Lógica de alerta** de entre las opciones mostradas de Condición, Agregación y, por último, Umbral. Como vista previa de la lógica proporcionada, la condición se muestra en la visualización junto con el historial de señales, a fin de indicar cuándo debería haberse desencadenado la alerta en el pasado. Por último, en la opción **Periodo** elija el tiempo durante el cual la alerta debe buscar la condición especificada y seleccione la **Frecuencia** con que debe ejecutarse la alerta.
    ![Configuración de la lógica de señal de métricas multidimensionales](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Alertas de registro*: asegúrese de que la opción **Tipo de recurso** se corresponde con un origen de análisis como *Log Analytics*; después, una vez elegido el **recurso** apropiado, haga clic en el botón *Listo*. A continuación, use el botón **Agregar criterios** para ver la lista de opciones de señal disponibles para el recurso y en la opción **Búsqueda de registros personalizada** de la lista de señales.
   ![Selección de un recurso: búsqueda de recursos personalizada](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

9.  *Alertas de registro*: una vez seleccionada esta opción, la consulta de alertas se puede indicar en el campo **Consulta de búsqueda**; si la sintaxis de la consulta es incorrecta, en el campo aparece el error en ROJO. Si la sintaxis de consulta es correcta, como referencia, se muestran los datos históricos de la consulta indicada en formato de gráfico con la opción de retocar la ventana de tiempo desde las últimas seis horas hasta la última semana. 
   ![Configuración de la regla de alertas](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

    > [!NOTE]
    > La visualización de los datos históricos solo se muestra si los resultados de la consulta tienen detalles temporales. Si la consulta genera datos resumidos o valores de columna específicos, estos se representan en un gráfico singular. 

10.  *Alertas de registro*: con la visualización activada, se puede seleccionar la **Lógica de alerta** de entre las opciones mostradas de Condición, Agregación y, por último, Umbral. Por último, especifique en la lógica el tiempo para evaluar la condición especificada mediante la opción **Periodo**. Además, especifique la frecuencia con que debe ejecutarse la alerta seleccionando la opción adecuada en el campo **Frecuencia**.
Para las **Alertas de registro**, las alertas pueden basarse en lo siguiente:
   - *Número de registros*: se crea una alerta si el número de registros devueltos por la consulta es mayor que o menor que el valor que proporcione.
   - *Unidades métricas*: se crea una alerta si cada *valor agregado* en los resultados excede el valor de umbral proporcionado y se *agrupa por* el valor elegido. El número de infracciones de una alerta es el número de veces que se supera el umbral en el período de tiempo seleccionado. Puede especificar Infracciones totales para cualquier combinación de infracciones en el conjunto de resultados o Infracciones consecutivas para que las infracciones deban tener lugar en muestras consecutivas.

   Obtenga más información sobre las [alertas de registro y sus tipos](monitor-alerts-unified-log.md).

    > [!TIP]
    > Actualmente, en Alertas (versión preliminar), las alertas de la búsqueda de registros pueden adoptar el valor personalizado de *periodo* y *frecuencia* en minutos. Los valores pueden variar de 5 minutos a 1440 minutos, es decir, 24 horas. Por tanto, si desea que el periodo de alerta sea de tres horas, conviértalas a minutos antes de empezar, que serían 180 minutos.

11. En segundo lugar, asigne un nombre a la alerta en el campo **Nombre de la regla de alertas** junto con una **Descripción**, en la que debe proporcionar información específica sobre la alerta, y debe indicar también un valor de **Gravedad** entre las opciones proporcionadas. Estos detalles se reutilizan en todos los correos electrónicos, las notificaciones o las notificaciones push de alerta enviados por Azure Monitor. Además, el usuario puede elegir activar inmediatamente la regla de alertas al crearla cambiando la opción **Habilitar regla tras la creación** según corresponda.

Solo para las **alertas de registro**, se encuentra disponible alguna funcionalidad adicional en los detalles de la alerta:
- *Desactivar alertas*: al activar la supresión de la regla de alertas, las acciones de la regla se deshabilitan durante un período de tiempo definido después de crear una alerta. La regla se sigue ejecutando y crea registros de alerta si se cumplen los criterios. De esta forma, dispone de tiempo para corregir el problema sin ejecutar acciones duplicadas.
    ![Desactivar las alertas de registro](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Como tercer y último paso, especifique si es necesario desencadenar algún **grupo de acciones** para la regla de alertas si se cumple la condición de alerta. Puede elegir cualquier grupo de acciones existente con alerta o crear uno. Según el grupo de acciones seleccionado, cuando se desencadena la alerta, Azure: envía correos electrónicos, envía SMS, llama a webhooks, la soluciona con runbooks de Azure, envía notificaciones push a la herramienta de ITSM, etc. Obtenga más información sobre los [grupos de acciones](monitoring-action-groups.md).

Para las **alertas de registro**, se encuentra disponible alguna funcionalidad adicional para reemplazar las acciones predeterminadas:
- *Notificación por correo electrónico*: reemplaza el asunto del correo electrónico enviado mediante el grupo de acciones. No se puede modificar el cuerpo del mensaje de correo.
- *Incluir carga de JSON personalizada*: reemplaza la carga de JSON del webhook que usan los grupos de acciones y, en su lugar, reemplaza la carga predeterminada por una carga personalizada. ![Reemplazo de acciones para alertas de registro](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Si todos los campos son válidos y tienen una marca verde, se puede hacer clic en el botón **Crear regla de alertas** y, a continuación, se crea la alerta en la pestaña Alertas (versión preliminar) de Azure Monitor. Todas las alertas pueden verse en el panel de Alertas (versión preliminar).
    ![Creación de reglas](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="view-your-alerts-in-azure-portal"></a>Visualización de las alertas en Azure Portal
1. En [Azure Portal](https://portal.azure.com/), seleccione **Supervisar** y, en la sección SUPERVISAR, elija **Alertas (versión preliminar)**.  

2. Se abre el **panel de Alertas (versión preliminar)**, donde se unifican y muestran todas las alertas de Azure en un ![panel de alertas](./media/monitoring-overview-unified/alerts-preview-overview.png) singular.
1. Desde la parte superior izquierda a la derecha, en el panel aparecen de un vistazo los siguientes elementos, en los que se puede hacer clic para ver una lista detallada:
    - *Alertas desencadenadas*: el número de alertas que cumplen la lógica actualmente y que tienen el estado de desencadenadas.
    - *Número total de reglas de alertas*: el número de reglas de alertas creadas y, en el subtexto, el número de las que están habilitadas actualmente.
2. Se muestra una lista de todas las alertas desencadenadas en las que el usuario puede hacer clic para ver los detalles.
3. Para facilitar la búsqueda de alertas específicas, se pueden usar las opciones de las listas desplegables de la parte superior para aplicar filtros específicos según la *suscripción, el grupo de recursos o el recurso*. Además, para cualquier alerta sin resolver, se puede usar la opción *Filtrar alerta* para buscar las palabras clave proporcionadas o alertas con coincidencias específicas con *nombre, criterios de alerta, grupo de recursos y recurso de destino*.

## <a name="managing-your-alerts-in-azure-portal"></a>Administración de las alertas en Azure Portal
1. En [Azure Portal](https://portal.azure.com/), seleccione **Supervisar** y, en la sección SUPERVISAR, elija **Alertas (versión preliminar)**.  
2. Seleccione el botón **Administrar reglas** situado en la barra superior para navegar hasta la sección de administración de reglas, donde se enumeran todas las reglas de alerta creadas, incluidas las alertas que se han deshabilitado.
3. Para buscar reglas de alertas específicas, se pueden usar los filtros desplegables de la parte superior, que permiten acotar la búsqueda de reglas de alertas específicas según los criterios específicos de *Suscripción, Grupos de recursos o Recurso*. Otra alternativa es usar el panel de búsqueda que está encima de la lista de reglas de alertas donde se lee *Filtrar alertas*, donde puede proporcionar palabras claves que coincidan con los valores *Nombre de alerta, Condición y Recurso de destino*, a fin de mostrar solo las reglas de coincidencia. 
   ![Reglas de administración de alertas](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. Para ver o modificar una regla de alertas específica, haga clic en su nombre, que debería aparecer como un vínculo en el que se puede hacer clic.
5. La alerta definida se muestra con una estructura de tres fases: 1) Condición de la alerta, 2) Detalles de la alerta y 3) Grupo de acciones. Se puede hacer clic en los **Criterios de destino** para modificar la lógica de la alerta o se pueden agregar criterios nuevos después de usar el icono de la papelera para eliminar la lógica anterior. De forma similar, en la sección de detalles de la alerta, se pueden modificar los campos **Descripción** y **Gravedad**. Además, se puede cambiar el grupo de acciones o se puede crear uno que se vincule con la alerta mediante el botón **Nuevo grupo de acciones**. ![Modificación de reglas de alertas](./media/monitor-alerts-unified/AlertsPreviewEdit.png)
6. Mediante el panel superior, los cambios de la alerta pueden ser reflexivos, como: *Guardar* para confirmar los cambios realizados en la alerta, *Descartar* para retroceder sin confirmar los cambios realizados en la alerta y *Deshabilitar* para desactivar la alerta, pero, después, la alerta ya no se ejecuta más ni desencadena ninguna acción. Por último, *Eliminar* para eliminar permanentemente toda la regla de alertas de Azure.

## <a name="next-steps"></a>pasos siguientes

* Más información sobre las nuevas [alertas de métrica casi en tiempo real (versión preliminar)](monitoring-near-real-time-metric-alerts.md).
* Obtenga [información general sobre la colección de métricas](insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
* Obtenga información sobre las [alertas de registro en Alertas de Azure (versión preliminar)](monitor-alerts-unified-log.md).
