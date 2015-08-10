<properties 
	pageTitle="Opciones para transferir datos almacenados a la nube | Azure" 
	description="Instrucciones sobre cómo elegir la mejor opción para transferir datos locales o de otros orígenes de nube a Microsoft Azure a fin de someterlos a un análisis avanzado." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jeffgoll" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="spelluru"/>

# Opciones para transferir datos almacenados a la nube de Azure

En este artículo, se proporcionan instrucciones sobre cómo elegir la opción adecuada para transferir datos desde una implementación local u otros orígenes de nube a Microsoft Azure a fin de someterlos a un análisis de datos avanzado. Transferir grandes cantidades de datos puede requerir mucho tiempo y exigir una seguridad adecuada.

En este artículo:

* [Servicio de importación/exportación de Azure para el almacenamiento de blobs](#blob)
* [Utilidad AZCopy](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Factoría de datos de Azure (vista previa)](#data-factory)
* [Herramientas de migración de Base de datos SQL de Azure](#tools)
* [Vista previa de SQL Azure Data Sync](#data-sync)
* [Centros de eventos de Azure](#event-hubs)
* [Otras opciones para transferir datos](#other)
* [Elección de la opción de transferencia de datos adecuada](#choose)


## Servicio de importación/exportación de Azure para el almacenamiento de blobs

Puede utilizar el servicio de importación y exportación de Azure para transferir grandes cantidades de datos de archivo desde o hasta el almacenamiento de blobs de Azure en situaciones en las que el proceso de carga o descarga mediante la red sea prohibitivamente caro o no sea viable. Los conjuntos de datos grandes tardan mucho tiempo en cargarse o descargarse a través de la red. Por ejemplo, 10 TB tardan 1 mes si se usa T3 (44,7 Mbps). Con el servicio de importación y exportación de Microsoft Azure, los clientes pueden enviar el disco duro para reducir el tiempo de carga o descarga de datos. Planee varios días, incluido el envío.

Para transferir un gran conjunto de datos de archivo al almacenamiento de blobs, puede enviar uno o varios discos duros que contengan los datos a un centro de datos de Azure, en el que se cargarán sus datos en la cuenta de almacenamiento. De forma similar, para exportar datos desde el almacenamiento de blobs, puede enviar discos duros vacíos a un centro de datos de Azure, en el que los datos de blobs de su cuenta de almacenamiento se copiarán en sus discos duros, y, a continuación, se le devolverán. Antes de enviar una unidad que contiene datos, deberá cifrar los datos de la unidad; cuando el servicio de importación y exportación exporte sus datos para enviárselos, los datos se cifrarán igualmente antes del envío.

Para obtener más información, consulte [Uso del servicio de importación y exportación de Microsoft Azure para transferir datos al almacenamiento en blobs][import-export].


## Utilidad AZCopy

AzCopy es una utilidad de línea de comandos diseñada para realizar operaciones de alto rendimiento de carga, descarga y copia de datos a y desde Almacenamiento de blobs, archivos y tablas de Microsoft Azure. Esta utilidad es adecuada para movimientos de datos puntuales entre el equipo local y el almacenamiento de Azure, en caso de que la transferencia de datos a través de la red sea viable. Consulte [Introducción a la utilidad de línea de comandos AzCopy][azcopy]

> [AZURE.NOTE]Usuarios de Linux: [descargue ACP, AzCopy para Linux](http://www.paratools.com/acp)


## Azure PowerShell

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Puede usar Azure PowerShell para cargar datos en el almacenamiento de blobs, a fin de que los datos se puedan procesar mediante trabajos de MapReduce o análisis avanzados.

Consulte también:

* [Carga de datos en el almacenamiento de blobs mediante Azure PowerShell][upload]
* [Instalación y configuración de Azure PowerShell][install]


## Factoría de datos de Azure (vista previa)

La Factoría de datos de Azure es un servicio totalmente administrado para la composición de servicios de almacenamiento, procesamiento y movimiento de datos en canalizaciones de producción de datos optimizadas, escalables y confiables.

Los desarrolladores pueden crear flujos de trabajo orientados a datos que combinan, agregan y transforman datos semiestructurados, desestructurados y estructurados procedentes de sus servicios locales, (mediante Data Management Gateway) basados en la nube y de Internet, además de configurar procesamientos de datos complejos a través de scripting de JSON simple. Los datos resultantes pueden guardarse en el almacenamiento de Azure o en Base de datos SQL de Azure para someterlos a análisis avanzados.

En concreto, un desarrollador puede organizar las actividades de copia regulares entre varios orígenes y destinos que se muestran en la sección "[Orígenes y receptores compatibles](data-factory-copy-activity.md#SupportedSourcesAndSinks)" de [Copia de datos con la Factoría de datos de Azure (actividad de copia)](data-factory-copy-activity.md), que también incluye propiedades para diferentes tipos de almacenes de datos, asignación de columnas, formatos de serialización y tratamiento de tipos.

El servicio puede controlar los errores con reinicio automático y permitir la conversión de formato al mover datos de un formato a otro. Para definir una actividad de copia, consulte [Introducción a la Factoría de datos de Azure][start]. Los procedimientos de instalación de una puerta de enlace y de registro del almacén de datos se describen en [Habilitación de canalizaciones para trabajar con datos locales][pipelines].

Consulte también:

* [Introducción al servicio Factoría de datos de Azure][intro]

## Herramientas de migración de Base de datos SQL de Azure

Hay muchas herramientas disponibles para migrar correctamente SQL Server local y las bases de datos que no sean de SQL Server a Base de datos SQL de Azure. La mejor herramienta para cada escenario depende del tipo, el tamaño y la complejidad de la base de datos que se vaya a migrar:

* Puede migrar el esquema y los datos de una Base de datos SQL de Azure existente; para ello, debe exportar la base de datos, almacenar el archivo de exportación en una cuenta de almacenamiento de blob de Azure y, a continuación, efectuar la importación como una nueva Base de datos SQL de Azure. El archivo que se crea cuando se realiza esta exportación se denomina "archivo BACPAC". Para obtener más información, consulte [Procedimiento: uso del servicio de importación y exportación en Base de datos SQL de Azure][sql-import].
* La característica de copia de base de datos crea una nueva base de datos en Azure que es una copia transaccional y coherente de una Base de datos SQL de Azure existente. Para obtener más información, consulte [Copiar bases de datos en Base de datos SQL de Azure][sql-copy].
* Los servicios SQL Server Integration Services (SSIS) se pueden usar cuando se necesitan transformaciones complejas de datos. Puede usar SSIS para mover datos hacia y desde Base de datos SQL de Azure. Para obtener más información, consulte [Procedimiento: Usar Integration Services para migrar una base de datos a Base de datos SQL de Azure][integrate] y [SSIS para Azure y movimientos de datos híbridos][SSIS].
* El Asistente para importación y exportación de SQL Server es una forma sencilla de crear un paquete SSIS para migrar datos. Después de configurar el origen y el destino, puede especificar transformaciones de datos básicas. Estos paquetes se pueden guardar, modificar, ejecutar y programar como un trabajo. Para obtener más información, consulte [Procedimiento: Usar el Asistente para importación y exportación para migrar una base de datos a Base de datos SQL de Azure][wizard].
* El Asistente para migración de Base de datos SQL es una herramienta que ayuda a migrar el esquema y los datos entre el SQL Server local y Base de datos SQL de Azure, así como entre los servidores de Base de datos SQL de Azure. La herramienta también analiza los archivos de seguimiento y los scripts para detectar problemas de compatibilidad con Base de datos SQL de Azure. Para obtener más información, consulte [Procedimiento: uso del Asistente para migración de Base de datos SQL][use-wizard].
* La utilidad bcp puede usarse para importar grandes cantidades de filas nuevas en tablas de SQL Server o para exportar datos de tablas en archivos de datos. Para obtener más información, consulte [Procedimiento: cómo usar bcp para migrar una base de datos a Base de datos SQL de Azure][bcp].

Consulte también:

* [Migrar bases de datos a bases de datos SQL de Azure][migrate]
 

## Vista previa de SQL Azure Data Sync

Vista previa de SQL Data Sync permite crear y programar sincronizaciones periódicas entre Base de datos SQL de Azure y las bases de datos hospedadas en SQL Server o en Base de datos SQL de Azure.

SQL Data Sync es adecuado para que los desarrolladores de datos sincronicen los cambios delta entre las bases de datos locales y las de Azure en la nube. Consulte [SQL Data Sync][sync].

##	Centros de eventos de Azure

Centros de eventos de Microsoft Azure es un servicio de administración de eventos que proporciona ingresos de telemetría y eventos a la nube a escala masiva, con una latencia baja y una confiabilidad alta. Este servicio, que se utiliza con otros servicios del flujo de trabajo, es especialmente útil en escenarios de Internet de las cosas, procesamiento de flujo de trabajo, experiencia del usuario e instrumentación de aplicaciones.

Los desarrolladores pueden escribir código para enviar datos a un centro de eventos, procesar los datos y almacenarlos en Azure Blob o en Base de datos SQL de Azure para su procesamiento.

Como alternativa, los desarrolladores pueden aprovechar Azure Stream Analytics, un servicio completamente administrado que proporciona un procesamiento de eventos complejo y escalable de los datos de transmisión para la salida. Los desarrolladores pueden elegir una secuencia en el centro de eventos de Azure, especificar el formato que se utiliza para serializar el evento de entrada (por ejemplo, los formatos JSON, CSV o Avro) y, a continuación, agregar la ubicación de salida, incluidos los blobs de Azure o Base de datos SQL de Azure.

Consulte:

* [Información sobre el servicio de Centro de eventos](/services/event-hubs/)
* [Información general de los centros de eventos][overview]
* [Introducción al Análisis de transmisiones de Azure][stream]

## Otras opciones para transferir datos

Las conexiones híbridas ofrecen un modo sencillo y práctico de conectar sitios web y servicios móviles de Azure a recursos locales. Los desarrolladores pueden crear sitios web para mover datos desde el entorno local a Azure. Consulte [Introducción a las conexiones híbridas][hybrid] para obtener más información.

Con [Red virtual](/services/virtual-network/), puede utilizar las herramientas de integración de datos que se ejecutan en la máquina virtual de Azure para conectarse de forma segura a bases de datos SQL Server locales en su centro de datos in situ. Solo las máquinas virtuales y los servicios incluidos en la misma red virtual pueden identificarse o conectarse entre sí. Si lo prefiere, puede incluso crear una conexión directa [ExpressRoute](/services/expressroute/) con Azure a través de su proveedor de servicios de red o el proveedor de Exchange, y omitir la red pública Internet.

[Azure Marketplace](?../source=datamarket.md) ofrece soluciones de socios que permiten mover datos a Azure, por ejemplo, mediante el protocolo Storm Managed File Transfer.

## Elección de la opción de transferencia de datos adecuada

### Árbol de decisión

![Ayuda para decidir la opción de transferencia de datos a la nube que se elige.][decision]

Notas sobre el árbol de decisión:

* Vista previa de SQL Azure Data Sync desencadena una sincronización a partir de los datos modificados.
* Factoría de datos de Azure (vista previa) es compatible con la copia de datos entre el almacenamiento de Azure, Base de datos SQL de Azure y SQL Server en local. 
* Además de utilizar Factoría de datos, puede ampliar los paquetes SSIS existentes para mover datos a la nube de manera programada.

### Mover GB de datos

<table border="1">
<tr>
<th>Mover datos</th>
<th>Una vez</th>
<th>Programado</th>
<th>Consideraciones</th>
</tr>

<tr>
<td><p>De archivo a almacenamiento de Azure</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Importación/Exportación de Azure</a></li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p>Utilice las importaciones y exportaciones de Azure en lugar de AzCopy, acp y Azure PowerShell cuando no se pueda realizar la transferencia de datos a través de la red. El servicio puede tardar hasta varios días, además del envío del disco al centro de datos.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server a Base de datos SQL de Azure</li>
<li>Base de datos SQL de Azure a SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Factoría de datos de Azure</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">Herramientas de migración de Base de datos SQL</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Factoría de datos de Azure</a></p>
</td>
<td><p>Se recomienda el uso de Factoría de datos de Azure (vista previa) en lugar de las herramientas de migración, que ofrece conversión de formato y recuperación automática de trabajos en casos excepcionales. Si SQL Server está instalado localmente, se necesita Data Management Gateway para conectarse a SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Del almacenamiento de Azure a Base de datos SQL de Azure</li>
<li>De Base de datos SQL de Azure al almacenamiento de Azure</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Factoría de datos de Azure</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Factoría de datos de Azure</a></p>
</td>
<td><p>Factoría de datos de Azure (vista previa) ofrece conversión de formato y recuperación automática de trabajo en casos excepcionales. Si SQL Server está instalado localmente, se necesita Data Management Gateway para conectarse a SQL Server.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>De SQL Server a SQL Data Sync</li>
<li>De SQL Data Sync a Base de datos SQL de Azure</li>
</ul>
</td>
<td><p>N/D</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">SQL Data Sync</a></p>
</td>
<td><p>Vista previa de SQL Data Sync sincroniza los datos mediante grupos de sincronización que definen las bases de datos, las tablas y las columnas que desea sincronizar, así como la programación de sincronización.</p>
</td>
</tr>

</table>



### Mover TB de datos

<table border="1">
<tr>
<th>Mover datos</th>
<th>Una vez</th>
<th>Programado</th>
<th>Consideraciones</th>
</tr>

<tr>
<td><p>De archivo a almacenamiento de Azure</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Importación/Exportación de Azure</a></p>
</td>
<td><p>N/D</p>
</td>
<td><p>El servicio puede tardar hasta varios días, además del tiempo necesario para que el disco llegue al centro de datos.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Azure Import/Export service for Blob storage]: #blob
[AZCopy utility]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (preview)]: #data-factory
[Azure SQL Database migration tools]: #tools
[Azure SQL Data Sync (preview)]: #data-sync
[Azure Event Hubs]: #event-hubs
[Other options for data transfer]: #other
[Choose the right data transfer option]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md
 

<!---HONumber=July15_HO5-->