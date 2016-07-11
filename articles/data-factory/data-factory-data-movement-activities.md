<properties 
	pageTitle="Actividades de movimiento de datos | Microsoft Azure" 
	description="Aprenda sobre el movimiento de datos en las canalizaciones de Data Factory: migración de datos entre almacenes en la nube, entre el entorno local y la nube. Utilice la actividad de copia." 
	keywords="movimiento de datos, migración de datos, copiar datos, transferir datos"
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
	ms.date="05/31/2016" 
	ms.author="spelluru"/>

# Movimiento de datos y actividad de copia: migración de datos a la nube y entre almacenes en la nube
El movimiento de datos de un origen a un receptor (destino) se realiza mediante la [actividad de copia](#copyactivity) de Data Factory de Azure. La actividad de copia se basa en un [servicio globalmente disponible](#global) que es seguro, confiable y escalable. El servicio elige automáticamente la región óptima para realizar el movimiento de datos, normalmente la región más cercana al almacén de datos del receptor.

Así es como se produce la migración de datos entre dos almacenes de datos en la nube, entre un almacén de datos local y un almacén de datos en la nube y desde/hasta un almacén de datos en una máquina virtual Iaas de Azure.

## Copia de datos entre dos almacenes de datos en la nube
Cuando los almacenes de datos de origen y receptor (destino) residen en la nube, la actividad de copia pasa por las siguientes fases para copiar o mover datos desde el origen hasta el receptor. El servicio que alimenta a la actividad de copia realiza lo siguiente:

1. lee datos del almacén de datos de origen,
2.	realiza serialización y deserialización, compresión y descompresión, asignación de columnas y conversión de tipos en función de las configuraciones del conjunto de datos de entrada, del conjunto de datos de salida y la actividad de copia,
3.	escribe datos en el almacén de datos de destino

![copia de la nube a la nube](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copia de datos entre un almacén de datos local y un almacén de datos en la nube
Para [mover datos entre almacenes de datos locales detrás del firewall corporativo y un almacén de datos en la nube de forma segura](#moveonpremtocloud), necesitará instalar Data Management Gateway, que es un agente que permite el procesamiento y movimiento de datos híbridos, en el equipo local. Data Management Gateway puede instalarse en la misma máquina que el propio almacén de datos o en una máquina independiente que tenga acceso para alcanzar el almacén de datos. En este escenario, la serialización y deserialización, la compresión y descompresión, la asignación de columnas y la conversión de tipos se realizan mediante Data Management Gateway. Los datos no fluyen a través del servicio Factoría de datos de Azure en ese caso. Data Management Gateway escribe directamente los datos en el almacén de destino.

![copia de local a la nube](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copia de datos con un almacén de datos en una VM IaaS de Azure 
También puede mover datos desde y hacia almacenes de datos compatibles hospedados en VM IaaS de Azure (máquinas virtuales de infraestructura como servicio) mediante Data Management Gateway. En este caso, Data Management Gateway puede instalarse en la misma VM de Azure que el propio almacén de datos o en una VM independiente que tenga acceso para alcanzar el almacén de datos.

## Almacenes de datos que se admiten
La actividad de copia copia los datos de un almacén de datos de **origen** a un almacén de datos **receptor**. Factoría de datos admite los siguientes almacenes de datos y **se pueden escribir datos de cualquier origen en cualquier receptor**. Haga clic en un almacén de datos para obtener información sobre cómo copiar datos desde/a ese almacén.

| Orígenes| Receptores |
|:------- | :---- |
| <ul><li>[Blob de Azure](data-factory-azure-blob-connector.md)</li><li>[Tabla de Azure](data-factory-azure-table-connector.md)</li><li>[Base de datos SQL de Azure](data-factory-azure-sql-connector.md)</li><li>[Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (consulte la nota siguiente)](data-factory-azure-documentdb-connector.md)</li><li>[Almacén de Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de archivos local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Base de datos de Oracle local/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[Base de datos MySQL local/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[Base de datos DB2 local/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Base de datos Teradata local/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Base de datos Sybase local/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[Base de datos PostgreSQL local/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[Orígenes de datos ODBC locales/Azure IaaS](data-factory-odbc-connector.md)</li><li>[Sistema de archivos distribuido de Hadoop (HDFS) local /Azure IaaS](data-factory-hdfs-connector.md)</li><li>[Orígenes de OData](data-factory-odata-connector.md)</li><li>[Tabla web (tabla de HTML)](data-factory-web-table-connector.md)</li><li>[GE Historian local/Azure IaaS](data-factory-odbc-connector.md#ge-historian-store)</li></ul> | <ul><li>[Blob de Azure](data-factory-azure-blob-connector.md)</li><li>[Tabla de Azure](data-factory-azure-table-connector.md)</li><li>[Base de datos SQL de Azure](data-factory-azure-sql-connector.md)</li><li>[Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (consulte la nota siguiente)](data-factory-azure-documentdb-connector.md)</li><li>[Almacén de Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de archivos local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Base de datos de Oracle local/Azure IaaS](data-factory-onprem-oracle-connector.md)</li></ul> |


> [AZURE.NOTE] En estos momentos, no se pueden copiar datos entre los almacenes de datos de IaaS de Azure o local y Azure DocumentDB. En breve, habilitaremos la matriz completa de Azure DocumentDB.

Si tiene que meter (o sacar) datos a un almacén de datos que no sea compatible con la **actividad de copia**, puede usar la **actividad personalizada** de Data Factory con su propia lógica para copiar o mover los datos. Consulte el artículo [Uso de actividades personalizadas en una canalización de Data Factory de Azure](data-factory-use-custom-activities.md) para más información sobre la creación y el uso de una actividad personalizada.

## Tutorial
Para obtener un tutorial rápido sobre el uso de la actividad de copia, consulte [Tutorial: Uso de la actividad de copia en una canalización de la Factoría de datos de Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). En el tutorial, usará la actividad de copia para copiar datos desde un almacenamiento de blobs de Azure en una base de datos SQL de Azure.

## <a name="copyactivity"></a>Actividad de copia
Esta actividad copia los datos de un conjunto de datos de entrada (**origen**) a uno de salida (**receptor**). La copia de datos se realiza por lotes según la programación especificada en la actividad. Para más información sobre cómo definir actividades en general, consulte el artículo [Canalizaciones y actividades en Data Factory de Azure](data-factory-create-pipelines.md).

La actividad de copia proporciona las siguientes capacidades:

### <a name="global"></a>Movimiento de datos disponible globalmente
Aunque la propia instancia de Data Factory de Azure solamente esté disponible en las regiones oeste de EE. UU. y Europa del Norte, el servicio que permite la actividad de copia está disponible globalmente en las siguientes regiones y zonas geográficas. La topología disponible globalmente garantiza un movimiento de datos eficiente que evita saltos entre regiones en la mayoría de los casos.

Tanto **Data Management Gateway** como **Data Factory de Azure** realizan el movimiento de datos en función de la ubicación de los almacenes de datos de origen y destino en una operación de copia. Para más información, consulte la tabla siguiente:

Ubicación de almacén de datos de origen | Ubicación de almacén de datos de destino | El movimiento de datos lo realiza  
-------------------------- | ------------------------------- | ----------------------------- 
local o VM de Azure (IaaS) | nube | **Data Management Gateway** en un equipo local o máquina virtual de Azure. Los datos no fluyen a través del servicio en la nube. <br/><br/>Nota: Data Management Gateway puede estar en el mismo equipo local o máquina virtual de Azure que el almacén de datos o en uno diferente, siempre que se pueda conectar a ambos almacenes de datos.
nube | local o VM de Azure (IaaS) | Igual que el anterior. 
local o VM de Azure (IaaS) | local o VM de Azure | **Data Management Gateway asociado al origen**. Los datos no fluyen a través del servicio en la nube. Consulte la nota anterior.   
nube | nube | **El servicio en la nube que permite la actividad de copia**. Factoría de datos de Azure usa la implementación de este servicio en la región más cercana a la ubicación del receptor en la misma zona geográfica. Consulte la tabla siguiente para realizar la asignación: <br/><br/><table><tr><th>Región del almacén de datos de destino</th> <th>Región usada para el movimiento de datos</th></tr><tr><td>Este de EE. UU.</td><td>Este de EE. UU.</td></tr><tr><td>Este de EE. UU. 2</td><td>Este de EE. UU. 2</td><tr/><tr><td>Centro de EE. UU.</td><td>Centro de EE. UU.</td><tr/><tr><td>Oeste de EE. UU.</td><td>Oeste de EE. UU.</td></tr><tr><td>Centro-norte de EE. UU.</td><td>Centro-norte de EE. UU.</td></tr><tr><td>Centro-sur de EE. UU.</td><td>Centro-sur de EE. UU.</td></tr><tr><td>Europa del Norte</td><td>Europa del Norte</td></tr><tr><td>Europa Occidental</td><td>Europa Occidental</td></tr><tr><td>Sudeste Asiático</td><td>Sudeste Asiático</td></tr><tr><td>Asia Oriental</td><td>Asia Suroriental</td></tr><tr><td>Japón Oriental</td><td>Japón Oriental</td></tr><tr><td>Japón Occidental</td><td>Japón Oriental</td></tr><tr><td>Sur de Brasil</td><td>Sur de Brasil</td></tr><tr><td>Este de Australia</td><td>Este de Australia</td></tr><tr><td>Sudeste de Australia</td><td>Sudeste de Australia</td></tr></table>


> [AZURE.NOTE] Si la región del almacén de datos de destino no está en la lista anterior, se producirá un error en la actividad de copia, en lugar de atravesar una región alternativa.



### <a name="moveonpremtocloud"></a>Transferencia de datos segura entre la nube y la ubicación local
Uno de los desafíos de la moderna integración de datos es transferir datos sin problemas entre el entorno local y la nube. Data management gateway es un agente que puede instalar de forma local para permitir canalizaciones de datos híbridas.

La puerta de enlace de datos proporciona las siguientes capacidades:

1.	Administrar el acceso a almacenes de datos locales de forma segura.
2.	Usar modelos de almacenes de datos locales y almacenes de datos en la nube dentro de la misma factoría de datos y mover los datos.
3.	Tener un solo panel de vidrio para la supervisión y administración con visibilidad del estado de la puerta de enlace mediante el panel basado en la nube de la factoría de datos.

Debe tratar el origen de datos como un origen de datos local (que está detrás de un firewall) aunque use **ExpressRoute** y **usar la puerta de enlace** para establecer la conectividad entre el servicio y el origen de datos.

Para más información, consulte [Movimiento de datos entre la ubicación local y la nube](data-factory-move-data-between-onprem-and-cloud.md).


### Movimiento de datos confiable y rentable
La actividad de copia está diseñada para mover grandes volúmenes de datos de forma segura, resistente a errores transitorios a través de una gran variedad de orígenes de datos. Se pueden copiar datos de una manera rentable con la posibilidad de habilitar la compresión por la red.

### Conversión de los tipos entre sistemas de tipos diferentes
Los diferentes almacenes de datos tienen sistemas con distintos tipos nativos. La actividad de copia realiza conversiones automáticas de los tipos del origen a los tipos del receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Puede encontrar la asignación de un determinado sistema de tipo nativo a .NET para el almacén de datos en los respectivos artículos del conector del almacén de datos. Puede usar estas asignaciones para determinar los tipos adecuados al crear las tablas, de forma que se realicen las conversiones adecuadas durante la actividad de copia.

### Trabajo con diferentes formatos de archivo
En los almacenes basados en archivos, la actividad de copia admite una gran variedad de formatos de archivo, incluidos los formatos binario, de texto, Avro, ORC y JSON. Puede usar la actividad de copia para convertir los datos de un formato a otro. Ejemplo: texto (CSV) para Avro. Si los datos no están estructurados, puede omitir la propiedad **Structure** en la definición de JSON del [conjunto de datos](data-factory-create-datasets.md).

### Propiedades de la actividad de copia
Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad.

En el caso de la actividad de copia, la sección **typeProperties** varía en función de los tipos de origen y receptor. Haga clic en un origen/receptor de la sección [Almacenes de datos que se admiten](#supported-data-stores) para más información sobre las propiedades de tipo admitidas por la actividad de copia de ese almacén de datos.

En cada la página específica del almacén de datos en los documentos enumerados anteriormente se encuentran las propiedades específicas del tipo de almacén de datos.

### Copia ordenada
Es posible ejecutar varias operaciones de copia sucesivas de manera secuencial y ordenada. Supongamos que tiene dos actividades de copia en una canalización: ActividadCopia1 y ActividadCopia2 con los siguientes conjuntos de datos de entrada y salida.

ActividadCopia1: entrada: ConjuntoDatos1; salida: ConjuntoDatos2

ActividadCopia2: entrada: ConjuntoDatos2; salida: ConjuntoDatos4

ActividadCopia2 solo se ejecutaría si ActividadCopia1 se hubiera ejecutado correctamente y ConjuntoDatos2 estuviera disponible.

En el ejemplo anterior, ActividadCopia2 puede tener una entrada distinta, como ConjuntoDatos3, pero también deberá especificar ConjuntoDatos2 como una entrada de ActividadCopia2 para que la actividad no se ejecute hasta que se haya completado ActividadCopia1. Por ejemplo:

ActividadCopia1: entrada: ConjuntoDatos1; salida: ConjuntoDatos2

ActividadCopia2: entrada: ConjuntoDatos3 y ConjuntoDatos2; salida: ConjuntoDatos4

Cuando se especifican varias entradas, solo se usa el primer conjunto de datos de entrada para copiar los datos. Sin embargo, los demás conjuntos de datos se usan como dependencias. ActividadCopia2 solo empezaría a ejecutarse cuando se cumplieran las siguientes condiciones:

- ActividadCopia2 se ha completado correctamente y ConjuntoDatos2 está disponible. Este conjunto de datos no se usará al copiar datos en ConjuntoDatos4. Solo actúa como una dependencia de programación de ActividadCopia2.
- ConjuntoDatos3 está disponible. Este conjunto de datos representa los datos que se copian en el destino.


### Optimización y rendimiento de la actividad de copia 
Vea el artículo [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md), en el que se describen los factores claves que afectan al rendimiento del movimiento de datos (actividad de copia) en la Factoría de datos de Azure. También muestra el rendimiento observado durante las pruebas internas y trata diversas maneras de optimizar el rendimiento de la actividad de copia.


## Asistente para copia de Data Factory
El **Asistente para copia de Data Factory** permite crear una canalización para copiar datos de orígenes compatibles en destinos sin escribir definiciones de JSON para servicios vinculados, conjuntos de datos ni canalizaciones. Si quiere iniciar el Asistente para copia, haga clic en el icono **Copiar datos** de la página de inicio de Data Factory.

![Asistente para copia de datos](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### Características

#### Un asistente intuitivo y sin interrupciones para copiar datos 
Gracias a este asistente, podrá trasladar datos de un origen a un destino en cuestión de minutos con los siguientes sencillos pasos:

1.	Seleccionar el **origen**
2.	Seleccionar el **destino**
3.	Ajustar la **configuración**

![Selección de origen de datos](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### Exploración de datos enriquecidos y asignaciones de esquemas
Puede examinar tablas o carpetas, previsualizar datos, asignar esquemas, validar expresiones y realizar transformaciones de datos simples en el propio asistente.

**Examinar tablas o carpetas** ![Examinación de tablas y carpetas](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### Experiencia escalable para distintos tipos de objetos y datos
Experiencia diseñada específicamente para los macrodatos desde el primer momento. Permite crear, de manera simple y eficiente, canalizaciones de Data Factory que trasladen cientos de carpetas, archivos o tablas.

**Previsualizar datos, asignar esquemas y realizar transformaciones simples** ![Configuración del formato de archivo](./media/data-factory-data-movement-activities/file-format-settings.png) ![Asignación de esquemas](./media/data-factory-data-movement-activities/schema-mapping.png) ![Validación de expresiones](./media/data-factory-data-movement-activities/validate-expressions.png)

#### Experiencia escalable para distintos tipos de objetos y datos
Experiencia diseñada específicamente para los macrodatos desde el primer momento. Con el asistente para copia, es posible crear, de forma simple y eficaz, operaciones que trasladen cientos de carpetas, archivos o tablas.

![Selección de tablas para copiar datos](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### Opciones de programación más exhaustivas
Puede ejecutar la operación de copia solo una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden utilizar para los distintos conectores locales y en la nube, así como para la copia del escritorio local. La copia única permite trasladar datos de un origen a un destino una sola vez y se aplica a datos de cualquier tamaño y con todos los formatos compatibles. Por su parte, la copia programada permite copiar datos con una frecuencia preestablecida. Puede aprovechar las opciones avanzadas (como el reintento, el tiempo de espera, las alertas, etc.) para configurar la copia programada.

![Propiedades de programación](./media/data-factory-data-movement-activities/scheduling-properties.png)


### Prueba 
Para ver un tutorial rápido sobre cómo usar el **Asistente para copia de Data Factory** a fin de crear una canalización con una actividad de copia, consulte el [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).


### Variables de la ruta de la carpeta de blobs de Azure
Puede usar variables de ruta de la carpeta para copiar datos desde una carpeta que se determina en el tiempo de ejecución según la [variable del sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Estas son las variables admitidas: **year**, **month**, **day**, **hour**, **minute** y **{personalizada}**. Ejemplo: carpetaDeEntrada/{year}/{month}/{day}.

Supongamos que tiene carpetas de entrada con el siguiente formato:
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Haga clic en el botón **Examinar** del **archivo o la carpeta**, vaya a una de estas carpetas, como 2016->03->01->02 y haga clic en **Seleccionar**. Verá **2016/03/01/02** en el cuadro de texto. Seguidamente, sustituya **2016** por **{year}**, **03** por **{month}**, **01** por **{day}** y **02** por **{hour}** y presione la tecla **Tab**. Aparecerán listas desplegables para seleccionar el **formato** de estas cuatro variables, como verá a continuación:

![Uso de variables del sistema](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

También puede usar una variable **personalizada** como se muestra a continuación y cualquier [cadena de formato admitida](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Asegúrese de seleccionar primero una carpeta con esa estructura usando primero el botón Examinar, sustituya el valor que quiera por **{personalizado}** y presione la tecla **Tab** para ver el cuadro de texto donde puede escribir la cadena de formato.

![Uso de la variable personalizada](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

<!---HONumber=AcomDC_0629_2016-->