<properties
	pageTitle="Niveles de coherencia en DocumentDB | Microsoft Azure"
	description="DocumentDB tiene cuatro niveles de coherencia para ayudar a equilibrar la coherencia, la disponibilidad y la latencia finales."
	keywords="eventual consistency, documentdb, azure, Microsoft azure"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="mimig"/>

# Niveles de coherencia en Base de datos de documentos

El diseño de Azure DocumentDB se ha llevado a cabo desde el principio con la distribución global en mente. Se ha diseñado para que ofrezca garantías de una baja latencia predecible, un Acuerdo de Nivel de Servicio (SLA) con un 99,99 % de disponibilidad y varios modelos de coherencia moderada bien definidos. Actualmente, DocumentDB admite cuatro niveles de coherencia: fuerte, de obsolescencia entrelazada, de sesión y eventual. Además de los modelos de **coherencia fuerte** y **eventual** proporcionados habitualmente con otras bases de datos NoSQL, DocumentDB también ofrece dos modelos de coherencia codificados y operacionalizados de forma cuidadosa: **de obsolescencia entrelazada** y **de sesión**. Además, ha validado su utilidad en casos de uso reales. De forma colectiva, estos cuatro niveles de coherencia permiten lograr equilibrios bien razonados entre la coherencia, la disponibilidad y la latencia.

## Ámbito de coherencia

El ámbito de la granularidad de coherencia se limita a una única solicitud de usuario. Una solicitud de escritura puede corresponder a una transacción de inserción, reemplazo, upsert o eliminación (con o sin la ejecución de un pre- o posdesencadenador asociado). O bien, una solicitud de escritura puede corresponder a la ejecución transaccional de un procedimiento almacenado JavaScript que funciona en varios documentos dentro de una partición. Al igual que con las escrituras, el ámbito de una transacción de lectura y consulta también se limita a una única solicitud de usuario. Es posible que el usuario deba paginar un conjunto grande de resultados, que abarque varias particiones, pero el ámbito de cada transacción de lectura se restringe a una sola página y se atiende desde dentro de una sola partición.

## Niveles de coherencia

Puede configurar el nivel de coherencia predeterminado en la cuenta de la base de datos que se aplica a todas las colecciones (en todas las bases de datos) de su cuenta de base de datos. De manera predeterminada todas las lecturas y consultas enviadas a los recursos definidos por el usuario utilizarán el nivel de coherencia predeterminado especificado en la cuenta de la base de datos. Sin embargo, puede moderar el nivel de coherencia de una solicitud de lectura o consulta concreta si especifica el encabezado de solicitud [[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx). Existen cuatro tipos de niveles de coherencia admitidos por el protocolo de replicación de DocumentDB que proporcionan un equilibrio claro entre garantías de coherencia específicas y rendimiento, como se describe a continuación.

![DocumentDB ofrece varios modelos de coherencia bien definidos entre los que elegir][1]

**Alta**:

- La coherencia fuerte una garantía de [linearizabilidad](https://aphyr.com/posts/313-strong-consistency-models) que asegura que las lecturas devolverán la versión más reciente de un documento.
- la coherencia alta garantiza que una escritura solo es visible después de confirmarse de forma duradera por la mayoría del cuórum de réplicas. Una escritura se confirma sincrónicamente de forma duradera tanto por la réplica principal como por el cuórum de las secundarias, o se anula. Una lectura siempre se confirma por la mayoría del cuórum de lectura; un cliente no puede ver nunca una escritura no confirmada o parcial y cuenta con la garantía de que leerá la última escritura confirmada.
- Las cuentas de DocumentDB que se han configurado con coherencia fuerte no pueden asociarse con más de una región de Azure.
- El costo de una operación de lectura (en términos de [unidades de solicitud](documentdb-request-units.md) consumidas) con coherencia fuerte es mayor que con la de sesión o la eventual, pero igual que con la de obsolescencia entrelazada.
 

**De obsolescencia entrelazada**:

- La coherencia de obsolescencia entrelazada garantiza que las lecturas vayan con retraso respecto a las escrituras en un máximo de versiones *K* o prefijos de un documento o el intervalo de tiempo *t*.
- Por consiguiente, al elegir la obsolescencia entrelazada, la obsolescencia se puede configurar de dos maneras:
    - Número de versiones *K* del documento en que las lecturas van con retraso respecto a las escrituras
    - Intervalo de tiempo *t*
- La obsolescencia entrelazada ofrece un orden global total excepto dentro de la "ventana de obsolescencia". Tenga en cuenta que la garantía de lectura monotónica existe dentro de una región tanto dentro como fuera de la "ventana de obsolescencia".
- La obsolescencia entrelazada proporciona una garantía de coherencia más fuerte que la coherencia de sesión o la eventual. Para las aplicaciones de distribución global, se recomienda utilizar la obsolescencia entrelazada para aquellos escenarios donde se desea disponer de coherencia fuerte pero también de una disponibilidad del 99,99 % y una latencia baja.
- Las cuentas de DocumentDB que están configuradas con la coherencia de obsolescencia entrelazada pueden asociarse con cualquier número de regiones de Azure.
- El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con uso vinculado es mayor que con la de sesión o la eventual, pero igual que con la de coherencia fuerte.

**Sesión**:

- A diferencia de los modelos de coherencia global ofrecidos por los niveles de coherencia fuerte y de obsolescencia entrelazada, el ámbito de la coherencia de sesión corresponde a una sesión de cliente.
- La coherencia de sesión es ideal para todos los escenarios en los que exista una sesión de usuario o dispositivo, ya que garantiza lecturas monotónicas, escrituras monotónicas y lectura de sus propias escrituras (RYW).
- La coherencia de sesión proporciona una coherencia predecible para una sesión, así como de un rendimiento de lectura máximo, al mismo tiempo que ofrece las escrituras y lecturas con menor latencia.
- Las cuentas de DocumentDB que están configuradas con la coherencia de sesión pueden asociarse con cualquier número de regiones de Azure.
- El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con coherencia de sesión es menor que con la fuerte o la de obsolescencia entrelazada, pero más que con la eventual.
 

**Ocasional**:

- La coherencia eventual garantiza que, en ausencia de escrituras adicionales, las réplicas dentro del grupo terminarán por converger.
- La coherencia eventual es la forma más débil de coherencia, ya que un cliente puede obtener los valores que son más antiguos que los que ha visto antes.
- La coherencia ocasional proporciona la coherencia de lectura más débil, pero ofrece la latencia más baja tanto para lecturas como para escrituras.
- Las cuentas de DocumentDB que están configuradas con la coherencia eventual pueden asociarse con cualquier número de regiones de Azure.
- El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con el nivel de coherencia eventual es el más bajo de entre todos los niveles de coherencia de DocumentDB.


## Garantías de coherencia

En la siguiente tabla, se reflejan las diversas garantías de coherencia que corresponden a los cuatro niveles de coherencia.

| Garantía | Alta | De uso vinculado | Sesión | Ocasional |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
| **Orden global total** | Sí | Sí, fuera de la "ventana de obsolescencia" | No, orden de "sesión" parcial | No |
| **Garantía de prefijo coherente** | Sí | Sí | Sí | Sí |
| **Lecturas monotónicas** | Sí | Sí, entre regiones fuera de la ventana de obsolescencia y dentro de una región todo el tiempo. | Sí, para la sesión dada | No |
| **Escrituras monotónicas** | Sí | Sí | Sí | Sí |
| **Lectura de sus escrituras** | Sí | Sí | Sí (en la región de escritura) | No |


## Configuración del nivel de coherencia predeterminado

1.  En la barra de accesos directos del [Portal de Azure](https://portal.azure.com/), haga clic en **DocumentDB (NoSQL)**.

2. En la hoja **DocumentDB (NoSQL)**, seleccione la cuenta de base de datos que se va a modificar.

3. En la hoja Cuenta, haga clic en **Coherencia predeterminada**.


4. En la hoja **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.

	![Captura de pantalla que muestra el icono Configuración y la entrada Coherencia predeterminada](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## Niveles de coherencia para consultas

De manera predeterminada, para los recursos definidos por el usuario, el nivel de coherencia de las consultas es igual que el de las lecturas. De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas de documentos. Aunque DocumentDB está optimizada para escrituras y admite volúmenes sostenidos de escrituras de documentos, un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinadas colecciones para actualizar el índice de manera diferida. La indización diferida incrementa aún más el rendimiento de las escrituras y es ideal para aquellos escenarios de ingesta en bloque en donde la carga de trabajo sea sobre todo de lecturas.

Modo de indexación|	Lecturas|	Consultas  
-------------|-------|---------
Coherente (predeterminado)|	Seleccionar entre fuerte, de obsolescencia entrelazada, de sesión y eventual|	Seleccionar entre fuerte, de obsolescencia entrelazada, de sesión y eventual|
Diferida|	Seleccionar entre fuerte, de obsolescencia entrelazada, de sesión y eventual|	Ocasional  

Al igual que con las solicitudes de lectura, puede disminuir el nivel de coherencia de una solicitud de consulta concreta si especifica el encabezado de solicitud [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx).

## Pasos siguientes

Si desea leer más sobre los niveles de coherencia y los compromisos, recomendamos los siguientes recursos:

-	Doug Terry. Vídeo Replicated Data Consistency explained through baseball (Coherencia de datos replicados explicada mediante el béisbol). [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-	Doug Terry. Explicación de la coherencia de datos replicados a través del béisbol. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Garantías de sesión para datos replicados con coherencia débil. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Compromisos de coherencia en el diseño de sistemas modernos de bases de datos distribuidas: CAP es solo una parte de la historia". [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Uso vinculado probabilístico (PBS) para cuórums parciales prácticos. [http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Coherencia ocasional - Revisión. [http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

<!---HONumber=AcomDC_0831_2016-->