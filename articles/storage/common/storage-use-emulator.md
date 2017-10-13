---
title: Uso del emulador de Azure Storage para desarrollo y pruebas | Microsoft Docs
description: "El emulador de Azure Storage ofrece un entorno de desarrollo local gratuito para desarrollo y pruebas de las aplicaciones de Azure Storage. Aprenda cómo se autentican las solicitudes, cómo conectarse al emulador desde su aplicación y cómo usar la herramienta de la línea de comandos."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: tamram
ms.openlocfilehash: 7d86d5e8547d977c07cfbb0597b74382172a8472
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Uso del emulador de Azure Storage para desarrollo y pruebas

El emulador de almacenamiento de Microsoft Azure proporciona un entorno local que emula los servicios de Azure de blob, cola y tabla para fines de desarrollo. Mediante el emulador de almacenamiento, puede probar la aplicación en los servicios de almacenamiento local, sin crear una suscripción a Azure ni incurrir en ningún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar al uso de una cuenta de almacenamiento de Azure en la nube.

## <a name="get-the-storage-emulator"></a>Obtención del emulador de almacenamiento
El emulador de almacenamiento se encuentra disponible con el [SDK de Microsoft Azure](https://azure.microsoft.com/downloads/). También puede instalar el emulador de almacenamiento mediante el [instalador independiente](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (descarga directa). Para instalar el emulador de almacenamiento, debe tener privilegios de administrador en el equipo.

El emulador de almacenamiento solo se ejecuta actualmente en Windows. Para quienes consideran un emulador de almacenamiento para Linux, una opción es el emulador de código abierto [Azurite](https://github.com/arafato/azurite), mantenido por la comunidad.

> [!NOTE]
> No se garantiza que los datos que se crean en una versión del emulador de almacenamiento estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de almacenamiento de Azure y no en el mismo emulador de almacenamiento.
> <p/>
> El emulador de almacenamiento depende de versiones específicas de las bibliotecas de OData. No se admite la sustitución de las DLL de OData usadas por el emulador de almacenamiento con otras versiones y, de ser así, puede provocar un comportamiento inesperado. Sin embargo, cualquier versión de OData admitida por el servicio de almacenamiento puede utilizarse para enviar las solicitudes al emulador.
>

## <a name="how-the-storage-emulator-works"></a>Cómo funciona el emulador de almacenamiento.
El emulador de almacenamiento usa una instancia de Microsoft SQL Server local, así como el sistema de archivos local para emular los servicios de almacenamiento de Azure. De manera predeterminada, el emulador de almacenamiento usa una base de datos en Microsoft SQL Server 2012 Express LocalDB. Puede configurar el emulador de almacenamiento para acceder a una instancia local de SQL Server en lugar de una de LocalDB. Para más información, consulte la sección [Iniciar e inicializar el emulador de almacenamiento](#start-and-initialize-the-storage-emulator) más adelante en este artículo.

El emulador de almacenamiento se conecta a SQL Server o LocalDB mediante la autenticación de Windows.

Existen algunas diferencias de funcionalidad entre el emulador de almacenamiento y los servicios de almacenamiento de Azure. Para más información sobre estas diferencias, consulte la sección [Diferencias entre el emulador de almacenamiento y Azure Storage](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar el emulador de almacenamiento
Para iniciar el emulador de Azure Storage:
1. Haga clic en el botón **Inicio** o pulse la tecla **Windows**.
1. Comience a escribir `Azure Storage Emulator`.
1. Seleccione el emulador de la lista de aplicaciones mostradas.

Cuando se inicie el emulador de almacenamiento, aparecerá una ventana de símbolo del sistema. Puede usar esta ventana de consola para iniciar y detener el emulador de almacenamiento, borrar datos, obtener su estado e inicializar el emulador. Para más información, consulte la sección [Referencia de la herramienta de la línea de comandos del emulador de almacenamiento](#storage-emulator-command-line-tool-reference) más adelante en este artículo.

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows.

Al cerrar la ventana de símbolo del sistema del emulador de almacenamiento, el emulador de almacenamiento continuará funcionando. Para abrir de nuevo la ventana de consola del emulador de almacenamiento, siga los pasos anteriores como si fuera a iniciar el emulador de almacenamiento.

Al ejecutar por primera vez el emulador de almacenamiento, el entorno de almacenamiento local se inicia automáticamente. El proceso de inicialización crea una base de datos en LocalDB y se reserva puertos HTTP para cada servicio de almacenamiento local.

El emulador de almacenamiento está instalado de forma predeterminada en `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Puede usar el [Explorador de Microsoft Azure Storage](http://storageexplorer.com) para trabajar con recursos locales del emulador de almacenamiento. Después de haber instalado e iniciado el emulador de almacenamiento, busque "(Desarrollo)" en "Cuentas de Storage" en el árbol de recursos del Explorador de Storage.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar el emulador de almacenamiento para usar otra base de datos SQL
La herramienta de la línea de comandos del emulador de almacenamiento se puede usar para inicializar el emulador de almacenamiento para que señale a una instancia de base de datos SQL distinta a la instancia LocalDB predeterminada:

1. Abra la ventana de consola del emulador de almacenamiento como se describe en la sección [Iniciar e inicializar el emulador de almacenamiento](#start-and-initialize-the-storage-emulator).
1. En la ventana de consola, escriba el siguiente comando, donde `<SQLServerInstance>` es el nombre de la instancia de SQL Server. Para usar LocalDb, especifique `(localdb)\MSSQLLocalDb` como instancia de SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  También puede usar el siguiente comando, el cual indicará al emulador que utilice la instancia predeterminada de SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  O bien, puede usar el comando siguiente, que reinicializa la base de datos a la instancia de LocalDB predeterminada:

  `AzureStorageEmulator.exe init /forceCreate`

Para más información sobre estos comandos, consulte la sección [Referencia de la herramienta de la línea de comandos del emulador de almacenamiento](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Puede usar [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para administrar las instancias de SQL Server, incluida la instalación de LocalDB. En el cuadro de diálogo **Conectar al servidor** de SMSS, especifique `(localdb)\MSSQLLocalDb` en el campo **Nombre del servidor:** para conectarse a la instancia de LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticar solicitudes frente al emulador de almacenamiento
Cuando haya instalado e iniciado el emulador de almacenamiento, puede probar el código en él. De la misma manera que con Azure Storage en la nube, todas las solicitudes que realice en el emulador de almacenamiento se deben autenticar, a menos que sea una solicitud anónima. Puede autenticar solicitudes contra el emulador de almacenamiento mediante la autenticación de clave compartida o con una firma de acceso compartido (SAS).

### <a name="authenticate-with-shared-key-credentials"></a>Autenticación con credenciales de clave compartida
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para más información sobre las cadenas de conexión, consulte [Configuración de las cadenas de conexión de Azure Storage](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>Autenticación con una firma de acceso compartido
Algunas bibliotecas de cliente de almacenamiento de Azure, como la biblioteca Xamarin, solo admiten la autenticación con un token de firma de acceso compartido (SAS). Puede crear el token de SAS mediante una herramienta como el [Explorador de Storage](http://storageexplorer.com/) u otra aplicación que admita la autenticación de clave compartida.

También puede generar un token de SAS mediante Azure PowerShell. En el ejemplo siguiente se genera un token de SAS con permisos completos a un contenedor de blobs:

1. Instale Azure PowerShell si no lo ha hecho ya (se recomienda el uso de la versión más reciente de los cmdlets de Azure PowerShell). Para ver las instrucciones de instalación, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Abra Azure PowerShell y ejecute los siguientes comandos; sustituya `ACCOUNT_NAME` y `ACCOUNT_KEY==` por sus propias credenciales y `CONTAINER_NAME` por un nombre de su elección:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

El identificador URI de la firma de acceso compartido resultante para el nuevo contenedor debe ser similar a:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

La firma de acceso compartido creada con este ejemplo es válida durante un día. La firma concede acceso completo (lectura, escritura, eliminación, enumeración) a blobs situados dentro del contenedor.

Para más información sobre las firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS) en Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Direccionar los recursos en el emulador de almacenamiento
Los extremos de servicio para el emulador de almacenamiento son diferentes de los de una cuenta de almacenamiento de Azure. La diferencia se debe al hecho de que el equipo local no realiza la resolución de nombres de dominio, por lo que los puntos de conexión del emulador de almacenamiento requieren una dirección local.

Al direccionar un recurso en una cuenta de Azure Storage, usará el siguiente esquema. El nombre de cuenta forma parte del nombre de host del identificador URI y el recurso que se redirige forma parte de la ruta de acceso del identificador URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Por ejemplo, el siguiente URI es una dirección válida para un blob en una cuenta de almacenamiento de Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Sin embargo, en el emulador de almacenamiento, dado que el equipo local no lleva a cabo la resolución de nombres de dominio, el nombre de la cuenta forma parte de la ruta de acceso del URI en lugar del nombre de host. Use el siguiente formato de URI para un recurso en el emulador de almacenamiento:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob en el emulador de almacenamiento:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Los extremos de servicio para el emulador de almacenamiento son:

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Queue service: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Direccionar la cuenta secundaria con RA-GRS
A partir de la versión 3.1, el emulador de almacenamiento admite la replicación con redundancia geográfica con acceso de lectura (RA-GRS). Para los recursos de almacenamiento en la nube y en el emulador local, puedes obtener acceso a la ubicación de la cuenta secundaria si anexa -secondary al nombre de la cuenta. Por ejemplo, la siguiente dirección se puede usar para obtener acceso a un blob usando la cuenta secundaria de solo lectura en el emulador de almacenamiento:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulte la [biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) si desea obtener más información.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referencia de la herramienta de la línea de comandos del emulador de almacenamiento
A partir de la versión 3.0, se muestra una ventana de consola cuando se inicia el emulador de almacenamiento. Use la línea de comandos de la ventana de consola para iniciar y detener el emulador, así como para consultar el estado y realizar otras operaciones.

> [!NOTE]
> Si tiene instalado el emulador de proceso de Microsoft Azure, aparece un icono de la bandeja del sistema al iniciar el emulador de almacenamiento. Haga clic con el botón derecho en el icono para abrir un menú que ofrece en forma de gráficos las opciones de iniciar y detener el emulador de almacenamiento.
>
>

### <a name="command-line-syntax"></a>Sintaxis de la línea de comandos
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opciones
Para ver la lista de opciones, escriba `/help` en el símbolo del sistema.

| Opción | Description | Comando | Argumentos |
| --- | --- | --- | --- |
| **Iniciar** |Inicia el emulador de almacenamiento. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: inicia el emulador en el proceso actual en lugar de crear un nuevo proceso. |
| **Detención** |Detiene el emulador de almacenamiento. |`AzureStorageEmulator.exe stop` | |
| **Estado** |Imprime el estado del emulador de almacenamiento. |`AzureStorageEmulator.exe status` | |
| **Borrar** |Borra los datos de todos los servicios especificados en la línea de comandos. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*blob*: borra datos del blob. <br/>*queue*: borra datos de cola. <br/>*table*: borra datos de tabla. <br/>*all*: borra todos los datos de todos los servicios. |
| **Init** |Realiza una inicialización única para configurar el emulador. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server Nombreservidor\nombreinstancia*: especifica el servidor que hospeda la instancia de SQL. <br/>*-sqlinstance instanceName*: especifica el nombre de la instancia de SQL que se usará en la instancia de servidor prdeterminada. <br/>*-forcecreate*: fuerza la creación de la base de datos SQL, aunque ya exista. <br/>*-skipcreate*: omite la creación de la base de datos SQL. Esto tiene prioridad sobre -forcecreate.<br/>*-reserveports*: intenta reservar los puertos HTTP asociados con los servicios.<br/>*-unreserveports*: intenta quitar las reservas de los puertos HTTP asociados con los servicios. Esto tiene prioridad sobre -reserveports.<br/>*-inprocess*: realiza la inicialización en el proceso actual, en lugar de generar un proceso nuevo. Tiene que iniciar el proceso actual con permisos elevados si se cambian las reservas de puerto. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferencias entre el emulador de almacenamiento y el almacenamiento de Azure
Dado que el emulador de almacenamiento es un entorno emulado que se ejecuta en una instancia de SQL local, hay diferencias de funcionalidad entre el emulador y una cuenta de Azure Storage en la nube:

* El emulador de almacenamiento es compatible con una sola cuenta fija y una clave de autenticación ya conocida.
* El emulador de almacenamiento no es un servicio de almacenamiento escalable y no es compatible con un gran número de clientes simultáneos.
* Como se describe en [Direccionar los recursos en el emulador de almacenamiento](#addressing-resources-in-the-storage-emulator), los recursos se tratan de forma diferente en el emulador de almacenamiento frente a una cuenta de almacenamiento de Azure. Esta diferencia se debe a que la resolución de nombres de dominio está disponible en la nube, pero no en el equipo local.
* A partir de la versión 3.1, la cuenta del emulador de almacenamiento admite la replicación con redundancia geográfica con acceso de lectura (RA-GRS). En el emulador, todas las cuentas tienen RA-GRS habilitado y no hay ningún retraso entre las réplicas principal y secundaria. Las operaciones Get Blob Service Stats, Get Queue Service Stats y Get Table Service Stats son compatibles con la cuenta secundaria y siempre devolverán el valor del elemento de respuesta `LastSyncTime` como la hora actual según la base de datos SQL subyacente.
* El servicio de archivo y los extremos de servicio de protocolo SMB no se admiten actualmente en el emulador de almacenamiento.
* Si usa una versión de los servicios de almacenamiento que no es compatible aún con el emulador, este devuelve un error VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta).

### <a name="differences-for-blob-storage"></a>Diferencias en el almacenamiento de blobs
Las siguientes diferencias se aplican al almacenamiento de blobs en el emulador:

* El emulador de almacenamiento solo admite tamaños de blobs de hasta 2 GB.
* La copia incremental permite copiar instantáneas desde los blobs sobrescritos, lo que devuelve un error en el servicio.
* Get Page Ranges Diff no funciona entre instantáneas copiadas con el blob de copia incremental.
* Una operación Put Blob puede ser correcta con un blob que existe en el emulador de almacenamiento con una concesión activa, incluso si el identificador de concesión no se ha especificado en la solicitud.
* El emulador no admite operaciones de blob en anexos. Intentando realizar una operación en un blob en anexos, devuelve un error FeatureNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta).

### <a name="differences-for-table-storage"></a>Diferencias en el almacenamiento de tabla
Las siguientes diferencias se aplican al almacenamiento de tablas en el emulador:

* Las propiedades de fecha en Table service del emulador de almacenamiento solo admiten el intervalo admitido por SQL Server 2005 (es decir, tienen que ser posteriores al 1 de enero de 1753). Todas las fechas anteriores al 1 de enero de 1753 se cambiarán a este valor. La precisión de las fechas se limita a la precisión de SQL Server 2005, lo que significa que las fechas son precisas hasta 1/300 de un segundo.
* El emulador de almacenamiento admite valores de propiedad de clave de fila y de clave de partición de menos de 512 bytes. Además, el tamaño total del nombre de la cuenta, el nombre de la tabla y los nombres de las propiedades de clave juntos no pueden superar los 900 bytes.
* El tamaño total de una fila de una tabla en el emulador de almacenamiento se limita a menos de 1 MB.
* En el emulador de almacenamiento, las propiedades de tipo de datos `Edm.Guid` o `Edm.Binary` solo admiten los operadores de comparación `Equal (eq)` y `NotEqual (ne)` en las cadenas de filtro de consultas.

### <a name="differences-for-queue-storage"></a>Diferencias en el almacenamiento de cola
No hay ninguna diferencia específica del almacenamiento en cola en el emulador.

## <a name="storage-emulator-release-notes"></a>Notas de la versión del emulador de almacenamiento
### <a name="version-52"></a>Versión 5.2
* El emulador de almacenamiento admite la versión 2017-04-17 de los servicios de almacenamiento en los puntos de conexión de los servicios de blob, cola y tabla.
* Se ha corregido un error por el que no se codificaban correctamente los valores de propiedad de tabla.

### <a name="version-51"></a>Versión 5.1
* Se ha corregido un error en el que el emulador de almacenamiento devolvía el encabezado `DataServiceVersion` en algunas respuestas en las que el servicio no lo hacía.

### <a name="version-50"></a>Versión 5.0
* El programa de instalación del emulador de almacenamiento ya no comprueba instalaciones existentes de MSSQL y .NET Framework.
* El programa de instalación del emulador de almacenamiento ya no crea la base de datos como parte de la instalación. Se seguirá creando la base de datos si se necesita como parte del proceso de inicio.
* La creación de la base de datos ya no requiere elevación.
* Las reservas de puertos ya no son necesarias para el inicio.
* Agrega las siguientes opciones a `init`: `-reserveports` (requiere elevación), `-unreserveports` (requiere elevación), `-skipcreate`.
* La opción de interfaz de usuario Emulador de almacenamiento en el icono de la bandeja del sistema ahora inicia la interfaz de línea de comandos. La interfaz gráfica de usuario anterior ya no está disponible.
* Algunos archivos DLL se han quitado o se han cambiado de nombre.

### <a name="version-46"></a>Versión 4.6
* El emulador de almacenamiento admite ahora la versión 2016-05-31 de los servicios de almacenamiento en los puntos de conexión de servicio Blob, Cola y Tabla.

### <a name="version-45"></a>Versión 4.5
* Se ha corregido un error que provocó la inicialización e instalación del emulador de almacenamiento cuando se cambia el nombre de la base de datos de copia de seguridad.

### <a name="version-44"></a>Version 4.4
* El emulador de almacenamiento admite ahora la versión 2015-12-11 de los servicios de almacenamiento en los puntos de conexión de Blob Storage, Queue Storage y Table Storage.
* La recolección de elementos no utilizados de datos del blobs del emulador de almacenamiento ahora es más eficaz cuando se trabaja con grandes cantidades de blobs.
* Se ha corregido un error que provocaba que el XML de ACL del contenedor se validara de forma algo diferente de cómo lo hace el servicio de almacenamiento.
* Se ha corregido un error que a veces causaba que los valores máx. y mín. de DateTime se notificarán en una zona horaria incorrecta.

### <a name="version-43"></a>Versión 4.3
* El emulador de almacenamiento admite ahora la versión 2015-07-08 de los servicios de almacenamiento en los puntos de conexión de los servicios Blob, Cola y Tabla.

### <a name="version-42"></a>Versión 4.2
* El emulador de almacenamiento admite ahora la versión 2015-04-05 de los servicios de almacenamiento en los extremos de servicio Blob, Cola y Tabla.

### <a name="version-41"></a>Versión 4.1
* El emulador de almacenamiento admite ahora la versión 2015-02-21 de los servicios de almacenamiento en los puntos de conexión de servicio Blob, Cola y Tabla, con la excepción de las nuevas características de blob en anexos.
* Si usa una versión de los servicios de almacenamiento que no es compatible aún con el emulador de almacenamiento, este devuelve un mensaje de error descriptivo. Se recomienda usar la versión más reciente del emulador. Si se produce un error VersionNotSupportedByEmulator (código de estado HTTP 400 - Solicitud incorrecta), descargue la última versión del emulador de almacenamiento.
* Se ha corregido un error en el que una condición de carrera hacía que los datos de entidad de tabla fuesen incorrectos durante las operaciones de mezcla simultáneas.

### <a name="version-40"></a>Versión 4.0
* Se ha cambiado el nombre de ejecutable del emulador de almacenamiento a *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versión 3.2
* El emulador de almacenamiento admite ahora la versión 2014-02-14 de los servicios de almacenamiento en los extremos de servicio de blob, cola y tabla. Los puntos de conexión de servicio de archivos no se admiten actualmente en el emulador de almacenamiento. Consulte [Versiones de los servicios de almacenamiento de Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) para obtener información acerca de la versión 2014-02-14.

### <a name="version-31"></a>Versión 3.1
* Ahora se admite el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) en el emulador de almacenamiento. Las API de las operaciones Get Blob Service Stats, Get Queue Service Stats y Get Table Service Stats son compatibles con la cuenta secundaria y siempre devolverán el valor del elemento de respuesta LastSyncTime como la hora actual según la base de datos SQL subyacente. Para el acceso mediante programación a la cuenta secundaria con el emulador de almacenamiento, usa la biblioteca de cliente de almacenamiento para la versión 3.2 de .NET o una versión posterior. Consulte la Documentación de referencia de la biblioteca cliente de Almacenamiento de Microsoft Azure para .NET para obtener más información.

### <a name="version-30"></a>Versión 3.0
* El emulador de almacenamiento de Azure ya no se incluye en el mismo paquete que el emulador de proceso.
* La interfaz gráfica de usuario del emulador de almacenamiento se ha sustituido por una interfaz de la línea de comandos que permite ejecutar scripts. Para obtener más información sobre la interfaz de la línea de comandos, vea Referencia de la herramienta de la línea de comandos del emulador de almacenamiento. La interfaz gráfica seguirá estando presente en la versión 3.0, pero solo se puede acceder a ella cuando se instala el emulador de proceso con el botón secundario en el icono de la bandeja del sistema y se selecciona la opción de mostrar IU del emulador de almacenamiento.
* La versión 2013-08-15 de los servicios de almacenamiento de Azure ahora es totalmente compatible. (Anteriormente esta versión solo era compatible con versión la versión 2.2.1 Preview del emulador de almacenamiento.)

## <a name="next-steps"></a>Pasos siguientes

* Evaluación del emulador de almacenamiento de código abierto multiplataforma [Azurite](https://github.com/arafato/azurite), mantenido por la comunidad. 
* [Ejemplos de Azure Storage mediante .NET](../storage-samples-dotnet.md) contiene vínculos a varios ejemplos de código que puede usar al desarrollar su aplicación.
* Puede usar el [Explorador de Microsoft Azure Storage](http://storageexplorer.com) para trabajar con recursos de su cuenta de nube de Storage y del emulador de almacenamiento.
