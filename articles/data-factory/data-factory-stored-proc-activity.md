<properties 
	pageTitle="Actividad de procedimiento almacenado de SQL Server"
	description="Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure desde una canalización de Factoría de datos."
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
	ms.date="08/04/2015"
	ms.author="spelluru"/>

# Actividad de procedimiento almacenado de SQL Server

Puede usar la actividad de procedimiento almacenado de SQL Server en una [canalización](data-factory-create-pipelines.md) de Factoría de datos para invocar un procedimiento almacenado en una Base de datos **SQL de Azure**. Este artículo se basa en las [actividades de transformación de datos](data-factory-data-transformation-activities.md) que presenta una descripción general de la transformación de datos y las actividades de transformación admitidas.

## Sintaxis
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Detalles de la sintaxis

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
name | Nombre de la actividad | Sí
description | Texto que describe para qué se usa la actividad. | No
type | SqlServerStoredProcedure | Sí
inputs | Los conjuntos de datos de entrada que deben estar disponibles (en estado "Listo") para que se ejecute la actividad de procedimiento almacenado. Las entradas a la actividad de procedimiento almacenado solo se usan para la administración de dependencias al encadenar esta actividad con otras. Los conjuntos de datos de entrada no se pueden usar en el procedimiento almacenado como parámetro. | No
outputs | Conjuntos de datos de salida generados por la actividad de procedimiento almacenado. Asegúrese de que la tabla de salida usa un servicio vinculado que vincula una Base de datos SQL de Azure a la Factoría de datos. Las salidas de la actividad de procedimiento almacenado pueden servir como una forma de pasar el resultado de la actividad de procedimiento almacenado para el posterior procesamiento o como administración de dependencias cuando se encadena esta actividad con otras. | Sí
storedProcedureName | Especifique el nombre del procedimiento almacenado en la Base de datos SQL de Azure que se representa mediante el servicio vinculado que usa la tabla de salida. | Sí
storedProcedureParameters | Especifique valores para los parámetros de procedimiento almacenado. | No

## Ejemplo

Veamos un ejemplo donde se crea una tabla en la Base de datos SQL de Azure que tiene dos columnas:

Columna | Tipo
------ | ----
ID | Un identificador único
Datetime | La fecha y la hora cuando se generó el identificador correspondiente.

![Datos de ejemplo](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

> [AZURE.NOTE]El **Nombre** y **el uso de mayúsculas y minúsculas** del parámetro (DateTime en este ejemplo) tienen que coincidir con los de los parámetros especificados en la actividad JSON a continuación. En la definición del procedimiento almacenado, asegúrese de que se usa **@** como prefijo para el parámetro.

Para ejecutar este procedimiento almacenado en una canalización de Factoría de datos, necesita hacer lo siguiente:

1.	Cree un [servicio vinculado](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) para registrar la cadena de conexión de la base de datos SQL de Azure donde debe ejecutarse el procedimiento almacenado.
2.	Cree un [conjunto de datos](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties) que apunte a la tabla de salida en la base de datos SQL de Azure. Llamemos sprocsampleout a este conjunto de datos. Este conjunto de datos debe hacer referencia al servicio vinculado del paso 1. 
3.	Cree el procedimiento almacenado en la Base de datos SQL de Azure.
4.	Cree la siguiente [canalización](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) con la actividad SqlServerStoredProcedure para invocar el procedimiento almacenado en la base de datos SQL de Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
		              	{
		                	"storedProcedureName": "sp_sample",
			        		"storedProcedureParameters": 
		        			{
		            			"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        			}
						}
	            	}
		        ]
		     }
		}
5.	Implemente la [canalización](data-factory-create-pipelines.md).
6.	[Supervise la canalización](data-factory-monitor-manage-pipelines.md) mediante las vistas de supervisión y administración de Factoría de datos.

> [AZURE.NOTE]En el ejemplo anterior, SprocActivitySample no tiene entradas. Si desea vincular esta cadena con una actividad de nivel superior (por ejemplo, antes del procesamiento), las salidas de la actividad de nivel superior pueden usarse como entradas en esta actividad. En este caso, esta actividad no se ejecutará hasta que se completa la actividad de nivel superior y las salidas de dichas actividades están disponibles (en estado Listo). Las entradas no se pueden usar directamente como un parámetro para la actividad de procedimiento almacenado.
> 
> Los nombres y los tipos de letra (mayúsculas y minúsculas) de los parámetros del procedimiento almacenado en el archivo JSON deben coincidir con los nombres de parámetros de procedimiento almacenado en la base de datos de destino.

Ahora, pensemos en agregar otra columna denominada 'Escenario' en la tabla que contiene un valor estático denominado 'Ejemplo de documento'.

![Datos de ejemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Para ello, pase el parámetro Escenario y el valor de la actividad de procedimiento almacenado. La sección typeProperties del ejemplo anterior es como sigue:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO9-->