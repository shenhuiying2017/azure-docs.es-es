<properties 
	pageTitle="Niveles de rendimiento en DocumentDB | Microsoft Azure" 
	description="Obtenga información sobre cómo los niveles de rendimiento de DocumentDB le permiten reservar capacidad de proceso para cada colección." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/27/2016" 
	ms.author="johnmac"/>

# Niveles de rendimiento en DocumentDB

Este artículo proporciona información general sobre los niveles de rendimiento en [DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Después de leer este artículo, podrá responder a las preguntas siguientes:

-	¿Qué es un nivel de rendimiento?
-	¿Cómo se reserva la capacidad de proceso para una cuenta de base de datos?
-	¿Cómo se trabaja con los niveles de rendimiento?
-	¿Cómo se me facturará por los niveles de rendimiento?

## Introducción a los niveles de rendimiento

Cada colección de DocumentDB creada con una cuenta estándar se aprovisiona con un nivel de rendimiento asociado. Cada colección incluida en una base de datos puede tener un nivel de rendimiento diferente que permite asignar una mayor capacidad de proceso a las colecciones de acceso frecuente y menos capacidad de proceso a las colecciones que a las que se accede con menos frecuencia. DocumentDB admite tanto niveles de rendimiento definidos por el usuario como niveles de rendimiento predefinidos.

Cada nivel de rendimiento tiene asociado un límite de velocidad de la [unidad de solicitud (RU)](documentdb-request-units.md). Esta es la capacidad de proceso que se reservará para una colección en función de su nivel de rendimiento, el cual estará disponible para uso exclusivo de esa colección.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
        	<td valign="top"><p></p></td>
            <td valign="top"><p>Detalles</p></td>
            <td valign="top"><p>Límites de rendimiento</p></td>
            <td valign="top"><p>Límites de almacenamiento</p></td>
            <td valign="top"><p>Versión</p></td>
 			<td valign="top"><p>API existentes</p></td>            
        </tr>
        <tr>
        	<td valign="top"><p>Rendimiento definido por el usuario</p></td>
            <td valign="top"><p>Almacenamiento medido según el uso en GB.</p><p>Rendimiento en unidades de 100 RU/s</p></td>
            <td valign="top"><p>Sin límite. 400-250 000 unidades de solicitud/s de forma predeterminada (puede aumentar a petición)</p></td>
            <td valign="top"><p>Sin límite. 250 GB de forma predeterminada (puede aumentar a petición) </p></td>
            <td valign="top"><p>V2</p></td>
 			<td valign="top"><p>API 2015-12-16 y versiones más recientes</p></td>  
        </tr>
        <tr>
        	<td valign="top"><p>Rendimiento predefinido</p></td>
            <td valign="top"><p>Almacenamiento reservado de 10 GB.</p><p>S1 = 250 RU/s, S2 = 1000 RU/s, S3 = 2500 RU/s</p></td>
            <td valign="top"><p>2500 RU/s</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
 			<td valign="top"><p>Cualquiera</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB permite efectuar una amplia gama de operaciones de base de datos, por ejemplo, consultas, consultas con funciones definidas por el usuario (UDF), procedimientos almacenados y desencadenadores. El coste de procesamiento asociado a cada una de estas operaciones variará en función de la CPU, las E/S y la memoria que se necesite para completar la operación. En lugar de pensar en los recursos de hardware y administrarlos, puede considerar que una unidad de solicitud (RU) es como una medida única para los recursos necesarios para realizar varias operaciones de base de datos y dar servicio a una solicitud de la aplicación.

Se pueden crear colecciones a través del [Portal de Microsoft Azure](https://portal.azure.com), la [API de REST](https://msdn.microsoft.com/library/azure/mt489078.aspx) o cualquiera de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Las API de DocumentDB permiten especificar el nivel de rendimiento de una colección.

> [AZURE.NOTE] El nivel de rendimiento de una colección se puede ajustar a través de las API o del [Portal de Microsoft Azure](https://portal.azure.com/). Se espera que los cambios de nivel de rendimiento se completen en 3 minutos.

## Definición de los niveles de rendimiento para las colecciones
Una vez que se crea una colección, la asignación completa de RU según el nivel de rendimiento designado se reserva para la colección.

Tenga en cuenta que, tanto en los niveles de rendimiento definidos por el usuario como en los predefinidos, DocumentDB funciona según la reserva de rendimiento. Al crear una colección, una aplicación tiene un rendimiento reservado por el que se le factura, independientemente de cuánto rendimiento se use de forma activa. Con los niveles de rendimiento definidos por el usuario, el almacenamiento se mide en función del consumo, pero con los niveles de rendimiento predefinidos, se reservan 10 GB de almacenamiento en el momento de creación de la colección.

Una vez creadas las colecciones, puede modificar el nivel de rendimiento a través de los SDK de DocumentDB o desde el Portal de Azure clásico.

> [AZURE.IMPORTANT] Las colecciones estándares de DocumentDB se facturan por horas y cada colección que se cree se facturará por un mínimo de una hora de uso.

Si ajusta el nivel de rendimiento de una colección para un plazo de una hora, se le facturará por el nivel de rendimiento más alto que se configure durante dicha hora. Por ejemplo, si aumenta el nivel de rendimiento de una colección a las 08:53 horas, se le cobrará el nuevo nivel a partir de las 08:00. Del mismo modo, si disminuye el nivel de rendimiento a las 08:53, la nueva tarifa se aplicará a las 09:00.

Las unidades de solicitud se reservan para cada colección en función del nivel de rendimiento definido. El consumo de las unidades de solicitud se evalúa por cada segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada (o nivel de rendimiento) en su colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para esa colección. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede aumentar el nivel de rendimiento para cada colección.

> [AZURE.NOTE] Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas. Se recomienda agregar

## Trabajo con niveles de rendimiento
Las colecciones de DocumentDB permiten agrupar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Con la función de indexación automática y de consultas de DocumentDB, es bastante habitual ubicar documentos heterogéneos en la misma colección. Estas son algunas de las consideraciones clave que se deben tener en cuenta a la hora de decidir si hay que utilizar colecciones independientes:

- Consultas: una colección es el ámbito para la ejecución de la consulta. Si necesita efectuar consultas en un conjunto de documentos, los patrones de lectura más eficaces se consiguen al colocar los documentos en una sola colección.
- Transacciones: todas las transacciones tienen como ámbito una sola colección. Si dispone de documentos que se deben actualizar en un único procedimiento almacenado o desencadenador, se deben almacenar en la misma colección. Más concretamente, el límite de la transacción es una clave de partición dentro de una colección. Consulte [Partición y escalado de datos en DocumentDB](documentdb-partition-data.md) para obtener más detalles.
- Aislamiento de rendimiento: una colección tiene un nivel de rendimiento asociado. Esto garantiza que cada colección tenga un rendimiento predecible mediante RU reservadas. Se pueden asignar datos a colecciones diferentes, con distintos niveles de rendimiento, en función de la frecuencia de acceso.

> [AZURE.IMPORTANT] Es importante tener en cuenta que se aplicarán tarifas estándares completas según el número de colecciones creadas por la aplicación.

Es recomendable que la aplicación haga uso de un pequeño número de colecciones a menos que tenga requisitos de almacenamiento o rendimiento elevados. Asegúrese de que ha comprendido bien patrones de aplicación para la creación de nuevas colecciones. Puede optar por reservar la creación de la colección como una acción de administración gestionada fuera de la aplicación. De forma similar, ajustar el nivel de rendimiento para una colección cambiará el precio por hora a la que se factura a la colección. Los niveles de rendimiento de la colección se deben supervisar en caso de que la aplicación los ajuste dinámicamente.

## Cambio de los niveles de rendimiento mediante el Portal de Azure clásico

El Portal de Azure es una opción disponible al administrar los niveles de rendimiento de las colecciones. Siga estos pasos para pasar del uso de niveles de rendimiento predefinidos a niveles de rendimiento definidos por el usuario en el Portal de Azure. Mediante los niveles de rendimiento definidos por el usuario, puede adaptar el rendimiento a sus necesidades. Y si aún utiliza una cuenta de S1, puede aumentar el rendimiento predeterminado de 250 RU/s a 400 RU/s con unos cuantos clics.

Para más información sobre los cambios de precio relativos al rendimiento predefinido y definido por el usuario, consulte la entrada del blob [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precio).

> [AZURE.VIDEO changedocumentdbcollectionperformance]

1. En el explorador, vaya al [**Portal de Azure**](https://portal.azure.com).
2. Haga clic en **Examinar** -> **Cuentas de DocumentDB** y luego seleccione la cuenta de DocumentDB que quiere modificar.
3. En la lente **Bases de datos**, seleccione la base de datos que quiere modificar, y, en la hoja **Base de datos**, seleccione la colección que modificará. Las cuentas con un rendimiento predefinido tienen un plan de tarifa de S1, S2 o S3.

      ![Captura de pantalla de la hoja Base de datos con una colección S1](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)

4. En la hoja **Colecciones**, haga clic en **Configuración** en la barra superior.
5. En la hoja **Configuración**, haga clic en **Plan de tarifa** y observe que el costo estimado mensual de cada plan se muestra en la hoja **Elija un plan de tarifa**. Para cambiar al rendimiento definido por el usuario, haga clic en **Estándar** y haga clic en **Seleccionar** para guardar el cambio.

      ![Captura de pantalla de las hojas Configuración y Elija un plan de tarifa de DocumentDB](./media/documentdb-performance-levels/documentdb-change-performance.png)

6. De vuelta en la hoja **Configuración**, el **plan de tarifa** cambia a **Estándar** y el cuadro **Rendimiento (RU/s)** se muestra con un valor predeterminado de 400. Establezca el rendimiento entre 400 y 10 000 [unidades de solicitud](documentdb-request-units.md)/segundo (RU/s). La opción **Resumen de precios** en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. Haga clic en **Aceptar** para guardar los cambios.
    
	![Captura de pantalla de la hoja Configuración que muestra dónde cambiar el valor de rendimiento](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

7. En la hoja **Base de datos**, puede comprobar el nuevo rendimiento de la colección.

	![Captura de pantalla de la hoja Base de datos con la colección modificada](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

Si cree que necesita más rendimiento (superior a 10 000 RU/s) o espacio de almacenamiento (mayor que 10 GB), puede crear una colección con particiones. Para crear una colección con particiones, consulte el [artículo sobre cómo crear una colección](documentdb-create-collection.md).

>[AZURE.NOTE] Cambiar los niveles de rendimiento de una colección puede llevar hasta 2 minutos.

## Cambio de los niveles de rendimiento mediante el SDK de .NET

Otra opción para cambiar los niveles de rendimiento de las colecciones es a través de nuestros SDK. Esta sección solo cubre el cambio del nivel de rendimiento de una colección mediante el [SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), pero el proceso es similar para otros [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si no está familiarizado con SDK. de NET, visite nuestro [tutorial de inicio](documentdb-get-started.md).

A continuación se muestra un fragmento de código para cambiar el rendimiento de la oferta a 50 000 unidades de solicitud por segundo:

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
		              .Where(r => r.ResourceLink == collection.SelfLink)    
		              .AsEnumerable()
		              .SingleOrDefault();
	                          
	// Set the throughput to 5000 request units per second
	offer = new OfferV2(offer, 5000);
	                    
	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);

	// Set the throughput to S2
	offer = new Offer(offer);
	offer.OfferType = "S2";
	                    
	//Now persist these changes to the database by replacing the original resource
	await client.ReplaceOfferAsync(offer);



> [AZURE.NOTE] Las colecciones aprovisionadas con menos de 10 000 unidades de solicitud por segundo se pueden migrar entre las ofertas con rendimiento definido por el usuario y con rendimiento predefinido (S1, S2, S3) en cualquier momento. Las colecciones aprovisionadas con más de 10 000 unidades de solicitud por segundo no se puede convertir a niveles de rendimiento predefinidos.

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver más ejemplos y obtener más información sobre nuestros métodos de oferta:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## Pasos siguientes

Para obtener más información sobre los precios y la administración de datos con Azure DocumentDB, consulte estos recursos:
 
- [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Administración de la capacidad de DocumentDB](documentdb-manage.md)
- [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
- [Partición de datos en DocumentDB](documentdb-partition-data.md)
- [Unidades de solicitud](http://go.microsoft.com/fwlink/?LinkId=735027)

Para obtener más información acerca de DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/) de Azure DocumentDB.

Para empezar a trabajar con pruebas de escala y rendimiento con DocumentDB, consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0629_2016-->