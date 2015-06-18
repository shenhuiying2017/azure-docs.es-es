<properties 
	pageTitle="Glosario de Escalado elástico de Azure" 
	description="Explicación de los términos usados en la característica de Escalado elástico de Base de datos SQL de Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="sidneyh@microsoft.com"/>

#Glosario de Escalado elástico
Los siguientes términos se definen para la característica Escalado elástico de Base de datos SQL de Azure.

![Elastic Scale terms][1]

**Base de datos**: una base de datos SQL de Azure. 

**Enrutamiento dependiente de los datos**: La funcionalidad que permite que una aplicación se conecta a una partición dada una clave de partición específica. Comparar con **Consulta a través de particiones múltiples**.

**Mapa de particiones global**: el conjunto de asignaciones entre las claves de particionamiento y sus respectivas particiones dentro de un **conjunto de particiones**. El GSM se almacena en el **Administrador de asignación de particiones**. Comparar con **mapa de particiones local**.

**Mapa de particiones de lista**: un mapa de particiones en el que las claves de particionamiento se asignan de manera individual. Comparar con **Mapa de particiones de intervalo**.   

**Mapa de particiones local**: almacenado en una partición, el mapa de particiones local contiene asignaciones para los shardlets que residen en la partición.


**Consulta de varias particiones**: la capacidad de emitir una consulta a través de varias particiones; los conjuntos de resultados se devuelven usando la semántica UNION ALL (conocida también como "consulta de distribución ramificada"). Comparar con **Enrutamiento dependiente de datos**.

**Mapa de particiones de intervalo**: un mapa de particiones en el que la estrategia de distribución de particiones se basa en varios intervalos de valores contiguos. 


**Tablas de referencia**: tablas no particionadas, pero que se replican entre particiones. 

**Partición**: una base de datos SQL de Azure que almacena datos desde un conjunto de datos particionados. 

**Elasticidad de partición** (SE): la posibilidad de realizar **escalado horizontal** y **escalado vertical**.

**Tablas particionadas**: tablas que se particionan, es decir, cuyos datos se distribuyen entre particiones según sus valores de clave de particionamiento. 

**Clave de particionamiento**: un valor de columna que determina cómo se distribuyen los datos entre particiones. El tipo de valor puede ser uno de los siguientes: int, bigint, varbinary o uniqueidentifier. 

**Conjunto de particiones**: la recopilación de particiones que se atribuyen al mismo mapa de particiones en el administrador de mapa de particiones.  

**Shardlet**: todos los datos asociados con un valor único de una clave de particionamiento en una partición. Un shardlet es la unidad de desplazamiento de datos más pequeña posible cuando se distribuyen tablas particionadas. 

**Mapa de particiones**: el conjunto de asignaciones entre las claves de particionamiento y sus respectivas particiones.

**Administrador de mapas de particiones**: un almacén de datos y objetos de administración que contiene los mapas de particiones, ubicaciones de particiones y asignaciones para uno o más conjuntos de particiones.

![Mappings][2]


##Verbos

**Escala do horizontal**: el acto de escalar (o reducir) horizontalmente una recopilación de particiones agregando o quitando particiones de un mapa de particiones.

**Combinar**: el acto de mover shardlets desde dos particiones a una partición y actualizar el mapa de particiones como corresponda.

**Desplazamiento de shardlets**: el acto ve mover un shardlet a una partición distinta. 

**Partición**: el acto de particionar de manera horizontal datos estructurados de manera idéntica entre varias bases de datos según una clave de particionamiento.

**División**: el acto de mover varios shardlets desde una partición a otra (normalmente nueva). El usuario proporciona una clave de particionamiento como el punto de división.

**Escalado vertical**: el acto de escalar (o reducir) verticalmente el nivel de rendimiento de una partición individual. Por ejemplo, cambiando una partición de Standard a Premium (según lo requerido por motivos de rendimiento). 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=47-->
 