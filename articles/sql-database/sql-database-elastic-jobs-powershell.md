<properties 
	pageTitle="Creación y administración de trabajos de base de datos elástica mediante PowerShell" 
	description="PowerShell usada para administrar grupos de Base de datos SQL de Azure" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="ddove; sidneyh" />

# Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Información general

La característica **Trabajos de base de datos elástica** (vista previa) le permite ejecutar de forma confiable un script de Transact-SQL (T-SQL) o aplicar un DACPAC transversalmente en un grupo de bases de datos, que puede incluir una colección personalizada de bases de datos, todas las bases de datos de un [grupo de bases de datos elásticas (vista previa)](sql-database-elastic-pool.md) o un conjunto de particiones (creado con la [biblioteca cliente de base de datos elástica](sql-database-elastic-database-client-library.md)). En la vista previa, **Trabajos de base de datos elástica** es actualmente un servicio en la nube de Azure alojado en el cliente que permite la ejecución de tareas administrativas ad hoc y programadas que se denominan trabajos. Con esta característica, puede administrar fácilmente y de forma confiable grandes cantidades de base de datos SQL de Azure a escala ejecutando scripts de Transact-SQL para realizar operaciones administrativas como, por ejemplo, cambios de esquema, administración de credenciales, actualizaciones de datos de referencia, recopilación de datos de rendimiento o recopilación de telemetría de inquilinos (cliente). Si desea obtener más información sobre esta característica, vea [Información general sobre Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

Con las API de PowerShell para **Trabajos de base de datos elástica** dispone de flexibilidad para definir el grupo de bases de datos en el que se ejecutarán los scripts. Actualmente, la funcionalidad de **Trabajos de base de datos elástica** a través del Portal de Azure tiene un conjunto de características reducido y se limita a la ejecución en Grupos de bases de datos elásticas.

**Trabajos de base de datos elástica** (vista previa) usa varios componentes de Azure para definir los trabajos que se van realizar, definir cuándo realizar los trabajos, ejecutar los trabajos, hacer el seguimiento de si los trabajos se han realizado correctamente o no y, opcionalmente, especificar un destino para los resultados que devuelven las consultas. Dado que las API de Powershell incluidas en esta vista previa contienen funcionalidad adicional desde la vista previa inicial a través del Portal, se recomienda instalar los componentes más recientes de **Trabajos de base de datos elástica**. Si ya están instalados, bastará con que actualice los componentes de **Trabajos de base de datos elástica**. Para obtener más información sobre la instalación de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs), vea [Instalación de los componentes de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md).

Este artículo muestra cómo crear los elementos necesarios para crear y administrar **Trabajos de base de datos elástica**, excepto en la suscripción de Azure. Si necesita una suscripción de Azure, haga clic en la opción VERSIÓN DE EVALUACIÓN GRATUITA situada en la parte superior de esta página y luego vuelva para finalizar este artículo. En este tema se amplía el ejemplo que aparece en [Introducción a las herramientas de la base de datos elástica](sql-database-elastic-scale-get-started.md). Al terminar, sabrá cómo crear y administrar trabajos para realizar operaciones administrativas en un grupo de bases de datos particionadas definidas con un **conjunto de particiones**, o bien, con una colección de bases de datos personalizada.

## Requisitos previos
* Una suscripción de Azure. Para obtener una prueba gratuita, vea [Prueba gratuita de un mes](http://azure.microsoft.com/pricing/free-trial/).
* **Trabajos de base de datos elástica**. En primer lugar, se debe descargar e importar el paquete de PowerShell, e instalar los componentes de Trabajos de base de datos elástica. Siga estos pasos: [Instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md)
* Azure PowerShell versión 0.8.16 o posterior. Instale la versión más reciente (0.9.5) a través del [Instalador de plataforma Web](http://go.microsoft.com/fwlink/p/?linkid=320376). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de la suscripción que quiera del conjunto de resultados. Cuando tenga la información de la suscripción, ejecute el siguiente cmdlet para establecer esta suscripción como predeterminada, es decir, el destino para crear y administrar trabajos:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Se recomienda el uso de [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) para desarrollar y ejecutar scripts de PowerShell en Trabajos de base de datos elástica.

## Objetos de Trabajos de base de datos elástica

En la tabla siguiente se enumeran todos los tipos de objetos de **Trabajos de base de datos elástica** con su descripción y las API de PowerShell relevantes.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descripción</th>
    <th>API de PowerShell relacionadas</th>
  </tr>
  <tr>
    <td>Credential:</td>
    <td>Nombre de usuario y contraseña que se usará al conectarse a bases de datos para la ejecución de scripts o la aplicación de archivos DACPAC. <p>La contraseña se cifra antes de enviarla y almacenarla en la base de datos de Trabajos de base de datos elástica. El servicio Trabajos de base de datos elástica descifra la contraseña a través de la credencial que se creó y cargó desde el script de instalación.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de T-SQL que se va a usar para la ejecución transversal en las bases de datos El script tiene que crearse para que sea idempotente, puesto que el servicio volverá a intentar la ejecución del script tras errores.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>Paquete de <a href="https://msdn.microsoft.com/library/ee210546.aspx">aplicación de capa de datos</a> que se va a aplicar transversalmente a las bases de datos.

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Destino de la base de datos</td>
    <td>Nombre del servidor y la base de datos que apuntan a una Base de datos SQL de Azure.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Destino de mapa de particiones</td>
    <td>Combinación de un destino de base de datos y una credencial que se usará para determinar la información almacenada en un mapa de particiones de base de datos elástica.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino de colección personalizada</td>
    <td>Grupo de bases de datos que se define para usar colectivamente en la ejecución.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino secundario de colección personalizada</td>
    <td>Destino de base de datos al que se hace referencia en una colección personalizada.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Trabajo</td>
    <td>
	<p>Definición de parámetros para un trabajo que puede usarse para desencadenar la ejecución o para cumplir una programación.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Ejecución de trabajos</td>
    <td>
	<p>Contenedor de las tareas necesarias para cumplir la ejecución de un script o para aplicar un DACPAC a un destino con las credenciales para las conexiones de base de datos, donde los errores se controlan según una directiva de ejecución.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Ejecución de tareas de trabajo</td>
    <td>
	<p>Unidad funcional para completar un trabajo.</p>
	<p>Si una tarea de trabajo no se puede ejecutar correctamente, se registrará el mensaje de excepción resultante y se creará una nueva tarea de trabajo coincidente que se ejecutará según la directiva de ejecución especificada.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Directiva de ejecución de trabajos</td>
    <td>
	<p>Controla los tiempos de espera, los límites de reintentos y los intervalos entre reintentos de la ejecución de trabajos.</p>
	<p>Trabajos de base de datos elástica incluye una directiva de ejecución de trabajos predeterminada que, básicamente, producirá un número infinito de reintentos de errores de tareas de trabajo con retroceso exponencial de intervalos entre reintentos.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Schedule</td>
    <td>
	<p>Especificación de tareas de duración definida para que la ejecución se produzca en un intervalo recurrente o en un momento dado.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Desencadenadores de trabajo</td>
    <td>
	<p>Asignación entre un trabajo y una programación que desencadena la ejecución del trabajo según la programación.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Tipos de grupo de trabajos de base de datos elástica admitidos
**Trabajos de base de datos elástica** habilita la ejecución de scripts de Transact-SQL (T-SQL) o la aplicación de archivos DACPAC transversalmente en un grupo de bases de datos. Cuando un trabajo se envía para su ejecución transversal en un grupo de bases de datos, Trabajos de base de datos elástica “expande” el trabajo en trabajos secundarios donde cada trabajo realiza la ejecución solicitada en una sola base de datos del grupo.
 
La siguiente lista muestra los tipos de grupo admitidos actualmente:

* [Mapa de particiones](sql-database-elastic-scale-shard-map-management.md): cuando un trabajo se destina a un mapa de particiones, el trabajo consulta primero el mapa de particiones para determinar su conjunto actual de particiones y luego se expande a los trabajos secundarios que coinciden con cada partición en el mapa de particiones.
* Colección personalizada: se especifica para que señale a un conjunto de bases de datos personalizado. Cuando un trabajo se destina a una colección personalizada, el trabajo se expande a los trabajos secundarios que coinciden con cada base de datos actualmente definida en la colección personalizada.

## Establecimiento de la conexión de Trabajos de base de datos elástica
Después de cargar el módulo de PowerShell, hay que establecer la conexión con la *Base de datos de control* de Trabajos de base de datos elástica antes de usar las API de trabajos. La invocación de este cmdlet desencadenará una ventana de credenciales emergente que solicita el nombre de usuario y la contraseña que se proporcionaron al instalar Trabajos de base de datos elástica. En todos los ejemplos que se ofrecen en este tema se supone que este primer paso ya se realizó.

Apertura de una conexión a Trabajos de base de datos elástica:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Credenciales cifradas en el servicio Trabajos de base de datos elástica
Las credenciales de la base de datos se pueden insertar en la *Base de datos de control* de Trabajos de base de datos elástica con su contraseña cifrada. Es preciso almacenar credenciales para habilitar la posterior ejecución de los trabajos, incluido el uso de programaciones de trabajos.
 
El cifrado funciona a través de un certificado creado como parte del script de instalación. El script de instalación crea y carga el certificado en el servicio en la nube de Azure para descifrar las contraseñas almacenadas que están cifradas. Más adelante, el servicio en la nube de Azure almacena la clave pública dentro de la *Base de datos de control* de Trabajos de base de datos elástica, lo que permite que la interfaz de la API de PowerShell o del Portal de Azure cifre una contraseña proporcionada sin que sea necesario que el certificado esté instalado localmente.
 
Aunque las contraseñas de credenciales se cifran y se protegen ante usuarios con acceso de solo lectura a objetos de Trabajos de base de datos elástica, es posible que un usuario malintencionado con acceso de lectura y escritura a objetos de Trabajos de base de datos elástica extraiga una contraseña. Las credenciales están diseñadas para su reutilización entre ejecuciones de trabajos. Las credenciales se pasan a las bases de datos de destino al establecer conexiones. Actualmente no hay ninguna restricción en las bases de datos de destino que se usan con cada credencial, por lo que es posible que un usuario malintencionado agregue un destino de base de datos en una base de datos bajo su control y posteriormente inicie un trabajo destinado a esta base de datos para conocer la contraseña de las credenciales.

Hay prácticas recomendadas de seguridad para Trabajos de base de datos elástica:

* Limitar el uso de las API a las personas de confianza.
* Las credenciales deben tener los privilegios mínimos necesarios para realizar la tarea de trabajo. Puede ver más información en este artículo de MSDN sobre SQL Server, [Autorización y permisos](https://msdn.microsoft.com/library/bb669084.aspx).

### Creación de una credencial cifrada para la ejecución transversal de trabajos en bases de datos
Para crear una nueva credencial cifrada, el cmdlet Get-Credential pedirá un nombre de usuario y una contraseña que puedan pasarse al cmdlet **New-AzureSqlJobCredential**.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Actualización de credenciales
Para actualizar una credencial existente a fin de que admita el cambio de contraseñas, use el cmdlet **Set-AzureSqlJobCredential** y establezca el parámetro **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Definición de un destino de mapa de particiones de base de datos elástica
Ejecute un trabajo en todas las bases de datos de un conjunto de particiones (creado con la [biblioteca cliente de base de datos elástica](sql-database-elastic-database-client-library.md)) utilizando un mapa de particiones como destino de la base de datos. Este ejemplo se basa en la creación de una aplicación particionada con la biblioteca cliente de base de datos elástica. Descargue y ejecute el ejemplo de [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

###Creación de un administrador de mapas de particiones con la aplicación de ejemplo

Aquí se creará un administrador de mapas de particiones junto con varias particiones, seguido de la inserción de datos en las particiones. Si resulta que ya dispone de la configuración de particiones, puede omitir los pasos siguientes y pasar a la próxima sección.

1. Compile y ejecute la aplicación de ejemplo **Introducción a las herramientas de base de datos elástica**. Siga los pasos hasta el paso 7 de la sección [Descarga y ejecución de la aplicación de ejemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Al final del paso 7, verá la siguiente línea de comandos:

	![símbolo del sistema][1]

2.  En la ventana de comandos, escriba “1” y presione **Entrar**. De esta forma, se creará el administrador de mapas de particiones y se agregarán dos particiones al servidor. Luego escriba “3” y presione **Entrar**; repita la acción cuatro veces. De esta forma, se insertan las filas de datos de ejemplo en sus particiones.
  
3.  El [Portal de vista previa de Azure](https://portal.azure.com) debe mostrar tres nuevas bases de datos en el servidor v12:

	![Confirmación de Visual Studio][2]

Ahora, cree un destino del mapa de particiones con el cmdlet **New-AzureSqlJobTarget**. Se debe establecer la base de datos de administrador de mapa de particiones como destino de la base de datos y luego especificar el mapa de particiones específico como destino.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Crear un script T-SQL para su ejecución transversal en las bases de datos

Al crear scripts de T-SQL para su ejecución, es muy recomendable hacerlo de forma que sean idempotentes y resistentes ante errores. Trabajos de base de datos elástica volverá a intentar la ejecución de un script cada vez que la ejecución encuentra un error, independientemente de la clasificación del error.

Use el cmdlet **New-AzureSqlJobContent** para crear y guardar un script de ejecución y establezca los parámetros **-ContentName** y **-CommandText**.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Creación de un script desde un archivo
Si el script de T-SQL se define dentro de un archivo, podría usarse el script siguiente para importar el script:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Actualización de un script de T-SQL para su ejecución transversal en bases de datos  

El siguiente script de PowerShell sirve para actualizar el texto de comandos de T-SQL en un script existente.

Establecimiento de las siguientes variables para que reflejen la definición que se quiera del script que se va a establecer:

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### Actualización de la definición de un script existente

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Creación de un trabajo para ejecutar transversalmente un script en un mapa de particiones

El siguiente script de PowerShell se puede usar a fin de iniciar un trabajo para la ejecución transversal de un script en cada partición de un mapa de particiones de escala elástica.

Establecimiento de las siguientes variables para que reflejen el script y el destino que se quiera:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

##Ejecución de un trabajo 

Puede usar este script de PowerShell para ejecutar un trabajo existente:

Actualice la variable siguiente para que refleje el nombre del trabajo que se quiera ejecutar:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Recuperación del estado de ejecución de un único trabajo

Use el cmdlet **Get-AzureSqlJobExecution** y establezca el parámetro **JobExecutionId** para ver el estado de una ejecución de trabajos.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Use el mismo cmdlet **Get-AzureSqlJobExecution** con el parámetro **IncludeChildren** para ver el estado de ejecuciones de trabajos secundarios, es decir, el estado específico de cada ejecución en cada base de datos destino del trabajo.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Ver el estado de varias ejecuciones de trabajos

El cmdlet **Get-AzureSqlJobExecution** tiene varios parámetros opcionales que sirven para mostrar varias ejecuciones del trabajo, filtradas por los parámetros proporcionados. Aquí mostramos algunas de las posibles formas de usar Get-AzureSqlJobExecution:

Recuperar todas las ejecuciones de trabajos activos de nivel superior:

	Get-AzureSqlJobExecution

Recuperar todas las ejecuciones de trabajos de nivel superior, incluidas las ejecuciones de trabajos inactivos:

	Get-AzureSqlJobExecution -IncludeInactive

Recuperar todas las ejecuciones de trabajos secundarios de un identificador de ejecución de trabajos proporcionado, incluidas las ejecuciones de trabajos inactivos:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperar todas las ejecuciones de trabajos creadas con una combinación de programación y trabajo, incluidos los trabajos inactivos:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recuperar todos los trabajos que se destinan a un mapa de particiones especificado, incluidos los trabajos inactivos:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recuperar todos los trabajos que se destinan a una colección personalizada especificada, incluidos los trabajos inactivos:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recuperar la lista de ejecuciones de tareas de trabajos dentro de la ejecución de un trabajo específico:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Recuperar los detalles de ejecución de tareas de trabajo:

Puede usar este script de PowerShell para ver los detalles de una ejecución de tareas de trabajo, lo que resulta especialmente útil al depurar errores de ejecución.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Recuperación de errores dentro de las ejecuciones de tareas de trabajo

El objeto JobTaskExecution incluye una propiedad para el ciclo de vida de la tarea y una propiedad de mensaje. Si no se realiza correctamente la ejecución de tareas de un trabajo, la propiedad de ciclo de vida se establecerá en *Failed* y la propiedad de mensaje se establecerá en el mensaje de excepción resultante y la pila. Si un trabajo no se realiza correctamente, es importante ver los detalles de las tareas de trabajo que no se realizaron correctamente en un trabajo determinado.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Esperar a que se complete la ejecución de un trabajo

Este script de PowerShell sirve para esperar a que una tarea de trabajo se complete:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Crear una directiva de ejecución personalizada

Trabajos de base de datos elástica admite la creación de directivas de ejecución personalizadas que se pueden aplicar al iniciar trabajos.
  
Actualmente, las directivas de ejecución permiten definir:

* Nombre: identificador de la directiva de ejecución.
* Tiempo de espera del trabajo: tiempo total antes de que Trabajos de base de datos elástica cancele un trabajo.
* Intervalo de reintento inicial: intervalo de espera antes del primer reintento.
* Intervalo máximo de reintento: límite de intervalos de reintento que se usan.
* Coeficiente de retroceso de intervalo de reintento: coeficiente que se usa para calcular el siguiente intervalo entre reintentos. Se usa la siguiente fórmula: (intervalo de reintento inicial) * Math.pow ((coeficiente de retroceso de intervalo), (número de intentos de) - 2). 
* Número máximo de intentos: número máximo de reintentos para llevar a cabo un trabajo.

La directiva de ejecución predeterminada usa los valores siguientes:

* Nombre: directiva de ejecución predeterminada
* Tiempo de espera del trabajo: 1 semana
* Intervalo de reintento inicial: 100 milisegundos
* Intervalo máximo de reintento: 30 minutos
* Coeficiente de intervalo de reintento: 2
* Número máximo de intentos: 2.147.483.647

Crear la directiva de ejecución que quiera:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Actualización de una directiva de ejecución personalizada

Actualizar la directiva de ejecución que se quiere actualizar:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Cancelación de un trabajo

Trabajos de base de datos elástica admite solicitudes de cancelación de trabajos. Si Trabajos de base de datos elástica detecta una solicitud de cancelación para un trabajo que está ejecutándose en ese momento, intentará detener el trabajo.

Trabajos de base de datos elástica puede realizar una cancelación de dos formas distintas:

1. Cancelación de tareas actualmente en ejecución: si se detecta una cancelación mientras se ejecuta una tarea, se intentará cancelar el aspecto de la tarea que se está ejecutando actualmente. Por ejemplo: si hay una consulta de larga ejecución en curso en el momento en que se intenta realizar una cancelación, se intentará cancelar la consulta.
2. Cancelación de reintentos de tareas: si el subproceso de control detecta una cancelación antes de iniciar una tarea para su ejecución, evitará iniciar la tarea y declarará cancelada la solicitud.

Si se solicita una cancelación de trabajo para un trabajo primario, se respetará la solicitud de cancelación para el trabajo primario y todos los trabajos secundarios.
 
Para enviar una solicitud de cancelación, use el cmdlet **Stop-AzureSqlJobExecution** y establezca el parámetro **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Eliminación de un trabajo por nombre y el historial de trabajos

Trabajos de base de datos elástica admite la eliminación asincrónica de trabajos. Un trabajo se puede marcar para su eliminación y el sistema eliminará el trabajo y su historial de trabajos una vez completadas todas las ejecuciones de trabajos para ese trabajo. El sistema no cancelará automáticamente las ejecuciones de trabajos activos.

En su lugar, se debe invocar Stop-AzureSqlJobExecution para cancelar las ejecuciones de trabajos activos.

Para desencadenar la eliminación del trabajo, use el cmdlet **Remove-AzureSqlJob** y establezca el parámetro **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Creación de un destino de base de datos personalizada
En Trabajos de base de datos elástica, se pueden definir destinos de base de datos personalizada que sirven para su ejecución directa o para su inclusión en un grupo de base de datos personalizada. Puesto que los **grupos de bases de datos elásticas** todavía no se admiten directamente a través de las API de PowerShell, cree solo un destino de base de datos personalizada y un destino de la colección de bases de datos personalizada que englobe todas las bases de datos del grupo.

Establecimiento de las siguientes variables para que reflejen la información de base de datos que se quiera:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Creación de un destino de colección de bases de datos personalizada
Puede definirse un destino de colección de bases de datos personalizada para habilitar la ejecución transversal en varios destinos de base de datos definidos. Después de crear un grupo de bases de datos, las bases de datos se pueden asociar al destino de la colección personalizada.

Establecimiento de las siguientes variables para que reflejen la configuración de destino de la colección personalizada que se quiera:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Adición de bases de datos a un destino de colección de bases de datos personalizada

Los destinos de base de datos se pueden asociar a los destinos de colección de bases de datos personalizada para crear un grupo de bases de datos. Cada vez que se crea un trabajo que se destina a una colección de bases de datos personalizada, se expandirá para dirigirse a las bases de datos asociadas al grupo en el momento de ejecución.

Adición de la base de datos que se quiera a una colección personalizada específica:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Revisión de las bases de datos incluidas en un destino de colección de bases de datos personalizada

Use el cmdlet **Get-AzureSqlJobTarget** para recuperar las bases de datos secundarias en un destino de la colección de bases de datos personalizada.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Creación de un trabajo para ejecutar un script transversalmente en un destino de colección de bases de datos personalizada

Use el cmdlet **New-AzureSqlJob** para crear un trabajo en un grupo de bases de datos definido por un destino de la colección de base de datos personalizada. Trabajos de base de datos elástica expande el trabajo en varios trabajos secundarios, cada uno correspondiente a una base de datos asociada al destino de la colección de bases de datos personalizada y garantiza que el script se ejecuta en cada una de las bases de datos. De nuevo, es importante que los scripts sean idempotentes para que sean resistentes a los reintentos.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Recopilación de datos de una base de datos a otra

**Trabajos de base de datos elástica** es compatible con la ejecución de una consulta transversal en un grupo de bases de datos y envía los resultados a la tabla de la base de datos especificada. La tabla se puede consultar a posteriori para ver los resultados de la consulta de cada base de datos. Esto ofrece un mecanismo asincrónico para ejecutar una consulta transversalmente en varias bases de datos. Los casos de error, como que una de las bases de datos no esté disponible temporalmente, se controlan automáticamente a través de reintentos.

Se creará automáticamente la tabla de destino especificada si todavía no existe ninguna que coincida con el esquema del conjunto de resultados devuelto. Si la ejecución de un script devuelve varios conjuntos de resultados, Trabajos de base de datos elástica solo enviará el primero a la tabla de destino proporcionada.

El siguiente script de PowerShell sirve para ejecutar un script que recopile sus resultados en una tabla especificada. Este script presupone que se creó un script T-SQL que genera un único conjunto de resultados y se creó un destino de la colección de bases de datos personalizada.

Establecimiento de las siguientes opciones para que reflejen el script, las credenciales y el destino de ejecución que se quieran:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Creación e inicio de un trabajo en escenarios de recolección de datos
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Creación de una programación para la ejecución de trabajos con un desencadenador de trabajo

El siguiente script de PowerShell sirve para crear una programación recurrente. Este script usa un intervalo de minutos, pero New-AzureSqlJobSchedule también admite los parámetros -DayInterval, -HourInterval, -MonthInterval y -WeekInterval. Se pueden crear programaciones que se ejecutan una sola vez pasando -OneTime.

Creación de una programación:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Creación de un desencadenador de trabajo para que un trabajo se ejecute según una programación de tiempo

Se puede definir un desencadenador de trabajo para que un trabajo se ejecute según una programación de tiempo. El siguiente script de PowerShell sirve para crear un desencadenador de trabajo.

Establecimiento de las siguientes variables para que se correspondan con el trabajo y la programación que se quiera:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Eliminación de una asociación programada para detener la ejecución de un trabajo en la programación

Para suspender la ejecución de un trabajo recurrente a través de un desencadenador de trabajo, se puede quitar el desencadenador de trabajo. Quite un desencadenador de trabajo para detener un trabajo que se ejecute según una programación con el cmdlet **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName

### Recuperación de desencadenadores de trabajo enlazados a una programación de tiempo

El siguiente script de PowerShell sirve para obtener y mostrar los desencadenadores de trabajo registrados para una programación de tiempo determinada.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Recuperación de desencadenadores enlazados a un trabajo 

El siguiente script de PowerShell puede usarse para obtener y mostrar programaciones que contengan un trabajo registrado.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Creación de una implementación de aplicación de capa de datos (DACPAC) para su ejecución transversal en bases de datos

Trabajos de base de datos elástica puede usarse para implementar una aplicación de capa de datos (DACPAC) a un grupo de bases de datos. Para crear una DACPAC, consulte esta documentación. Para que Trabajos de base de datos elástica implemente una DACPAC en un grupo de bases de datos, la DACPAC debe ser accesible para el servicio. Se recomienda cargar una DACPAC creada en Almacenamiento de Azure y crear un URI con signo para la DACPAC.

El siguiente script de PowerShell sirve para insertar una DACPAC en Trabajos de base de datos elástica:

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Actualización de una implementación de aplicación de capa de datos (DACPAC) para su ejecución transversal en bases de datos

Las DACPAC existentes que se registren en Trabajos de base de datos elástica pueden actualizarse para que señalen a nuevos URI. El siguiente script de PowerShell sirve para actualizar el URI de DACPAC en un DACPAC registrado existente:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Creación de una implementación de aplicación de capa de datos (DACPAC) para su aplicación transversal en bases de datos

Una vez creada una DACPAC en Trabajos de base de datos elástica, puede crearse un trabajo que aplique transversalmente la DACPAC en un grupo de bases de datos. El siguiente script de PowerShell sirve para crear un trabajo DACPAC transversalmente en una colección personalizada de bases de datos:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->