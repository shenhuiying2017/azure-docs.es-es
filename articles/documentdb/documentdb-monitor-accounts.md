<properties
	pageTitle="Supervisión de las solicitudes y el almacenamiento de DocumentDB | Microsoft Azure"
	description="Obtenga información sobre cómo supervisar la cuenta de DocumentDB para aplicar métricas de rendimiento, como solicitudes y errores de servidor, y métricas de uso, como consumo de almacenamiento."
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="mimig"/>

# Supervisión de las solicitudes, el uso y el almacenamiento de DocumentDB

Puede supervisar las cuentas de Azure DocumentDB en el [Portal de Microsoft Azure](https://portal.azure.com/). Para cada cuenta de DocumentDB, existen métricas de rendimiento, como solicitudes y errores de servidor, y métricas de uso, como consumo de almacenamiento.

## Visualización de las métricas de rendimiento en el portal 
1.	En una nueva ventana, abra el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**, **Cuentas de DocumentDB** y luego en el nombre de la cuenta de DocumentDB de la que quiere ver métricas de rendimiento.
2.	En el modo **Supervisión** puede ver estos datos de forma predeterminada:
	*	El total de solicitudes del día actual.
	*	RU totales ([unidades de solicitud](documentdb-request-units.md)) usadas durante el día actual.
	*	Almacenamiento utilizado.

	Si en la tabla se muestra **Sin datos disponibles**, consulte la sección [Solución de problemas](#troubleshooting).

	![Captura de pantalla del modo Supervisión que muestra el total de solicitudes del día y el uso de almacenamiento](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Al hacer clic en el icono **Solicitudes**, **RU totales** o **Almacenamiento** se abre una hoja **Métrica** detallada.
4.	La hoja **Métrica** muestra los detalles sobre las métricas que ha seleccionado. En la parte superior de la hoja hay un gráfico de solicitudes representadas por hora y debajo una tabla que muestra los valores de agregación y las solicitudes totales. El cuadro Métrica muestra también la lista de alertas que se han definido, filtrada por las métricas que aparecen en el cuadro actual (de esta forma, si tiene un número de alertas, solo verá aquí las pertinentes).

	![Captura de pantalla de la hoja Métrica que incluye solicitudes limitadas](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personalización de las vistas de métricas de rendimiento en el portal

1.	Para personalizar las métricas que se muestran en un gráfico determinado, haga clic en el gráfico para abrirlo en la hoja **Métrica** y luego haga clic en **Editar gráfico**. ![Captura de pantalla de los controles de la hoja Métrica, con Editar gráfico resaltado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	En la hoja **Editar gráfico**, hay opciones para modificar las métricas que se muestran, junto con su intervalo de tiempo. ![Captura de pantalla de la hoja Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para cambiar las métricas que se muestran en el gráfico, solo tiene que marcar o desmarcar las métricas de rendimiento disponibles y luego hacer clic en **Aceptar** en la parte inferior de la hoja.
4.	Para cambiar el intervalo de tiempo, elija un intervalo distinto (por ejemplo, **Personalizado**) y haga clic en **Guardar** en la parte inferior de la hoja.

	![Captura de pantalla de la parte Intervalo de tiempo de la hoja Editar gráfico que muestra cómo especificar un intervalo de tiempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)


## Creación de gráficos en paralelo en el portal
El Portal de Azure le permite crear gráficos de métricas en paralelo.

1.	En primer lugar, haga clic con el botón derecho en el gráfico que quiere copiar y seleccione **Personalizar**.

	![Captura de pantalla del gráfico Total de solicitudes con el botón Personalizar destacado](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Haga clic en **Clonar** en el menú para copiar la parte y, a continuación, haga clic en ** Personalización efectuada**.

	![Captura de pantalla del gráfico Total de solicitudes con las opciones Clonar y Personalización efectuada destacadas](./media/documentdb-monitor-accounts/madocdb7.png)


Ahora puede tratar esta parte como otra parte de métricas y personalizar las métricas y el intervalo de tiempo que se muestra en la parte. De esta forma, puede ver dos gráficos de métricas diferentes en paralelo al mismo tiempo. ![Captura de pantalla del gráfico Total de solicitudes y el nuevo gráfico Hora pasada del total de solicitudes.](./media/documentdb-monitor-accounts/madocdb8.png)

## Configuración de alertas en el portal
1.	En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**, **Cuentas de DocumentDB** y luego haga clic en el nombre de la cuenta de DocumentDB cuyas alertas de métricas de rendimiento desee configurar.

2.	Si la hoja **Toda la configuración** no está abierta, haga clic en el comando **Configuración** en la parte superior izquierda para abrir la hoja. ![Captura de pantalla del comando de configuración en la hoja de la cuenta de base de datos](./media/documentdb-monitor-accounts/madocdb10.png)

3.	En la hoja **Toda la configuración**, haga clic en **Reglas de alerta** para abrir la hoja Reglas de alerta. ![Captura de pantalla de la parte de reglas de alerta seleccionada](./media/documentdb-monitor-accounts/madocdb10.5.png)

4.	En la hoja Reglas de alerta, haga clic en **Agregar alerta**. ![Captura de pantalla de la hoja Reglas de alerta, con el botón Agregar alerta seleccionado](./media/documentdb-monitor-accounts/madocdb11.png)

5.	En la hoja **Agregar una regla de alerta**, especifique:
	*	El nombre de la regla de alerta que va a configurar.
	*	Una descripción de la nueva regla de alerta.
	*	La métrica de la regla de alerta.
	*	La condición, el umbral y el período que determinan cuándo se activa la alerta. Por ejemplo, un número de errores de servidor mayor que cinco durante los últimos 15 minutos.
	*	Si se envía un correo electrónico al administrador del servicio y a los coadministradores cuando la alerta de dispara.
	*	Direcciones de correo electrónico adicionales para las notificaciones de alerta. ![Captura de pantalla de la hoja Agregar una regla de alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## Supervisión de DocumentDB mediante programación
Las métricas de nivel de cuenta disponibles en el portal, como el uso de almacenamiento de cuenta y el total de solicitudes, no están disponibles mediante las API de DocumentDB. Sin embargo, puede recuperar datos de uso en el nivel de colección mediante las API de DocumentDB. Para recuperar datos de nivel de colección, haga lo siguiente:

- Para usar la API de REST, [ejecute una operación GET en la colección](https://msdn.microsoft.com/library/mt489073.aspx). La información de cuota y uso de la colección se devuelve en los encabezados x-ms-resource-quota y x-ms-resource-usage de la respuesta.
- Para usar el SDK de .NET, use el método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que devuelve un objeto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contiene varias propiedades de uso, como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** y otras más.

Para acceder a métricas adicionales, use el [SDK de Azure Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights). Se pueden recuperar definiciones de métricas mediante la llamada a:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08 

Las consultas para recuperar métricas individuales utilizan el siguiente formato:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para más información, consulte [Retrieving Resource Metrics via the Azure Insights API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/) (Recuperación de métricas de recursos mediante la API de Azure Insights).

## Solución de problemas
Si los iconos de supervisión muestran el mensaje **Sin datos disponibles** y recientemente se realizaron solicitudes o se agregaron datos a la base de datos, puede editar el icono para que refleje el uso reciente.

### Edición de un icono para actualizar los datos actuales
1.	Para personalizar las métricas que se muestran en una parte determinada, haga clic en el gráfico para abrir la hoja **Métrica** y luego haga clic en **Editar gráfico**. ![Captura de pantalla de los controles de la hoja Métrica, con Editar gráfico resaltado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	En la hoja **Editar gráfico**, en la sección **Intervalo de tiempo**, haga clic en **última hora** y luego en **Aceptar**. ![Captura de pantalla de la hoja Editar gráfico con la última hora seleccionada](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	El icono debería actualizarse y mostrar los datos y el uso actuales. ![Captura de pantalla del icono actualizado de última hora de Total de solicitudes](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Pasos siguientes
Para obtener más información acerca de la capacidad de DocumentDB, consulte [Administración de la capacidad de DocumentDB](documentdb-manage.md).

<!---HONumber=AcomDC_0720_2016-->