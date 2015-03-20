<properties 
	pageTitle="Niveles de rendimiento en Base de datos de documentos | Azure" 
	description="Obtenga información sobre cómo los niveles de rendimiento de Base de datos de documentos le permiten reservar capacidad de proceso para cada colección." 
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
	ms.date="03/04/2015" 
	ms.author="johnmac"/>

# Niveles de rendimiento en Base de datos de documentos #

Este artículo proporciona información general sobre los niveles de rendimiento en [Base de datos de documentos de Microsoft Azure](http://azure.microsoft.com/services/documentdb/). 

> [AZURE.IMPORTANTE] Los niveles de rendimiento estarán disponibles cuando Base de datos de documentos de Azure esté en versión de disponibilidad general. Esta versión está programada para abril de 2015.

Después de leer este artículo, podrá responder a las preguntas siguientes:  

-	¿Qué es un nivel de rendimiento?
-	¿Cómo se reserva la capacidad de proceso para una cuenta de base de datos?
-	¿Cómo se trabaja con los niveles de rendimiento?
-	¿Cómo se me facturará por los niveles de rendimiento?

## <a id="Sub1"></a>Introducción a los niveles de rendimiento##

Cada colección de Base de datos de documentos creada con una cuenta estándar se aprovisiona con un nivel de rendimiento asociado. Los niveles de rendimiento se designan como S1, S2 o S3, de menor a mayor rendimiento. El nivel de rendimiento de la colección determina la cantidad de recursos de servicios reservada para la aplicación. Cada colección incluida en una base de datos puede tener un nivel de rendimiento diferente que permite asignar una mayor capacidad de proceso a las colecciones de acceso frecuente y menos capacidad de proceso a las colecciones que a las que se accede con menos frecuencia. 

Cada nivel de rendimiento tiene asociado un límite de velocidad de la unidad de solicitud (RU). Esta es la capacidad de proceso que se reservará para una colección en función de su nivel de rendimiento, el cual estará disponible para uso exclusivo de esa colección. Se pueden crear colecciones a través del [portal de Azure](http://portal.azure.com) o de cualquiera de los [SDK de Base de datos de documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). Las API de Base de datos de documentos permiten especificar el nivel de rendimiento de una colección. 

<table> 
<tbody>
<tr>
<td valign="top" ><p><b>Nivel de rendimiento de una colección</b></p></td>
<td valign="top" ><p><b>Capacidad de proceso reservada</b></p></td>
</tr>

<tr>
<td valign="top" ><p>S1</p></td>
<td valign="top" ><p>250 RU/s</p></td>
</tr>

<tr>
<td valign="top" ><p>S2</p></td>
<td valign="top" ><p>1000 RU/s</p></td>
</tr>

<tr>
<td valign="top" ><p>S3</p></td>
<td valign="top" ><p>2500 RU/s</p></td>
</tr>

</tbody>
</table>

Base de datos de documentos permite efectuar una amplia gama de operaciones de base de datos, por ejemplo, consultas, consultas con funciones definidas por el usuario (UDF), procedimientos almacenados y desencadenadores. El coste de procesamiento asociado a cada una de estas operaciones variará en función de la CPU, las E/S y la memoria que se necesite para completar la operación. En lugar de pensar en los recursos de hardware y administrarlos, puede considerar que una unidad de solicitud (RU) es como una medida única para los recursos necesarios para realizar varias operaciones de base de datos y dar servicio a una solicitud de la aplicación.

> [AZURE.NOTE] Los niveles de rendimiento se miden en unidades de solicitud. Cada nivel de rendimiento tiene asociada una tasa máxima de unidades de solicitud por segundo. Se puede ajustar el nivel de rendimiento de una colección a través de las API o del [portal de Azure](https://portal.azure.com/).

## <a id="Sub2"></a>Definición de los niveles de rendimiento para las colecciones##
Una vez que se crea una colección, la asignación completa de RU según el nivel de rendimiento designado se reserva para la colección. Por ejemplo, si una colección se establece como S3, la colección será capaz de procesar 2.500 RU por segundo. Cada colección reserva su capacidad de proceso designada y 10 GB de almacenamiento de base de datos. El precio de la colección varía según el nivel elegido rendimiento (S1, S2 o S3). Tenga en cuenta que Base de datos de documentos funciona según la reserva de capacidad: al crear una colección, se reserva una aplicación y se factura según el almacenamiento de base de datos y el rendimiento reservados, con independencia de la cantidad de almacenamiento y del rendimiento que se use activamente.

Una vez creadas las colecciones, puede modificar el nivel de rendimiento a través de los SDK de Base de datos de documentos o desde el portal de administración de Azure. 

> [AZURE.IMPORTANTE] Las colecciones estándares de Base de datos de documentos se facturan por horas y cada colección que se cree se facturará por un mínimo de una hora de uso.

Si ajusta el nivel de rendimiento de una colección para un plazo de una hora, se le facturará por el nivel de rendimiento más alto que se configure durante dicha hora. Por ejemplo, si aumenta el nivel de rendimiento de una colección a las 08:53 horas, se le cobrará el nuevo nivel a partir de las 08:00. Del mismo modo, si disminuye el nivel de rendimiento a las 08:53, la nueva tarifa se aplicará a las 09:00.

Las unidades de solicitud se reservan para cada colección en función del nivel de rendimiento definido. El consumo de las unidades de solicitud se evalúa por cada segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada (o nivel de rendimiento) en su colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para esa colección. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede aumentar el nivel de rendimiento para cada colección.

> [AZURE.NOTE] Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas.

## <a id="Sub3"></a>Trabajo con niveles de rendimiento##
Las colecciones de Base de datos de documentos permiten particionar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Consulte la [documentación sobre las particiones de datos](http://azure.microsoft.com/documentation/articles/documentdb-partition-data/) para obtener más información sobre cómo efectuar particiones de datos con Base de datos de documentos. Con la función de indexación automática y de consultas de Base de datos de documentos, es bastante habitual ubicar documentos heterogéneos en la misma colección. Estas son algunas de las consideraciones clave que se deben tener en cuenta a la hora de decidir si hay que utilizar colecciones independientes para los datos:

- Consultas: una colección es el ámbito para la ejecución de la consulta. Si necesita efectuar consultas en un conjunto de documentos, los patrones de lectura más eficaces se consiguen al colocar los documentos en una sola colección.
- Transacciones: una colección es el dominio de transacción para los procedimientos almacenados y los desencadenadores. Todas las transacciones tienen como ámbito una sola colección. 
- Rendimiento: una colección tiene un nivel de rendimiento asociado. Esto garantiza que cada colección tenga un rendimiento predecible mediante RU reservadas. Se pueden asignar datos a colecciones diferentes, con distintos niveles de rendimiento, en función de la frecuencia de acceso.

> [AZURE.IMPORTANTE] Es importante tener en cuenta que se aplicarán tarifas estándares completas según el número de colecciones creadas dentro de la aplicación.

Se recomienda que la aplicación haga uso de un pequeño número de colecciones y que se conozcan bien los patrones de la aplicación para la creación de nuevas colecciones. Puede optar por reservar la creación de la colección como una acción de administración gestionada fuera de la aplicación. De forma similar, ajustar el nivel de rendimiento para una colección cambiará el precio por hora a la que se factura a la colección. Los niveles de rendimiento de la colección se deben supervisar en caso de que la aplicación los ajuste dinámicamente.

## <a name="NextSteps"></a>Pasos siguientes

Para obtener más información sobre los precios y la administración de datos con Base de datos de documentos de Azure, consulte estos recursos:
 
- [Precios de Base de datos de documentos](http://azure.microsoft.com/pricing/details/documentdb/)
- [Administración de la capacidad de Base de datos de documentos](http://azure.microsoft.com/documentation/articles/documentdb-manage/) 
- [Modelado de datos en Base de datos de documentos](http://azure.microsoft.com/documentation/articles/documentdb-modeling-data/)
- [Partición de datos en Base de datos de documentos](http://azure.microsoft.com/documentation/articles/documentdb-partition-data/)

Para obtener más información acerca de Base de datos de documentos, consulte la [documentación](http://azure.microsoft.com/documentation/services/documentdb/) de Base de datos de documentos de Azure. 



<!--HONumber=47-->
