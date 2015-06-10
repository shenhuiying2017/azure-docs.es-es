<properties
   pageTitle="Migración a Base de datos SQL mediante SSMS"
   description="Base de datos SQL de Microsoft Azure, migrar la base de datos sql, migrar con ssms"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#Migración de una base de datos compatible con SSMS 

![texto alternativo](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Si un esquema de base de datos ya es compatible con Base de datos SQL d Azure, la migración será sencilla. Como no es necesaria ninguna transformación, la migración solo requiere que la base de datos se importe a Azure. Esto puede hacerse en un solo paso mediante SSMS con la ’implementación' de la base de datos a Base de datos SQL de Azure, o como un proceso de dos pasos por exportar primero un BACPAC y después importarlo a un servidor de Base de datos SQL de Azure para crear una nueva base de datos.

Puede cargar el archivo BACPAC exportado en Almacenamiento de Azure e importarlo mediante el portal. La ejecución de la importación en la nube reducirá la latencia en el paso de importación que mejorará el rendimiento y la confiabilidad de la migración con grandes bases de datos.

La implementación directa desde SSMS siempre va a implementar el esquema y los datos, mientras que la importación y la exportación siempre implementan el esquema y proporcionan una opción para implementar datos desde todas o desde un subconjunto de las tablas. Si se implementa desde SSMS o se exporta y después se importar de SSMS (o después del portal), se usa la misma tecnología DAC interna y el resultado es el mismo.

Esta opción también se usa como el último paso de la opción 3 para migrar las bases de datos después de que se haya actualizado para que sea compatible con Base de datos SQL de Azure.

##Uso de SSMS para implementar en Base de datos SQL de Azure
1.	Aprovisione un servidor mediante el Portal de Azure tal como se describe en Creación del servidor de destino para la base de datos migrada.
2. Busque la base de datos de origen en el Explorador de objetos de SSMS y ejecute la tarea **Implementación de bases de datos en Base de datos SQL de Microsoft Azure**

	![texto alternativo](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	En el asistente para la implementación configure la conexión con el servidor de Base de datos SQL de Azure de destino que se aprovisionó en el paso.
4.	Proporcione el **nombre** para la base de datos y establezca la **edición** (capa de servicio) y el **objetivo de servicio** (nivel de rendimiento). Consulte Elección de un nivel de rendimiento/nivel de precios de la base de datos para la migración, para obtener más información sobre cómo configurar estas opciones. 

	![texto alternativo](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Complete el Asistente para realizar la migración de la base de datos. Según el tamaño y la complejidad de la implementación de la base de datos puede tardar desde unos minutos a varias horas. Si se producen errores que indican que el esquema de base de datos no es compatible con Base de datos SQL, se deberá utilizar una opción diferente.
##Uso de SSMS para exportar un BACPAC y después importarlo en Base de datos SQL
El proceso de implementación se puede dividir en dos pasos: exportar e importar. En el primer paso se crea un archivo BACPAC que después se utiliza como entrada en el segundo paso.

1.	Aprovisione un servidor mediante la última actualización de Base de datos SQL, tal como se describe en Creación del servidor de destino para la base de datos migrada.
2.	Busque la base de datos de origen en el Explorador de objetos de SSMS y seleccione la tarea **Implementación de bases de datos en Base de datos SQL de Microsoft Azure**

	![texto alternativo](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. En el Asistente para exportación configure la exportación para guardar localmente el archivo BACPAC. El archivo BACPAC exportado siempre incluye el esquema de la base de datos completa y, de manera predeterminada, los datos de todas las tablas. Si desea excluir los datos de algunas o todas las tablas, utilice la pestaña de opciones avanzadas. Por ejemplo, puede exportar únicamente los datos de las tablas de referencia.
	>[AZURE.NOTE]Nota: una vez que el Portal de administración de Azure admite la importación que se ejecuta en Azure, puede elegir guardar el archivo BACPAC exportado al Almacenamiento de Azure y ejecutar la importación en la nube.

	![texto alternativo](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Una vez creado el archivo BACPAC, conéctese al servidor que creó en el paso 1, haga clic con el botón derecho en la carpeta Bases de datos y seleccione Importar aplicación de capa de datos...

	![texto alternativo](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	En el Asistente para importación, seleccione el archivo BACPAC que acaba de exportar para crear la nueva base de datos en Base de datos SQL de Azure.

	![texto alternativo](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Proporcione el nombre para la base de datos y establezca la edición (capa de servicio) y el objetivo de servicio (nivel de rendimiento).
	 
7.	Complete el asistente para importar el archivo BACPAC y cree la base de datos en Base de datos SQL de Azure.

	![texto alternativo](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##Alternativas
También puede usar la utilidad de línea de comandos sqlpackage.exe para implementar la base de datos o exportar e importar un archivo BACPAC. Sqlpackage.exe usa la misma tecnología DAC como SSMS, por lo que el resultado es el mismo. Para obtener más información, vaya [aquí](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=58-->