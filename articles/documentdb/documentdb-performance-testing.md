<properties 
	pageTitle="Pruebas de escala y rendimiento de DocumentDB | Microsoft Azure" 
	description="Obtenga información sobre cómo realizar pruebas de escala y rendimiento con Azure DocumentDB"
	keywords="pruebas de rendimiento"
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2016" 
	ms.author="arramac"/>

# Pruebas de escala y rendimiento con Azure DocumentDB
Las pruebas de escala y rendimiento representan un paso clave en el desarrollo de aplicaciones. Para muchas aplicaciones, el nivel de la base de datos repercute significativamente en la escalabilidad y el rendimiento generales y, por tanto, es un componente esencial de las pruebas de rendimiento. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) está diseñado específicamente para el escalado flexible y un rendimiento predecible y, por lo tanto, supone una elección excelente para aplicaciones que necesitan un nivel de base de datos de alto rendimiento.

Este artículo es una referencia para los desarrolladores que implementan conjuntos de pruebas de rendimiento para sus cargas de trabajo de DocumentDB o evalúan DocumentDB para escenarios de aplicaciones de alto rendimiento. Se centra principalmente en las pruebas de rendimiento aislado de la base de datos, pero también incluye prácticas recomendadas para las aplicaciones de producción.

Después de leer este artículo, podrá responder a las siguientes preguntas:

- ¿Dónde puedo encontrar una aplicación cliente de .NET de ejemplo para pruebas de rendimiento de Azure DocumentDB?
- ¿Cuáles son los factores clave que afectan el rendimiento de un extremo a otro de las solicitudes realizadas a Azure DocumentDB? 
- ¿Cómo se pueden alcanzar niveles de alto rendimiento con Azure DocumentDB desde mi aplicación cliente?

Para empezar a trabajar con código, descargue el proyecto de [Ejemplo de pruebas de rendimiento de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark).

## Opciones de configuración de cliente importantes
DocumentDB es una base de datos distribuida rápida y flexible que se escala sin problemas con una latencia y un rendimiento garantizados. No es necesario realizar cambios de arquitectura importantes ni escribir código complejo para escalar el nivel de base de datos con DocumentDB. Escalar o reducir verticalmente es tan sencillo como realizar una única llamada de API o con el método SDK. Sin embargo, al realizar pruebas a escala, es importante tener en cuenta que se accede a DocumentDB a través de llamadas de red. Si escribe una aplicación de cliente independiente para realizar pruebas de rendimiento de DocumentDB, debe configurarla apropiadamente para contrarrestar el impacto de la latencia de red en las mediciones de rendimiento.

Para obtener el máximo rendimiento de un extremo a otro con DocumentDB, considere las siguientes opciones de configuración de cliente:

- **Aumentar el número de subprocesos o tareas**: dado que las llamadas a DocumentDB se realizan por la red, puede que necesite variar el grado de paralelismo de las solicitudes para reducir todo lo posible el tiempo que la aplicación espera entre una solicitud y otra. Por ejemplo, si utiliza la [biblioteca TPL](https://msdn.microsoft.com//library/dd460717.aspx) de .NET, cree en el pedido cientos de tareas de lectura o escritura en DocumentDB.
- **Pruebas en la misma región de Azure**: siempre que sea posible, realice las pruebas desde una máquina virtual o Servicio de aplicaciones implementados en la misma región de Azure. Para obtener una comparación aproximada, las llamadas a DocumentDB en la misma región se realizan en menos de 1 o 2 ms, pero la latencia entre las costas este y oeste de Estados Unidos es mayor de 50 ms.
- **Aumentar el valor de MaxConnections de System.Net por host**: las solicitudes de DocumentDB se realizan a través de HTTPS o REST de forma predeterminada y están condicionados por los límites de conexión predeterminados por nombre de host o dirección IP. Puede que deba establecerlo en un valor mayor (100-1000) para que la biblioteca del cliente pueda utilizar varias conexiones simultáneas a DocumentDB. En .NET, es [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).
- **Activar GC en el servidor**: en algunos casos, puede resultar útil reducir la frecuencia de recopilación de elementos no utilizados. En. NET, establezca [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) en true.
- **Usar la conectividad directa con el protocolo TCP**: use la [conectividad directa](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionmode.aspx) con [protocolo TCP](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.protocol.aspx) para obtener el mejor rendimiento. 
- **Implementar retrocesos en intervalos de RetryAfter**: durante las pruebas de rendimiento, debe aumentar la carga hasta que se limite una pequeña tasa de solicitudes. Si se limita, la aplicación del cliente debe retroceder de acuerdo con la limitación para el intervalo de reintento que el servidor especificó. Ello le permite dedicar una cantidad de tiempo de espera mínima entre reintentos. Consulte [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
- **Escalar horizontalmente la carga de trabajo del cliente**: si va a realizar pruebas en niveles de alto rendimiento (>50 000 RU/s), la aplicación del cliente puede convertirse en un cuello de botella debido a que la máquina limita el uso de CPU o la red. Si llega a este punto, puede seguir insertando la cuenta de DocumentDB mediante la escala horizontal de las aplicaciones cliente en varios servidores.

## Primeros pasos
La forma más rápida de empezar es compilar y ejecutar este ejemplo de .NET, tal como se describe en los pasos siguientes. También puede revisar el código fuente e implementar configuraciones similares a sus propias aplicaciones cliente.

**Paso 1:** descargue el proyecto del [ejemplo de pruebas de rendimiento de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) o copie el repositorio de Github.

**Paso 2:** modifique la configuración de EndpointUrl, AuthorizationKey, CollectionThroughput y DocumentTemplate (opcional) en el archivo App.config.

> [AZURE.NOTE] Antes de aprovisionar colecciones con un alto rendimiento, consulte el [página de precios](https://azure.microsoft.com/pricing/details/documentdb/) para estimar los costos por colección. DocumentDB factura el almacenamiento y el rendimiento por separado y por horas, por lo que puede ahorrar costos eliminando o reduciendo el rendimiento de las colecciones de DocumentDB una vez realizadas las pruebas.

**Paso 3:** compile y ejecute la aplicación de consola en la línea de comandos. El resultado debe ser parecido a lo siguiente:

	Summary:
	---------------------------------------------------------------------
	Endpoint: https://docdb-scale-demo.documents.azure.com:443/
	Collection : db.testdata at 50000 request units per second
	Document Template*: Player.json
	Degree of parallelism*: 500
	---------------------------------------------------------------------

	DocumentDBBenchmark starting...
	Creating database db
	Creating collection testdata
	Creating metric collection metrics
	Retrying after sleeping for 00:03:34.1720000
	Starting Inserts with 500 tasks
	Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
	Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
	Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
	Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
	Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
	Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
	Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
	Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
	Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
	Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
	Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
	Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
	Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
	Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
	Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
	Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
	Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
	Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
	Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
	Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

	Summary:
	---------------------------------------------------------------------
	Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
	---------------------------------------------------------------------
	DocumentDBBenchmark completed successfully.


**Paso 4 (si es necesario):** el rendimiento notificado (RU/s) de la herramienta debe ser igual o mayor que el rendimiento de aprovisionamiento de la colección. Si no es así, puede alcanzar el límite si aumenta el valor de DegreeOfParallelism en incrementos pequeños. Si el rendimiento de la aplicación cliente se estanca, iniciar varias instancias de la aplicación en los mismos equipos u otros distintos lo ayudará a alcanzar el límite de aprovisionamiento en distintas instancias. Si necesita ayuda con este paso, póngase en contacto con nosotros a través de [Ask DocumentDB](askdocdb@microsoft.com) (Solicitar DocumentDB) o presente una incidencia de soporte técnico.

Una vez que se ejecute la aplicación, puede probar diferentes [directivas de indexación](documentdb-indexing-policies.md) y [niveles de coherencia](documentdb-consistency-levels.md) para conocer su repercusión en el rendimiento y la latencia. También puede revisar el código fuente e implementar configuraciones similares a sus propios conjuntos de pruebas o aplicaciones de producción.

## Resumen
En este artículo, hemos examinado cómo puede realizar el rendimiento y escalar pruebas con DocumentDB mediante una aplicación de consola .NET, y además hemos repasado las opciones de configuración clave para obtener el mejor rendimiento de Azure DocumentDB. Consulte los siguientes vínculos para obtener información adicional acerca de cómo trabajar con DocumentDB.

* [Ejemplo de pruebas de rendimiento de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Creación de particiones en el lado servidor en DocumentDB](documentdb-partition-data.md)
* [Colecciones y niveles de rendimiento de DocumentDB](documentdb-performance-levels.md)
* [Documentación del SDK de .NET de DocumentDB en MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Ejemplos de .NET de DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB sobre sugerencias de rendimiento](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)

<!---HONumber=AcomDC_0525_2016-->