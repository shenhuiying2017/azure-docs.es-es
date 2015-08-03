<properties
	pageTitle="Guía de características de Visión operativa"
	description="Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# Guía de características de Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Esta guía le ayuda a comprender los problemas que Visión operativa puede ayudarle a resolver, de qué consta el entorno de Visión operativa y cómo puede crear una cuenta de Visión operativa e iniciar sesión en el servicio.

## Transformar datos de la máquina

Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos.

Con Visión operativa, puede transformar datos de máquina en inteligencia operativa con las características siguientes.


|**Icono** | **Lo que debe hacer** | **Qué hace**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Planificación de capacidad](operational-insights-capacity.md) | Puede utilizar la solución Planificación de capacidad en Visión operativa de Microsoft Azure para ayudarle a entender la capacidad de su infraestructura de servidor. |
| ![](./media/operational-insights-feature-guide/update.png) | [Evaluación de la actualización del sistema](operational-insights-updates.md) | Puede usar la solución de actualizaciones del sistema de Microsoft Azure Operational Insights para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura. |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [Administración de registros](operational-insights-search.md) | Puede usar la solución de administración de registros para recopilar eventos y registros de IIS para las búsquedas de registro en Visión operativa. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Evaluación de malware](operational-insights-antimalware.md) | Puede usar la solución Antimalware incluida en Visión operativa de Microsoft Azure para proteger los servidores de su infraestructura frente al malware. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Seguridad y auditoría](operational-insights-security-audit.md) | Puede usar la solución Seguridad y auditoría para conseguir una vista completa de la postura de seguridad de TI de su organización con consultas de búsqueda integradas para problemas importantes que requieren su atención |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Evaluación de SQL y Active Directory](operational-insights-assessment.md) | Puede usar periódicamente soluciones de evaluación para evaluar el riesgo y el estado de los entornos de servidor. |
| ![](./media/operational-insights-feature-guide/alert.png) | [Administración de alertas](operational-insights-alerts.md) | Puede utilizar la solución Administración de alertas para administrar las alertas servidores supervisados por Operations Manager. |


También puede:

- **Envío de datos de máquina al sistema con o sin usar un agente o junto con System Center Operations Manager**. Para más información, consulte:
	- [Conexión a Visión operativa desde System Center Operations Manager](operational-insights-connect-scom.md)
	- [Conexión de equipos directamente en Visión operativa](operational-insights-direct-agent.md)
	- [Análisis de datos de servidores en Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Hacer todo lo anterior sobre la marcha con la aplicación móvil**
	- Para obtener más información acerca de la aplicación de Windows Phone, consulte [Aplicación móvil de Visión operativa](http://www.windowsphone.com/es-es/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)

## Entorno de Visión operativa

El entorno de Visión operativa se compone de:

- Áreas de trabajo hospedadas por Microsoft Azure que son contenedores para las cuentas de Azure
- el servicio web de Visión operativa, que se hospeda en la nube
- cualquiera de los agentes independientes que se conectan directamente al servicio web
- o bien, un servicio adjunto a System Center Operations Manager, pero no es necesario


Si ha usado la versión anterior de Visión operativa denominada System Center Advisor, podría tener software de Advisor instalado en su entorno local. Sin embargo, el software de Advisor no es compatible con Visión operativa.

Mediante el software de Visión operativa como un servicio de Operations Manager consta de uno o varios grupos de administración y al menos un agente por grupo de administración. Los agentes de Operations Manager recopilan datos de los servidores y los analizan mediante soluciones (similares a un paquete de administración de System Center Operations Manager). Los datos se envían periódicamente de Operations Manager para el servicio web de información operativa (si fuera necesario, pasando a través de un servidor proxy), sin nada que se almacenan en cualquiera de las bases de datos de Operations Manager, así que no hay ninguna carga adicional coloca en ellos.

De forma similar, los agentes instalados en equipos individuales pueden conectarse directamente al servicio web para enviar los datos recopilados para el procesamiento.

Los datos de cada solución se analizan y se presentan en el portal de Visión operativa. Puede ver las alertas y las orientaciones sobre correcciones asociadas, las evaluaciones de configuración, los problemas de capacidad de infraestructura, el estado de actualización del sistema, las advertencias sobre antimalware y los datos de registro. También puede realizar búsquedas de registro detalladas ad-hoc y exploraciones.

![Imagen del diagrama de información general de Visión operativa](./media/operational-insights-feature-guide/environment.png)

### ¿Dónde está disponible Visión operativa?
Visión operativa de Microsoft Azure se hospeda en Estados Unidos. Aunque el idioma de Visión operativa es el inglés, el servicio está disponible en otros mercados. Para obtener información, consulte [Disponibilidad internacional](http://go.microsoft.com/fwlink/?LinkId=229842).

<!---HONumber=July15_HO4-->