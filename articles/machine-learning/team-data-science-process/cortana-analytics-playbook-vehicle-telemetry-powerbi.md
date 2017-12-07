---
title: "Panel de Power BI de hábitos de mantenimiento y conducción de vehículos (Azure) | Microsoft Docs"
description: "Aproveche las posibilidades de Cortana Intelligence para obtener información en tiempo real y predictiva del estado de los vehículos y los hábitos de conducción."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 626987ec0648f9e770499b4a48bc4ca2d175d2b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instrucciones de configuración del panel de Power BI para la plantilla de la solución Vehicle Telemetry Analytics
Este menú está relacionado con los capítulos de este playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

La solución Vehicle Telemetry Analytics demuestra cómo los concesionarios de vehículos, los fabricantes de automóviles y las compañías de seguros pueden usar las funcionalidades de Cortana Intelligence. Así, pueden obtener información predictiva y en tiempo real sobre los hábitos de mantenimiento y conducción de vehículos con el objetivo de mejorar la experiencia del cliente, la investigación y el desarrollo, así como las campañas de marketing. Estas instrucciones paso a paso muestran cómo puede configurar los informes y el panel de Power BI después de implementar la solución en su suscripción. 

## <a name="prerequisites"></a>Requisitos previos
* Implemente la solución [Vehicle Telemetry Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90). 
* [Instale Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Obtenga una [suscripción a Azure](https://azure.microsoft.com/pricing/free-trial/). Si no tiene una suscripción a Azure, comience con una suscripción gratuita.
* Abra una cuenta de Power BI.

## <a name="cortana-intelligence-suite-components"></a>Componentes de Cortana Intelligence Suite
Como parte de la plantilla de la solución Vehicle Telemetry Analytics, se implementan los siguientes servicios de Cortana Intelligence en la suscripción:

* **Azure Event Hubs** introduce millones de eventos de telemetría del vehículo en Azure.
* **Azure Stream Analytics** proporciona información en tiempo real sobre el mantenimiento del vehículo y conserva esos datos en almacenamiento a largo plazo para un análisis por lotes más completo.
* **Azure Machine Learning** detecta anomalías en tiempo real y usa el procesamiento por lotes para proporcionar información detallada de predicción.
* **Azure HDInsight** transforma los datos a escala.
* **Azure Data Factory** controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes.

**Power BI** ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo. 

Esta solución usa dos orígenes de datos diferentes:

* Conjuntos de datos de diagnóstico y señales de vehículos simulados
* Catálogo de vehículos

Se incluye un simulador telemático del vehículo como parte de esta solución. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y los patrones de conducción en un momento dado en el tiempo. 

El catálogo de vehículos es un conjunto de datos de referencia que asigna números VIN a los modelos.

## <a name="power-bi-dashboard-preparation"></a>Preparación del panel de Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Configuración del panel de Power BI en tiempo real

#### <a name="start-the-real-time-dashboard-application"></a>Inicio de la aplicación de panel en tiempo real
Una vez finalizada la implementación, siga las instrucciones de funcionamiento manual.

1. Descargue la aplicación de panel en tiempo real RealtimeDashboardApp.zip y descomprímala.

2.  En la carpeta descomprimida, abra el archivo de configuración de aplicación RealtimeDashboardApp.exe.config. Reemplace appSettings en las conexiones de los servicios Event Hubs, Azure Blob Storage y Azure Machine Learning por los valores de las instrucciones de funcionamiento manual. Guarde los cambios.

3. Ejecute la aplicación RealtimeDashboardApp.exe. En la ventana de inicio de sesión, escriba sus credenciales válidas de Power BI. 

   ![Ventana de inicio de sesión de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Seleccione **Aceptar**. La aplicación comienza a ejecutarse.

   ![Permisos del panel de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Inicie sesión en el sitio web de Power BI y cree un panel en tiempo real.

Ahora está listo para configurar el panel de Power BI.  

### <a name="configure-power-bi-reports"></a>Configuración de informes de Power BI
Los informes en tiempo real y el panel tardan aproximadamente entre 30 y 45 minutos en completarse. 

1. Vaya a la página web de [Power BI](http://powerbi.com) e inicie sesión.

    ![Página de inicio de sesión de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Se genera un nuevo conjunto de datos en Power BI. Seleccione el conjunto de datos **ConnectedCarsRealtime**.

    ![Conjunto de datos ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Para guardar el informe en blanco, presione CTRL+S.

    ![Informe en blanco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Escriba el nombre del informe **Análisis de telemetría del vehículo en tiempo real - Informes**.

    ![Nombre del informe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Informes en tiempo real
Hay tres informes en tiempo real en esta solución:

* Vehículos en funcionamiento
* Vehículos que requieren mantenimiento
* Estadísticas de mantenimiento de vehículos

Puede configurar los tres informes en tiempo real, o puede detenerse después de cualquier fase. A continuación, puede continuar con la siguiente sección sobre cómo configurar informes por lotes. Se recomienda crear los tres informes para visualizar la información completa de la ruta en tiempo real de la solución.  

### <a name="vehicles-in-operation-report"></a>Informe de vehículos en funcionamiento
1. Haga doble clic en **Página 1** y cambie el nombre de la página por **Vehículos en funcionamiento**.

    ![Vehículos en funcionamiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. En la pestaña **Campos**, seleccione **vin**. En la pestaña **Visualizaciones**, seleccione la visualización **Tarjeta**.  

    La visualización **Tarjeta** se crea como se muestra en la siguiente ilustración:

    ![Selección de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Haga clic en el área en blanco para agregar una nueva visualización:  

4. En la pestaña **Campos**, seleccione **city** y **vin**. En la pestaña **Visualizaciones**, seleccione la visualización **Mapa**. Arrastre **vin** al área **Valores**. Arrastre **city** al área **Leyenda**. 

    ![Visualización de tarjeta](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. En la pestaña **Visualizaciones**, seleccione la sección **Formato**. Seleccione **Título** y cambie el **texto** a **Vehículos en funcionamiento por ciudad**.

    ![Vehículos en funcionamiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    La visualización final es parecida a la del ejemplo siguiente:

    ![Visualización final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Haga clic en el área en blanco para agregar una nueva visualización:  

7. En la pestaña **Campos**, seleccione **city** y **vin**. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de columnas agrupadas**. Arrastre **city** al área **Eje**. Arrastre **vin** al área **Valor**.

8. Ordene el gráfico por **Recuento de vin**.

    ![Recuento de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Cambie el **título** del gráfico por **Vehículos en funcionamiento por ciudad**. 

10. Seleccione la sección **Formato** y, a continuación, seleccione **Colores de datos**. Cambie **Mostrar todo** a **Activado**.

    ![Colores de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Cambie el color de una ciudad mediante la selección del símbolo de color.

    ![Cambio de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Haga clic en el área en blanco para agregar una nueva visualización:  

13. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de columnas agrupadas**. En la pestaña **Campos**, arrastre **city** al área **Eje**. Arrastre **Model** al área **Leyenda**. Arrastre **vin** al área **Valor**.

    ![Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    El gráfico es similar a la siguiente imagen:

    ![Representación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Reorganice todas las visualizaciones para que la página tenga un aspecto similar al ejemplo siguiente:

    ![Panel con visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Ha configurado correctamente el informe en tiempo real de "Vehículos en funcionamiento". Puede crear el siguiente informe en tiempo real o detenerse aquí y configurar el panel. 

### <a name="vehicles-requiring-maintenance-report"></a>Informe de vehículos que requieren mantenimiento

1. Seleccione ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar un nuevo informe. Cambie el nombre por **Vehículos que requieren mantenimiento**.

    ![Vehículos que requieren mantenimiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. En la pestaña **Campos**, seleccione **VIN**. En la pestaña **Visualizaciones**, seleccione la visualización **Tarjeta**.

    ![Visualización de tarjeta de VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    El conjunto de datos contiene un campo denominado **MaintenanceLabel**. Este campo puede tener un valor de "0" o "1". El valor se establece mediante el modelo de aprendizaje automático que se proporciona como parte de la solución. Se integra con la ruta en tiempo real. El valor "1" indica que un vehículo requiere mantenimiento. 

3. Para agregar un **filtro de nivel de página** para mostrar los datos de los vehículos que requieren mantenimiento, siga estos pasos: 

   a. Arrastre el campo **MaintenanceLabel** a **Filtros de nivel de página**.
  
      ![Filtros de nivel de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. En la parte inferior de **Filtros de nivel de página de MaintenanceLabel**, seleccione **Filtrado básico**.

      ![Filtrado básico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Establezca el valor de filtro en **1**.

      ![Valor de filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Haga clic en el área en blanco para agregar una nueva visualización:  

5. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de columnas agrupadas**. 

    ![Tarjeta de VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. En la pestaña **Campos**, arrastre **Model** al área **Eje**. Arrastre **vin** al área **Valor**. Después, ordene la visualización por **Recuento de vin**. Cambie el **título** del gráfico a **Vehículos que necesitan mantenimiento por modelo**. 

7. En la sección **Campos** ![Campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de la pestaña **Visualizaciones**, arrastre **vin** a **Saturación de color**.

    ![Saturación de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. En la sección **Formato**, cambie los **colores de datos** de la visualización: 

    a. Cambie el color **mínimo** por **F2C812**.

    b. Cambie el color **máximo** por **FF6300**.

    ![Nuevos colores de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Los nuevos colores de visualización se parecen al ejemplo siguiente:

    ![Nuevos colores de visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Haga clic en el área en blanco para agregar una nueva visualización:  

10. En la pestaña **Visualizaciones**, seleccione **Gráfico de columnas agrupadas**. Arrastre **vin** al área **Valor**. Arrastre **city** al área **Eje**. Ordene el gráfico por **Recuento de vin**. Cambie el **título** del gráfico a **Vehículos que necesitan mantenimiento por ciudad**.

    ![Vehículos que requieren mantenimiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Haga clic en el área en blanco para agregar una nueva visualización:  

12. En la pestaña **Visualizaciones**, seleccione la visualización **Tarjeta de varias filas**. Arrastre **Model** y **vin** al área **Campos**.

    ![Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Reorganice todas las visualizaciones para que el informe final sea similar al ejemplo siguiente: 

    ![Reorganización del informe final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Ha configurado correctamente el informe en tiempo real "Vehículos que requieren mantenimiento". Puede crear el siguiente informe en tiempo real o detenerse aquí y configurar el panel. 

### <a name="vehicle-health-statistics-report"></a>Informe de estadísticas de mantenimiento de vehículos

1. Seleccione ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar un nuevo informe. Cambie el nombre por **Estadísticas de mantenimiento de vehículos**. 

2. En la pestaña **Visualizaciones**, seleccione la visualización **Indicador**. Arrastre **speed** a las áreas **Valor**, **Valor mínimo** y **Valor máximo**.

   ![Estadísticas de estado de los vehículos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. En el área **Valor**, cambie la agregación predeterminada de **speed** a **Media**.

4. En el área **Valor mínimo**, cambie la agregación predeterminada de **speed** a **Mínimo**.

5. En el área **Valor máximo**, cambie la agregación predeterminada de **speed** a **Máximo**.

   ![Valores de velocidad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Cambie el nombre del **título del indicador** a **Velocidad media**.

   ![Indicador](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Haga clic en el área en blanco para agregar una nueva visualización:  

    Del mismo modo, agregue un **indicador** para **aceite medio del motor**, **combustible medio** y **temperatura media del motor**.  

8. Cambie la agregación predeterminada de los campos de cada indicador como hizo en los pasos anteriores con el indicador de **Velocidad media**.

    ![Indicadores adicionales](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Haga clic en el área en blanco para agregar una nueva visualización:

10. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de columnas agrupadas y de líneas**. Arrastre **city** a **Eje compartido**. Arrastre **tirepressure**, **engineoil** y **speed** al área **Valores de columna**. Cambie su tipo de agregación por **Media**. 

11. Arrastre **engineTemperature** al área **Valores de líneas**. Cambie el tipo de agregación a **Media**. 

    ![Valores de líneas y columnas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Cambie el **título** del gráfico a **Valores medios de velocidad, presión de los neumáticos, aceite del motor y temperatura del motor**.  

    ![Título del gráfico de columnas agrupadas y de líneas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Haga clic en el área en blanco para agregar una nueva visualización:

14. En la pestaña **Visualizaciones**, seleccione la visualización **Treemap**. Arrastre **Model** al área **Grupo**. Arrastre **MaintenanceProbability** al área **Valores**.

15. Cambie el **título** del gráfico a **Modelos de vehículo que necesitan mantenimiento**.

    ![Título de Treemap](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Haga clic en el área en blanco para agregar una nueva visualización:

17. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de barras 100 % apiladas**. Arrastre **city** al área **Eje**. Arrastre **MaintenanceProbability** y **RecallProbability** al área **Valor**.

    ![Áreas de eje y valor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. En la sección **Formato**, seleccione **Colores de datos**. Establezca el color de **MaintenanceProbability** en el valor **F2C80F**.

19. Cambie el **título** del gráfico a **Probabilidad de mantenimiento del vehículo y retirada por ciudad**.

    ![Título del gráfico de barras 100 % apiladas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Haga clic en el área en blanco para agregar una nueva visualización:

21. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de áreas**. Arrastre **Model** al área **Eje**. Arrastre **engineOil**, **tirepressure**, **speed** y **MaintenanceProbability** al área **Valores**. Cambie su tipo de agregación por **Media**. 

    ![Tipo de agregación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Cambie el **título** del gráfico por **Media de aceite del motor, presión de los neumáticos, velocidad y probabilidad de mantenimiento por modelo**.

    ![Título del gráfico de áreas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Haga clic en el área en blanco para agregar una nueva visualización:

24. En la pestaña **Visualizaciones**, seleccione la visualización **Gráfico de dispersión**. Arrastre **Model** a las áreas **Detalles** y **Leyenda**. Arrastre **fuel** al área **Eje X**. Cambie la agregación a **Media**. Arrastre **engineTemperature** al área **Eje Y**. Cambie la agregación a **Media**. Arrastre **vin** al área **Tamaño**.

    ![Áreas Detalles, Leyenda, Eje y Tamaño](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Cambie el **Título** del gráfico a **Media de combustible, media de temperatura del motor y recuento de vin por modelo**.

    ![Título del gráfico de dispersión](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    El informe final es similar al ejemplo siguiente:

    ![Informe final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Anclaje de las visualizaciones desde los informes al panel en tiempo real.
1. Cree un panel en blanco; para ello, seleccione el símbolo del signo más situado junto a **Paneles**. Escriba el nombre **Panel de análisis de telemetría de vehículos**.

    ![Símbolo del signo más en el panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Ancle las visualizaciones de los informes anteriores al panel. 

    ![Símbolo de anclaje al panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Cuando los tres informes estén anclados al panel, deberían tener el aspecto siguiente. Si no creó todos los informes, el panel podría presentar un aspecto diferente. 

    ![Panel con informes](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Ha creado correctamente el panel en tiempo real. Mientras ejecuta CarEventGenerator.exe y RealtimeDashboardApp.exe, verá actualizaciones en directo en el panel. Los siguientes pasos tardarán unos 10 o 15 minutos en completarse.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Configuración del panel de procesamiento por lotes de Power BI
> [!NOTE]
> La canalización del procesamiento por lotes de un extremo a otro tarda unas dos horas en ejecutarse (a partir de la finalización correcta de la implementación) y en procesar un año de datos generados. Espere a que el procesamiento finalice antes de continuar con los pasos siguientes. 
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Descarga del archivo de Power BI Designer

1. Se incluye un archivo de Power BI Designer configurado previamente como parte de las instrucciones de funcionamiento manual. Busque "2. Configuración del panel de procesamiento por lotes de PowerBI".

2. Descargue la plantilla de Power BI para el panel de procesamiento por lotes, que aquí se llama **ConnectedCarsPbiReport.pbix**.

3. Guárdela de forma local.

### <a name="configure-power-bi-reports"></a>Configuración de informes de Power BI

1. Abra el archivo de diseñador '**ConnectedCarsPbiReport.pbix** con Power BI Desktop. Si aún no lo tiene, instale Power BI Desktop desde el sitio web de [instalación](http://www.microsoft.com/download/details.aspx?id=45331).

2. Seleccione **Editar consultas**.

    ![Editar consultas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Haga doble clic en **Origen**.

    ![Origen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Actualice la cadena de conexión de servidor con el servidor SQL de Azure que se ha aprovisionado como parte de la implementación. Busque en las instrucciones de funcionamiento manual en Azure SQL Database:

    * Servidor: somethingsrv.database.windows.net
    * Base de datos: connectedcar
    * Nombre de usuario: username
    * Contraseña: puede administrar la contraseña de SQL Server desde Azure Portal.

5. Establezca **Base de datos** como **connectedcar**.

    ![Base de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Seleccione **Aceptar**.

7. La pestaña **Credencial de Windows** se selecciona de forma predeterminada. Cámbiela a **Credenciales de base de datos**; para ello, seleccione la pestaña **Base de datos** a la derecha.

8. Especifique los valores de **Nombre de usuario** y **Contraseña** de su instancia de Azure SQL Database que ha especificado durante la configuración de su implementación.

    ![Credenciales de base de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Seleccione **Conectar**.

10. Repita los pasos anteriores con cada una de las tres consultas restantes presentes en el panel derecho. A continuación, actualice los detalles de la conexión del origen de datos.

11. Seleccione **Cerrar y cargar**. Los conjuntos de datos de archivos de Power BI Desktop se conectan a tablas de SQL Database.

12. Seleccione **Cerrar** para cerrar el archivo de Power BI Desktop.

    ![cierre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Seleccione **Guardar** para guardar los cambios. 

Ahora, ha configurado todos los informes correspondientes a la ruta de procesamiento por lotes de la solución. 

## <a name="upload-to-powerbicom"></a>Carga en powerbi.com
1. Vaya al [portal web de Power BI](http://powerbi.com) e inicie sesión.

2. Seleccione **Obtener datos**.

3. Cargue el archivo de Power BI Desktop. Seleccione **Obtener datos** > **Obtener archivos** > **Archivo Local**.

4. Vaya a **ConnectedCarsPbiReport.pbix**.

5. Una vez cargado el archivo, vuelva a su área de trabajo de Power BI. Se crea un conjunto de datos, un informe y un panel en blanco.  

6. Ancle los gráficos a un nuevo panel denominado **Panel de análisis de telemetría de vehículos** en Power BI. Seleccione el panel en blanco que se creó anteriormente y, a continuación, vaya a la sección **Informes**. Seleccione el informe recién cargado.  

    ![Nuevo panel de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    El informe tiene seis páginas:

    Página 1: Densidad de vehículos  
    Página 2: Estado de vehículos en tiempo real  
    Página 3: Vehículos conducidos de forma agresiva   
    Página 4: Vehículos retirados  
    Página 5: Vehículos conducidos con un consumo eficiente  
    Página 6: Logotipo de Contoso Motors  

    ![Informe de Power BI con seis páginas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. En la **página 3**, ancle el siguiente contenido:  

    a. **Recuento de vin**  

   ![Página 3: Recuento de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Vehículos que se han conducido de forma agresiva por modelo: gráfico de cascada** 

   ![Página 3: Gráfico 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. En la **página 5**, ancle el siguiente contenido: 

    a. **Recuento de vin**

   ![Página 5: Gráfico 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Vehículos con ahorro de combustible por modelo: gráfico de columnas agrupadas**

   ![Página 5: Gráfico 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. En la **página 4**, ancle el siguiente contenido:  

    a. **Recuento de vin** 

   ![Página 4: Gráfico 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Vehículos retirados por ciudad y modelo: gráfico de rectángulos**

   ![Página 4: Gráfico 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. En la **página 6**, ancle el siguiente contenido:  

    * **Logotipo de Contoso Motors**

    ![Logotipo de Contoso Motors](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organización del panel  

1. Vaya al panel.

2. Mantenga el puntero sobre cada gráfico. Cambie el nombre de cada gráfico según la denominación proporcionada en el siguiente ejemplo de panel terminado:

   ![Organización de panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Mueva los gráficos de modo similar al siguiente ejemplo de panel:

    ![Panel reorganizado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Después de crear todos los informes que se mencionan en este documento, el panel terminado final es similar al ejemplo siguiente: 

   ![Panel final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Ha creado correctamente los informes y el panel para obtener información predictiva y por lotes en tiempo real sobre los hábitos de mantenimiento y conducción de vehículos.  
