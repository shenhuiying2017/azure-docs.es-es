<properties
   pageTitle="Qué es Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="barbkess;twounder;JRJ@BigBangData.co.uk;"/>


# ¿Qué es Almacenamiento de datos SQL de Azure?

Almacenamiento de datos SQL de Azure es una base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos con capacidades de SQL demostradas.

Almacenamiento de datos SQL combina la mejor calidad y confiabilidad de las bases de datos relacionales de clase empresarial de SQL Server con informática en la nube. Siga leyendo acerca de los componentes principales del diseño de Almacenamiento de datos SQL.

## Aumento, reducción o pausa de los recursos de proceso
El Almacenamiento de datos SQL presenta el proceso y el almacenamiento separado, lo que permite a cada uno escalar de manera independiente. Mediante el almacenamiento de escala de nube y el proceso de Azure, el Almacenamiento de datos SQL le permite pagar por el rendimiento de consultas conforme lo necesita cuando lo necesita. Ahora puede implementar dinámicamente, aumentar, reducir e incluso pausar el proceso en segundos para que pueda comprar la información que su empresa necesita. El cambio de la cantidad del proceso es tan sencillo como mover un control deslizante hacia la izquierda o la derecha.

Con la capacidad para controlar por completo la cantidad del cálculo con independencia del almacenamiento, el Almacenamiento de datos SQL le permite pausar el proceso. Al mantenerlo local, el proceso se vuelve a insertar en el grupo central, lo que le permite ahorrar costos. Cuando sea necesario, solo tendrá que reanudar el proceso y tener los datos y el proceso disponibles para su carga de trabajo.

> [AZURE.NOTE]La unidad de almacenamiento de datos (DWU) es la unidad de escala para los recursos de proceso en Almacenamiento de datos SQL.

- Cuando necesite resultados con más rapidez, aumente las DWU y pague por un mayor número de DWU; cuando no precise tanta capacidad de proceso, disminuya las DWU y vuelva a pagar por una cantidad menor de unidades. La capacidad de proceso aumenta en proporción al número de DWUs: si se duplican las DWU, se duplican los recursos de proceso. 
- Cuando no es necesario ejecutar consultas, tal vez por la noche o los fines de semana, ponga en pausa los recursos de proceso para cancelar todas las consultas en ejecución y eliminar todas las DWU asignadas a su almacenamiento de datos. El almacenamiento de datos permanece intacto, pero no hay ningún cargo por los recursos de proceso. Cuando necesite iniciar la ejecución de consultas, quizá el lunes por la mañana, reanude los recursos de proceso. 

## Índices de almacén de columnas y procesamiento masivo en paralelo para un rendimiento definitivo
Almacenamiento de datos SQL usa la arquitectura de procesamiento en paralelo masivo (MPP) de Microsoft y la tecnología de índice de almacén de columnas de SQL Server para ofrecer un rendimiento sin parangón.

> [AZURE.NOTE]MPP adopta un enfoque de "divide y vencerás" para solucionar problemas de datos a gran escala mediante el uso de los recursos de proceso en paralelo. Los datos se dividen y se distribuyen entre muchos recursos de proceso y cada recurso opera en paralelo en su parte de los datos.

- Gran parte del ingrediente secreto está en la tecnología de consultas distribuidas de Microsoft. Almacenamiento de datos SQL usa un optimizador de consultas avanzadas que determina cómo optimizar las consultas distribuidas según la evaluación del costo de las operaciones de consulta. También tiene algoritmos y técnicas avanzados que mueven los datos entre los recursos de proceso según sea necesario para realizar la consulta de forma eficaz.
- Los índices de almacén de columnas son fundamentales para conseguir un rendimiento rápido en las consultas de almacenamiento de datos. Mediante el uso del almacenamiento basado en columnas, los índices de almacén de columnas obtienen hasta 5 veces más ganancias de compresión que el almacenamiento tradicional orientado a filas y hasta 10 veces el rendimiento de la consulta. Las consultas del almacenamiento de datos funcionan a la perfección en los índices de almacén de columnas porque a menudo exploran toda la tabla o la partición entera de una tabla. En cambio, las consultas OLTP funcionan muy bien en los índices de árbol binario debido a que buscan en determinadas filas de la tabla.


## Experiencia de nube híbrida con SQL Server de clase empresarial
Almacenamiento de datos SQL se basa en el motor de base de datos relacional de SQL Server de eficacia probada e incluye las características que se esperan de un almacenamiento de datos empresariales, incluidos los procedimientos almacenados, las funciones definidas por el usuario, la partición de tablas, los índices y las intercalaciones.

> [AZURE.NOTE]Si ya conoce Transact-SQL, le resultará muy sencillo transferir sus conocimientos a Almacenamiento de datos SQL. Los ejemplos de la documentación ayudan a empezar tanto a los expertos como a los novatos.

- Almacenamiento de datos SQL usa Transact-SQL de SQL Server, el índice de almacén de columnas y las tecnologías PolyBase junto con la arquitectura de procesamiento paralelo masivo (MPP) del sistema de plataforma analítica para crear esta experiencia de almacenamiento de datos de plataforma como-servicio (PaaS) única e integrada.  

- Con la uniformidad de Transact-SQL y las características comunes existentes entre SQL Server, Almacenamiento de datos SQL, Base de datos SQL y Sistema de plataforma de análisis, es posible desarrollar una solución que se ajuste a las necesidades de los datos. Puede decidir dónde guardar los datos, basándose en los requisitos de escala, seguridad y rendimiento y, a continuación, transferir los datos según sea necesario entre el almacenamiento local y la nube.


## Consultas en datos relacionales y no relacionales
Polybase permite consultar los datos no relacionales mantenidos en el almacenamiento de blobs de Azure o en el sistema de archivos de Hadoop (HDFS) como si fuera una tabla normal. Utilice Polybase para consultar los datos no relacionales o para importar datos no relacionales en Almacenamiento de datos SQL.

> [AZURE.NOTE]Polybase es fácil de usar y permite aprovechar los datos de orígenes diferentes mediante el uso de los mismos comandos T-SQL que ya son conocidos. No hay ninguna necesidad de aprender HiveQL u otro lenguaje para beneficiarse de Hadoop.

- Polybase es independiente en su integración. Expone las mismas características y funcionalidad para todas las distribuciones compatibles de Hadoop. Los datos leídos por Polybase pueden estar en una variedad de formatos, incluidos archivos delimitados o ORC.
- PolyBase utiliza tablas externas para tener acceso a datos no relacionales. Las definiciones de tabla se almacenan en el almacenamiento de datos SQL y los datos se almacenan externamente en el almacenamiento de blobs de Azure o de Hadoop.


## Implementación segura de infraestructuras sin costos de mantenimiento
Almacenamiento de datos SQL se implementa fácilmente en cuestión de segundos. Este servicio es una oferta totalmente administrada que elimina la molestia de invertir tiempo en revisiones y mantenimiento de software. Almacenamiento de datos SQL tiene copias de seguridad integradas que admiten la restauración de autoservicio; el servicio realiza automáticamente una copia de los datos en el almacenamiento de Azure al hacer instantáneas de los puntos de restauración de la base de datos.


## Pasos siguientes
Obtenga información sobre [carga de trabajo del almacenamiento de datos]. [Aprovisionar]y cargar[datos de ejemplo] para comenzar.

<!--Image references-->

<!--Article references-->
[carga de trabajo del almacenamiento de datos]: ./sql-data-warehouse-overview-workload.md
[datos de ejemplo]: ./sql-data-warehouse-get-started-load-samples.md
[Aprovisionar]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->