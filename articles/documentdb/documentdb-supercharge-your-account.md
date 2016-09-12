<properties 
	pageTitle="Aumento del rendimiento de la cuenta S1 de DocumentDB | Microsoft Azure" 
	description="Aproveche las ventajas del aumento de rendimiento de su cuenta S1 de DocumentDB con tan solo unos cambios en el Portal de Azure." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="mimig"/>

# Aumento del rendimiento de la cuenta de DocumentDB

Siga estos pasos para aprovechar el aumento de rendimiento de su cuenta S1 de Azure DocumentDB. Con poco o sin ningún costo adicional, puede aumentar el rendimiento de su cuenta S1 existente de 250 [RU/s](documentdb-request-units.md) a 400 RU/s o más.

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## Cambio al rendimiento definido por el usuario en el Portal de Azure

1. En el explorador, vaya al [**Portal de Azure**](https://portal.azure.com).
2. Haga clic en **Examinar** -> **DocumentDB (NoSQL)** y luego seleccione la cuenta de DocumentDB que quiere modificar.
3. En la lente **Bases de datos**, seleccione la base de datos que quiere modificar, y, en la hoja **Base de datos**, seleccione la colección con el plan de tarifa S1.

      ![Captura de pantalla de la hoja Base de datos con una colección S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. En la hoja **Colección**, haga clic en **Más** y, después, en **Configuración**.
5. En la hoja **Configuración**, haga clic en **Plan de tarifa** y observe que se muestra el costo estimado mensual de cada plan. En la hoja **Elija su plan de tarifa**, haga clic en **Estándar** y luego haga clic en **Seleccionar** para guardar el cambio.

      ![Captura de pantalla de las hojas Configuración y Elija un plan de tarifa de DocumentDB](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. En la hoja **Configuración**, el **plan de tarifa** cambia a **Estándar** y el cuadro **Rendimiento (RU/s)** se muestra con un valor predeterminado de 400. Haga clic en **Aceptar** para guardar los cambios.

    > [AZURE.NOTE] Puede establecer el rendimiento entre 400 y 10 000 [unidades de solicitud](../articles/documentdb/documentdb-request-units.md)/segundo (RU/s). La opción **Resumen de precios** en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual.
    
	![Captura de pantalla de la hoja Configuración que muestra dónde cambiar el valor de rendimiento](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. En la hoja **Base de datos**, puede comprobar el aumento del rendimiento de la colección.

	![Captura de pantalla de la hoja Base de datos con la colección modificada](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Para obtener más información sobre los cambios de precio relativos al rendimiento predefinido y definido por el usuario, consulte la entrada del blob [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precio).

## Pasos siguientes

Si cree que necesita más rendimiento (superior a 10 000 RU/s) o espacio de almacenamiento (mayor que 10 GB), puede crear una colección con particiones. Para crear una colección con particiones, consulte el [artículo sobre cómo crear una colección](documentdb-create-collection.md).

<!---HONumber=AcomDC_0831_2016-->