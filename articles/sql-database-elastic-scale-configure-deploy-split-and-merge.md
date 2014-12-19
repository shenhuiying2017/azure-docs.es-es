<properties title="Split and Merge Service Tutorial" pageTitle="Tutorial del servicio de combinación y división de SQL de Azure" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Tutorial del servicio de combinación y división de escalado elástico

## Descargue los paquetes de división-combinación 
1. Descargue la versión más reciente de NuGet en [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Abra un símbolo del sistema y desplácese al directorio donde ha descargado nuget.exe. 
3. Descargue el paquete de división-combinación más reciente en el directorio actual con el comando siguiente: 
"nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge"  

Los pasos anteriores permiten descargar los archivos de división-combinación en el directorio actual. Los archivos se colocan en un directorio llamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, donde *x.x.xxx.x* refleja el número de versión. Busque los archivos del servicio división-combinación en el subdirectorio **content\splitmerge\service** y los scripts de PowerShell división-combinación (y el cliente requerido .dlls) en el subdirectorio **content\splitmerge\powershell**.

##Requisitos previos 

1. Cree una base de datos de Base de datos SQL de Azure que se usará como la base de datos de estado para división-combinación. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com). En la parte inferior izquierda, haga clic en **Nuevo**, en **Servicios de datos** -> **Base de datos SQL** -> **Creación personalizada**. Rellene el nombre de la base de datos y cree un nuevo usuario y una contraseña. Asegúrese de que registra el nombre y la contraseña para su uso posterior.

2. Asegúrese de que el servidor de Base de datos SQL de Azure permite que los servicios de Microsoft Azure se conecten a él. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com) y, en el panel izquierdo, haga clic en **Bases de datos SQL**. A continuación, haga clic en **Servidores** en la cinta de opciones en la parte superior de la pantalla y seleccione el servidor. A continuación, haga clic en **Configurar** en la parte superior y asegúrese de que la configuración de **Servicios de Microsoft Azure** está establecida en **Sí**.

    ![Allowed services][1]

3. Cree una cuenta de Almacenamiento de Azure que se usará para resultados de diagnóstico. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com). En la parte inferior izquierda, haga clic en **Nuevo**, **Servicios de datos**, **Almacenamiento** y **Creación rápida**. 

4. Cree un servicio de nube de Azure que contendrá el servicio de división-combinación.  Vaya al [Portal de administración de Azure](https://manage.windowsazure.com). En la parte inferior izquierda, haga clic en **Nuevo**, **Proceso**, **Servicio de nube** y **Creación rápida**. 


## Configuración del servicio de división-combinación 

### Configuración del servicio de división-combinación 

1. En la carpeta en la que descargó los bits de División/Combinación, cree una copia del archivo **ServiceConfiguration.Template.cscfg** proporcionado con **SplitMergeService.cspkg** y asígnele el nombre **ServiceConfiguration.cscfg**.

2. Abra ServiceConfiguration.cscfg en el editor de texto favorito. Se recomienda utilizar Visual Studio, ya que validará entradas como el formato de huellas digitales de certificados. 

3. Cree una nueva base de datos o elija una base de datos existente para actuar como la base de datos de estado para las operaciones división/combinación y recuperar la cadena de conexión de base de datos. Con Base de datos SQL de Azure, la cadena de conexión normalmente tiene el formato:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Escriba esta cadena de conexión en el archivo en las secciones **SplitMergeWeb** y **SplitMergeWorker** de la configuración de ElasticScaleMetadata.

5.    La configuración del destino para los registros de diagnóstico requiere una cuenta de Almacenamiento de Azure. La configuración de división/combinación requiere la cadena de conexión para la cuenta de Almacenamiento de Azure. La cadena de conexión debe tener el formato:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Para determinar la clave de acceso, vaya al [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en la pestaña **Almacenamiento** de la izquierda, seleccione el nombre correspondiente a la cuenta de almacenamiento y haga clic en **Administrar claves de acceso** en la parte inferior. Haga clic en el botón **copiar** para la **Clave de acceso primaria**.

![manage access keys][2]

6.    Escriba el nombre de la cuenta de almacenamiento y una de las claves de acceso proporcionadas en los marcadores de posición en la cadena de conexión de almacenamiento. Esta cadena de conexión se usa en las secciones de rol **SplitMergeWeb** y **SplitMergeWorker** en la configuración de **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Puede utilizar cuentas de almacenamiento potencialmente diferentes para roles diferentes. 

### Configuración de seguridad 
Para obtener instrucciones detalladas para configurar la seguridad del servicio, consulte [Configuraciones de seguridad de escalado elástico](./sql-database-elastic-scale-configure-security.md).

Para los fines de una implementación de prueba sencilla apta para completar este tutorial, se ejecutarán los pasos de configuración mínimos para poner en marcha el servicio. Estos pasos permiten que solo el equipo/la cuenta que los ejecuta se comunique con el servicio.

### Creación de un certificado autofirmado 

Cree un directorio nuevo y, desde ahí, ejecute el siguiente comando usando una ventana [Símbolo del sistema para desarrolladores de Visual Studio](http://msdn.microsoft.com/es-es/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Se le pedirá una contraseña para proteger la clave privada. Escriba una contraseña segura y confírmela. Después de este paso, se le pedirá la contraseña una vez más. Haga clic en **Sí** al final para importarla en el almacén raíz de las autoridades de certificación de confianza.

###    Creación de un archivo PFX 

Ejecute el siguiente comando desde la misma ventana donde se ejecutó makecert; utilice la misma contraseña que usó para crear el certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importe el certificado de cliente en el almacén personal
1. En el Explorador de Windows, haga doble clic en **MyCert.pfx**.
2. En el **Asistente para importación de certificados**, seleccione **Usuario actual** y haga clic en **Siguiente**.
3. Confirme al ruta de acceso al archivo y haga clic en **Siguiente**.
4. Escriba la contraseña, deje la opción **Incluir todas las propiedades extendidas** marcada y haga clic en **Siguiente**.
5. Deje la opción **Seleccionar automáticamente el almacén de certificados[...]** marcada y haga clic en **Siguiente**.
6. Haga clic en **Finalizar** y, a continuación, en **Aceptar**.

### Carga del archivo PFX en el servicio de nube

Vaya al [Portal de administración de Azure](https://manage.windowsazure.com).

1. Seleccione **Servicios en la nube**.
2. Seleccione el servicio de nube creado anteriormente para el servicio división/combinación.
3. Haga clic en **Certificados** en el menú superior.
4. Haga clic en **Cargar** en la barra inferior.
5. Seleccione el archivo PFX y escriba la misma contraseña anterior.
6. Una vez completado, copie la huella digital del certificado de la nueva entrada en la lista.

### Actualice el archivo de configuración de servicio

Pegue la huella digital del certificado copiada anteriormente en el atributo huella digital/valor de esta configuración:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Tenga en cuenta que deben usarse certificados independientes de implementaciones de producción para entidades de certificación, el certificado del servidor y los certificados de clientes. Para obtener instrucciones al respecto, consulte [Configuración de seguridad](./sql-database-elastic-scale-configure-security.md).

### Implementación del servicio división-combinación
1. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en la pestaña **Servicios en la nube** a la izquierda y seleccione el servicio en la nube que ha creado anteriormente. 
3. Haga clic en **Panel**. 
4. Elija un entorno de ensayo y, a continuación, haga clic en **Cargue una nueva implementación de ensayo**.

    ![Staging][3]

5. En el cuadro de diálogo, escriba una etiqueta de implementación. Para "Paquete" y "Configuración", haga clic en "Desde local" y seleccione el archivo **SplitMergeService.cspkg** y el archivo .cscfg que ha configurado anteriormente.
6. Asegúrese de que está activada la casilla **Implementar aunque uno o varios roles contengan una sola instancia**.
7. Presione el botón de marca en la parte inferior derecha para iniciar la implementación. Espero unos minutos hasta que se complete el proceso.

![Upload][4]


## Solución de problemas de implementación
Si no se puede poner en línea el rol web, probablemente se debe a un problema con la configuración de seguridad. Compruebe que la conexión SSL está configurada como se ha descrito anteriormente.

Si su rol de trabajo no se puede poner en línea, pero el rol web se realiza correctamente, probablemente se debe a un problema de conexión a la base de datos de estado que ha creado anteriormente. 

* Asegúrese de que la cadena de conexión del archivo .cscfg es precisa. 
* Compruebe que el servidor y la base de datos existen y que el identificador de usuario y la contraseña son correctos.
* Para Base de datos SQL de Azure, la cadena de conexión debe tener el formato: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Asegúrese de que el nombre del servidor no empieza por **https://**.
* Asegúrese de que el servidor de Base de datos SQL de Azure permite que los servicios de Microsoft Azure se conecten a él. Para ello, abra https://manage.windowsazure.com, haga clic en "Bases de datos SQL" a la izquierda, haga clic en "Servidores" en la parte superior y seleccione el servidor. Haga clic en **Configurar** en la parte superior y asegúrese de que la opción **Servicios de Microsoft Azure** está establecida en "Sí". (Consulte la sección Requisitos previos al principio de este artículo).

* Revise los registros de diagnósticos para la instancia de servicio de división/combinación. Abra una instancia de Visual Studio y, en la barra de menús, haga clic en **Ver** y en **Explorador de servidores**. Haga clic en el icono de **Microsoft Azure** para conectarse a la suscripción de Azure. Vaya a Microsoft Azure -> Almacenamiento -> <your storage account> -> Tablas -> WADLogsTable. Para obtener más información, consulte [Explorar recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/es-es/library/azure/ff683677.aspx) 

    ![][5]

    ![][6]

## Pruebas de la implementación del servicio de división-combinación
### Conexión con un explorador web

Determine el extremo web del servicio de división-combinación. Puede encontrarlo en el Portal de administración de Azure; para ello, vaya al **Panel** del servicio en la nube y observe el campo **Dirección URL del sitio** en el lado derecho. Reemplace **http://** con **https://**, ya que la configuración de seguridad predeterminada deshabilita el extremo de HTTP. Cargue la página para esta dirección URL en el explorador.

### Pruebas con scripts de PowerShell

La implementación y el entorno se pueden probar mediante la ejecución de los scripts de PowerShell de ejemplo incluidos.

Los archivos de script incluidos son:

1. **SetupSampleSplitMergeEnvironment.ps1**: establece un nivel de datos de prueba para división/combinación (consulte la tabla siguiente para obtener una descripción detallada)
2. **ExecuteSampleSplitMerge.ps1**: ejecuta operaciones de prueba en cada nivel de datos de prueba (consulte la tabla siguiente para obtener una descripción detallada)
3. **GetMappings.ps1**: script de ejemplo de nivel superior que imprime el estado actual de las asignaciones de particiones.
4. **ShardManagement.psm1**: script de la aplicación auxiliar que ajusta la API ShardManagement.
5. **SqlDatabaseHelpers.psm1**: script de la aplicación auxiliar para crear y administrar bases de datos SQL.

<table style="width:100%">
  <tr>
    <th>Archivo de PowerShell</th>
    <th>Pasos</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Crea una base de datos para administradores de asignaciones de particiones</td>
  </tr>
  <tr>
    <td>2.    Crea dos bases de datos de particiones. 
  </tr>
  <tr>
    <td>3.    Crea un asignación de particiones para la base de datos (elimina las asignaciones de particiones existentes de las bases de datos). </td>
  </tr>
  <tr>
    <td>4.    Crea una tabla de ejemplo pequeña en las particiones y rellena la tabla en una de las particiones.</td>
  </tr>
  <tr>
    <td>5.    Declara SchemaInfo para la tabla de particiones.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Archivo de PowerShell</th>
    <th>Pasos</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Envía una solicitud de división al frontend web del servicio división-combinación, que divide la mitad de los datos entre la primera partición y la segunda.</td>
  </tr>
  <tr>
    <td>2.    Sondea el frontend web para el estado de la solicitud de división y espera a que finalice la solicitud.</td>
  </tr>
  <tr>
    <td>3.    Envía una solicitud de combinación al frontend web del servicio división-combinación, que devuelve los datos de nuevo de la segunda partición a la primera.</td>
  </tr>
  <tr>
    <td>4.    Sondea el frontend web para el estado de la solicitud de combinación y espera a que finalice la solicitud.</td>
  </tr>
</table>

##Uso de PowerShell para verificar la implementación

1.    Abra una nueva ventana de PowerShell y desplácese al directorio donde descargó el paquete de división-combinación y, a continuación, desplácese al directorio "powershell".
2.    Cree un servidor de Base de datos SQL de Azure (o elija un servidor existente) donde se crearán el Administrador de asignación de particiones y las particiones. 

Nota: El script SetupSampleSplitMergeEnvironment.ps1 crea todas estas bases de datos en el mismo servidor de forma predeterminada para simplificar el script. No se trata de una restricción del servicio de división-combinación.

    Necesitará un inicio de sesión de autenticación de SQL con acceso de lectura y escritura a las bases de datos para que el servicio de división-combinación pueda mover datos y actualizar la asignación de particiones. Habida cuenta de que el servicio de división-combinación se ejecuta en la nube, no admite actualmente la autenticación integrada.

    Asegúrese de que el servidor SQL de Azure está configurado para permitir el acceso desde la dirección IP del equipo que ejecuta estos scripts. Puede encontrar esta configuración en el servidor SQL de Azure / configuración / direcciones IP permitidas.

3.    Ejecute el script SetupSampleSplitMergeEnvironment.ps1 para crear el entorno de ejemplo. 

    La ejecución de este script limpiará cualquier estructura de datos de administración de asignaciones de particiones existente en la base de datos del administrador de asignación de particiones y las particiones. Puede ser útil volver a ejecutar el script si desea volver a inicializar la asignación de partición o las particiones.

    Línea de comandos de ejemplo:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Ejecute el script Getmappings.ps1 para ver las asignaciones que existen actualmente en el entorno de ejemplo.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Ejecute el script ExecuteSampleSplitMerge.ps1 para ejecutar una operación de división (mover la mitad de las datos de la primera partición a la segunda partición) y, a continuación, una operación de combinación (mover los datos de nuevo a la primera partición). Si configura SSL y deja el extremo de http deshabilitado, asegúrese de utilizar el extremo https://.

    Línea de comandos de ejemplo:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Si recibe el siguiente error, probablemente es un problema con el certificado del extremo web. Intente conectarse al extremo web con su explorador web favorito y comprobar si hay un error de certificado.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Si se ha realizado correctamente, el resultado debería ser el siguiente:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request. 
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Experimentar con otros tipos de datos. Todos estos scripts adoptan un parámetro opcional -ShardKeyType que permite especificar el tipo de clave. El valor predeterminado es Int32, pero también puede especificar Int64, Guid o binario. 

## Creación de sus propias solicitudes 

El servicio puede utilizarse mediante la interfaz de usuario web o importar y utilizar el módulo de PowerShell SplitMerge.psm1.

El servicio de división-combinación puede mover datos en tablas de particiones y tablas de referencia. Una tabla de particiones tiene una columna de clave de partición y datos de fila diferentes en cada partición. Una tabla de referencia no está particionada, por lo que contiene los mismos datos de fila en cada partición. Las tablas de referencia son útiles para los datos que no cambian con frecuencia y se utilizan en combinación con tablas de particiones en las consultas.

Para realizar una operación división-combinación, debe declarar las tablas de particiones y las tablas de referencia que desee mover. Esto se consigue con la API **SchemaInfo**. Esta API está en el espacio de nombres **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Para cada tabla de particiones, cree un objeto **ShardedTableInfo** que describa el nombre del esquema principal de la tabla (opcional, con valores predeterminados "dbo"), el nombre de la tabla y el nombre de columna en dicha tabla que contenga la clave de partición.
2.    Para cada tabla de referencia, cree un objeto **ReferenceTableInfo** que describa el nombre del esquema principal de la tabla (opcional, con valores predeterminados "dbo") y el nombre de la tabla.
3.    Agregue los objetos anteriores TableInfo a un nuevo objeto **SchemaInfo**.
4.    Obtenga una referencia a un objeto **ShardMapManager** y llame a **GetSchemaInfoCollection**.
5.    Agregue **SchemaInfo** a **SchemaInfoCollection**, proporcionando el nombre de la asignación de particiones.

Un ejemplo de esto puede verse en el script SetupSampleSplitMergeEnvironment.ps1.

Tenga en cuenta que el servicio de división-combinación no crea la base de datos de destino (o el esquema de tablas en la base de datos) por usted. Debe crearse previamente antes de enviar una solicitud al servicio.


## Solución de problemas
Es posible que vea el siguiente mensaje al ejecutar los scripts de powershell de ejemplo:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Este error significa que el certificado SSL no está configurado correctamente. Siga las instrucciones de la sección "Conexión con un explorador web".

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
