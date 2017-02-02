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
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6c5bf8907a5f69e45e7b62fb466bdc53460e9029
ms.openlocfilehash: 86a5911e99e7631b09604afcb0f53ed2887b576b


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

## <a name="start-the-local-emulator-data-explorer"></a>Inicio del Explorador de datos del emulador local

Cuando se inicia el emulador local, se abre automáticamente el Explorador de datos de DocumentDB en el explorador. La dirección aparecerá como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si cierra el explorador y desea volver a abrirlo más adelante, puede abrir la dirección URL en el explorador web o iniciarlo desde el icono de la bandeja de Windows del Emulador de DocumentDB, tal y como se muestra a continuación.

![Iniciador del explorador de datos del emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>Desarrollo con el Emulador de DocumentDB
Una vez que el Emulador de DocumentDB se esté ejecutando en el escritorio, puede usar cualquier [SDK de DocumentDB](documentdb-sdk-dotnet.md) compatible o la [API de REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con el Emulador. El Emulador de DocumentDB también incluye un Explorador de datos integrado que le permite crear colecciones y ver y editar documentos sin escribir ningún código. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> Al conectar con el emulador, debe establecer EnableEndpointDiscovery = false en la configuración de conexión.

Si usa la [compatibilidad de protocolo de DocumentDB con MongoDB](documentdb-protocol-mongodb.md), use la siguiente cadena de conexión:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

También puede usar herramientas existentes como [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) para conectar con el Emulador de DocumentDB. Además, puede migrar datos entre el Emulador de DocumentDB y el servicio Azure DocumentDB utilizando la [herramienta de migración de datos de DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>Exportación del certificado SSL del Emulador de DocumentDB

El tiempo de ejecución y los lenguajes de .Net utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de DocumentDB. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Java utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mientras que Python utiliza [contenedores de sockets](https://docs.python.org/2/library/ssl.html).

Si quiere obtener un certificado que se pueda utilizar con los lenguajes y los tiempos de ejecución sin que se integre con el almacén de certificados de Windows, tendrá que realizar la exportación por medio del administrador de certificados de Windows. Para iniciarlo, ejecute certlm.msc o siga las instrucciones paso a paso de la publicación "[Export the DocumentDB Emulator Certificates](./documentdb-nosql-local-emulator-export-ssl-certificates.md)" (Exportación de los certificados del Emulador de DocumentDB). Una vez que el Administrador de certificados se esté ejecutando, abra los certificados personales, tal y como se muestra a continuación, y exporte el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate" como archivo X.509 codificado en BASE-64 (.cer).

![Certificado SSL de un emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

El certificado X.509 puede importarse en el almacén de certificados de Java siguiendo las instrucciones de la publicación "[Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)".  Una vez que el certificado se importa en el almacén de certificados CA, las aplicaciones de Java y MongoDB podrán conectarse al emulador local de DocumentDB.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>Referencia de la herramienta de la línea de comandos del Emulador de DocumentDB
Desde la ubicación de instalación, puede usar la línea de comandos para iniciar y detener el emulador, configurar las opciones y realizar otras operaciones.

### <a name="command-line-syntax"></a>Sintaxis de línea de comandos

    DocumentDB.Emulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

Para ver la lista de opciones, escriba `DocumentDB.Emulator.exe /?` en el símbolo del sistema.

<table>
<tr>
  <td><strong>Opción</strong></td>
  <td><strong>Description</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[Sin argumentos]</td>
  <td>Inicia el Emulador de DocumentDB con la configuración predeterminada</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Apaga el Emulador de DocumentDB</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>Ayuda</td>
  <td>Muestra la lista de argumentos de la línea de comandos</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>Especifica la ruta de acceso en la que se almacenarán los archivos de datos</td>
  <td>DocumentDB.Emulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: una ruta accesible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Especifica el número de puerto que se utilizará para el emulador.  El valor predeterminado es 8081.</td>
  <td>DocumentDB.Emulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica el número de puerto que se utilizará para la API de compatibilidad de MongoDB. El valor predeterminado es 10250.</td>
  <td>DocumentDB.Emulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica los puertos que se usarán para la conectividad directa. Los valores predeterminados son 10251,10252,10253,10254.</td>
  <td>DocumentDB.Emulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: una lista delimitada por comas de 4 puertos</td>
</tr>
<tr>
  <td>Clave</td>
  <td>Clave de autorización para el emulador. Las claves deben ser la codificación en base 64 de un vector de 64 bytes.</td>
  <td>DocumentDB.Emulator.exe /key:&lt;key&gt;</td>
  <td>&lt;key&gt;: la clave debe ser la codificación en base 64 de un vector de 64 bytes</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>Especifica que el comportamiento de limitación de solicitudes está habilitado.</td>
  <td>DocumentDB.Emulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>Especifica que el comportamiento de limitación de solicitudes está deshabilitado.</td>
  <td>DocumentDB.Emulator.exe /disablethrottling</td>
  <td></td>
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

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de DocumentDB, vea [Introduction to Azure DocumentDB](documentdb-introduction.md) (Introducción a Azure DocumentDB).
* Para empezar a desarrollar en el Emulador de DocumentDB, descargue uno de los [SDK de DocumentDB admitidos](documentdb-sdk-dotnet.md).



<!--HONumber=Dec16_HO3-->


