---
title: "Panel de Power BI del estado de vehículo y los hábitos de conducción - Azure | Microsoft Docs"
description: "Aproveche las posibilidades de Cortana Intelligence para obtener información en tiempo real y predictiva del estado de los vehículos y los hábitos de conducción."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 24d440049b5e889c6d4417cc16787edd42bc5848
ms.lasthandoff: 01/24/2017


---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instrucciones de configuración del panel de Power BI para la plantilla de la solución Análisis de telemetría de vehículos
Este **menú** está relacionado con los capítulos de este cuaderno de estrategias. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

La solución de análisis de telemetría de vehículos muestra cómo los concesionarios y los fabricantes de automóviles, así como las compañías de seguros, pueden aprovechar las posibilidades de Cortana Intelligence para obtener información predictiva y en tiempo real sobre el estado del vehículo y los hábitos de conducción con el objetivo de impulsar mejoras en el área de la experiencia del cliente, el I+D y las campañas de marketing. Este documento contiene instrucciones paso a paso sobre cómo configurar el panel y los informes de Power BI una vez que la solución se implementa en la suscripción. 

## <a name="prerequisites"></a>Requisitos previos
1. Implementar la solución Análisis de telemetría del vehículo navegando a [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2. [Instalar Microsoft Power BI Desktop.](http://www.microsoft.com/download/details.aspx?id=45331)
3. Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/). Si no tiene una suscripción de Azure, comience con una suscripción gratis de Azure.
4. Cuenta de Microsoft Power BI

## <a name="cortana-intelligence-suite-components"></a>Componentes de Cortana Intelligence Suite
Como parte de la plantilla de la solución de análisis de telemetría de vehículo, se implementan los siguientes servicios de Cortana Intelligence en la suscripción.

* **Centros de eventos** para la introducción de millones de eventos de telemetría del vehículo en Azure.
* **Análisis de transmisiones**para obtener información en tiempo real sobre el estado del vehículo y conservar esos datos en almacenamiento a largo plazo para un análisis por lotes más completo.
* **Aprendizaje automático** para detección de anomalías en tiempo real y procesamiento por lotes para obtener información predictiva.
* **HDInsight** se aprovecha para transformar datos a escala.
* **Factoría de datos** controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes.

**Power BI** ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo. 

La solución usa dos orígenes de datos diferentes: **señales simuladas de vehículos y conjunto de datos de diagnóstico** y el **catálogo de vehículos**.

Se incluye un simulador telemático del vehículo como parte de esta solución. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo. 

El catálogo de vehículo es un conjunto de datos de referencia que contiene una asignación de número de identificación del vehículo (VIN) a modelo.

## <a name="power-bi-dashboard-preparation"></a>Preparación de panel de Power BI
### <a name="deployment"></a>Implementación
Una vez completada la implementación, debería ver el diagrama siguiente con todos estos componentes marcados en VERDE. 

* Haga clic en la flecha situada en la esquina superior derecha de los nodos verdes para navegar a los servicios correspondientes para validar si todos ellos se han implementado correctamente.
* Haga clic en la flecha situada en la esquina superior derecha del nodo **Simulador telemático del vehículo** para descargar el paquete del simulador de datos. Guarde y extraiga los archivos localmente en su máquina. 

![Componentes implementados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Ahora, está listo para configurar el panel de Power BI con visualizaciones enriquecidas para obtener información predictiva y en tiempo real sobre el estado del vehículo y los hábitos de conducción. Se tarda entre 45 minutos y una hora en crear todos los informes y configurar el panel. 

### <a name="setup-power-bi-real-time-dashboard"></a>Configuración del panel de Power BI en tiempo real
**Generación de datos simulados**

1. En el equipo local, vaya a la carpeta en la que extrajo el paquete del simulador telemático de vehículo.
  ![Carpeta Simulator](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2. Ejecute la aplicación ***CarEventGenerator.exe***.
3. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo. Todo ello se publica en una instancia del Centro de eventos de Azure que está configurada como parte de la implementación.

![Diagnóstico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)

**Inicio de la aplicación de panel en tiempo real**

La solución incluye una aplicación que genera un panel en tiempo real en Power BI. Esta aplicación escucha a una instancia de Event Hubs, desde la que Stream Analytics publica los eventos de forma continua. Para cada evento que esta aplicación recibe, procesa los datos mediante un punto de conexión de puntuación de solicitud-respuesta de Machine Learning. El conjunto de datos resultante se publica en las API de inserción de Power BI para su visualización. 

Para descargar la aplicación:

1. Haga clic en el nodo de Power BI en la vista de diagrama y, luego, en el panel de propiedades en el vínculo **Descargar aplicación del panel en tiempo real**.![Panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2. Extraiga y guarde la aplicación localmente. ![Aplicación de panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)
3. Ejecute la aplicación **RealtimeDashboardApp.exe**
4. Proporcione credenciales válidas de Power BI, inicie sesión y haga clic en **Aceptar**
   
   ![Inicio de sesión en Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Permisos de panel de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

### <a name="configure-power-bi-reports"></a>Configuración de informes de Power BI
Los informes y el panel en tiempo real tardan aproximadamente entre 30 y 45 minutos en completarse. Vaya a [http://powerbi.com](http://powerbi.com) e inicie sesión.

![Inicio de sesión en Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Se genera un nuevo conjunto de datos en Power BI. Haga clic en el conjunto de datos **ConnectedCarsRealtime** .

![Seleccione conjunto de datos en tiempo real de automóviles conectados.](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Guarde el informe en blanco mediante la combinación de teclas **Ctrl + S**.

![Guardar el informe en blanco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Proporcione el nombre del informe *Análisis de telemetría del vehículo en tiempo real - Informes*.

![Proporcione el nombre del informe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Informes en tiempo real
Hay tres informes en tiempo real en esta solución:

1. Vehículos en funcionamiento
2. Vehículos que requieren mantenimiento
3. Estadísticas de estado de los vehículos

Puede elegir configurar los tres informes en tiempo real o parar después de una fase y continuar con la siguiente sección de configuración de los informes por lotes. Se recomienda crear los tres informes para visualizar la información completa de la ruta en tiempo real de la solución.  

### <a name="1-vehicles-in-operation"></a>1. Vehículos en funcionamiento
Haga doble clic en **Página 1** y cambie el nombre de la página a “Vehículos en funcionamiento”.  
    ![Automóviles conectados: Vehículos en funcionamiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Seleccione el campo **vin** en **Campos** y elija el tipo de visualización como **“Tarjeta”**.  

Se crea la visualización de tarjeta como se muestra en la ilustración.  
    ![Automóviles conectados - Seleccionar vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione **city** y **vin** en los campos. Cambie la visualización a **"Mapa"**. Arrastre **vin** al área de valores. Arrastre **city** desde los campos hasta el área **Leyenda**.   
    ![Automóviles conectados: Visualización de tarjeta](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Seleccione la sección **Formato** en **Visualizaciones**, haga clic en **Título** y cambie el **texto** a **“Vehículos en funcionamiento por ciudad”**.  
    ![Automóviles conectados: Vehículos en funcionamiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

La visualización final tiene el aspecto que se muestra en la ilustración.    
    ![Automóviles conectados - Visualización final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione **city** y **vin**, cambie el tipo de visualización a **Gráfico de columnas agrupadas**. Asegúrese de que el campo **city** esté en el área **Eje** y **vin** en el área **Valor**.  

Ordene el gráfico por **“Recuento de vin”**.  
    ![Automóviles conectados: Recuento de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Cambie el **Título** del gráfico a **“Vehículos en funcionamiento por ciudad”**.  

Haga clic en la sección **Formato** y, después, seleccione **Colores de datos**. Haga clic en **“Activar”** para **Mostrar todo**.  
    ![Automóviles conectados: Mostrar todos los colores de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Cambie el color de cada ciudad haciendo clic en el icono de color.  
    ![Automóviles conectados - Cambiar colores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione la visualización **Gráfico de columnas agrupadas** en Visualizaciones, arrastre el campo **city** al área **Eje**, el campo **Model** al área **Leyenda** y el campo **vin** al área **Valor**.  
    ![Automóviles conectados: Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Automóviles conectados: Representación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Reorganice toda la visualización de esta página como se muestra en la ilustración.  
    ![Automóviles conectados - Visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Ha configurado correctamente el informe en tiempo real de "Vehículos en funcionamiento". Puede continuar y crear el siguiente informe en tiempo real o detenerse aquí y configurar el panel. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Vehículos que requieren mantenimiento
Haga clic en ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar un nuevo informe y cambie su nombre a **"Vehículos que requieren mantenimiento"**

![Automóviles conectados - Vehículos que requieren mantenimiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Seleccione el campo **vin** y cambie el tipo de visualización a **Tarjeta**.  
    ![Automóviles conectados: Visualización de tarjeta vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

En el conjunto de datos, tenemos un campo llamado "MaintenanceLabel". Este campo puede tener un valor de "0" o "1". Lo establece el modelo de aprendizaje automático de Azure aprovisionado como parte de la solución y se integra con la ruta en tiempo real. El valor "1" indica que un vehículo requiere mantenimiento. 

Iremos agregando filtros de **nivel de página** para mostrar datos de vehículos que requieren mantenimiento: 

1. Arrastre el campo **“MaintenanceLabel”** a **Filtros de nivel de página**.  
   ![Automóviles conectados - Filtros de nivel de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Haga clic en el menú **Filtrado básico** que se encuentra en la parte inferior del filtro de nivel de página MaintenanceLabel.  
   ![Automóviles conectados - Filtrado básico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Establezca su valor de filtro en **“1”**  .  
   ![Automóviles conectados - Valor de filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione **Gráfico de columnas agrupadas** en Visualizaciones.  
![Automóviles conectados - Visualización de tarjeta vind](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Automóviles conectados - Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Arrastre el campo **Model** al área **Eje** y el campo **vin** al área **Valor**. Después, ordene la visualización por **Recuento de vin**.  Cambie el **Título** del gráfico a **“Vehículos que necesitan mantenimiento por modelo”**.  

Arrastre los campos **vin** a **Saturación de color**, que se encuentra en la sección **Campos** ![Campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de la pestaña **Visualización**.  
![Automóviles conectados - Saturación de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Cambie **Colores de datos** en las visualizaciones de la sección **Formato**.  
Cambie el color mínimo a: **F2C812**  
Cambie el color máximo a: **FF6300**  
![Automóviles conectados - Cambios de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Automóviles conectados - Nuevos colores de visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione **Gráfico de columnas agrupadas** en Visualizaciones, arrastre el campo **vin** al área **Valor** y arrastre el campo **city** al área **Eje**. Ordene el gráfico por **“Recuento de vin”**. Cambie el **Título** del gráfico a **“Vehículos que necesitan mantenimiento por ciudad”** .  
![Automóviles conectados - Vehículos que requieren mantenimiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Haga clic en el área en blanco para agregar una nueva visualización.  

Seleccione la visualización **Tarjeta de varias filas** en Visualizaciones y arrastre los campos **Model** y **vin** al área **Campos**.  
![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Al reorganizar todos los datos de la visualización, el informe final tiene el aspecto siguiente:   
![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Ha configurado correctamente el informe en tiempo real "Vehículos que requieren mantenimiento". Puede continuar y crear el siguiente informe en tiempo real o detenerse aquí y configurar el panel. 

### <a name="3-vehicles-health-statistics"></a>3. Estadísticas de estado de los vehículos
Haga clic en ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar el nuevo informe y cambie su nombre a **"Estadísticas de estado de los vehículos"**  

Seleccione la visualización **Indicador** en Visualizaciones y, después, arrastre el campo **speed** a las áreas **Valor, Valor mínimo y Valor máximo**.  
![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Cambie la agregación predeterminada de **speed** en el área **Valor** a **Media**. 

Cambie la agregación predeterminada de **speed** en el área **Valor mínimo** a **Mínimo**.

Cambie la agregación predeterminada de **speed** en el área **Valor máximo** a **Máximo**.

![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Cambie el nombre de **Título del indicador** a **“Velocidad media”**. 

![Automóviles conectados - Medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Haga clic en el área en blanco para agregar una nueva visualización.  

Del mismo modo, agregue un **Indicador** para el **aceite medio del motor**, el **combustible medio** y la **temperatura media del motor**.  

Cambie la agregación predeterminada de los campos de cada indicador según los pasos anteriores del indicador de **"Velocidad media"** .

![Automóviles conectados - Medidores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **Gráfico de columnas agrupadas y de líneas** en Visualizaciones y, después, arrastre el campo **city** a **Eje compartido** y los campos **speed**, **tirepressure y engineoil** al área **Valores de columna**. Después, cambie su tipo de agregación a **Media**. 

Arrastre el campo **engineTemperature** al área **Valores de línea** y cambie el tipo de agregación a **Media**. 

![Automóviles conectados - Campos de visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Cambie el **Título** del gráfico a **“Promedio de velocidad media, presión de los neumáticos, aceite del motor y temperatura del motor”**.  

![Automóviles conectados - Campos de visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de rectángulos** en Visualizaciones, arrastre el campo **Model** al área **Grupo** y el campo **MaintenanceProbability** al área **Valores**.

Cambie el **Título** del gráfico a **“Modelos de vehículo que necesitan mantenimiento”**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de barras&100; % apiladas**, arrastre el campo **city** al área **Eje** y los campos **MaintenanceProbability** y **RecallProbability** al área **Valor**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Haga clic en **Formato**, seleccione **Colores de datos** y establezca el color de **MaintenanceProbability** en el valor **“F2C80F”**.

Cambie el **Título** del gráfico a **“Probabilidad de mantenimiento del vehículo y retirada por ciudad”**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de áreas** en Visualizaciones, arrastre el campo **Model** al área **Eje** y los campos **engineOil, tirepressure, speed y MaintenanceProbability** al área **Valor**. Cambie el tipo de agregación a **"Promedio"**. 

![Automóviles conectados - Cambiar tipo de agregación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Cambie el título del gráfico a **"Promedio de aceite del motor, presión de los neumáticos, velocidad y probabilidad de mantenimiento por modelo"**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Haga clic en el área en blanco para agregar una nueva visualización:

1. Seleccione la visualización **Gráfico de dispersión** en Visualizaciones.
2. Arrastre el campo **Model** al área **Detalles** y **Leyenda**.
3. Arrastre el campo **fuel** al área **Eje X** y cambie la agregación a **Media**.
4. Arrastre el campo **engineTemperature** al área **Eje Y** y cambie la agregación a **Media**.
5. Arrastre el campo **vin** al área **Tamaño**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Cambie el **Título** del gráfico a **“Medias de combustible y temperatura del motor por modelo”**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

El informe final será similar al mostrado a continuación.

![Automóviles conectados - Informe final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Anclaje de las visualizaciones desde los informes al panel en tiempo real.
Cree un panel en blanco haciendo clic en el icono del signo más junto a Paneles. Puede asignarle el nombre "Panel de análisis de telemetría de vehículos".

![Automóviles conectados - Panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Ancle la visualización de los informes anteriores al panel. 

![Automóviles conectados - Panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Cuando los tres informes se crean y las visualizaciones correspondientes se anclan al panel, el panel debe verse como se muestra a continuación. Puede parecer diferente si no ha creado todos los informes. 

![Automóviles conectados - Panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

¡Enhorabuena! Ha creado correctamente el panel en tiempo real. Mientras sigue ejecutando CarEventGenerator.exe y RealtimeDashboardApp.exe, verá actualizaciones en directo en el panel. En unos 10 o 15 minutos se completarán los siguientes pasos.

## <a name="setup-power-bi-batch-processing-dashboard"></a>Configuración del panel de procesamiento por lotes de Power BI
> [!NOTE]
> La canalización del procesamiento por lotes de un extremo a otro tarda unas dos horas en ejecutarse (a partir de la finalización correcta de la implementación) y en procesar un año de datos generados. Por tanto, espere a que el procesamiento finalice antes de continuar con los siguientes pasos. 
> 
> 

**Descarga del archivo de Power BI Designer**

* Se incluye un archivo de Power BI Designer configurado previamente como parte de la implementación.
* Haga clic en el nodo de Power BI en la vista de diagrama y haga clic en el vínculo **Descargar archivo de Power BI Designer** en el panel de propiedades. ![Descarga de Power BI Designer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)
* Guarde localmente.

**Configuración de informes de Power BI**

* Abra el archivo de diseñador 'VehicleTelemetryAnalytics - Desktop Report.pbix' mediante Power BI Desktop. Si no lo ha hecho ya, instale Power BI Desktop desde la [instalación de Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331). 
* Haga clic en **Editar consultas**.

![Edición de la consulta de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Haga doble clic en el **Origen**.

![Establecimiento de origen de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Actualice la cadena de conexión de servidor con el servidor SQL de Azure que se ha aprovisionado como parte de la implementación. Haga clic en el nodo SQL Azure en el diagrama y vea el nombre del servidor del panel de propiedades.

![Ver nombre de servidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

* Establezca **Base de datos** como *connectedcar*.

![Establecimiento de la base de datos Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Haga clic en **Aceptar**.
* Verá la pestaña **Credenciales de Windows** seleccionada de forma predeterminada, cámbiela a **Credenciales de base de datos** (para hacerlo, haga clic en la pestaña **Base de datos** a la derecha).
* Especifique los valores de **Nombre de usuario** y **Contraseña** de la Azure SQL Database que ha especificado durante la instalación de su implementación.

![Introduzca las credenciales de la base de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Haga clic en **Conectar**
* Repita los pasos anteriores para cada una de las&3; consultas restantes del panel derecho y, después, actualice los detalles de conexión del origen de datos.
* Haga clic en **Cerrar y cargar**. Los conjuntos de datos de archivos de Power BI Desktop se conectan a tablas de Base de datos SQL de Azure.
* **Cierre** el archivo de Power BI Desktop

![Cierre Power BI Desktop](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Haga clic en el botón **Guardar** para guardar los cambios. 

Ahora, ha configurado todos los informes correspondientes a la ruta de procesamiento por lotes de la solución. 

## <a name="upload-to-powerbicom"></a>Carga en *powerbi.com*
1. Navegue hasta el portal web de Power BI en http://powerbi.com e inicie sesión.
2. Haga clic en **Obtener datos**  
3. Cargue el archivo de Power BI Desktop.  
4. Para cargarlo, haga clic en **Obtener datos -> Archivos Get -> Archivo local**.  
5. Desplácese hasta el archivo **"VehicleTelemetryAnalytics – Desktop Report.pbix"**  
6. Una vez cargado el archivo, volverá al espacio de trabajo de Power BI.  

Se crearán un conjunto de datos, un informe y un panel en blanco.  

Ancle los gráficos al panel existente **Panel de análisis de telemetría de vehículos** en **Power BI**. Haga clic en el panel en blanco creado anteriormente y luego vaya a la sección **Informes** y haga clic en el informe recién cargado.  

![Telemetría de vehículos Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Tenga en cuenta que el informe tiene seis páginas:**  
Página 1: Densidad de vehículos  
Página 2: Estado de vehículos en tiempo real  
Página 3: Vehículos conducidos de forma agresiva   
Página 4: Vehículos retirados  
Página 5: Vehículos conducidos con un consumo eficiente  
Página 6: Logotipo de Contoso  

![Automóviles conectados Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**En la página 3**, ancle lo siguiente:  

1. Recuento de vin  
   ![Automóviles conectados Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Vehículos que se han conducido de forma agresiva por modelo: gráfico de cascada   
   ![Telemetría del vehículo - Anclar gráficos 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**En la página 5**, ancle lo siguiente: 

1. Recuento de vin    
   ![Telemetría del vehículo - Anclar gráficos 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Vehículos con ahorro de combustible por modelo: gráfico de columnas agrupadas   
   ![Telemetría del vehículo - Anclar gráficos 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**En la página 4**, ancle lo siguiente:  

1. Recuento de vin  
   ![Telemetría del vehículo - Anclar gráficos 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Vehículos retirados por ciudad y modelo: gráfico de rectángulos   
   ![Telemetría del vehículo - Anclar gráficos 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**En la página 6**, ancle lo siguiente:  

1. Logotipo de Contoso Motors  
   ![Telemetría del vehículo - Anclar gráficos 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organización del panel**  

1. Vaya al panel.
2. Mantenga el puntero sobre cada gráfico y cambie su nombre en función de la nomenclatura proporcionada en la siguiente imagen de panel completo. Asimismo, mueva los gráficos de un lado a otro hasta conseguir un aspecto del panel similar al siguiente.  
   ![Telemetría del vehículo - Organizar panel 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Telemetría de vehículos Power BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Si ha creado todos los informes como se ha mencionado en este documento, el panel completado final debe parecerse al de la siguiente ilustración. 

![Telemetría del vehículo - Organizar panel 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

¡Enhorabuena! Ha creado correctamente los informes y el panel para obtener información predictiva y por lotes en tiempo real sobre los hábitos de mantenimiento y conducción.  


