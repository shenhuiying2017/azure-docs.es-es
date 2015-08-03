<properties
    pageTitle="Soluciones de Visión operativa"
    description="Para agregar funcionalidad adicional a Visión operativa, puede usar las soluciones."
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Soluciones de Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Visión operativa de Microsoft Azure incluye el módulo base de evaluación de la configuración. Sin embargo, puede obtener una mayor funcionalidad si le agrega soluciones desde la página de información general.

![imagen del icono de soluciones](./media/operational-insights-add-solution/sol-gallery.png)

Tras haber agregado una solución, se recopilan datos de los servidores de la infraestructura y se envían al servicio Visión operativa. El procesamiento por parte del servicio Visión operativa puede tardar desde unos minutos hasta varias horas. Una vez que el servicio ha procesado los datos, podrá verlos en Visión operativa.

Es posible quitar fácilmente una solución cuando ya no es necesaria. Al quitar una solución, sus datos no se enviarán a Visión operativa, lo que reduce la cantidad de datos usados por la cuota diaria.

## Soluciones admitidas por Microsoft Monitoring Agent

En este momento, los servidores conectados directamente a Visión operativa de Microsoft Azure mediante Microsoft Monitoring Agent pueden usar la mayoría de las soluciones disponibles, incluidas las siguientes:

- [Actualizaciones del sistema](operational-insights-updates.md)

- [Administración de registros](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Seguimiento de cambios](operational-insights-change-tracking.md)

- [Evaluación de SQL y Active Directory](operational-insights-assessment.md)

Sin embargo, las siguientes soluciones *no* son compatibles con Microsoft Monitoring Agent y requieren System Center Operations Manager (SCOM).

- [Administración de la capacidad](operational-insights-capacity.md)

- [Administración de alertas](operational-insights-alerts.md)

- [Evaluación de la configuración](operational-insights-solutions.md#configuration-assessment)

Consulte [Consideraciones de Operations Manager con Visión operativa](operational-insights-operations-manager.md) para obtener instrucciones sobre el uso de estas soluciones con Operations Manager.

La colección de registros de IIS se admite en equipos con:

- Windows Server 2012

- Windows Server 2012 R2

### Para agregar una solución


1. En la página de información general de Visión operativa, haga clic en el icono **Galería de soluciones**.


2. En la página Galería de soluciones de Visión operativa, puede obtener información acerca de cada una de las soluciones disponibles. Haga clic en el nombre de la solución que desea agregar a Visión operativa.


3. En la página de la solución que eligió, aparecerá información detallada sobre la solución. Haga clic en **Agregar**.


4. En la página de confirmación, haga clic en **Aceptar** para aceptar la declaración de privacidad y las condiciones de uso.


5. En la página de información general de Visión operativa aparece un icono nuevo para la solución que ha agregado y puede empezar a usarlo una vez que el servicio mencionado procese los datos.




### Para quitar una solución



1. En la página de información general de Visión operativa, haga clic en el icono **Galería de soluciones**.


2. En la página Galería de soluciones de Visión operativa, haga clic en **Quitar** bajo la solución que desea quitar.


3. En la página de confirmación, haga clic en **Sí** para quitar la solución.

<!---HONumber=July15_HO4-->