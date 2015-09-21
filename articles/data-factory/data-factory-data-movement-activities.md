<properties 
	pageTitle="Actividades de movimiento de datos" 
	description="Obtenga información sobre las entidades de Factoría de datos que puede usar en las canalizaciones de Factoría de datos para mover datos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Actividades de movimiento de datos
Factoría de datos tiene un [servicio disponible globalmente](#global) para admitir el movimiento de datos con [actividad de copia](#copyactivity) en diversos almacenes de datos que se muestran a continuación. Factoría de datos también tiene compatibilidad integrada para [mover los datos de forma segura entre ubicaciones locales y la nube](#moveonpremtocloud) mediante la puerta de enlace de administración de datos.

## Almacenes de datos admitidos para la actividad de copia
La actividad de copia copia los datos de un almacén de datos de **origen** a un almacén de datos **receptor**. Factoría de datos admite las siguientes combinaciones de almacenes de datos, origen y receptor. Haga clic en un almacén de datos para obtener información sobre cómo copiar datos desde/a ese almacén.

| **Origen** | **Sink** |
| ------ | ---- |
| [Blob de Azure](data-factory-azure-blob-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS, DocumentDB de Azure, sistema de archivos local |
| [Tabla de Azure](data-factory-azure-table-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS, DocumentDB de Azure |
| [Base de datos SQL de Azure](data-factory-azure-sql-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS, DocumentDB de Azure |
| [DocumentDB de Azure](data-factory-azure-documentdb-connector.md) | Blob de Azure, tabla de Azure, Base de datos SQL de Azure |
| [SQL Server en IaaS](data-factory-sqlserver-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Sistema de archivos local](data-factory-onprem-file-system-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS, sistema de archivos local |
| [SQL Server local](data-factory-sqlserver-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos de Oracle local](data-factory-onprem-oracle-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos MySQL local](data-factory-onprem-mysql-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos DB2 local](data-factory-onprem-db2-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos Teradata local](data-factory-onprem-teradata-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos Sybase local](data-factory-onprem-sybase-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |
| [Base de datos PostgreSQL local](data-factory-onprem-postgresql-connector.md) | Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, SQL Server local, SQL Server en IaaS |

## <a name="copyactivity"></a>Actividad de copia
La actividad de copia toma un conjunto de datos de entrada (**origen**) y copia los datos de acuerdo con la configuración de la actividad en un conjunto de datos de salida (**receptor**). La copia de datos se realiza por lotes según la programación especificada en la actividad.

> [AZURE.NOTE]Para obtener información acerca de cómo definir actividades en general en un nivel alto como varias secciones JSON y las propiedades disponibles para todas las actividades, consulte el artículo [Descripción de canalizaciones y actividades](data-factory-create-pipelines.md).

La actividad de copia proporciona las siguientes capacidades:

### <a name="global"></a>Movimiento de datos disponibles globalmente
La actividad de copia que proporciona el servicio de movimiento de datos está disponible globalmente en las siguientes regiones y zonas geográficas. La topología disponible globalmente garantiza un movimiento de datos eficiente que evita saltos entre regiones en la mayoría de los casos.

| Region | Geography |
| ------ | --------- | 
| Central EE. UU.: | US |
| Este de EE. UU. | US |
| Este de EE. UU. 2 | US |
| Centro-Norte de EE. UU | US |
| Centro-Sur de EE. UU | US |
| Oeste de EE. UU. | US |
| Sur de Brasil | LATINOAMÉRICA |
| Europa del Norte | EMEA |
| Europa occidental | EMEA |
| Sudeste asiático | Asia y Pacífico Sur |
| Este de Japón | Asia y Pacífico Sur |

### <a name="moveonpremtocloud"></a>Movimiento de datos seguro entre la nube y la ubicación local
Uno de los desafíos de la integración de datos moderna es mover datos sin problemas entre ubicación la local y la nube. Data management gateway es un agente que puede instalar de forma local para permitir canalizaciones de datos híbridas.

La puerta de enlace de datos proporciona las siguientes capacidades:

1.	Administrar el acceso a almacenes de datos locales de forma segura.
2.	Usar modelos de almacenes de datos locales y almacenes de datos en la nube dentro de la misma factoría de datos y mover los datos.
3.	Tener un solo panel de vidrio para la supervisión y administración con visibilidad del estado de la puerta de enlace mediante el panel basado en la nube de la factoría de datos.


Consulte [Movimiento de datos entre una ubicación local y la nube](data-factory-move-data-between-onprem-and-cloud.md)para obtener más detalles.

### Movimiento de datos confiable y rentable
La actividad de copia está diseñada para mover grandes volúmenes de datos de forma segura, resistente a errores transitorios a través de una gran variedad de orígenes de datos. Se pueden copiar datos de una manera rentable con la posibilidad de habilitar la compresión por la red.

### Conversión de los tipos entre sistemas de tipos diferentes
Los diferentes almacenes de datos tienen sistemas con distintos tipos nativos. La actividad de copia realiza conversiones automáticas de los tipos del origen a los tipos del receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Puede encontrar la asignación de un determinado sistema de tipo nativo a .NET para el almacén de datos en los respectivos artículos del conector del almacén de datos. Puede usar estas asignaciones para determinar los tipos adecuados al crear las tablas, de forma que se realicen las conversiones adecuadas durante la actividad de copia.

### Trabajo con diferentes formatos de archivo
Para los orígenes basados en archivos, la actividad de copia admite una gran variedad de formatos de archivo, incluidos los formatos binario, texto y Avro. Puede usar la actividad de copia para convertir de un formato a otro. Ejemplo: texto (CSV) para Avro.

### Propiedades de la actividad de copia
Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad.

En el caso de la actividad de copia, la sección **typeProperties** varía en función de los tipos de origen y receptor. En cada la página específica del almacén de datos en los documentos enumerados anteriormente se encuentran las propiedades específicas del tipo de almacén de datos.


## Enviar comentarios
Agradecemos sus comentarios sobre este artículo. Dedique unos minutos a enviar sus comentarios por [correo electrónico](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-movement-activities.md).

<!---HONumber=Sept15_HO2-->