---
title: Desarrollo local con el Emulador de DocumentDB | Documentos de Microsoft
description: "Con el Emulador de Azure DocumentDB, puede desarrollar y probar su aplicación localmente de forma gratuita, sin necesidad de crear una suscripción a Azure."
services: documentdb
documentationcenter: 
keywords: Emulador de DocumentDB
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 5f9783232e9b03ca3777a000ffc189863d0956ab
ms.openlocfilehash: ffc481943a9dc55593fa8b46dffef0098f288eaf


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Uso del Emulador de Azure DocumentDB para desarrollo y pruebas

[**Descarga del Emulador**](https://aka.ms/documentdb-emulator)

El Emulador de Azure DocumentDB proporciona un entorno local que emula el servicio de Azure DocumentDB con fines de desarrollo. Mediante el Emulador de DocumentDB, puede desarrollar y probar su aplicación localmente sin necesidad de crear una suscripción a Azure ni incurrir en gastos. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador de DocumentDB, puede cambiar al uso de una cuenta de Azure DocumentDB en la nube.

Se recomienda que antes de nada vea el siguiente vídeo, donde Kirill Gavrylyuk describe las funciones básicas del Emulador de DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>Requisitos de sistema del Emulador de DocumentDB
El Emulador de DocumentDB presenta los siguientes requisitos de hardware y software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 o Windows 10
*   Requisitos mínimos de hardware
  * 2 GB DE RAM
  * 10 GB de espacio disponible en disco duro

## <a name="installing-the-documentdb-emulator"></a>Instalación del Emulador de DocumentDB
Puede descargar e instalar el Emulador de DocumentDB desde el [Centro de descarga de Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Para instalar, configurar y ejecutar el Emulador de DocumentDB, debe tener privilegios administrativos en el equipo.

## <a name="checking-for-documentdb-emulator-updates"></a>Comprobación de la existencia de actualizaciones del Emulador de DocumentDB
El Emulador de DocumentDB incluye un explorador de datos de Azure DocumentDB integrado para examinar los datos almacenados en DocumentDB y crear nuevas recopilaciones; además, le permite saber si hay una nueva actualización disponible para su descarga. 

> [!NOTE]
> No se garantiza que los datos que se crean en una versión del Emulador de DocumentDB estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de Azure DocumentDB y no en el Emulador de DocumentDB. 

## <a name="how-the-documentdb-emulator-works"></a>Funcionamiento del Emulador de DocumentDB
El Emulador de DocumentDB proporciona una emulación de alta fidelidad del servicio DocumentDB. Admite una funcionalidad idéntica a la de Azure DocumentDB, incluida la compatibilidad para crear y consultar documentos JSON, aprovisionar y escalar colecciones y ejecutar procedimientos y desencadenadores almacenados. Puede desarrollar y probar aplicaciones mediante el Emulador de DocumentDB e implementarlas en Azure a escala global realizando simplemente un solo cambio de configuración en el punto de conexión de la conexión de DocumentDB.

Aunque se crea una emulación local de alta fidelidad del propio servicio DocumentDB, la implementación del Emulador de DocumentDB es diferente de la del servicio. Por ejemplo, el Emulador de DocumentDB utiliza componentes del sistema operativo estándar, como el sistema de archivos local para la persistencia y la pila del protocolo HTTPS para la conectividad. Esto significa que ciertas funcionalidades que se basan en la infraestructura de Azure, como la replicación global, la latencia de milisegundos de un solo dígito para lecturas/escrituras y los niveles de coherencia ajustable, no están disponibles a través del Emulador de DocumentDB.


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>Autenticación de solicitudes en el Emulador de DocumentDB
De la misma manera que con el documento de Azure en la nube, se deben autenticar todas las solicitudes que se realicen en el Emulador de DocumentDB. El Emulador de DocumentDB es compatible con una sola cuenta fija y una clave de autenticación ya conocida para la autenticación de clave maestra. Esta cuenta y clave son las únicas credenciales que se admiten para su uso con el Emulador de DocumentDB. Son las siguientes:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La clave maestra admitida por el Emulador de DocumentDB está destinada a su uso exclusivo con el emulador. No puede usar la cuenta y la clave de DocumentDB con el Emulador de DocumentDB. 

Además, al igual que el servicio Azure DocumentDB, el emulador de DocumentDB admite solo una comunicación segura mediante SSL.

## <a name="start-and-initialize-the-documentdb-emulator"></a>Inicio del Emulador de DocumentDB

Para iniciar el Emulador de Azure DocumentDB, seleccione el botón Inicio o pulse la tecla Windows. Comience a escribir **Emulador de DocumentDB** y seleccione el emulador en la lista de aplicaciones. 

![Seleccione el botón Inicio o presione la tecla Windows, comience a escribir **Emulador de DocumentDB** y seleccione el emulador en la lista de aplicaciones.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows. El Emulador de DocumentDB se ejecuta de forma predeterminada en el equipo local ("localhost") que escucha en el puerto 8081.

![Notificación en la barra de tareas del emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

El Emulador de DocumentDB se instala de forma predeterminada en el directorio `C:\Program Files\DocumentDB Emulator`. También puede iniciar y detener el emulador desde la línea de comandos. Vea la [referencia de la herramienta de la línea de comandos](#command-line) para obtener más información.

## <a name="start-the-documentdb-emulator-data-explorer"></a>Inicio del Explorador de datos del emulador de DocumentDB

Cuando se inicia el emulador de DocumentDB, se abre automáticamente el Explorador de datos de DocumentDB en el explorador. La dirección aparecerá como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si cierra el explorador y desea volver a abrirlo más adelante, puede abrir la dirección URL en el explorador web o iniciarlo desde el icono de la bandeja de Windows del Emulador de DocumentDB, tal y como se muestra a continuación.

![Iniciador del explorador de datos del emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>Desarrollo con el Emulador de DocumentDB
Una vez que el Emulador de DocumentDB se esté ejecutando en el escritorio, puede usar cualquier [SDK de DocumentDB](documentdb-sdk-dotnet.md) compatible o la [API de REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con el Emulador. El Emulador de DocumentDB también incluye un Explorador de datos integrado que le permite crear colecciones y ver y editar documentos sin escribir ningún código. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Si usa la [compatibilidad de protocolo de DocumentDB con MongoDB](documentdb-protocol-mongodb.md), use la siguiente cadena de conexión:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

También puede usar herramientas existentes como [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) para conectar con el Emulador de DocumentDB. Además, puede migrar datos entre el Emulador de DocumentDB y el servicio Azure DocumentDB utilizando la [herramienta de migración de datos de DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

Con el emulador de DocumentDB, puede crear de forma predeterminada hasta 25 colecciones de partición única o una colección con particiones. Para más información acerca de cómo cambiar este valor, consulte la sección sobre la [configuración del valor de PartitionCount](#set-partitioncount).

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>Exportación del certificado SSL del Emulador de DocumentDB

El sistema en tiempo de ejecución y los lenguajes de .NET utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de DocumentDB. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Java utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mientras que Python utiliza [contenedores de sockets](https://docs.python.org/2/library/ssl.html).

Si quiere obtener un certificado que se pueda utilizar con los lenguajes y los tiempos de ejecución sin que se integre con el almacén de certificados de Windows, tendrá que realizar la exportación por medio del administrador de certificados de Windows. Para iniciarlo, ejecute certlm.msc o siga las instrucciones paso a paso de [Exportación de certificados del Emulador de DocumentDB](./documentdb-nosql-local-emulator-export-ssl-certificates.md). Una vez que el Administrador de certificados se esté ejecutando, abra los certificados personales, tal y como se muestra a continuación, y exporte el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate" como archivo X.509 codificado en BASE-64 (.cer).

![Certificado SSL de un emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

El certificado X.509 puede importarse en el almacén de certificados de Java siguiendo las instrucciones de [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store). Una vez que el certificado se importa en el almacén de certificados CA, las aplicaciones de Java y MongoDB podrán conectarse al emulador de DocumentDB.

Al conectarse al emulador desde los SDK de Node.js y Python, se deshabilita la verificación de SSL.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>Referencia de la herramienta de la línea de comandos del Emulador de DocumentDB
Desde la ubicación de instalación, puede usar la línea de comandos para iniciar y detener el emulador, configurar las opciones y realizar otras operaciones.

### <a name="command-line-syntax"></a>Sintaxis de línea de comandos

    DocumentDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para ver la lista de opciones, escriba `DocumentDB.Emulator.exe /?` en el símbolo del sistema.

<table>
<tr>
  <td><strong>Opción</strong></td>
  <td><strong>Descripción</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[Sin argumentos]</td>
  <td>Inicia el Emulador de DocumentDB con la configuración predeterminada.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ayuda]</td>
  <td>Muestra la lista de argumentos de la línea de comandos admitidos.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Apaga el Emulador de DocumentDB.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica la ruta de acceso en la que se almacenarán los archivos de datos. El valor predeterminado es %LocalAppdata%\DocumentDBEmulator.</td>
  <td>DocumentDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: una ruta accesible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Especifica el número de puerto que se utilizará para el emulador.  El valor predeterminado es 8081.</td>
  <td>DocumentDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica el número de puerto que se utilizará para la API de compatibilidad de MongoDB. El valor predeterminado es 10250.</td>
  <td>DocumentDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica los puertos que se usarán para la conectividad directa. Los valores predeterminados son 10251,10252,10253,10254.</td>
  <td>DocumentDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: una lista delimitada por comas de 4 puertos</td>
</tr>
<tr>
  <td>Clave</td>
  <td>Clave de autorización para el emulador. La clave debe ser la codificación en base 64 de un vector de 64 bytes.</td>
  <td>DocumentDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: la clave debe ser la codificación en base 64 de un vector de 64 bytes</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está habilitado.</td>
  <td>DocumentDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está deshabilitado.</td>
  <td>DocumentDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>No muestra la interfaz de usuario del emulador.</td>
  <td>DocumentDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>No muestra el Explorador de documentos en el inicio.</td>
  <td>DocumentDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica el número máximo de las colecciones particionadas. Consulte [Cambio del número de colecciones](#set-partitioncount) para más información.</td>
  <td>DocumentDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: número máximo de colecciones de una sola partición permitidas. El valor predeterminado es 25. El máximo permitido es 250.</td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>Diferencias entre el Emulador de DocumentDB y Azure DocumentDB 
Dado que el Emulador de DocumentDB proporciona un entorno emulado que se ejecuta en una estación de trabajo de desarrollador local, hay algunas diferencias de funcionalidad entre el emulador y una cuenta de Azure DocumentDB en la nube:

* El Emulador de DocumentDB es compatible con una sola cuenta fija y una clave maestra ya conocida.  La regeneración de claves no es posible en el Emulador de DocumentDB.
* El Emulador de DocumentDB no es un servicio escalable y no será compatible con un gran número de colecciones.
* El Emulador de DocumentDB no simula diferentes [niveles de coherencia de DocumentDB](documentdb-consistency-levels.md).
* El Emulador de DocumentDB no simula [la replicación en varias regiones](documentdb-distribute-data-globally.md).
* El Emulador de DocumentDB no es compatible con las invalidaciones de la cuota de servicio que están disponibles en el servicio Azure DocumentDB (por ejemplo, los límites de tamaño del documento, el mayor almacenamiento de colección particionada).
* Como la copia del Emulador de DocumentDB puede no contener los cambios más recientes del servicio Azure DocumentDB, inicie el [planeador de capacidad de DocumentDB](https://www.documentdb.com/capacityplanner) para calcular con precisión las necesidades de rendimiento de producción (RU) de la aplicación.

## <a name="a-idset-partitioncountachange-the-number-of-collections"></a><a id="set-partitioncount"></a>Cambio del número de colecciones

Con el emulador de DocumentDB, puede crear de forma predeterminada hasta 25 colecciones de partición única o una colección con particiones. Al modificar el valor de **PartitionCount**, puede crear hasta 250 colecciones de partición única o 10 colecciones con particiones o cualquier combinación de las dos que no superen las 250 particiones de partición única (donde 1 colección con particiones = 25 colecciones de partición única).

Si intenta crear una colección después de que se haya excedido el recuento de particiones actual, el emulador generará una excepción ServiceUnavailable, con el siguiente mensaje.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para cambiar el número de colecciones disponibles en el emulador de DocumentDB, haga lo siguiente:

1. Elimine todos los datos del emulador de DocumentDB local haciendo clic con el botón derecho en el icono del **Emulador de DocumentDB** de la bandeja del sistema y, luego, haciendo clic en **Reset Data…** (Restablecer datos...).
2. Elimine todos los datos del emulador en la carpeta C:\Users\user_name\AppData\Local\DocumentDBEmulator.
3. Salga de todas las instancias abiertas haciendo clic con el botón derecho en el icono del **Emulador de DocumentDB** de la bandeja del sistema y, luego, haciendo clic en **Salir**. Todas las instancias pueden tardar un minuto en salir.
4. Instale la versión más reciente del [Emulador de DocumentDB](https://aka.ms/documentdb-emulator).
5. Para iniciar el emulador con la marca PartitionCount, establezca un valor <= 250. Por ejemplo: `C:\Program Files\DocumentDB Emulator>DocumentDB.Emulator.exe /PartitionCount=100`.

## <a name="troubleshooting"></a>Solución de problemas

Use las siguientes sugerencias para solucionar los problemas que puedan surgir con el Emulador de DocumentDB:

- Si el Emulador de DocumentDB se bloquea, recopile los archivos de volcado de memoria de la carpeta c:\Users\user_name\AppData\Local\CrashDumps, comprímalos, adjúntelos a un correo electrónico y envíelo a [ askdocdb@microsoft.com ](mailto:askdocdb@microsoft.com).

- Si se produce un problema de conectividad, [recopile los archivos de seguimiento](#trace-files), comprímalos y adjúntelos a un correo electrónico y envíelo a [ askdocdb@microsoft.com ](mailto:askdocdb@microsoft.com).

### <a name="a-idtrace-filesacollect-trace-files"></a><a id="trace-files"></a>Recopilación de archivos de seguimiento

Para recopilar los seguimientos de depuración, ejecute los siguientes comandos desde un símbolo del sistema administrativo:

1. `cd /d "%ProgramFiles%\DocumentDB Emulator"`
2. `DocumentDB.Emulator.exe /shutdown`. Vea la bandeja del sistema para asegurarse de que el programa se ha cerrado; esto podría tardar un minuto. Puede también hacer clic en **Salir** en la interfaz de usuario del Emulador de DocumentDB.
3. `DocumentDB.Emulator.exe /starttraces`
4. `DocumentDB.Emulator.exe`
5. Reproduzca el problema. Si el Explorador de datos no funciona, solo necesitará esperar a que el explorador se abra durante unos segundos para detectar el error.
5. `DocumentDB.Emulator.exe /stoptraces`
6. Vaya a `%ProgramFiles%\DocumentDB Emulator` y localice el archivo docdbemulator_000001.etl.
7. Envíe el archivo .etl junto con los pasos para reproducirlo a [ askdocdb@microsoft.com ](mailto:askdocdb@microsoft.com) para la depuración.


## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de DocumentDB, vea [Introduction to Azure DocumentDB](documentdb-introduction.md) (Introducción a Azure DocumentDB).
* Para empezar a desarrollar en el Emulador de DocumentDB, descargue uno de los [SDK de DocumentDB admitidos](documentdb-sdk-dotnet.md).



<!--HONumber=Feb17_HO1-->


