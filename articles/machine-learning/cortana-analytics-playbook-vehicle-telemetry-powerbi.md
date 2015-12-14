<properties 
	pageTitle="Instrucciones de configuración del panel de PowerBI para la plantilla de la solución Análisis de telemetría del vehículo | Microsoft Azure" 
	description="Use las funcionalidades de Cortana Analytics para obtener información predictiva y en tiempo real del estado del vehículo y los hábitos de conducción." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Instrucciones de configuración del panel de PowerBI para la plantilla de la solución Análisis de telemetría del vehículo

Este **menú** está relacionado con los capítulos de este cuaderno de estrategias.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


La solución Análisis de telemetría del vehículo muestra cómo los concesionarios y los fabricantes de automóviles, así como las compañías de seguros pueden aprovechar las funcionalidades de Cortana Analytics para obtener información predictiva y en tiempo real sobre el estado del vehículo y los hábitos de conducción para impulsar mejoras en el área de la experiencia del cliente, I+D y las campañas de marketing. Este documento contiene instrucciones paso a paso sobre cómo configurar los informes de PowerBI y el panel una vez que la solución se implementa en la suscripción.


## Requisitos previos
1.	Implementar la solución Análisis de telemetría del vehículo navegando a [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3).  
2.	[Instalar Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
3.	Una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/). Si no tiene una suscripción de Azure, comience con una suscripción gratis de Azure.
4.	Cuenta de Microsoft PowerBI.
	

## Componentes de Cortana Analytics Suite
Como parte de la plantilla de la solución Análisis de telemetría del vehículo, se implementan los siguientes servicios de Cortana Analytics en la suscripción.

- **Centros de eventos** para la introducción de millones de eventos de telemetría del vehículo en Azure.
- **Análisis de transmisiones** para obtener información en tiempo real sobre el estado del vehículo y conservar esos datos en almacenamiento a largo plazo para un análisis por lotes más completo.
- **Aprendizaje automático** para detección de anomalías en tiempo real y procesamiento por lotes para obtener información predictiva.
- **HDInsight** se aprovecha para transformar datos a escala.
- **Factoría de datos** controla la orquestación, programación, administración de recursos y supervisión de la canalización del procesamiento por lotes.

**Power BI** ofrece a esta solución un panel completo de datos en tiempo real y visualizaciones de análisis predictivo.

La solución utiliza dos orígenes de datos diferentes: **señales simuladas de vehículo y conjunto de datos de diagnóstico** y el **catálogo de vehículo**.

Se incluye un simulador telemático del vehículo como parte de esta solución. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo.

El catálogo de vehículo es un conjunto de datos de referencia que contiene una asignación de número de identificación del vehículo (VIN) a modelo.


## Preparación del panel de PowerBI

### Implementación

Una vez completada la implementación, debería ver el diagrama siguiente con todos estos componentes marcados en VERDE.

- Haga clic en la flecha situada en la esquina superior derecha de los nodos verdes para navegar a los servicios correspondientes para validar si todos ellos se han implementado correctamente.
- Haga clic en la flecha situada en la esquina superior derecha del nodo Simulador telemático del vehículo para descargar el paquete del simulador de datos. Guarde y extraiga los archivos localmente en su máquina. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Ahora, está listo para configurar el panel de PowerBI con visualizaciones enriquecidas para obtener información predictiva y en tiempo real sobre el estado del vehículo y los hábitos de conducción. Se tarda entre 45 minutos y una hora en crear todos los informes y configurar el panel.


### Configuración del panel de Power BI en tiempo real

**Generación de datos simulados**

1. En la máquina local, vaya a la carpeta donde extrajo el paquete Simulador telemático del vehículo. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)

2.	Ejecute la aplicación ***CarEventGenerator.exe***.
3.	Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo. Todo ello se publica en una instancia del Centro de eventos de Azure que está configurada como parte de la implementación.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
Inicio de la aplicación del panel en tiempo real Se incluye una aplicación como parte de la solución para generar el panel en tiempo real en PowerBI. Esta aplicación escucha a una instancia del Centro de eventos donde Análisis de transmisiones publica los eventos de forma continua. Para cada evento que esta aplicación recibe, procesa los datos mediante el punto de conexión de puntuación de solicitud-respuesta de Aprendizaje automático y el conjunto de datos resultante se publica en las API de inserción de PowerBI para visualización. Para descargar la aplicación:

1.	Haga clic en el nodo PowerBI en la vista de diagrama y haga clic en el vínculo **Descargar aplicación del panel en tiempo real** en el panel de propiedades.

2.	Realice la extracción y el almacenamiento localmente.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	Ejecute la aplicación ‘RealtimeDashboardApp.exe’.
4.	Proporcione credenciales válidas de Power BI, inicie sesión y haga clic en **Aceptar**.
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## Creación de informes de Power BI en tiempo real 

### Configuración de informes de PowerBI

Se generará un nuevo conjunto de datos en Power BI. Haga clic en el conjunto de datos **ConnectedCarsRealtime**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Guarde el informe en blanco mediante la combinación de teclas **Ctrl + S**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Proporcione el nombre del informe *Análisis de telemetría del vehículo en tiempo real - Informes*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### Crear un informe en tiempo real: *Vehículos en funcionamiento*
  
Haga doble clic en **Página 1** y cámbiele el nombre a "Vehículos en funcionamiento". ![Automóviles conectados - Vehículos en funcionamiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

Seleccione el campo **vin** en **Campos** y elija el tipo de visualización como **"Tarjeta"**.

La visualización de tarjeta se creará como se muestra en la figura. ![Automóviles conectados - Seleccionar vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **city** y **vin** en los campos. Cambie la visualización a **"Mapa"**. Arrastre **vin** al área de valores. Arrastre **city** desde los campos al área **Leyenda**. ![Automóviles conectados - Visualización de tarjeta](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Seleccione la sección **formato** en **Visualizaciones**, haga clic en **Título** y cambie el texto a **"Vehículos en funcionamiento por ciudad"**. ![Automóviles conectados - Vehículos en funcionamiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

La visualización final tendrá el aspecto que se muestra en la figura. ![Automóviles conectados - Visualización final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **city** y **vin**, cambie el tipo de visualización a **Gráfico de columnas agrupadas**. Asegure el campo **city** en el área **Eje** y **vin** en el área **Valor**.

Ordene el gráfico por **“Recuento de vin”**. ![Automóviles conectados - Recuento de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

Cambie el **Título** del gráfico a **“Vehículos en funcionamiento por ciudad”**.

Haga clic en la sección **Formato** y después seleccione **Colores de datos**. Haga clic en **"Activar"** en **Mostrar todo**. ![Automóviles conectados - Mostrar todos los colores de datos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

Cambie el color de cada ciudad haciendo clic en el icono de color. ![Automóviles conectados - Cambiar colores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de columnas agrupadas** en Visualizaciones, arrastre el campo **city** al área **Eje**, **Model** al área **Leyenda** y **vin** al área **Valor**. ![Automóviles conectados - Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![Automóviles conectados - Representación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Reorganice toda la visualización en esta página como se muestra en la figura. ![Automóviles conectados - Visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### Agregue un nuevo informe: *Vehículos que requieren mantenimiento*.
  
Haga clic en ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar el nuevo informe, cambie su nombre a **“Vehículos que requieren mantenimiento”**.

![Automóviles conectados - Vehículos que requieren mantenimiento](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

Seleccione el campo **vin** y cambie el tipo de visualización a **Tarjeta**. ![Automóviles conectados - Visualización de tarjeta vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

En el conjunto de datos, tenemos el campo llamado **"MaintenanceLabel"**. Este campo tiene el valor **"0"** y **"1"**. El valor “1” indica que el vehículo requiere mantenimiento

Iremos agregando **filtros de nivel de página** para mostrar datos de vehículos que requieren mantenimiento.

1. Arrastre el campo **"MaintenanceLabel"** a **Filtros de nivel de página**. ![Automóviles conectados - Filtros de nivel de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Haga clic en el menú **Filtrado básico** presente en la parte inferior del filtro de nivel de página MaintenanceLabel. ![Automóviles conectados - Filtrado básico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  Establezca su valor de filtro en **“1”**. ![Automóviles conectados - Valor de filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]Podemos establecer el filtro en el nivel de gráfico, cuyo nombre es **Filtros de nivel Visual**. Solamente se aplicarán a una visualización específica. Los filtros de nivel de página se aplican a todas las visualizaciones de un informe.

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **Gráfico de columnas agrupadas** en Visualizaciones. ![Automóviles conectados - Visualización de tarjeta vind](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![Automóviles conectados - Gráfico de columnas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Arrastre el campo **Model** al área **Eje** área y el campo **vin** al área **Valor**. Después, ordene la visualización por **Recuento de vin**. Cambie el **Título** del gráfico a **“Vehículos que requieren mantenimiento por modelo”**.

Arrastre los campos **vin** a **Saturación de color** presente en la sección **Campos** ![Fields](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) de la ficha **Visualizaciones**. ![Automóviles conectados - Saturación de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

Cambie **Colores de datos** en las visualizaciones de la sección **Formato**. Cambie el color mínimo a **F2C812**. Cambie el color máximo a **FF6300**. ![Automóviles conectados - Cambios de color](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![Automóviles conectados - Nuevos colores de visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **Gráfico de columnas agrupadas** en Visualizaciones, arrastre el campo **vin** al área **Valor** y arrastre el campo **city** al área **Eje**. Ordene el gráfico por **“Recuento de vin”**. Cambie el **Título** del gráfico a **“Vehículos que requieren mantenimiento por ciudad”**. ![Automóviles conectados - Vehículos que requieren mantenimiento por ciudad](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Tarjeta de varias filas** en Visualizaciones y arrastre **Modelo** y **vin** al área **Campos**. ![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

Reorganice toda la visualización. El informe final será similar al mostrado a continuación. ![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### Agregue un nuevo informe: *Estadísticas del estado del vehículo*.
  
Haga clic en ![Agregar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para agregar el nuevo informe, cambie su nombre a **“Estadísticas del estado del vehículo”**.

Seleccione la visualización **Medidor** en Visualizaciones y después arrastre el campo **speed** a las áreas **Valor, Valor mínimo y Valor máximo**. ![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

Cambie la agregación predeterminada de **speed** en el área **Valor** a **Promedio**.

Cambie la agregación predeterminada de **speed** en el área **Valor mínimo** a **Mínimo**.

Cambie la agregación predeterminada de **speed** en el área **Valor máximo** a **Máximo**.

![Automóviles conectados - Tarjeta de varias filas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

Cambie el nombre de **Título del medidor** a **“Velocidad media”**.
 
![Automóviles conectados - Medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Del mismo modo, agregue un **Medidor** para el **aceite medio del motor**, el **combustible medio** y la **temperatura media del motor**.

Cambie la agregación predeterminada de los campos de cada medidor según los pasos anteriores del medidor **"Velocidad media"**.

![Automóviles conectados - Medidores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione **Gráfico de columnas agrupadas y de líneas** en Visualizaciones y después arrastre el campo **city** a **Eje compartido**, arrastre los campos **speed**, **tirepressure y engineoil** al área **Valores de columna**, y cambie su tipo de agregación a **Promedio**.

Arrastre el campo **engineTemperature** al área **Valores de línea** y cambie el tipo de agregación a **Promedio**.

![Automóviles conectados - Campos de visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Cambie el **Título** del gráfico a **“Velocidad media, presión de los neumáticos, aceite del motor y temperatura del motor”**.

![Automóviles conectados - Campos de visualizaciones](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de rectángulos** en Visualizaciones, arrastre el campo **Model** al área **Grupo** y arrastre el campo **MaintenanceProbability** al área **Valores**.

Cambie el **Título** del gráfico a **“Modelos de vehículo que requieren mantenimiento”**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de barras 100 % apiladas** en Visualizaciones, arrastre el campo **city** al área **Eje** y arrastre los campos **MaintenanceProbability** y **RecallProbability** al área **Valor**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Haga clic en **Formato**, seleccione **Colores de datos** y cambie el color de **MaintenanceProbability** al valor **“F2C80F”**.

Cambie el **Título** del gráfico a **“Probabilidad de mantenimiento del vehículo y retirada por ciudad”**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Haga clic en el área en blanco para agregar una nueva visualización.

Seleccione la visualización **Gráfico de áreas** en Visualizaciones, arrastre el campo **Model** al área **Eje** y arrastre los campos **engineOil, tirepressure, speed y MaintenanceProbability** al área **Valor**. Cambie el tipo de agregación a **“Promedio”**.

![Automóviles conectados - Cambiar tipo de agregación](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Cambie el título del gráfico a **“Aceite del motor medio, presión de los neumáticos, velocidad y probabilidad de mantenimiento por modelo”**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Haga clic en el área en blanco para agregar una nueva visualización.

1. Seleccione la visualización **Gráfico de dispersión** en Visualizaciones
2. Arrastre el campo **Model** al área **Detalles** y **Leyenda**. 
3. Arrastre el campo **fuel** al área **Eje X** y cambie la agregación a Promedio.
4. Arrastre el campo **engineTemparature** al área **Eje Y** y cambie la agregación a **Promedio**.
5. Arrastre el campo **vin** al área **Tamaño**.

![Automóviles conectados - Agregar nueva visualización](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Cambie el **Título** del gráfico a **“Promedios de combustible y temperatura del motor por modelo”**.

![Automóviles conectados - Cambiar el título del gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

El informe final será similar al mostrado a continuación.

![Automóviles conectados - Informe final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### Anclaje de las visualizaciones desde los informes al panel en tiempo real.
  
Ancle la visualización desde los informes anteriores hasta el panel en tiempo real que hemos creado en la sección 2 - **Configuración del panel de Power BI**.

El panel debe tener el aspecto que se muestra a continuación.

![Automóviles conectados - Panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  Configuración del panel de procesamiento por lotes de Power BI

>[AZURE.ALERT]La ejecución de la canalización del procesamiento por lotes completa tarda unas 2 horas en finalizar aproximadamente. Espere antes de continuar con los siguientes pasos.

**Descargar el archivo del diseñador de PowerBI** • Se incluye un archivo de diseñador de PowerBI como parte de la implementación • Haga clic en el nodo PowerBI en la vista de diagrama y haga clic en vínculo ‘Descargar el archivo de diseñador de PowerBI’ en el panel de propiedades • Guarde localmente.

**Configurar informes de PowerBI ** • Abra el archivo de diseñador ‘VehicleTelemetryAnalytics - Desktop Report.pbix’ mediante PowerBI Desktop. Si todavía no lo tiene, instale PowerBI Desktop en - http://www.microsoft.com/es-ES/download/details.aspx?id=45331 • Haga clic en **Editar consultas**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]El **nombre y la contraseña** de su Base de datos SQL de Azure es la que proporcionó durante la configuración de implementación.

- Haga doble clic en **Origen**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Cadena de conexión del servidor de fechas
- Establezca **Base de datos** como *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Haga clic en **Aceptar**.
- Verá la pestaña **Credencial de Windows** seleccionada de forma predeterminada, cámbiela a **Credenciales de base de datos** haciendo clic en la pestaña **Base de datos** a la derecha.
- Proporcione la información pertinente en los campos **Nombre de usuario** y **Contraseña**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Haga clic en **Conectar**.
- Repita los pasos anteriores para las 3 consultas restantes del panel derecho y actualice los detalles de conexión del origen de datos.
- Haga clic en **Cerrar y cargar**. Los conjuntos de datos de archivos de Power BI Desktop se conectan a tablas de Base de datos SQL de Azure.
- **Cierre** el archivo de Power BI Desktop

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Haga clic en el botón **Guardar** para guardar los cambios. 
 
**Cargue en *powerbi.com***. Vaya a powerbi.com e inicie sesión. ![Telemetría del vehículo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	Haga clic en Obtener datos.  
2.	Cargue el archivo de Power BI Desktop.  
3.	Para cargar, haga clic en **Obtener datos -> Archivos Get -> Archivo local**.  
4.	Navegue a **“Connected Cars – Desktop Report.pbix”**.  
5.	Una vez cargado el archivo, volverá al espacio de trabajo de Power BI.  

	Se crearán un conjunto de datos, un informe y un panel en blanco.

	>**Nota:** puede que necesite crear un panel en blanco haciendo clic en el icono del signo más junto a Paneles.

**Anclar gráficos al panel** En Power BI, haga clic en el panel en blanco creado anteriormente, navegue a la sección Informes y haga clic en el informe recién cargado.

![Telemetría del vehículo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**Tenga en cuenta que el informe tiene seis páginas:** Página 1: densidad del vehículo Página 2: estado del vehículo en tiempo real Página 3: vehículos que se han conducido de forma agresiva Página 4: vehículos retirados Página 5: vehículos que se han conducido ahorrando combustible Página 6: logotipo de Contoso

![Automóviles conectados PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**En la página 3**, ancle lo siguiente: 1. Recuento de VIN ![Automóviles conectados PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2. Vehículos que se han conducido de forma agresiva - Gráfico de cascada ![Telemetría del vehículo - Anclar gráficos 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**En la página 5**, ancle lo siguiente: 1. Recuento de vin ![Telemetría del vehículo - Anclar gráficos 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. Vehículos con ahorro de combustible por modelo - Gráfico de columnas agrupadas ![Telemetría del vehículo - Anclar gráficos 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**En la página 4**, ancle lo siguiente:

1.	Recuento de vin ![Telemetría del vehículo - Anclar gráficos 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	Vehículos retirados por ciudad y modelo - Gráfico de rectángulos ![Telemetría del vehículo - Anclar gráficos 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**En la página 6**, ancle lo siguiente:

1.	Logotipo de Contoso Motors ![Telemetría del vehículo - Anclar gráficos 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organización del panel**

1.	Navegue al panel ![Telemetría del vehículo - Organizar panel 1](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	Mantenga el puntero sobre cada gráfico y cambie su nombre en función de la nomenclatura proporcionada en la siguiente imagen de panel completo. Asimismo, mueva los gráficos de un lado a otro hasta conseguir un aspecto del panel similar al siguiente. ![Telemetría del vehículo - Organizar panel 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![Telemetría del vehículo PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->