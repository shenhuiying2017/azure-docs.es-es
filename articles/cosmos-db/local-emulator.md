---
title: Desarrollo local con el Emulador de Azure Cosmos DB | Microsoft Docs
description: "Con el Emulador de Azure Cosmos DB, puede desarrollar y probar su aplicación localmente de forma gratuita, sin necesidad de crear una suscripción a Azure."
services: cosmos-db
documentationcenter: 
keywords: Emulador de Azure Cosmos DB
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d2a6af66b6c1e92b8b694685ab8c16781d1ade48
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Uso del Emulador de Azure Cosmos DB para desarrollo y pruebas de forma local

<table>
<tr>
  <td><strong>Archivos binarios</strong></td>
  <td>[Descargar MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Origen de Docker</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
El Emulador de Azure Cosmos DB proporciona un entorno local que emula el servicio de Azure Cosmos DB con fines de desarrollo. Mediante el Emulador de Azure Cosmos DB, puede desarrollar y probar su aplicación localmente, sin crear una suscripción de Azure o realizar algún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador, puede cambiar a una cuenta de Azure Cosmos DB en la nube.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Instalación del Emulador
> * Ejecución del Emulador en Docker para Windows
> * Autenticación de solicitudes
> * Uso del Explorador de datos en el Emulador
> * Exportación de certificados SSL
> * Llamada al Emulador desde la línea de comandos
> * Recopilación de archivos de seguimiento

Antes que nada, se recomienda ver el siguiente vídeo, donde Kirill Gavrylyuk describe las funciones básicas del Emulador de Azure Cosmos DB. Tenga en cuenta que en el vídeo se hace referencia al emulador como el "Emulador de DocumentDB", pero después de que se grabara el vídeo se ha cambiado el nombre de la herramienta a "Emulador de Azure Cosmos DB". Toda la información contenida en el vídeo sigue siendo correcta para el Emulador de Azure Cosmos DB. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Funcionamiento del emulador
El Emulador de Azure Cosmos DB proporciona una emulación de gran fidelidad del servicio Azure Cosmos DB. Admite una funcionalidad idéntica a la de Azure Cosmos DB, incluida la compatibilidad con la creación y la consulta de documentos JSON, el aprovisionamiento y el escalado de colecciones y la ejecución de procedimientos y desencadenadores almacenados. Puede desarrollar y probar aplicaciones mediante el Emulador de Azure Cosmos DB e implementarlas en Azure a escala global realizando simplemente un solo cambio de configuración en el punto de conexión de la conexión de Azure Cosmos DB.

Aunque se crea una emulación local de alta fidelidad del propio servicio Azure Cosmos DB, la implementación del Emulador de Azure Cosmos DB es diferente de la del servicio. Por ejemplo, el Emulador de Azure Cosmos DB usa componentes del sistema operativo estándar, como el sistema de archivos local para la persistencia y la pila del protocolo HTTPS para la conectividad. Esto significa que ciertas funcionalidades que se basan en la infraestructura de Azure, como la replicación global, la latencia de milisegundos de un solo dígito para lecturas/escrituras y los niveles de coherencia ajustable, no están disponibles a través del Emulador de Azure Cosmos DB.

> [!NOTE]
> En este momento, el Explorador de datos del emulador solo admite la creación de colecciones de API de DocumentDB y colecciones de MongoDB. El Explorador de datos del emulador no admite actualmente la creación de tablas y gráficos. 

## <a name="system-requirements"></a>Requisitos del sistema
El Emulador de Azure Cosmos DB presenta los siguientes requisitos de hardware y software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 o Windows 10
*   Requisitos mínimos de hardware
  * 2 GB DE RAM
  * 10 GB de espacio disponible en disco duro

## <a name="installation"></a>Instalación
Puede descargar e instalar el Emulador de Azure Cosmos DB desde el [Centro de descarga de Microsoft](https://aka.ms/cosmosdb-emulator). 

> [!NOTE]
> Para instalar, configurar y ejecutar el Emulador de Azure Cosmos DB, debe tener privilegios administrativos en el equipo.

## <a name="running-on-docker-for-windows"></a>Ejecución en Docker para Windows

El Emulador de Azure Cosmos DB se puede ejecutar en Docker para Windows. El emulador no funciona en Docker para Oracle Linux.

Una vez que tenga [Docker para Windows](https://www.docker.com/docker-windows) instalado, puede extraer la imagen del emulador de Docker Hub ejecutando el siguiente comando desde su shell favorito (cmd.exe, PowerShell, etc.).

```      
docker pull microsoft/azure-cosmosdb-emulator 
```
Para iniciar la imagen, ejecute los siguientes comandos.

``` 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i microsoft/azure-cosmosdb-emulator 
```

La respuesta será similar a la siguiente:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Si cierra el shell interactivo una vez iniciado el emulador, se cerrará el contenedor de este.

Use el punto de conexión y la clave maestra de la respuesta en el cliente e importe el certificado SSL en el host. Para importar el certificado SSL, haga lo siguiente desde un símbolo del sistema de administración:

```
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```


## <a name="start-the-emulator"></a>Iniciar el emulador

Para iniciar el Emulador de Azure Cosmos DB, seleccione el botón Inicio o presione la tecla Windows. Comience a escribir **Emulador de Azure Cosmos DB** y seleccione el emulador de la lista de aplicaciones. 

![Seleccione el botón Inicio o presione la tecla Windows, comience a escribir **Emulador de Azure Cosmos DB** y seleccione el emulador en la lista de aplicaciones.](./media/local-emulator/database-local-emulator-start.png)

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows. ![Notificación en la barra de tareas del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

El Emulador de Azure Cosmos DB se ejecuta de forma predeterminada en la máquina local ("localhost") que escucha en el puerto 8081.

El Emulador de Azure Cosmos DB se instala de forma predeterminada en el directorio `C:\Program Files\Azure Cosmos DB Emulator`. También puede iniciar y detener el emulador desde la línea de comandos. Vea la [referencia de la herramienta de la línea de comandos](#command-line) para obtener más información.

## <a name="start-data-explorer"></a>Inicio del Explorador de datos

Cuando se inicia el Emulador de Azure Cosmos DB, se abre automáticamente el Explorador de datos de Azure Cosmos DB en su explorador. La dirección aparecerá como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si cierra el explorador y desea volver a abrirlo más adelante, puede abrir la dirección URL en el explorador o iniciarla desde el icono de la bandeja de Windows del Emulador de Azure Cosmos DB, tal y como se muestra a continuación.

![Iniciador del Explorador de datos del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Búsqueda de actualizaciones
En el Explorador de datos se indica si hay una nueva actualización disponible para descarga. 

> [!NOTE]
> No se garantiza que los datos que se crean en una versión del Emulador de Azure Cosmos DB estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de Azure Cosmos DB y no en el Emulador de Azure Cosmos DB. 

## <a name="authenticating-requests"></a>Autenticación de solicitudes
Al igual que con los documentos de Azure en la nube, se deben autenticar todas las solicitudes que se realicen en el Emulador de Azure Cosmos DB. El Emulador de Azure Cosmos DB es compatible con una sola cuenta fija y una clave de autenticación ya conocida para la autenticación de clave maestra. Esta cuenta y clave son las únicas credenciales que se admiten para su uso con el Emulador de Azure Cosmos DB. Son las siguientes:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La clave maestra admitida por el Emulador de Azure Cosmos DB está destinada a su uso exclusivo con el emulador. No puede usar su clave y cuenta de producción de Azure Cosmos DB con el Emulador de Azure Cosmos DB. 

Además, lo mismo que el servicio Azure Cosmos DB, el Emulador de Azure Cosmos DB solo admite la comunicación segura a través de SSL.

## <a name="developing-with-the-emulator"></a>Desarrollo con el emulador
Cuando tenga el Emulador de Azure Cosmos DB funcionando en su escritorio, puede usar cualquier [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) admitido o la [API de REST de Azure Cosmos DB](/rest/api/documentdb/) para interactuar con el Emulador. El Emulador de Azure Cosmos DB también incluye un Explorador de datos integrado que le permite crear colecciones para las API de DocumentDB y MongoDB, y ver y editar documentos sin escribir ningún código.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Si va a utilizar la [compatibilidad del protocolo de Azure Cosmos DB con MongoDB](mongodb-introduction.md), use la siguiente cadena de conexión:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

También puede usar herramientas existentes como [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) para conectar con el Emulador de Azure Cosmos DB. También puede migrar datos entre el Emulador de Azure Cosmos DB y el servicio Azure Cosmos DB con la [Herramienta de migración de datos de Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Con el Emulador de Azure Cosmos DB, puede crear de forma predeterminada hasta 25 colecciones de una única partición o una colección con particiones. Para más información acerca de cómo cambiar este valor, consulte la sección sobre la [configuración del valor de PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportación del certificado SSL

El sistema en tiempo de ejecución y los lenguajes .NET utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de Azure Cosmos DB. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Java utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mientras que Python utiliza [contenedores de sockets](https://docs.python.org/2/library/ssl.html).

Si quiere obtener un certificado que se pueda utilizar con los lenguajes y los tiempos de ejecución sin que se integre con el almacén de certificados de Windows, tendrá que realizar la exportación por medio del administrador de certificados de Windows. Para iniciarlo, ejecute certlm.msc o siga las instrucciones paso a paso de [Exportación de los certificados del Emulador de Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Una vez que el Administrador de certificados se esté ejecutando, abra los certificados personales, tal y como se muestra a continuación, y exporte el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate" como archivo X.509 codificado en BASE-64 (.cer).

![Certificado SSL del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

El certificado X.509 puede importarse en el almacén de certificados de Java siguiendo las instrucciones de [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Cuando el certificado se haya importado en el almacén de certificados, las aplicaciones de Java y MongoDB podrán conectarse al Emulador de Azure Cosmos DB.

Al conectarse al emulador desde los SDK de Node.js y Python, se deshabilita la verificación de SSL.

## <a id="command-line"></a>Referencia de la herramienta de la línea de comandos
Desde la ubicación de instalación, puede usar la línea de comandos para iniciar y detener el emulador, configurar las opciones y realizar otras operaciones.

### <a name="command-line-syntax"></a>Sintaxis de línea de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para ver la lista de opciones, escriba `CosmosDB.Emulator.exe /?` en el símbolo del sistema.

<table>
<tr>
  <td><strong>Opción</strong></td>
  <td><strong>Descripción</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[Sin argumentos]</td>
  <td>Inicia el Emulador de Azure Cosmos DB con la configuración predeterminada.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ayuda]</td>
  <td>Muestra la lista de argumentos de la línea de comandos admitidos.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Cierra el Emulador de Azure Cosmos DB.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica la ruta de acceso en la que se almacenarán los archivos de datos. El valor predeterminado es %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: una ruta accesible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Especifica el número de puerto que se utilizará para el emulador.  El valor predeterminado es 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica el número de puerto que se utilizará para la API de compatibilidad de MongoDB. El valor predeterminado es 10250.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica los puertos que se usarán para la conectividad directa. Los valores predeterminados son 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: una lista delimitada por comas de 4 puertos</td>
</tr>
<tr>
  <td>Clave</td>
  <td>Clave de autorización para el emulador. La clave debe ser la codificación en base 64 de un vector de 64 bytes.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: la clave debe ser la codificación en base 64 de un vector de 64 bytes</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está habilitado.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está deshabilitado.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>No muestra la interfaz de usuario del emulador.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>No muestra el Explorador de documentos en el inicio.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica el número máximo de las colecciones particionadas. Consulte [Cambio del número de colecciones](#set-partitioncount) para más información.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: número máximo de colecciones de una sola partición permitidas. El valor predeterminado es 25. El máximo permitido es 250.</td>
</tr>
</table>

## <a name="differences-between-the-azure-cosmos-db-emulator-and-azure-cosmos-db"></a>Diferencias entre el Emulador de Azure Cosmos DB y Azure Cosmos DB 
Dado que el Emulador de Azure Cosmos DB proporciona un entorno emulado que se ejecuta en una estación de trabajo de desarrollador local, hay algunas diferencias de funcionalidad entre el emulador y una cuenta de Azure Cosmos DB en la nube:

* El Emulador de Azure Cosmos DB es compatible con una sola cuenta fija y una clave maestra ya conocida.  La regeneración de claves no es posible en el Emulador de Azure Cosmos DB.
* El Emulador de Azure Cosmos DB no es un servicio escalable y no será compatible con un gran número de colecciones.
* El Emulador de Azure Cosmos DB no simula diferentes [niveles de coherencia de Azure Cosmos DB](consistency-levels.md).
* El Emulador de Azure Cosmos DB no simula [la replicación en varias regiones](distribute-data-globally.md).
* El Emulador de Azure Cosmos DB no es compatible con las invalidaciones de cuotas de servicio que están disponibles en el servicio Azure Cosmos DB (por ejemplo, los límites de tamaño del documento, el mayor almacenamiento de colección con particiones).
* Como la copia del Emulador de Azure Cosmos DB puede no contener los cambios más recientes del servicio Azure Cosmos DB, inicie el [planeador de capacidad de Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para calcular con precisión las necesidades de rendimiento de producción (RU) de la aplicación.

## <a id="set-partitioncount"></a>Cambio del número de colecciones

Con el Emulador de Azure Cosmos DB, puede crear de forma predeterminada hasta 25 colecciones de partición única o una colección con particiones. Al modificar el valor de **PartitionCount**, puede crear hasta 250 colecciones de partición única o 10 colecciones con particiones o cualquier combinación de las dos que no superen las 250 particiones de partición única (donde 1 colección con particiones = 25 colecciones de partición única).

Si intenta crear una colección después de que se haya excedido el recuento de particiones actual, el emulador generará una excepción ServiceUnavailable, con el siguiente mensaje.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para cambiar el número de colecciones disponibles en el Emulador de Azure Cosmos DB, haga lo siguiente:

1. Elimine todos los datos del emulador local de Azure Cosmos DB; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y, luego, haga clic en **Reset Data…** (Restablecer datos...).
2. Elimine todos los datos del emulador en la carpeta C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Cierre todas las instancias abiertas; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y, luego, haga clic en **Salir**. Todas las instancias pueden tardar un minuto en salir.
4. Instale la versión más reciente del [Emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Para iniciar el emulador con la marca PartitionCount, establezca un valor <= 250. Por ejemplo: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="troubleshooting"></a>Solución de problemas

Use las siguientes sugerencias para solucionar los problemas que puedan surgir con el Emulador de Azure Cosmos DB:

- Si el Emulador de Azure Cosmos DB se bloquea, recopile los archivos de volcado de memoria de la carpeta c:\Users\user_name\AppData\Local\CrashDumps, comprímalos y adjúntelos a un correo electrónico para [askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Si experimenta bloqueos en CosmosDB.StartupEntryPoint.exe, ejecute el siguiente comando desde un símbolo del sistema de administración: `lodctr /R` 

- Si se produce un problema de conectividad, [recopile los archivos de seguimiento](#trace-files), comprímalos y adjúntelos a un correo electrónico y envíelo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Si recibe un mensaje de **Servicio no disponible**, es posible que el emulador no pueda inicializar la pila de red. Compruebe si tiene instalado el cliente de Pulse Secure o el cliente de Juniper Networks, ya que sus controladores de filtro de red podrían ser la causa del problema. Normalmente el problema se corrige si se desinstalan los controladores de filtro de red de otros fabricantes.

### <a id="trace-files"></a>Recopilación de archivos de seguimiento

Para recopilar los seguimientos de depuración, ejecute los siguientes comandos desde un símbolo del sistema administrativo:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Vea la bandeja del sistema para asegurarse de que el programa se ha cerrado; esto podría tardar un minuto. Puede también hacer clic en **Salir** en la interfaz de usuario del Emulador de Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduzca el problema. Si el Explorador de datos no funciona, solo necesitará esperar a que el explorador se abra durante unos segundos para detectar el error.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Vaya a `%ProgramFiles%\Azure Cosmos DB Emulator` y localice el archivo docdbemulator_000001.etl.
7. Envíe el archivo .etl junto con los pasos para reproducirlo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) para la depuración.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha instalado el emulador local
> * Ha ejecutado el Emulador en Docker para Windows
> * Ha autenticado solicitudes
> * Ha usado el Explorador de datos en el Emulador
> * Ha exportado certificados SSL
> * Ha llamado al Emulador desde la línea de comandos
> * Ha recopilado archivos de seguimiento

En este tutorial, ha aprendido a usar el emulador local para el desarrollo local gratuito. Ahora puede pasar al siguiente tutorial y aprender a exportar certificados SSL del Emulador. 

> [!div class="nextstepaction"]
> [Exportación de los certificados del Emulador de Azure Cosmos DB](local-emulator-export-ssl-certificates.md)

