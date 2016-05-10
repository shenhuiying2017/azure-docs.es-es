<properties
   pageTitle="Qué es Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# ¿Qué es Almacenamiento de datos SQL de Azure?

Almacenamiento de datos SQL de Azure es una base de datos de escalado horizontal y basada en la nube capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales). Almacenamiento de datos SQL se basa en nuestra arquitectura de procesamiento paralelo masivo (MPP) y puede controlar la carga de trabajo de la empresa.

Almacenamiento de datos SQL

- Combina nuestra reconocida base de datos relacional de SQL Server con nuestras funcionalidades de escalado horizontal en la nube de Azure. Puede aumentar, reducir, pausar o reanudar el proceso en segundos. Esto le permite ahorrar costos mediante el escalado horizontal de la CPU cuando se necesite y el recorte de su uso durante las horas de poca actividad.
- Saca provecho de la plataforma Azure. Es fácil de implementar, su mantenimiento no genera problemas y es totalmente tolerante a errores, gracias a las copias de seguridad automáticas. 
- Es el complemento perfecto del ecosistema de SQL Server. Le permite desarrollar con conocidas herramientas y T-SQL de SQL Server.

Siga leyendo para más información acerca de las principales características de Almacenamiento de datos SQL.

## Optimizado

### Arquitectura de procesamiento paralelo masivo (MPP)

Almacenamiento de datos SQL usa la arquitectura de procesamiento paralelo masivo (MPP) de Microsoft, que se diseñó para ejecutar algunos de los mayores almacenamientos de datos locales del mundo.

Actualmente, nuestra arquitectura de MPP propaga los datos entre 60 unidades de procesamiento y almacenamiento shared-nothing. Los datos se almacenan en blobs de Almacenamiento de Azure redundantes y con replicación geográfica, y están vinculados a los nodos de proceso para la ejecución de consultas. Con esta arquitectura, se puede adoptar un enfoque de "divide y vencerás" para ejecutar consultas T-SQL complejas. Cuando se realiza el procesamiento, el nodo de control analiza la consulta y luego cada nodo de proceso "conquista" su parte de los datos en paralelo.

Mediante la combinación de nuestra arquitectura de MPP y las funcionalidades de Almacenamiento de Azure, Almacenamiento de datos SQL puede:

- Aumentar o reducir el almacenamiento independiente del proceso.
- Aumentar o reducir el proceso sin mover datos. 
- Pausar la funcionalidad de proceso y, al mismo tiempo, mantener los datos intactos.
- Reanudar la capacidad de proceso inmediatamente tras un aviso.

La arquitectura se describe detalladamente a continuación.

![Arquitectura de Almacenamiento de datos SQL][1]


- **Nodo de control:** el nodo el control "controla" el sistema. Es el front-end que interactúa con todas las aplicaciones y conexiones. En Almacenamiento de datos SQL, el nodo de control tiene tecnología de Base de datos SQL y, al conectarse, adopta la misma apariencia. Debajo de la superficie, el nodo de control coordina todo el movimiento de datos y los cálculos requeridos para ejecutar consultas en paralelo de los datos distribuidos. Al enviar una consulta TSQL a Almacenamiento de datos SQL, el nodo de control lo transforma en consultas independientes que se ejecutarán en cada nodo de proceso en paralelo.

- **Nodos de proceso:** los nodos de proceso sirven como la potencia que hay detrás de Almacenamiento de datos SQL. Son bases de datos de SQL las que procesan los pasos de la consulta y administran los datos. Al agregar datos, Almacenamiento de datos SQL distribuye las filas mediante los nodos de proceso. Los nodos de proceso son también los trabajos que ejecutan las consultas en paralelo en los datos. Después del procesamiento, devuelven los resultados al nodo de control. Para finalizar la consulta, el nodo de control agrega los resultados y devuelve el resultado final.


- **Almacenamiento:** los datos se almacenan en blobs de Almacenamiento de Azure. Cuando los nodos de proceso interactúan con los datos, escribir y leen directamente del Almacenamiento de blobs. Dado que el Almacenamiento de Azure se amplía de forma transparente e ilimitada, el Almacenamiento de datos SQL puede hacer lo mismo. Puesto que el proceso y el almacenamiento son independientes, Almacenamiento de datos SQL puede escalar automáticamente el almacenamiento por separado del escalado del proceso, y viceversa. Almacenamiento de Azure también es totalmente tolerante a errores y simplifica el proceso de copia de seguridad y restauración.
   

- **Servicio de movimiento de datos:** el servicio de movimiento de datos (DMS) es nuestra tecnología para mover datos entre los nodos. DMS proporciona a los nodos de proceso acceso a los datos que necesitan para la combinaciones y agregaciones. DMS no es un servicio de Azure Es un servicio de Windows que se ejecuta junto con Base de datos SQL en todos los nodos. Dado que DMS se ejecuta en segundo plano, el usuario no interactúa directamente con él. Sin embargo, al examinar los planes de consulta observará incluyen varias operaciones de DMS, ya que para ejecutar cada consulta en paralelo, es preciso cierto movimiento de datos.


### Rendimiento de consultas optimizado

Además de la estrategia de "divide y vencerás", el enfoque MPP se refuerza con varias optimizaciones de rendimiento específicas del almacenamiento de datos, entre las que se incluyen:

- Un optimizador de consultas distribuido y un conjunto de estadísticas complejas en todos los datos. Gracias a la información sobre la distribución y el tamaño de los datos, el servicio puede optimizar las consultas, para lo que evalúa del costo de las operaciones de consulta específicas.

- Técnicas y algoritmos avanzados integrados en el proceso de movimiento de datos, con el fin de mover datos de forma eficaz entre los recursos de proceso según sea necesario para realizar la consulta. Estas operaciones de movimiento de datos están integradas y todas las optimizaciones del servicio de movimiento de datos se producen de forma automática.

- Índices de almacén de columnas en clúster de manera predeterminada Mediante el uso del almacenamiento basado en columnas, el Almacenamiento de datos SQL obtiene unas ganancias de compresión que quintuplican las del almacenamiento tradicional orientado a filas y que multiplica por 10 veces las del rendimiento de las consultas. Las consultas de Análisis que necesitan examinar un gran número de filas funcionan estupendamente en índices de almacén de columnas.

## Escalabilidad

La arquitectura del Almacenamiento de datos SQL presenta el proceso y el almacenamiento por separado, lo que permite a cada uno escalar de manera independiente. La estructura de implementación rápida y sencilla de la Base de datos SQL permite disponer de procesos adicionales en un instante. Como complemento, está el uso de Blobs de almacenamiento de Azure. El uso de blobs no solo nos ofrece un almacenamiento estable y replicado, sino que también proporciona la infraestructura necesaria para una realizar una expansión más fluida con un costo menor. Mediante esta combinación de almacenamiento de escala en la nube y de proceso de Azure, el Almacenamiento de datos SQL le permite pagar por un almacenamiento de rendimiento de consultas conforme lo necesita cuando lo necesita. El cambio de la cantidad de proceso es una operación muy simple, solo hay que mover un control deslizante del Portal de Azure hacia la derecha o izquierda, pero también se puede programar mediante T-SQL y PowerShell.

Junto con la capacidad de controlar totalmente la cantidad del proceso con independencia del almacenamiento, el Almacenamiento de datos SQL le permite realizar una pausa completa en el almacenamiento de datos. A la vez que mantiene el almacenamiento en su sitio, se lanzan todos los procesos al grupo principal de Azure, lo que supone un ahorro de dinero inmediato. Cuando sea necesario, solo tendrá que reanudar el proceso y tener los datos y el proceso disponibles para su carga de trabajo.

El uso de proceso en el Almacenamiento de datos SQL se mide en unidades de almacenamiento de datos (DWU) SQL. Los DWU son una medición de la potencia subyacente que tiene su almacenamiento de datos y están diseñados para garantizar que tenga una cantidad de rendimiento estándar asociada al almacenamiento en un momento dado. En concreto, usamos DWU para asegurarnos de que:

- Puede escalar de forma eficaz el almacenamiento de datos sin preocuparse por el hardware o software subyacente.

- Puede entender el rendimiento que verá en un nivel de DWU antes de cambiar el tamaño del almacenamiento de datos.

- Puede cambiar o mover el hardware y software subyacentes de la instancia sin que esto afecte al rendimiento de su carga de trabajo.

- Podemos hacer ajustes en la arquitectura subyacente del servicio sin que el rendimiento de la carga de trabajo se vea afectado.

- Según vamos mejorando rápidamente el rendimiento en el Almacenamiento de datos SQL, podemos garantizar que lo hacemos de forma que sea escalable y que afecte de manera uniforme al sistema.

### Unidades de almacenamiento de datos

En concreto, consideramos las unidades de almacenamiento de datos como una medición de tres métricas precisas que pensamos que están muy correlacionadas con el rendimiento de la carga de trabajo del almacenamiento de datos. Nuestro objetivo es que, para la disponibilidad general, estas métricas clave de carga de trabajo aumentarán linealmente con las DWU que ha elegido para el almacenamiento de datos.

**Examen y agregación:** esta métrica de carga de trabajo toma una consulta de almacenamiento de datos estándar que examina un gran número de filas y realiza después una agregación compleja. Se trata de una operación con un gran consumo de E/S y CPU.

**Carga:** esta métrica mide la capacidad de insertar datos en el servicio. Las cargas se completan con PolyBase que carga un conjunto de datos representativo de un Blob de almacenamiento de Azure. Esta métrica está diseñada para resaltar los aspectos de red y de CPU del servicio.

**CREATE TABLE AS SELECT (CTAS):** CTAS mide la capacidad de crear una copia de una tabla. Esto implica la lectura de datos del almacenamiento, su distribución entre los nodos del dispositivo y su escritura de nuevo en el almacenamiento. Se trata de una operación con un gran consumo de CPU y red.

### Cuándo se debe escalar

En general, queremos que las DWU sean sencillas. Cuando necesite resultados más rápidos, aumente el número de DWU y pague por un mayor rendimiento. Cuando necesite menos capacidad de proceso, reduzca el número de DWU y pague solo por lo que necesita. El momento para pensar en cambiar el número de DWU puede ser uno de los siguientes:

- Cuando no es necesario ejecutar consultas, tal vez por la noche o los fines de semana, ponga en pausa los recursos de proceso para cancelar todas las consultas en ejecución y eliminar todas las DWU asignadas a su almacenamiento de datos.

- Al realizar una operación de carga o de transformación de gran cantidad de datos, quizá desee escalar verticalmente para que los datos estén disponibles con mayor rapidez.

- Para entender el valor ideal de su DWU, intente escalar verticalmente hacia arriba y hacia abajo, y ejecute algunas consultas después de cargar los datos. Puesto que el escalado se realiza rápidamente, puede probar distintos niveles de rendimiento sin dedicarle más de una hora.

> [AZURE.NOTE] Tenga en cuenta que debido a la arquitectura o al Almacenamiento de datos SQL, puede que no vea el escalado del rendimiento esperado en volúmenes de datos más reducidos. Se recomienda comenzar con volúmenes de datos a partir de 1 TB o más a fin de obtener unos resultados de pruebas de rendimiento exactos.

## Integrado

El Almacenamiento de datos SQL se basa en el motor de base de datos relacional probado de SQL Server e incluye muchas de las características que se esperan de un almacenamiento de datos empresarial. Si ya conoce Transact-SQL, le resultará muy sencillo transferir sus conocimientos a Almacenamiento de datos SQL. Ya sea un usuario avanzado o principiante, los ejemplos que aparecen en la documentación le servirán de apoyo para comenzar. En general, puede pensar en la forma en que hemos construido los elementos de lenguaje del Almacenamiento de datos SQL del modo siguiente:

- El Almacenamiento de datos SQL usa la sintaxis de Transact-SQL (TSQL) de SQL Server para numerosas operaciones y admite un amplio conjunto de construcciones SQL tradicionales como procedimientos almacenados, funciones definidas por el usuario, partición de tablas, índices e intercalaciones.

- El Almacenamiento de datos SQL también contiene una serie de características innovadoras de SQL Server como índices de almacén de columnas en clúster, integración de PolyBase y auditoría de datos (completa con valoración de amenazas).

- Como el Almacenamiento de datos SQL está aún en desarrollo, es posible que algunos elementos del lenguaje TSQL que son menos habituales en las cargas de trabajo de almacenamiento de datos o son más recientes en SQL Server no estén disponibles en este momento. Consulte la documentación sobre migración para obtener más información relativa a este tema.

Con la uniformidad de Transact-SQL y las características comunes existentes entre SQL Server, Almacenamiento de datos SQL, Base de datos SQL y Sistema de plataforma de análisis, es posible desarrollar una solución que se ajuste a las necesidades de los datos. Puede decidir dónde guardar los datos, basándose en los requisitos de escala, seguridad y rendimiento y, a continuación, transferir los datos según sea necesario entre distintos sistemas.

Además de adoptar el área expuesta de TSQL de SQL Server, el Almacenamiento de datos SQL también se integra con numerosas herramientas ya conocidas por los usuarios de SQL Server. En concreto, nos hemos centrado en integrar algunas categorías de herramientas con el Almacenamiento de datos SQL, entre ellas:

**Herramientas tradicionales de SQL Server:** hay una integración completa con SQL Server Analysis Services, Integration Services y Reporting Services disponible con el Almacenamiento de datos SQL.

**Herramientas basadas en la nube:** el Almacenamiento de datos SQL se puede usar junto algunas herramientas nuevas de Azure y presenta una profunda integración con Factoría de datos de Azure, análisis de transmisiones, aprendizaje automático y Power BI.

**Herramientas de terceros:** un gran número de proveedores de herramientas de terceros ha certificado la integración de sus herramientas con el Almacenamiento de datos SQL. Consulte la lista completa.

## Híbrido

El uso del Almacenamiento de datos SQL con PolyBase proporciona a los usuarios una capacidad sin precedentes para mover datos en su ecosistema, lo que libera la capacidad de configurar escenarios híbridos avanzados con orígenes de datos no relacionales y locales.

Polybase es fácil de usar y permite aprovechar datos de orígenes diferentes con los mismos comandos conocidos de T-SQL. Polybase permite consultar los datos no relacionales que se mantienen en el Almacenamiento de blobs de Azure al igual que una tabla normal. Utilice Polybase para consultar los datos no relacionales o para importar datos no relacionales en Almacenamiento de datos SQL.

- PolyBase utiliza tablas externas para tener acceso a datos no relacionales. Las definiciones de tabla se almacenan en el Almacenamiento de datos SQL, y SQL y las herramientas pueden acceder a ellas del mismo modo que se accede a datos relacionales normales.

- Polybase es independiente en su integración. Presenta las mismas características y funcionalidad para todos los orígenes compatibles. Los datos leídos por Polybase pueden estar en una variedad de formatos, incluidos archivos delimitados o ORC.

- PolyBase se puede usar para acceder al Almacenamiento de blobs, que sirve también como almacenamiento para un clúster de HD Insight, lo que proporciona un acceso innovador a los mismos datos con herramientas relacionales y no relacionales.


## Pasos siguientes

Ahora que ya conoce un poco el Almacenamiento de datos SQL, obtenga información acerca de la [carga de trabajo de almacenamiento de datos], el [aprovisionamiento] y la carga de [datos de ejemplo] para comenzar.

>[AZURE.NOTE] Queremos mejorar este artículo. Si elige responder que no a la pregunta de si le resultó útil este artículo, incluya una sugerencia breve sobre lo que falta o cómo piensa que se podría mejorar el artículo. Gracias de antemano.

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[carga de trabajo de almacenamiento de datos]: ./sql-data-warehouse-overview-workload.md
[datos de ejemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0504_2016-->