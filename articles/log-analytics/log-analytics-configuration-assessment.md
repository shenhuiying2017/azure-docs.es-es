<properties
	pageTitle="Solución de evaluación de la configuración en Log Analytics | Microsoft Azure"
	description="La solución de evaluación de la configuración de Log Analytics proporciona información detallada sobre el estado actual de su infraestructura de servidores de System Center Operations Manager cuando se usan agentes de Operations Manager o un grupo de administración de Operations Manager."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="banders"/>

# Solución de evaluación de la configuración de Log Analytics

La solución de evaluación de la configuración de Log Analytics le ayuda a buscar posibles problemas de configuración del servidor a través de alertas y recomendaciones de conocimiento.

Esta solución requiere System Center Operations Manager. La evaluación de la configuración no está disponible si solo usa agentes conectados directamente.

Para ver información en la solución de evaluación de la configuración se requiere el complemento Silverlight para su explorador.

>[AZURE.NOTE] La solución de evaluación de la configuración solo está disponible para áreas de trabajo de la región este de EE. UU., y no se agregará a otras regiones. Las capacidades de la solución de evaluación de la configuración se están reemplazando por evaluaciones específicas de la cargas de trabajo, incluida la evaluación de Active Directory y la evaluación de SQL Server.

![Icono de evaluación de la configuración](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Los datos de la configuración se recopilan de servidores supervisados y luego se envían al servicio de OMS en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Los datos procesados para los servidores se muestran para las siguientes áreas:

- **Alertas:** muestra las alertas proactivas relacionadas con la configuración que han surgido para los servidores supervisados. Estas alertas se generan a partir de reglas creadas por la organización de asistencia al cliente de Microsoft (CSS) con procedimientos recomendados del campo.
- **Recomendaciones de conocimiento**: muestra los artículos de Microsoft Knowledge Base recomendados para cargas de trabajo que se encuentran en su infraestructura; estos se sugieren automáticamente según la configuración mediante el uso del aprendizaje automático
- **Servidores y cargas de trabajo analizados**: muestra los servidores y las cargas de trabajo que supervisa OMS.

![Panel de evaluación de la configuración](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### Tecnologías que se pueden analizar con la evaluación de la configuración

La evaluación de la configuración de OMS analiza las cargas de trabajo siguientes:

- Windows Server 2012 y Microsoft Hyper-V Server 2012
- Windows Server 2008 y Windows Server 2008 R2, incluido:
    - Active Directory
	- Host de Hyper-V
	- Sistema operativo general
- SQL Server 2008 y versiones posteriores
    - Motor de base de datos de SQL Server
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 y Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 y Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

Para SQL Server, se admiten las siguientes ediciones de 32 bits y 64 bits para el análisis:

- SQL Server 2016: todas las ediciones
- SQL Server 2014: todas las ediciones
- SQL Server 2008 y 2008 R2: todas las ediciones

El motor de base de datos de SQL Server se analiza en todas las ediciones compatibles. Además, se admite la edición de 32 bits de SQL Server cuando se ejecuta en la implementación de WOW64.

## Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

- Se necesita Operations Manager para la solución de evaluación de la configuración.
- Debe tener un agente de Operations Manager en cada equipo en el que desee evaluar la configuración.
- Agregue la solución de evaluación de la configuración al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.

## Detalles de la recopilación de datos para la evaluación de la configuración

En la tabla siguiente se muestran los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para la evaluación de la configuración.

| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|
|Windows|![No](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Sí](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![No](./media/log-analytics-configuration-assessment/oms-bullet-red.png)| ![Sí](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Sí](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| dos veces al día|


## Alertas de evaluación de la configuración
Puede ver y administrar las alertas de la evaluación de la configuración con la página de alertas. Las alertas indican el problema que se detectó, la causa y el modo de solucionarlo. También proporcionan información sobre las opciones de configuración del entorno que podrían causar problemas de rendimiento.

![vista de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Las alertas de evaluación de la configuración son diferentes de las otras alertas de Log Analytics. Para ver las alertas se requiere un complemento de Silverlight para su explorador.

Al seleccionar un elemento o una categoría de elementos en la página de alertas, verá una lista de servidores o cargas de trabajo con las alertas que se aplican a cada elemento.

![lista de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Cada alerta incluye un vínculo a un artículo de Microsoft Knowledge Base. Estos artículos proporcionan información adicional acerca de la alerta.

>[AZURE.TIP] De forma predeterminada, el número máximo de alertas que se muestra es 2000. Para ver más alertas, haga clic en la barra de notificaciones encima de la lista de alertas.

Puede hacer clic en cualquier elemento de la lista para ver el artículo de KB que puede ayudarle a resolver la causa del problema que generó la alerta.

![artículo de KB](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Puede administrar reglas de alertas para omitir reglas específicas o una clase de reglas.

![administración de reglas de alerta](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## Recomendaciones de conocimiento
En las recomendaciones de conocimiento se le presentan los resultados de búsqueda de registros con los artículos de Microsoft KB recomendados para las cargas de trabajo y los equipos que proporcionan información adicional acerca de la alerta.

![resultados de la búsqueda de recomendaciones de conocimiento](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## Servidores y cargas de trabajo analizados
En las recomendaciones de conocimiento se le presentan los resultados de búsqueda de registros con todos los servidores y las cargas de trabajo que se conocen para OMS de Operations Manager.

![Servidores y cargas de trabajo](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## Pasos siguientes

- Utilice [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver los datos de la evaluación de la configuración detallados.

<!---HONumber=AcomDC_0518_2016-->