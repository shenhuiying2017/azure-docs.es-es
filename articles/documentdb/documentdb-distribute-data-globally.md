<properties
   pageTitle="Distribución de datos global con DocumentDB| Microsoft Azure"
   description="Aprenda acerca de la replicación geográfica, la conmutación por error y la recuperación de datos a escala mundial mediante bases de datos globales de Azure DocumentDB, un servicio de base de datos NoSQL totalmente administrado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="kipandya"/>
   
   
# Distribución de datos global con DocumentDB

> [AZURE.NOTE] La distribución global de las bases de datos de DocumentDB está disponible con carácter general y habilitada automáticamente para cualquier cuenta de DocumentDB que se haya creado recientemente. Estamos trabajando para habilitar la distribución global en todas las cuentas existentes, pero, mientras tanto, si quiere que la habilitemos ahora en la suya, [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Azure DocumentDB está diseñado para satisfacer las necesidades de aplicaciones de IoT que constan de millones de dispositivos distribuidos globalmente y aplicaciones a escala de Internet que proporcionan experiencias con una gran capacidad de respuesta a usuarios de todo el mundo. Estos sistemas de base de datos se enfrentan al desafío de lograr acceso de baja latencia a los datos de aplicaciones desde varias regiones geográficas con garantías bien definidas de coherencia y disponibilidad de datos. En cuando sistema de base de datos distribuido globalmente, DocumentDB simplifica la distribución global de los datos al ofrecer cuentas de base de datos de varias regiones completamente administradas que proporcionan un claro equilibrio entre coherencia, disponibilidad y rendimiento, todo ello con sus garantías correspondientes. Las cuentas de base de datos de DocumentDB se ofrecen con alta disponibilidad, latencias de ms de un solo dígito, varios [niveles de coherencia bien definidos][consistency], conmutación por error regional transparente con API de hospedaje múltiple, y la posibilidad de escalar elásticamente la capacidad de proceso y el rendimiento en todo el mundo.

  
## Configuración de cuentas de varias regiones

La configuración de la cuenta de DocumentDB para escalarse en todo el planeta se puede realizar en menos de un minuto mediante el Portal de Azure. Todo lo que tiene que hacer es seleccionar el nivel de coherencia adecuado entre los diversos niveles bien definidos admitidos y asociar un número cualquiera de regiones de Azure con su cuenta de base de datos. Los niveles de coherencia de DocumentDB proporcionan un claro equilibrio entre rendimiento y garantía de coherencia específica.

![DocumentDB ofrece varios modelos de coherencia bien definidos entre los que elegir][1]

DocumentDB ofrece varios modelos de coherencia bien definidos (moderados) entre los que elegir.

La selección del nivel de coherencia adecuado depende de la garantía de coherencia que precise su aplicación. DocumentDB replica automáticamente los datos entre en las regiones especificadas y garantiza la coherencia que ha seleccionado para su cuenta de base de datos.


## Uso de conmutación por error de varias regiones 

Azure DocumentDB es capaz de conmutar por error de forma transparente cuentas de base de datos entre varias regiones de Azure; las nuevas [API de hospedaje múltiple][developingwithmultipleregions] garantizan que la aplicación puede seguir usando un punto de conexión lógico y que no se verá interrumpida por la conmutación por error. El usuario es quien controla la conmutación por error, lo que proporciona la flexibilidad para volver a ubicar la cuenta de base de datos en caso de que se produzca algunas de la posibles condiciones de error, como errores de aplicación, infraestructura, servicio o regionales (reales o simulados). En caso de error regional de DocumentDB, el servicio conmuta por error de forma transparente su cuenta de base de datos y la aplicación sigue accediendo a los datos sin pérdida de disponibilidad. Aunque DocumentDB ofrece [SLA de un 99,99 % de disponibilidad][sla], puede probar las propiedades de disponibilidad completa de la aplicación simulando un error regional. Esto puede hacerlo tanto [mediante programación][arm] como por medio del Portal de Azure.


## Escalado en el mundo
DocumentDB le permite aprovisionar el rendimiento y consumir almacenamiento de forma independiente para cada colección de DocumentDB a cualquier escala, a nivel global en todas las regiones asociadas con su cuenta de base de datos. Una colección de DocumentDB se distribuye automáticamente a nivel global y se administra en todas las regiones asociadas con su cuenta de base de datos. Las colecciones dentro de la cuenta de base de datos se pueden distribuir entre todas las regiones de Azure en las que el [servicio DocumentDB se encuentre disponible][serviceregions].

El rendimiento adquirido y el almacenamiento consumido en cada colección de DocumentDB se aprovisionan automáticamente en todas las regiones por igual. De esta manera, la aplicación se escala sin problemas en todo el mundo, [y solo se paga por el rendimiento y el almacenamiento que se utilizan dentro de cada hora][pricing]. Por ejemplo, si ha aprovisionado 2 millones de RU para una colección de DocumentDB, cada una de las regiones asociadas con la cuenta de base de datos obtiene 2 millones de RU para esa colección. Esto se ilustra a continuación:

![Escalado del rendimiento en una colección de DocumentDB entre cuatro regiones][2]

DocumentDB garantiza latencias de lectura < 10 ms y latencias de escritura < 15 ms en P99. Las solicitudes de lectura nunca traspasan los límites del centro de datos para garantizar los [requisitos de coherencia seleccionados][consistency]. Las escrituras siempre se confirman en el cuórum de manera local antes de confirmarse en los clientes. Cada cuenta de base de datos se configura con una prioridad de región de escritura. La región designada con prioridad más alta funciona como la región actual de escritura para la cuenta. Todos los SDK enrutarán de forma transparente las escrituras de cuenta de base de datos a la región de escritura actual.

Por último, dado que DocumentDB es completamente [independiente del esquema][vldb], nunca tendrá que preocuparse por administrar o actualizar esquemas o índices secundarios entre varios centros de datos. Las [consultas SQL][sqlqueries] siguen funcionando mientras la aplicación y los modelos de datos sigan evolucionando.


## Habilitación de la distribución global 

Puede decidir que los datos se distribuyan local o globalmente mediante la asociación de una o varias regiones de Azure con una cuenta de base de datos de DocumentDB. Puede agregar o quitar regiones a su cuenta de base de datos en cualquier momento.

## Pasos siguientes

Más información sobre la distribución de datos global con DocumentDB en los siguientes artículos:

* [Obtener información acerca del almacenamiento y el aprovisionamiento de rendimiento predecible en DocumentDB][throughputandstorage]
* [Developing with multi-region DocumentDB accounts (Desarrollo con cuentas de DocumentDB de varias regiones)][developingwithmultipleregions]
* [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB][consistency]
* [Contrato de nivel de servicio para DocumentDB][sla]
* [Administración de una cuenta de DocumentDB][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: https://azure.microsoft.com/documentation/articles/documentdb-partition-data/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: https://azure.microsoft.com/documentation/articles/documentdb-developing-with-multiple-regions/
[createaccount]: https://azure.microsoft.com/documentation/articles/documentdb-create-account/
[manageaccount]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/
[manageaccount-consistency]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#consistency
[manageaccount-addregion]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#addregion
[throughputandstorage]: https://azure.microsoft.com/documentation/articles/documentdb-manage/
[arm]: https://azure.microsoft.com/documentation/articles/documentdb-automation-resource-manager-cli/
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: https://azure.microsoft.com/documentation/articles/documentdb-sql-query/

<!---HONumber=AcomDC_0803_2016-->