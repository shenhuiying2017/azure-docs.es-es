<properties
   pageTitle="Conexión a Vista operativa desde System Center Operations Manager"
   description="Obtenga información acerca de cómo conectarse a Visión operativa a través de Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# Conexión a Vista operativa desde System Center Operations Manager


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede conectar Vista operativa a un entorno de System Center Operations Manager existente. Esto le permitirá utilizar agentes de Operations Manager existentes para la recopilación de datos. Para obtener información adicional sobre el uso de Operations Manager con Visión operativa, consulte [Consideraciones sobre Operations Manager con Visión operativa](operational-insights-operations-manager.md).

Si usa Operations Manager para supervisar cualquiera de las siguientes cargas de trabajo, deberá establecer las cuentas de ejecución de Operations Manager para ellas. Consulte [Consideraciones sobre Operations Manager con Visión operativa](operational-insights-operations-manager.md) para obtener más información sobre la configuración de las cuentas.

- Evaluación de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]La compatibilidad con Vista operativa está disponible a partir de Operations Manager 2012 SP1 UR6 y Operations Manager 2012 R2 UR2. Se agregó compatibilidad con proxy en UR7 de System Center Operations Manager 2012 SP1 y System Center Operations Manager 2012 R2 UR3.

## Para conectar Operations Manager a Vista operativa y agregar agentes

1. En la consola de Operations Manager, haga clic en **Administración**.

2. Expanda el nodo **Visión operativa** y haga clic en **Conexión a Visión operativa**.

3. Haga clic en el vínculo **Registrar en Visión operativa** y siga las instrucciones en pantalla.

4. Después de completar el asistente de registro, haga clic en **Agregar un equipo/grupo**. ![Operations Manager agregar un grupo de equipos](./media/operational-insights-connect-scom/om01.png)
5. En el cuadro de diálogo **Búsqueda de equipos** puede seleccionar los equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos que incorporar a Visión operativa, haga clic en **Agregar** y, a continuación, haga clic en **Aceptar**. ![Operations Manager agregar equipos](./media/operational-insights-connect-scom/om02.png)
## Pasos siguientes

[Configuración de los ajustes del proxy y del firewall (opcional)](operational-insights-proxy-firewall.md)

<!---HONumber=August15_HO6-->