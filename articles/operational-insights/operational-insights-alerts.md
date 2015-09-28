<properties
   pageTitle="Visualización de alertas de Operations Manager"
   description="Aprenda cómo administrar las alertas de Operations Manager para los servidores supervisados en la infraestructura."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="banders" />



# Visualización de alertas de Operations Manager

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Antes de usar Administración de alertas en Visión operativa de Microsoft Azure, debe tener instalada la solución. Para obtener más información sobre cómo instalar soluciones, consulte [Configuración del área de trabajo](operational-insights-setup-workspace.md). La solución solo funciona cuando los agentes de Operations Manager supervisan los servidores. Consulte [Conexión a Visión operativa desde System Center Operations Manager](operational-insights-connect-scom.md) para obtener información sobre el uso de Operations Manager con Visión operativa.

Una vez instalada la solución, puede ver las alertas de los servidores supervisados mediante el icono de **Administración de alertas** en el panel **Información general** de Visión operativa.

![Imagen del icono Administración de alertas](./media/operational-insights-alerts/overview-alert.png)


Puede ver y administrar las alertas de Visión operativa de Microsoft Azure desde el panel **Alertas**. La información de las alertas en el panel se muestra dentro de las siguientes categorías:

- Alertas activas
	- Alertas críticas
	- Alertas de advertencia
	- Orígenes de alerta
- Todas las alertas activas
	- Muestra el porcentaje de alertas que son críticas, advertencias e informativas.
- Consultas de alertas comunes
	- Este apartado contiene consultas precompiladas creadas por el equipo de desarrollo de software de Visión operativa para ayudarle a empezar a usar las alertas.


Las alertas indican que se ha detectado un problema, el servidor al que afecta la alerta, la prioridad y el nombre de la alerta.

![imagen del panel de alertas](./media/operational-insights-alerts/alert-drilldown1.png)

![imagen del panel de alertas](./media/operational-insights-alerts/alert-drilldown2.png)


En el panel **Administración de alertas**, puede ver todas las alertas que ha encontrado Visión operativa de Microsoft Azure.

## Para ver las alertas de Visión operativa

1. En la página **Información general**, haga clic en el icono de **Administración de alertas**.
2. En el panel **Administración de alertas**, vea las categorías de alerta y elija una con la que trabajar.
3. Haga clic en un icono o en cualquier elemento para ver información detallada sobre él en la página **Buscar**.
4. Si usa la información encontrada, puede investigar la alerta y determinar las acciones adicionales que puede ser necesario realizar para resolverla.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=Sept15_HO3-->