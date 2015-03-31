<properties 
	pageTitle="Información general de particionamiento" 
	description="Se describen las razones para el particionamiento: escalar recursos de base de datos para aumentar la disponibilidad o el rendimiento." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>
# Información general de particionamiento 

## Principios de particionamiento 

**Particionamiento**: es una técnica para distribuir grandes cantidades de datos estructurados de manera idéntica entre un número de bases de datos independientes. De uso muy extendido entre desarrolladores de nube que crean ofertas de software como servicio (SAAS) para empresas o clientes finales. Estos clientes finales a menudo se conocen como "Inquilinos". El particionamiento puede ser necesario por diversos motivos: 

* La cantidad total de datos es demasiado grande para caber dentro de las restricciones de una base de datos 
* El rendimiento de transacciones de la carga de trabajo total supera la capacidad de una sola base de datos 
* Los inquilinos pueden requerir el aislamiento físico entre sí, por lo que se necesitan bases de datos independientes para cada inquilino 
* Distintas secciones de una base de datos pueden tener que residir en diferentes geografías por motivos de cumplimiento, rendimiento o geopolíticos. 
 
El particionamiento funciona mejor cuando todas las transacciones de una aplicación pueden restringirse a un único valor de una clave de particionamiento. De este modo se garantiza que todas las transacciones sean locales con respecto a una base de datos. 

Algunas aplicaciones usan el enfoque más simple de crear una base de datos independiente para cada inquilino. Se trata del **patrón de particionamiento de un solo inquilino** que proporciona aislamiento, capacidad de copia de seguridad y restauración, y ajuste de escala de recursos en la granularidad del inquilino. Con el particionamiento de un solo inquilino, cada base de datos se asocia a un determinado valor de identificador de inquilino (o valor de clave de cliente), pero no es necesario que esa clave esté siempre presente en los propios datos. Es responsabilidad de la aplicación enrutar cada solicitud a la base de datos adecuada. 

![Single tenant versus multi tenant][1]

Otros escenarios empaquetan varios inquilinos juntos en bases de datos, en lugar de aislarlos en bases de datos independientes. Se trata de un **patrón de partición de varios inquilinos** típico y puede estar impulsado por las consideraciones de costo, eficiencia o por el hecho de que una aplicación administra grandes cantidades de inquilinos muy pequeños. En el particionamiento de varios inquilinos, las filas de las tablas de bases de datos están diseñadas para contener una clave que identifique la clave de particionamiento o el identificador del inquilino. De nuevo, la capa de aplicación es la responsable de enrutar la solicitud del inquilino a la base de datos adecuada. 

En otros escenarios, como la ingesta de datos desde dispositivos distribuidos, el particionamiento puede usarse para rellenar un conjunto de bases de datos distribuidas por un período de tiempo. Por ejemplo, puede dedicarse una base de datos independiente a cada día o semana. En ese caso, la clave de particionamiento puede ser un entero que representa la fecha (presente en todas las filas de las tablas particionadas) y la aplicación debe enrutar las consultas que recuperan información de un intervalo de fechas al subconjunto de bases de datos que abarca el intervalo en cuestión.

Independientemente del modelo de particionamiento que se use, una estructura de datos especial, conocida como **mapa de particiones**, funciona como tabla de búsqueda que asocia los valores de clave de particionamiento a bases de datos; esto permite a la aplicación realizar el enrutamiento de las solicitudes de base de datos. Esto se conoce como **enrutamiento dependiente de los datos** y es una funcionalidad básica necesaria para que una aplicación use una capa de datos particionada. Las [API de cliente de Escalado elástico](http://go.microsoft.com/?linkid=9862592) proporcionan un amplio conjunto de funciones necesarias para [administrar mapas de particiones](http://go.microsoft.com/?linkid=9862595) y habilitar las [funciones de enrutamiento dependiente de los datos](http://go.microsoft.com/?linkid=9862596), eficientes y fáciles de usar, en una aplicación. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png

<!--HONumber=47-->
