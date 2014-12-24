<properties title="Consistency levels in DocumentDB" pageTitle="Niveles de coherencia en Base de datos de documentos" description="DocumentDB has four consistency levels with associated performance levels to help application developers make predictable consistency-availability-latency trade-offs." metaKeywords="Optional" services="documentdb" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="Optional" scriptId="Optional" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Niveles de coherencia en Base de datos de documentos
Los desarrolladores con frecuencia se enfrentan al desafío de elegir entre los dos extremos de una coherencia alta y una coherencia ocasional. Lo cierto es que hay varios puntos intermedios entre estos dos extremos de la coherencia. En la mayoría de los escenarios reales, las aplicaciones se benefician de la realización de equilibrios muy ajustados entre la coherencia, la disponibilidad y la latencia. Base de datos de documentos ofrece cuatro niveles de coherencia bien definidos con niveles de rendimiento asociados. Esto permite a los desarrolladores de aplicaciones realizar equilibrios predecibles entre la coherencia, la disponibilidad y la latencia.  
 
Todos los recursos del sistema, como cuentas de base de datos, bases de datos, colecciones, usuarios y permisos son siempre absolutamente coherentes para lecturas y consultas. Los niveles de coherencia se aplican únicamente a los recursos definidos por el usuario. En aquellas operaciones de consultas y lectura en los recursos definidos por el usuario, como documentos, elementos adjuntos, procedimientos almacenados, desencadenadores y funciones definidas por el usuario, Base de datos de documentos ofrece cuatro niveles de coherencia distintos: alta, de uso vinculado, sesión y ocasional. Estos niveles de coherencia granulares y bien definidos le permiten realizar sólidos equilibrios entre la coherencia, la disponibilidad y el rendimiento. Estos niveles de coherencia están respaldados por niveles de rendimiento predecibles que garantizan unos resultados coherentes para su aplicación.   

##Niveles de coherencia
Puede configurar el nivel de coherencia predeterminado en la cuenta de la base de datos que se aplica a todas las colecciones (en todas las bases de datos) de su cuenta de base de datos. De manera predeterminada todas las lecturas y consultas enviadas a los recursos definidos por el usuario utilizarán el nivel de coherencia predeterminado especificado en la cuenta de la base de datos. Sin embargo, puede bajar el nivel de coherencia a una solicitud de lectura/consulta determinada especificando [x-ms-consistency-level] en el encabezado de la solicitud. Los cuatro tipos de niveles de coherencia admitidos por el protocolo de replicación de Base de datos de documentos se describen brevemente a continuación. 

>[AZURE.NOTE] En una versión futura se admitirá el reemplazo del nivel de coherencia predeterminado en cada colección individual.  

**Alta**: la coherencia alta garantiza que una escritura solo es visible después de confirmarse de forma duradera por la mayoría del cuórum de réplicas. Una escritura se confirma sincrónicamente de forma duradera tanto por el principal y por el cuórum de secundarios o se anula. Una lectura siempre se confirma por la mayoría del cuórum de lectura: el cliente nunca ve una escritura no confirmada o parcial y siempre se garantiza la lectura de la última escritura reconocida.   
 
La coherencia alta proporciona garantías absolutas con relación a la coherencia de datos, pero ofrece el nivel más bajo de rendimiento de lectura y escritura.  

**De uso vinculado**: la coherencia de uso vinculado garantiza el orden total de propagación de escrituras pero con lecturas potencialmente demoradas por detrás de las escrituras como máximo por prefijos K. La lectura siempre se confirma por cuórums de réplicas mayoritarios. La respuesta de una consulta de lectura especifica su relativa actualización (en términos de K).  

La coherencia de uso vinculado proporciona un comportamiento más predecible para la coherencia de lectura mientras ofrece las escrituras de latencia más bajas. Como las lecturas se confirman por un cuórum mayoritario, la latencia de lecturas no es la más baja ofrecida por el sistema.    

**Sesión**: a diferencia de los modelos de coherencia global ofrecidos por los niveles de coherencia Alta y De uso vinculado, la coherencia de "sesión" está pensada para una sesión de cliente específica. La coherencia de sesión suele ser suficiente ya que proporciona las escrituras y lecturas monotónicas garantizadas, así como la capacidad de leer sus propias escrituras. Se envía a una réplica una solicitud de lectura para la coherencia de sesión, que puede atender a la versión solicitada del cliente (parte de la cookie de sesión).  

La sesión proporciona una coherencia de datos de lectura predecibles para una sesión, mientras ofrece escrituras de latencia más bajas. Las lecturas también son de latencia baja ya que, excepto en casos raros, una única réplica atenderá a las lecturas.  

**Ocasional**: la coherencia ocasional es la forma más débil de coherencia, ya que con el tiempo un cliente puede obtener los valores que son más antiguos que los que ha visto antes. En ausencia de escrituras adicionales, las réplicas dentro del grupo convergirán finalmente. Los secundarios atienden a las solicitudes de lecturas.  

La coherencia ocasional proporciona la coherencia de lectura más débil, pero ofrece la latencia más baja tanto para lecturas como para escrituras. 

##Coherencia de consultas
De manera predeterminada, para los recursos definidos por el usuario, el nivel de coherencia de las consultas es igual al de las lecturas. De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas de documentos. Aunque Base de datos de documentos está optimizada para escrituras y admite volúmenes sostenidos de escrituras de documentos junto con un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinadas colecciones para actualizar el índice de manera diferida.  La indexación diferida incrementa aún más el rendimiento de las escrituras y es ideal para aquellos escenarios de ingesta en bloque en donde la carga de trabajo sea sobre todo de lecturas.  

Modo de indización|	Lecturas|	Consultas  
-------------|-------|---------
Coherente (predeterminado)|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|
Diferida|	Seleccionar entre Alta, De uso vinculado, Sesión y Ocasional|	Ocasional  

Al igual que con las solicitudes de lecturas, puede bajar el nivel de coherencia a una solicitud de lectura/consulta específica especificando [x-ms-consistency-level] en el encabezado de la solicitud.  

##Referencias
-	Doug Terry. Coherencia de datos replicados explicados mediante el béisbol.   
[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Garantías de sesión para datos replicados con coherencia débil.   
[http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html](http://www2.parc.com/csl/projects/bayou/pubs/sg-pdis-94/www/SessionGuaranteesPDIS_1.html)
-	Daniel Abadi. Equilibrios de coherencia en el diseño de los modernos sistemas de bases de datos distribuidas: CAP es solo parte de la historia".   
[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Uso vinculado probabilístico (PBS) para cuórums parciales prácticos.   
[http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Coherencia ocasional - Revisión.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
