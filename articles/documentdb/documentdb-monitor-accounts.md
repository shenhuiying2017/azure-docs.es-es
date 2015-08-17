<properties 
	pageTitle="Supervisión de una cuenta de DocumentDB mediante el Portal de vista previa de Azure | Microsoft Azure" 
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
	ms.date="07/29/2015" 
	ms.author="mimig"/>

# Supervisión de una cuenta de DocumentDB mediante el Portal de vista previa de Azure 

Puede supervisar las cuentas de DocumentDB en el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/). Para cada cuenta de DocumentDB, existen métricas de rendimiento, como solicitudes y errores de servidor, y métricas de uso, como consumo de almacenamiento.

## Visualización de las métricas de rendimiento para una cuenta de DocumentDB
1.	En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar todo**, **Cuentas de DocumentDB** y luego haga clic en el nombre de la cuenta de DocumentDB cuyas métricas de rendimiento desee ver.
2.	En el modo **Supervisión** puede ver estos datos de forma predeterminada:
	*	El total de solicitudes del día actual.
	*	La media de solicitudes por segundo del día actual. 
	
	![Captura de pantalla del modo Supervisión](./media/documentdb-monitor-accounts/madocdb1.png)


3.	Al hacer clic en **Total de solicitudes** o **Promedio de solicitudes por segundo**, se abre una hoja detallada denominada **Métrica**.
4.	La hoja **Métrica** muestra los detalles sobre las métricas que ha seleccionado. En la parte superior de la hoja hay un gráfico y, debajo, una tabla que muestra los valores de agregación de las métricas seleccionadas, como el promedio o el valor máximo y el mínimo. El cuadro Métrica muestra también la lista de alertas que se han definido, filtrada por las métricas que aparecen en el cuadro actual (de esta forma, si tiene un número de alertas, solo verá aquí las pertinentes).   

	![Captura de pantalla de la hoja Métrica](./media/documentdb-monitor-accounts/madocdb2.png)


## vistas de métricas de rendimiento para una cuenta de Base de datos de documentos

1.	Para personalizar las métricas que se muestran en una determinada parte, haga clic con el botón derecho en el gráfico de métricas y, a continuación, elija **Editar gráfico**. ![Captura de pantalla del gráfico Total de solicitudes con el botón Editar gráfico destacado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	En la hoja **Editar gráfico**, hay opciones para modificar las métricas que se muestran en esa parte, junto con su intervalo de tiempo. ![Captura de pantalla de la hoja Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para cambiar las métricas que se muestran en la parte, solo tiene que marcar o desmarcar las métricas de rendimiento disponibles y luego hacer clic en **Guardar** en la parte inferior de la hoja.
4.	Para cambiar el intervalo de tiempo, elija un intervalo distinto (por ejemplo, **Personalizado**) y haga clic en **Guardar** en la parte inferior de la hoja.  

	![Captura de pantalla de la parte Intervalo de tiempo de la hoja Editar gráfico que muestra cómo especificar un intervalo de tiempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)


## Creación de gráficos de métricas de rendimiento paralelos
El Portal de vista previa de Azure le permite crear gráficos de métricas en paralelo.

1.	En primer lugar, haga clic con el botón derecho en el gráfico que desea clonar y modificar. Después, seleccione **Personalizar**. 

	![Captura de pantalla del gráfico Total de solicitudes con el botón Personalizar destacado](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Haga clic en **Clonar** en el menú para copiar la parte y, a continuación, haga clic en ** Personalización efectuada**.

	![Captura de pantalla del gráfico Total de solicitudes con las opciones Clonar y Personalización efectuada destacadas](./media/documentdb-monitor-accounts/madocdb7.png)


Ahora puede tratar esta parte como otra parte de métricas y personalizar las métricas y el intervalo de tiempo que se muestra en la parte. De esta forma, puede ver dos gráficos de métricas diferentes en paralelo al mismo tiempo. ![Captura de pantalla del gráfico Total de solicitudes y el nuevo gráfico Hora pasada del total de solicitudes.](./media/documentdb-monitor-accounts/madocdb8.png)

## métricas de uso para una cuenta de Base de datos de documentos
1.	En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Explorar**, **Cuentas de DocumentDB** y luego haga clic en el nombre de la cuenta de DocumentDB cuyas métricas de uso desee ver.
2.	En el modo **Uso**, puede ver la siguiente información de forma predeterminada:
	*	Costo estimado a la fecha de la cuenta de DocumentDB correspondiente al período de facturación actual.
	*	Almacenamiento consumido en la cuenta
	*	Máximo almacenamiento disponible en la cuenta (umbral)
	*	Usuario y permiso de uso
	*	Uso de datos adjuntos

	![Captura de pantalla del modo Uso](./media/documentdb-monitor-accounts/madocdb9.png)
 
## alertas de métricas de rendimiento para una cuenta de Base de datos de documentos
1.	En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar todo**, **Cuentas de DocumentDB** y luego haga clic en el nombre de la cuenta de DocumentDB para la que desea configurar alertas de métricas de rendimiento.
2.	En el modo **Operaciones**, haga clic en la parte **Reglas de alerta**. ![Captura de pantalla del modo Operaciones, con la parte Reglas de alerta seleccionada](./media/documentdb-monitor-accounts/madocdb10.png)

3.	En la hoja Reglas de alerta, haga clic en **Agregar alerta**. ![Captura de pantalla de la hoja Reglas de alerta, con el botón Agregar alerta seleccionado](./media/documentdb-monitor-accounts/madocdb11.png)

4.	En la hoja **Agregar una regla de alerta**, especifique:
	*	El nombre de la regla de alerta que va a configurar.
	*	Una descripción de la nueva regla de alerta.
	*	La métrica de la regla de alerta.
	*	La condición, el umbral y el período que determinan cuándo se activa la alerta. Por ejemplo, un número de errores de servidor mayor que cinco durante los últimos 15 minutos.
	*	Si se envía un correo electrónico al administrador del servicio y a los coadministradores cuando la alerta de dispara.
	*	Direcciones de correo electrónico adicionales para las notificaciones de alerta. ![Captura de pantalla de la hoja Agregar una regla de alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## Pasos siguientes
Para obtener más información acerca de la capacidad de DocumentDB, consulte [Administración de la capacidad de DocumentDB](documentdb-manage.md).
 

<!---HONumber=August15_HO6-->