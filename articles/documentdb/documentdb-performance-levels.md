---
title: Niveles de rendimiento en DocumentDB | Microsoft Docs
description: "Obtenga información sobre cómo los niveles de rendimiento de DocumentDB le permiten reservar capacidad de proceso para cada colección."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: 237a92713ee8dca72a09550c47519189f2fd23cc


---
# <a name="performance-levels-and-pricing-tiers-in-documentdb"></a>Niveles de rendimiento y planes de tarifa de DocumentDB
Este artículo proporciona información general sobre los niveles de rendimiento en [DocumentDB de Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Después de leer este artículo, podrá responder a las preguntas siguientes:  

* ¿Qué es un nivel de rendimiento?
* ¿Cómo se reserva la capacidad de proceso para una cuenta de base de datos?
* ¿Cómo se trabaja con los niveles de rendimiento?
* ¿Cómo se me facturará por los niveles de rendimiento?

## <a name="introduction-to-performance-levels"></a>Introducción a los niveles de rendimiento
Cada colección de DocumentDB creada con una cuenta estándar de DocumentDB se aprovisiona con un nivel de rendimiento asociado. Cada colección incluida en una base de datos puede tener un nivel de rendimiento diferente que permite asignar una mayor capacidad de proceso a las colecciones de acceso frecuente y menos capacidad de proceso a las colecciones que a las que se accede con menos frecuencia. 

DocumentDB admite tanto niveles de rendimiento **definidos por el usuario** como niveles de rendimiento **predefinidos**, como se muestra en la tabla siguiente.  El rendimiento definido por el usuario permite obtener una capacidad de proceso reservada en unidades de 100 RU/s y tiene un almacenamiento ilimitado, mientras que los tres niveles de rendimiento predefinidos tienen opciones de capacidad de proceso especificadas y una cuota de almacenamiento de 10 GB. En la siguiente tabla se compara el rendimiento **definido por el usuario** con el rendimiento **predefinido**.

|Tipo de rendimiento|Detalles|Rendimiento|Almacenamiento|Versión|API existentes|
|----------------|-------|----------|-------|-------|----|
|Rendimiento definido por el usuario|El usuario define la capacidad de proceso en unidades de 100 RU/s|Sin límite|Sin límite|V2|API 2015-12-16 y versiones más recientes|
|Rendimiento predefinido|Almacenamiento reservado de 10 GB.<br><br>S1 = 250 RU/s<br>S2 = 1000 RU/s<br>S3 = 2500 RU/s|2500 RU/s|10 GB|V1|Cualquiera|

La capacidad de proceso está reservada por colección y está disponible para su uso en esa colección exclusivamente. La capacidad de proceso se mide en [unidades de solicitud (RU)](documentdb-request-units.md), que identifican la cantidad de recursos necesarios para realizar diversas operaciones de base de datos de DocumentDB.

> [!NOTE]
> El nivel de rendimiento de una colección se puede ajustar a través de los [SDK](documentdb-sdk-dotnet.md) o de [Azure Portal](https://portal.azure.com/). Se espera que los cambios de nivel de rendimiento se completen en 3 minutos.
> 
> 

## <a name="setting-performance-levels-for-collections"></a>Definición de los niveles de rendimiento para las colecciones
Una vez que se crea una colección, la asignación completa de RU según el nivel de rendimiento designado se reserva para la colección.

Tenga en cuenta que, tanto en los niveles de rendimiento definidos por el usuario como en los predefinidos, DocumentDB funciona según la reserva de rendimiento. Al crear una colección, una aplicación tiene un rendimiento reservado por el que se le factura, independientemente de cuánto rendimiento se use de forma activa. Con los niveles de rendimiento definidos por el usuario, el almacenamiento se mide en función del consumo, pero con los niveles de rendimiento predefinidos, se reservan 10 GB de almacenamiento en el momento de creación de la colección.  

Una vez creadas las colecciones, puede modificar el nivel de rendimiento o la capacidad de proceso a través de los [SDK](documentdb-sdk-dotnet.md) o de [Azure Portal](https://portal.azure.com/).

> [!IMPORTANT]
> Las colecciones estándares de DocumentDB se facturan por horas y cada colección que se cree se facturará por un mínimo de una hora de uso.
> 
> 

Si ajusta el nivel de rendimiento de una colección para un plazo de una hora, se le facturará por el nivel de rendimiento más alto que se configure durante dicha hora. Por ejemplo, si aumenta el nivel de rendimiento de una colección a las 08:53 horas, se le cobrará el nuevo nivel a partir de las 08:00. Del mismo modo, si disminuye el nivel de rendimiento a las 08:53, la nueva tarifa se aplicará a las 09:00.

Las unidades de solicitud se reservan para cada colección en función del nivel de rendimiento definido. El consumo de las unidades de solicitud se evalúa por cada segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada (o nivel de rendimiento) en su colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para esa colección. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede aumentar el nivel de rendimiento para cada colección.

> [!NOTE]
> Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas. Cuando exista una limitación, se recomienda agregar un número de reintentos reducido para atender los picos de tráfico de solicitud.
> 
> 

## <a name="working-with-performance-levels"></a>Trabajo con niveles de rendimiento
Las colecciones de DocumentDB permiten agrupar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Con la función de indexación automática y de consultas de DocumentDB, es bastante habitual ubicar documentos heterogéneos en la misma colección. Estas son algunas de las consideraciones clave que se deben tener en cuenta a la hora de decidir si hay que utilizar colecciones independientes:

* Consultas: una colección es el ámbito para la ejecución de la consulta. Si necesita efectuar consultas en un conjunto de documentos, los patrones de lectura más eficaces se consiguen al colocar los documentos en una sola colección.
* Transacciones: todas las transacciones tienen como ámbito una sola colección. Si dispone de documentos que se deben actualizar en un único procedimiento almacenado o desencadenador, se deben almacenar en la misma colección. Más concretamente, el límite de la transacción es una clave de partición dentro de una colección. Consulte [Partición y escalado de datos en DocumentDB](documentdb-partition-data.md) para obtener más detalles.
* Aislamiento de rendimiento: una colección tiene un nivel de rendimiento asociado. Esto garantiza que cada colección tenga un rendimiento predecible mediante RU reservadas. Se pueden asignar datos a colecciones diferentes, con distintos niveles de rendimiento, en función de la frecuencia de acceso.

> [!IMPORTANT]
> Es importante tener en cuenta que se aplicarán tarifas estándares completas según el número de colecciones creadas por la aplicación.
> 
> 

Es recomendable que la aplicación haga uso de un pequeño número de colecciones a menos que tenga requisitos de almacenamiento o rendimiento elevados. Asegúrese de que ha comprendido bien patrones de aplicación para la creación de nuevas colecciones. Puede optar por reservar la creación de la colección como una acción de administración gestionada fuera de la aplicación. De forma similar, ajustar el nivel de rendimiento para una colección cambiará el precio por hora a la que se factura a la colección. Los niveles de rendimiento de la colección se deben supervisar en caso de que la aplicación los ajuste dinámicamente.

## <a name="a-idchanging-performance-levels-using-the-azure-portalachange-from-s1-s2-s3-to-user-defined-performance"></a><a id="changing-performance-levels-using-the-azure-portal"></a>Cambio de los niveles S1, S2 o S3 a un rendimiento definido por el usuario
Siga estos pasos para pasar del uso de niveles de rendimiento predefinidos a niveles de rendimiento definidos por el usuario en el Portal de Azure. Mediante los niveles de rendimiento definidos por el usuario, puede adaptar el rendimiento a sus necesidades. Y si aún utiliza una cuenta de S1, puede aumentar el rendimiento predeterminado de 250 RU/s a 400 RU/s con unos cuantos clics. Tenga en cuenta que después de mover una colección de S1, S2 o S3 a estándar (definida por el usuario), no se puede volver a migrar a S1, S2 o S3; sin embargo puede modificar la capacidad de proceso de una colección estándar en cualquier momento.

Para obtener más información sobre los cambios de precio relativos al rendimiento predefinido y definido por el usuario, consulte la entrada del blob [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)(DocumentDB: todo lo que necesita saber sobre el uso de las nuevas opciones de precio).

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

1. En [**Azure Portal**](https://portal.azure.com), haga clic en **NoSQL (DocumentDB)** y luego seleccione la cuenta de DocumentDB que quiere modificar. 
 
    Si **NoSQL (DocumentDB)** no está en la barra de accesos, haga clic en >, vaya a **Bases de datos**, seleccione **NoSQL (DocumentDB)** y luego seleccione la cuenta de DocumentDB.  

2. En el menú de recursos, en **Colecciones**, haga clic en **Escala**, seleccione la colección que desea modificar en la lista desplegable y luego haga clic en **Plan de tarifa**. Las cuentas con un rendimiento predefinido tienen un plan de tarifa de S1, S2 o S3.  En la hoja **Elegir su nivel de precios**, haga clic en **Estándar** para cambiar la capacidad de proceso definida por el usuario y luego haga clic en **Seleccionar** para guardar el cambio.

    ![Captura de pantalla de la hoja Configuración que muestra dónde cambiar el valor de rendimiento](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. En la hoja **Escala**, el **plan de tarifa** cambia a **Estándar** y el cuadro **Procesamiento (RU/s)** se muestra con un valor predeterminado de 400. Establezca el rendimiento entre 400 y 10 000 [unidades de solicitud](documentdb-request-units.md)/segundo (RU/s). La opción **Estimated Monthly Bill** (Factura mensual estimada) en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. Haga clic en **Guardar** para guardar los cambios.

    Si cree que necesita más rendimiento (superior a 10 000 RU/s) o espacio de almacenamiento (mayor que 10 GB), puede crear una colección con particiones. Para crear una colección con particiones, consulte el [artículo sobre cómo crear una colección](documentdb-create-collection.md).

> [!NOTE]
> Cambiar los niveles de rendimiento de una colección puede llevar hasta 2 minutos.
> 
> 

## <a name="changing-performance-levels-using-the-net-sdk"></a>Cambio de los niveles de rendimiento mediante el SDK de .NET
Otra opción para cambiar los niveles de rendimiento de las colecciones es a través de nuestros SDK. En esta sección solo se trata el cambio del nivel de rendimiento de una colección mediante el [SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), pero el proceso es similar para otros [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si no está familiarizado con SDK. de NET, visite nuestro [tutorial de inicio](documentdb-get-started.md).

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



> [!NOTE]
> Las colecciones aprovisionadas con menos de 10 000 unidades de solicitud por segundo se pueden migrar entre las ofertas con rendimiento definido por el usuario y con rendimiento predefinido (S1, S2, S3) en cualquier momento. Las colecciones aprovisionadas con más de 10 000 unidades de solicitud por segundo no se puede convertir a niveles de rendimiento predefinidos.
> 
> 

Visite [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para ver más ejemplos y obtener más información sobre nuestros métodos de oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a name="a-idchange-throughputachanging-the-throughput-of-a-collection"></a><a id="change-throughput"></a>Cambio del rendimiento de una colección
Si ya utiliza el rendimiento definido por el usuario, puede cambiar el rendimiento de la colección mediante el procedimiento siguiente. Si necesita cambiar un nivel de rendimiento S1, S2 o S3 (rendimiento predefinidos) a uno definido por el usuario, consulte [Cambio de los niveles S1, S2 o S3 a un rendimiento definido por el usuario](#changing-performance-levels-using-the-azure-portal).

1. En [**Azure Portal**](https://portal.azure.com), haga clic en **NoSQL (DocumentDB)** y luego seleccione la cuenta de DocumentDB que quiere modificar.    
2. En el menú de recursos, en **Colecciones**, haga clic en **Escala** y seleccione la colección que desea modificar en la lista desplegable.
3. En el cuadro **Procesamiento (RU/s)**, escriba el nuevo nivel de capacidad de proceso. 
   
    La opción **Estimated Monthly Bill** (Factura mensual estimada) en la parte inferior de la página se actualiza automáticamente para ofrecer una estimación del costo mensual. Haga clic en **Guardar** para guardar los cambios.

    Si no está seguro de cuánto aumentar la capacidad de proceso, vea [Estimación de necesidades de rendimiento](documentdb-request-units.md#estimating-throughput-needs) y [Calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner).

## <a name="troubleshooting"></a>Solución de problemas

Si no ve la opción para cambiar entre los niveles de rendimiento S1, S2 o S3 en la hoja **Elegir su nivel de precios**, haga clic en **Ver todo** para mostrar los niveles de rendimiento estándar, S1, S2 y S3. Si usa el plan de tarifa estándar, no se puede cambiar entre S1, S2 y S3.

![Captura de pantalla de la hoja Elegir su nivel de precios con la opción Ver todo seleccionada](./media/documentdb-performance-levels/azure-documentdb-database-view-all-performance-levels.png)

Una vez que se cambia una colección de S1, S2 o S3 a estándar, no se puede volver a migrar a S1, S2 o S3.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los precios y la administración de datos con Azure DocumentDB, consulte estos recursos:

* [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modelado de datos en DocumentDB](documentdb-modeling-data.md)
* [Partición de datos en DocumentDB](documentdb-partition-data.md)
* [Unidades de solicitud](http://go.microsoft.com/fwlink/?LinkId=735027)

Para obtener más información acerca de DocumentDB, consulte la [documentación](https://azure.microsoft.com/documentation/services/documentdb/)de Azure DocumentDB.

Para empezar a utilizar pruebas de escala y rendimiento con DocumentDB, consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png



<!--HONumber=Jan17_HO2-->


