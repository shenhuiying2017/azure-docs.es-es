<properties
   pageTitle="Carga de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Conozca los escenarios comunes para la carga de datos en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# Carga de datos en Almacenamiento de datos SQL
Almacenamiento de datos SQL ofrece numerosas opciones para la carga de datos en diferentes escenarios. Por ejemplo, es posible que desee cargar:

- lotes grandes una vez al día,
- lotes más pequeños a lo largo del día o
- actualizaciones sencillas para tablas (de dimensiones) más pequeñas

Las necesidades de cada escenario son tan diversas como los tipos de datos que se desean cargar en Almacenamiento de datos SQL. En este artículo enumeramos algunas de las opciones disponibles para cargar datos.

## Tecnologías
El servicio Almacenamiento de datos SQL es compatible con estas herramientas estándar de carga de datos:

- Factoría de datos de Azure
- utilidad de línea de comandos bcp (programa de copia masiva)
- PolyBase
- SQL Server Integration Services (SSIS)
- Herramientas de carga de datos de terceros

### Factoría de datos de Azure (ADF)
ADF es un servicio totalmente administrado para la composición de servicios de almacenamiento, procesamiento y movimiento de datos en canalizaciones de producción de datos optimizadas, escalables y confiables. Almacenamiento de datos SQL es un [origen/receptor admitido][] en la [actividad de copia][] de ADF.

### utilidad de línea de comandos bcp (programa de copia masiva)
El ejecutable de línea de comandos **bcp** es una utilidad de Microsoft que permite la carga y extracción de datos de SQL Server, Base de datos SQL y Almacenamiento de datos SQL. Para comenzar, siga el tutorial [Carga de datos con bcp][].

### PolyBase
PolyBase es una tecnología de Microsoft que simplifica el análisis de los datos proporcionando una forma de consulta al almacenamiento de blobs de Almacenamiento de Azure y Hadoop, todo ello con Transact-SQL estándar y sin usar MapReduce. PolyBase también puede cargar datos del almacenamiento de blobs de Azure en Almacenamiento de datos SQL. Para comenzar, siga el tutorial [Carga con PolyBase][].

### SQL Server Integration Services (SSIS)
[SSIS][] es una plataforma para compilar soluciones de integración y transformación de datos de nivel empresarial. Para generar paquetes que se conecten a Almacenamiento de datos SQL, use el [adaptador de destino OLE DB][] estándar con un administrador de conexión de ADO.Net.

### Herramientas de terceros
Almacenamiento de datos SQL es compatible con soluciones líderes de la industria para la carga de datos. Para obtener más detalles, consulte nuestra lista de [soluciones de asociados][].

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Carga de datos con bcp]: sql-data-warehouse-load-with-bcp.md
[Carga con PolyBase]: sql-data-warehouse-load-with-polybase.md
[soluciones de asociados]: sql-data-warehouse-solution-partners.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[origen/receptor admitido]: https://msdn.microsoft.com/library/dn894007.aspx
[actividad de copia]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO5-->