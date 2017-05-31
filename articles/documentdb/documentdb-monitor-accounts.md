---
title: "Supervisión de las solicitudes y el almacenamiento de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo supervisar la cuenta de Azure Cosmos DB para aplicar métricas de rendimiento, como solicitudes y errores de servidor, y métricas de uso, como consumo de almacenamiento."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b0158d0f9f0a8f66ae6dda44476efb7b1914c599
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="monitor-azure-cosmos-db-requests-usage-and-storage"></a>Supervisión de las solicitudes, uso y almacenamiento de Azure Cosmos DB
Puede supervisar las cuentas de Azure Cosmos DB en [Azure Portal](https://portal.azure.com/). Para cada cuenta de Azure Cosmos DB, existen métricas de rendimiento, como solicitudes y errores de servidor, y métricas de uso, como consumo de almacenamiento.

Las métricas pueden revisarse en la hoja Cuenta, en la nueva hoja Métricas o en Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-blade"></a>Visualización de las métricas de rendimiento en la hoja Métricas
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Más servicios**, desplácese a **Bases de datos**, seleccione **Azure Cosmos DB** y, luego, haga clic en el nombre de la cuenta de Cosmos DB de la que quiere ver métricas de rendimiento.
2. En el menú de recursos, en **Supervisión**, haga clic en **Métricas**.

Se abre la hoja Métricas y podrá seleccionar la colección para revisarla. Puede revisar las métricas de disponibilidad, solicitudes, rendimiento y almacenamiento, además de compararlas con los SLA de Azure Cosmos DB.

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Visualice las métricas de rendimiento mediante el uso de supervisión de Azure
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Supervisar** en la barra de salto.
2. En el menú de recursos, haga clic en **Métricas**.
3. En la ventana **Supervisar: métricas**, en el menú de lista desplegable **Grupo de recursos**, seleccione el grupo de recursos asociado a la cuenta de Azure Cosmos DB que le gustaría supervisar. 
4. En el menú de lista desplegable **Recursos**, seleccione la base de datos de la cuenta que desee supervisar.
5. En la lista de **Métricas disponibles**, seleccione las métricas que desee mostrar. Utilice la tecla CTRL para seleccionar varios elementos. 

    Las métricas se muestran en la ventana **trazar**. 

## <a name="view-performance-metrics-on-the-account-blade"></a>Visualización de métricas de rendimiento en la hoja Cuenta
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Más servicios**, desplácese a **Bases de datos**, seleccione **Azure Cosmos DB** y, luego, haga clic en el nombre de la cuenta de Cosmos DB de la que quiere ver métricas de rendimiento.
2. La lente **Supervisión** muestra los iconos siguientes de forma predeterminada:
   
   * El total de solicitudes del día actual.
   * Almacenamiento utilizado.
   
   Si se muestra en la tabla **No hay datos disponibles** y cree que hay datos en la base de datos, vea la sección [Solución de problemas](#troubleshooting) .
   
   ![Captura de pantalla del modo Supervisión que muestra las solicitudes y el uso de almacenamiento](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)
3. Al hacer clic en el icono **Solicitudes** o **Cuota de uso** se abre una hoja detallada denominada **Métrica**.
4. La hoja **Métrica** muestra los detalles sobre las métricas que ha seleccionado.  En la parte superior de la hoja hay un gráfico de solicitudes representadas por hora y debajo una tabla que muestra los valores de agregación y las solicitudes totales.  El cuadro Métrica muestra también la lista de alertas que se han definido, filtrada por las métricas que aparecen en el cuadro actual (de esta forma, si tiene un número de alertas, solo verá aquí las pertinentes).   
   
   ![Captura de pantalla de la hoja Métrica que incluye solicitudes limitadas](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)

## <a name="customize-performance-metric-views-in-the-portal"></a>Personalización de las vistas de métricas de rendimiento en el portal
1. Para personalizar las métricas que se muestran en un gráfico determinado, haga clic en el gráfico para abrirlo en la hoja **Métrica** y luego haga clic en **Editar gráfico**.  
   ![Captura de pantalla de los controles de la hoja Métrica, con Editar gráfico resaltado](./media/documentdb-monitor-accounts/madocdb3.png)
2. En la hoja **Editar gráfico** , hay opciones para modificar las métricas que se muestran, junto con su intervalo de tiempo.  
   ![Captura de pantalla de la hoja Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)
3. Para cambiar las métricas que se muestran en el gráfico, solo tiene que marcar o desmarcar las métricas de rendimiento disponibles y luego hacer clic en **Aceptar** en la parte inferior de la hoja.  
4. Para cambiar el intervalo de tiempo, elija un intervalo distinto (por ejemplo, **Personalizado**) y haga clic en **Aceptar** en la parte inferior de la hoja.  
   
   ![Captura de pantalla de la parte Intervalo de tiempo de la hoja Editar gráfico que muestra cómo especificar un intervalo de tiempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)

## <a name="create-side-by-side-charts-in-the-portal"></a>Creación de gráficos en paralelo en el portal
El Portal de Azure le permite crear gráficos de métricas en paralelo.  

1. En primer lugar, haga clic con el botón derecho en el gráfico que quiere copiar y seleccione **Personalizar**.
   
   ![Captura de pantalla del gráfico Total de solicitudes con el botón Personalizar destacado](./media/documentdb-monitor-accounts/madocdb6.png)
2. Haga clic en **Clonar** en el menú para copiar la parte y, a continuación, haga clic en **Personalización efectuada**.
   
   ![Captura de pantalla del gráfico Total de solicitudes con las opciones Clonar y Personalización efectuada destacadas](./media/documentdb-monitor-accounts/madocdb7.png)  

Ahora puede tratar esta parte como otra parte de métricas y personalizar las métricas y el intervalo de tiempo que se muestra en la parte.  De esta forma, puede ver dos gráficos de métricas diferentes en paralelo al mismo tiempo.  
    ![Captura de pantalla del gráfico Total de solicitudes y el nuevo gráfico Hora pasada del total de solicitudes.](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a name="set-up-alerts-in-the-portal"></a>Configuración de alertas en el portal
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Más servicios**, desplácese a **Azure Cosmos DB** y, luego, haga clic en el nombre de la cuenta de Azure Cosmos DB de la que quiere ver métricas de rendimiento.
2. En el menú de recursos, haga clic en **Reglas de alerta** para abrir la hoja Reglas de alerta.  
   ![Captura de pantalla de la parte de reglas de alerta seleccionada](./media/documentdb-monitor-accounts/madocdb10.5.png)
3. En la hoja **Reglas de alerta**, haga clic en **Agregar alerta**.  
   ![Captura de pantalla de la hoja Reglas de alerta, con el botón Agregar alerta seleccionado](./media/documentdb-monitor-accounts/madocdb11.png)
4. En la hoja **Agregar una regla de alerta** , especifique:
   
   * El nombre de la regla de alerta que va a configurar.
   * Una descripción de la nueva regla de alerta.
   * La métrica de la regla de alerta.
   * La condición, el umbral y el período que determinan cuándo se activa la alerta. Por ejemplo, un número de errores de servidor mayor que cinco durante los últimos 15 minutos.
   * Si se envía un correo electrónico al administrador del servicio y a los coadministradores cuando la alerta de dispara.
   * Direcciones de correo electrónico adicionales para las notificaciones de alerta.  
     ![Captura de pantalla de la hoja Agregar una regla de alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programatically"></a>Supervisión de Azure Cosmos DB mediante programación
Las métricas de nivel de cuenta disponibles en el portal, como el uso de almacenamiento de cuenta y el total de solicitudes, no están disponibles mediante las API de DocumentDB. Sin embargo, puede recuperar datos de uso en el nivel de colección mediante las API de DocumentDB. Para recuperar datos de nivel de colección, haga lo siguiente:

* Para usar la API de REST, [ejecute una operación GET en la colección](https://msdn.microsoft.com/library/mt489073.aspx). La información de cuota y uso de la colección se devuelve en los encabezados x-ms-resource-quota y x-ms-resource-usage de la respuesta.
* Para usar el SDK de .NET, use el método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que devuelve un objeto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contiene varias propiedades de uso, como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** y otras más.

Para acceder a métricas adicionales, use el [SDK de Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Se pueden recuperar definiciones de métricas mediante la llamada a:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Las consultas para recuperar métricas individuales utilizan el siguiente formato:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Para obtener más información, consulte [Retrieving Resource Metrics via the Azure Monitor API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)(Recuperación de métricas de recursos mediante la API de Azure Monitor). Tenga en cuenta que se ha cambiado el nombre "Azure Inights" a "Azure Monitor".  Esta entrada de blog hace referencia al nombre anterior.

## <a name="troubleshooting"></a>Solución de problemas
Si los iconos de supervisión muestran el mensaje **Sin datos disponibles** y recientemente se realizaron solicitudes o se agregaron datos a la base de datos, puede editar el icono para que refleje el uso reciente.

### <a name="edit-a-tile-to-refresh-current-data"></a>Edición de un icono para actualizar los datos actuales
1. Para personalizar las métricas que se muestran en una parte determinada, haga clic en el gráfico para abrir la hoja **Métrica** y luego haga clic en **Editar gráfico**.  
   ![Captura de pantalla de los controles de la hoja Métrica, con Editar gráfico resaltado](./media/documentdb-monitor-accounts/madocdb3.png)
2. En la hoja **Editar gráfico**, en la sección **Intervalo de tiempo**, haga clic en **última hora** y luego en **Aceptar**.  
   ![Captura de pantalla de la hoja Editar gráfico con la última hora seleccionada](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)
3. El icono debería actualizarse y mostrar los datos y el uso actuales.  
   ![Captura de pantalla del icono actualizado de última hora de Total de solicitudes](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## <a name="next-steps"></a>Pasos siguientes
Para conocer más sobre el planeamiento de la capacidad de Azure Cosmos DB, consulte la [calculadora del planificador de capacidad de Azure Cosmos DB](https://www.documentdb.com/capacityplanner).


