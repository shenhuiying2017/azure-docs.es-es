<properties 
	pageTitle="Uso del almacenamiento de Azure para copias de seguridad y restauración de SQL Server | Azure" 
	description="Realizar una copia de seguridad de SQL Server y de la Base de datos SQL en el almacenamiento de Azure. Explica las ventajas de la copia de seguridad de las Bases de datos SQL en el almacenamiento de Azure y los componentes de SQL Server y almacenamiento de Azure que se necesitan" 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="jeffreyg"/>



# Uso del almacenamiento de Azure para copias de seguridad y restauración de SQL Server

## Información general

La característica que ofrece la posibilidad de escribir copias de seguridad de SQL Server en el servicio de almacenamiento de bobs de Azure se lanzó en SQL Server 2012 SP1 CU2. Puede usar esta funcionalidad para realizar la copia de seguridad en el servicio BLOB de Azure y restaurar desde él a partir de una base de datos SQL Server local o una base de datos SQL Server de una máquina virtual de Azure. Las copias de seguridad en la nube ofrecen ventajas de disponibilidad, almacenamiento externo ilimitado replicado geográficamente y facilidad de migración de datos en la nube y desde ella.   En esta versión puede emitir instrucciones BACKUP o RESTORE usando T-SQL o SMO.

## Ventajas del uso del servicio BLOB de Azure para las copias de seguridad de SQL Server

La administración del almacenamiento, el riesgo de errores en él, el acceso al almacenamiento externo y la configuración de los dispositivos son algunos de los desafíos generales de las copias de seguridad.  Para que SQL Server se ejecute en una máquina virtual de Azure, hay dificultades adicionales para configurar y realizar copias de seguridad de un VHD o para configurar las unidades adjuntas. A continuación se incluyen algunas de las ventajas principales de usar el servicio de almacenamiento BLOB de Azure para las copias de seguridad de SQL Server:

* Flexibilidad, confiabilidad y almacenamiento externo ilimitado: Almacenar las copias de seguridad en el servicio BLOB de Azure puede ser una opción práctica, flexible y que ofrece un fácil acceso externo. Crear almacenamiento externo para sus copias de seguridad de SQL Server puede ser tan fácil como modificar sus scripts o trabajos existentes. El almacenamiento externo normalmente debe estar lo suficientemente alejado de la base de datos de producción como para impedir que un solo desastre pueda afectar tanto a las ubicaciones de la base de datos de producción como a la externa. Al elegir la replicación geográfica del almacenamiento de blobs tiene una capa adicional de protección en caso de que se produzca un desastre que pudiera afectar a toda la región. Además, las copias de seguridad están disponibles desde cualquier lugar y en cualquier momento y se puede obtener acceso a ellas fácilmente para la restauración.
* Archivo de copias de seguridad: El servicio de almacenamiento BLOB de Azure ofrece una alternativa mejor a las opciones de cinta que se usan normalmente para archivar las copias de seguridad. El almacenamiento en cintas puede requerir transporte físico a unas instalaciones externas y medidas para proteger los soportes. Almacenar las copias de seguridad en el almacenamiento de blobs de Azure ofrece una opción de archivado instantánea, altamente disponible y resistente.
* Sin sobrecarga por la administración del hardware: Con los servicios de Azure no hay sobrecarga por la administración del hardware. Los servicios de Azure administran el hardware y ofrecen replicación geográfica para conseguir redundancia y protección contra los errores del hardware.
* Actualmente, para las instancias de SQL Server que se ejecutan en una máquina virtual de Azure, realizar la copia de seguridad en los servicios de almacenamiento de blobs de Azure se puede conseguir creando discos adjuntos. No obstante, hay un límite en el número de discos que se pueden adjuntar a una máquina virtual de Azure. Este límite es de 16 discos para una instancia muy grande e inferior para las instancias menores. Al habilitar una copia de seguridad directa en el almacenamiento de blobs de Azure, puede omitir el límite de 16 discos.
* Además, el archivo de copia de seguridad que se almacena ahora en el servicio de almacenamiento de blobs de Azure está directamente disponible para un servidor SQL Server local u otro servidor SQL Server que se ejecute en una máquina virtual de Azure, sin necesidad de adjuntar o desasociar la base de datos o descargar y adjuntar el VHD.
* Ventajas económicas: Pague solo por el servicio que use. Puede ser tan rentable como una opción de archivado de copias de seguridad externa. Consulte la [Calculadora de precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator") y el [artículo sobre precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article") para obtener más información.

Para obtener más información, consulte [Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Las dos secciones siguientes presentan el servicio de almacenamiento de blobs de Azure y los componentes de SQL Server que se usan para realizar la copia de seguridad en el servicio de almacenamiento de blobs de Azure o desde él. A tal efecto, es importante conocer los componentes y la interacción que se produce entre ellos. 

El primer paso de este proceso es crear una cuenta de Azure. SQL Server usa el nombre de la cuenta de almacenamiento de Azure y sus valores de clave de acceso para autenticar, escribir y leer los blobs en el servicio de almacenamiento. La credencial de SQL Server almacena esta información de autenticación y la usa durante las operaciones de copia de seguridad o restauración. 

Para ver un tutorial completo de cómo crear una cuenta de almacenamiento y realizar una restauración sencilla, consulte [Introducción al servicio de almacenamiento de Azure para copia de seguridad y restauración de SQL Server](http://go.microsoft.com/fwlink/?LinkId=271615) 

## Componentes del servicio de almacenamiento de blobs de Azure 

* Cuenta de almacenamiento: La cuenta de almacenamiento es el punto de partida de todos los servicios de almacenamiento. Para obtener acceso a un servicio de almacenamiento de blobs de Azure, primero debe crear una cuenta de almacenamiento de Azure. El nombre de la cuenta de almacenamiento y sus propiedades de clave de acceso son necesarios para autenticarse en el servicio de almacenamiento de blobs de Azure y sus componentes. 
Para obtener más información acerca del servicio de almacenamiento de blobs de Azure, consulte [Uso del servicio de almacenamiento de blobs de Azure](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)

* Contenedor: Un contenedor ofrece la agrupación de un conjunto de blobs y puede almacenar un número ilimitado de ellos. Para realizar una copia de seguridad de SQL Server en un servicio BLOB de Azure, debe haber creado un contenedor raíz como mínimo. 

* Blob: un archivo de cualquier tipo y tamaño. Existen dos tipos de blobs que pueden almacenarse en el servicio de almacenamiento de blobs de Azure: blobs en páginas y en bloques.  La copia de seguridad de SQL Server usa blobs en páginas como tipo de blob. Es posible dirigir los blobs con el siguiente formato de dirección URL: `https://<storage account>.blob.core.windows.net/<container>/<blob>`
Para obtener más información sobre los blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx)

## Componentes de SQL Server

* URL: Una URL especifica el Identificador uniforme de recursos (URI) para un único archivo de copia de seguridad. La URL se usa para proporcionar la ubicación y el nombre del archivo de la copia de seguridad de SQL Server. En esta implementación, la única URL válida es la que lleva a los blobs en páginas de una cuenta de almacenamiento de Azure. La URL debe llevar a un blob real, no a un contenedor. Si el blob no existe, se crea. Si se especifica un blob que ya existe, BACKUP provoca un error, a menos que se especifique la opción > WITH FORMAT. 
A continuación encontrará un ejemplo de la URL que debería especificar en el comando BACKUP: 
**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>Nota:</b> no es necesario usar HTTPS, pero sí recomendable.
<b>Importante</b>
Si elige copiar y cargar un archivo de copia de seguridad en un servicio de almacenamiento de blobs de Azure, debe usar blob en páginas como opción de almacenamiento si tiene pensado usar este archivo para las operaciones de restauración. RESTORE desde un tipo de blob en bloques provocará un error. 

* Credencial: La información necesaria para conectarse y autenticarse en un servicio de almacenamiento de blobs de Azure se almacena como credencial.  Para que SQL Server escriba copias de seguridad en un blob de Azure o realice la restauración desde él es preciso crear una credencial de SQL Server. La credencial almacena el nombre de la cuenta de almacenamiento y la clave de acceso de la misma.  Cuando se haya creado la credencial, hay que especificarla en la opción WITH CREDENTIAL al emitir las instrucciones BACKUP/RESTORE. 
Para obtener instrucciones detalladas acerca de cómo crear una credencial de SQL Server, consulte [Introducción al servicio de almacenamiento de Azure para copia de seguridad y restauración de SQL Server](http://go.microsoft.com/fwlink/?LinkId=271615).

## Copia de seguridad y restauración de bases de datos SQL Server con los blobs de Azure: conceptos y tareas.

**Conceptos, consideraciones y ejemplos de código:**

[Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Azure](http://go.microsoft.com/fwlink/?LinkId=271617)

**Tutorial introductorio:**

[Tutorial: copias de seguridad y restauración de SQL Server en el servicio de almacenamiento Blob de Windows Azure](http://go.microsoft.com/fwlink/?LinkID=271615 "Tutorial")

**Prácticas recomendadas y solución de problemas:**
	
[Prácticas recomendadas de copia de seguridad y restauración (servicio de almacenamiento de blobs de Azure)](http://go.microsoft.com/fwlink/?LinkId=272394)




	





<!--HONumber=49-->