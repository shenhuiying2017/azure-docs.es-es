<properties 
	pageTitle="Uso de diagnósticos" 
	description="Obtenga información acerca de la configuración del diagnóstico de los recursos en Azure." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>

# Configuración de diagnóstico

En el Portal de vista previa de Azure, puede configurar los datos frecuentes, enriquecidos, de diagnóstico y supervisión, sobre cuentas de almacenamiento y máquinas virtuales de Windows.

## Recopilar datos enriquecidos de las máquinas virtuales
1. En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar** y luego en **Máquinas virtuales**. Seleccione la máquina virtual que quiere supervisar.
2. El modo **Supervisión** contiene algunas métricas predeterminadas, como **Porcentaje de CPU**, **Lectura y escritura de disco** y **Conexión y desconexión de red**. Al hacer clic en cualquiera de estos apartados, aparece la hoja **Métrica**.  
    ![Monitoring lens](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3. La hoja **Métrica** le muestra detalles acerca de las métricas que seleccione. En la parte superior del cuadro hay un gráfico y, debajo, una tabla que muestra una recopilación de dichas métricas, como el promedio, el valor máximo y el mínimo. Debajo de la tabla hay una lista de las alertas que ha definido, filtradas por las métricas que aparecen en la hoja.  
    ![Metric blade](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4. Para habilitar el diagnóstico enriquecido, haga clic en el botón **Configuración** y aparecerá la hoja **Diagnóstico**. Elija **Activado**:  
    ![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)
    - **Métricas básicas**: métricas de mantenimiento de la máquina virtual (como las relacionadas con el procesador y la memoria). 
    - **Métricas por disco**: métricas de todos los discos acoplados a la máquina virtual.
    - **Métricas .NET**: métricas de las aplicaciones .NET y ASP.NET que se ejecutan en la máquina virtual.
    - **Métricas de red**: métricas de los servicios web y las conexiones de red.
    - **Registros de aplicaciones de eventos de Windows**: eventos de Windows que se envían al canal de la aplicación.
    - **Registros del sistema de eventos de Windows**: eventos de Windows que se envían al canal del sistema. También incluye todos los eventos de [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409). 
    - **Registros de seguridad de eventos de Windows**: eventos de Windows que se envían al canal de seguridad.
    - **Registros de infraestructura de diagnóstico**: registros de la infraestructura de recopilación de diagnósticos.
    - **Registros de IIS**: registros acerca del servidor de IIS.
    todos los registros y las métricas se registran una vez por minuto para que siempre disponga de la información más actualizada sobre la máquina.

Cuando se habilita la información de diagnóstico para una cuenta de almacenamiento, se cargan en dicha cuenta los costes habituales de salida, transacción y almacenamiento. En cualquier caso y con la posible excepción de los registros de IIS, estas características no producen grandes volúmenes de datos. Para minimizar los costes de salida, seleccione una cuenta de almacenamiento en la misma región en la que se encuentra la máquina virtual.

Una vez que hace clic en **Aceptar**, los datos comienzan a aparecer en la cuenta de almacenamiento a los pocos minutos. No se puede habilitar el diagnóstico de máquinas virtuales que ejecutan Linux. Para habilitarlo, instale el agente invitado.

## Recopilar datos enriquecidos de las cuentas de almacenamiento

Aunque siempre se pudieron recopilar ciertos datos de las cuentas de almacenamiento, en el Portal de vista previa de Azure ahora puede recopilar datos con una granularidad de un minuto, para conocer de forma fiable lo que ocurre en la cuenta de almacenamiento. Los pasos necesarios para habilitar las métricas de un minuto son similares a los de las máquinas virtuales:

1. Para ir a la hoja **Métrica**, haga clic en cualquiera de los gráficos de la hoja **Cuenta de almacenamiento**.
2. Haga clic en el botón **Diagnóstico** de la barra de comandos.
3. Seleccione los datos que quiere recopilar de la cuenta de almacenamiento:  
    ![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Haga clic en **Aceptar**. Los datos tardarán algunos minutos en aparecer por primera vez.

## Visualizar los datos de diagnóstico 

Tras habilitar el diagnóstico, si quiere ver una lista completa de las métricas disponibles, haga clic con el botón secundario en cualquiera de los gráficos y vaya a **Editar consulta**:

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

Puede trazar estas métricas y restringirlas hasta la **hora anterior**, ampliarlas hasta la **semana pasada**, o incluso elegir un intervalo de tiempo **personalizado**:
 
![Custom timerange](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Como puede ver, estas métricas son bastante más granulares que los datos de los que podía disponer anteriormente y el tiempo de retardo es mínimo.

En este momento no se pueden trazar métricas con varias instancias, como las métricas por disco o proceso. Para obtener más información sobre cómo personalizar gráficos de supervisión, consulte [Personalización de la supervisión](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Alertar de los datos de diagnóstico

Además de visualizar las métricas, puede emitir alertas de cada una de ellas en el Portal de vista previa. En primer, desplácese hacia abajo hasta el apartado **Reglas de alertas** de la máquina virtual o la hoja de almacenamiento y haga clic en **Agregar alerta**:

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Ya puede seleccionar las métricas que ha habilitado para el diagnóstico:

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

El gráfico muestra una vista previa del umbral de alerta en comparación con la métrica del día anterior. Después de hacer clic en **Guardar**, se le informará si la métrica que eligió excede el umbral. 

Tenga en cuenta que en la versión íntegra del portal no se pueden emitir alertas de las métricas que solo aparecen en el Portal de vista previa. Como resultado, algunas reglas de alerta del Portal de vista previa no se muestran en la versión íntegra del portal.

<!--HONumber=46--> 

<!--HONumber=46--> 
 