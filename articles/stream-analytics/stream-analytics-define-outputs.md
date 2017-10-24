---
title: "Salidas de Stream Analytics: opciones para almacenamiento y análisis | Microsoft Docs"
description: "Aprenda a seleccionar el destino de las opciones de salida de datos de Stream Analytics, lo que incluye Power BI para los resultados de análisis."
keywords: "transformación de datos, resultados del análisis, opciones de almacenamiento de datos"
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 33d0b9aa37cc92dda27f1cf21f1d393b42b8c09b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Salidas de Stream Analytics: opciones para almacenamiento y análisis
Al crear un trabajo de Stream Analytics, tenga en cuenta cómo se consumirán los datos resultantes. ¿Cómo se van a ver los resultados del trabajo de Stream Analytics y dónde se almacenarán?

Para habilitar diversos patrones de aplicación, Análisis de transmisiones de Azure ofrece distintas opciones para el almacenamiento y la visualización de los resultados del trabajo. Esto facilita la visualización de la salida del trabajo y proporciona flexibilidad en el consumo y almacenamiento de la salida del trabajo para el almacenamiento de datos y otros fines. Todos los elementos de salida que se configuren en el trabajo deben existir antes de que se inicie el trabajo y los eventos empiecen a fluir. Por ejemplo, si usa el almacenamiento de blobs como salida, el trabajo no creará una cuenta de almacenamiento automáticamente. Debe crearla el usuario antes de que se inicie el trabajo ASA.

## <a name="azure-data-lake-store"></a>Almacén de Azure Data Lake
Análisis de transmisiones admite el [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). Esta solución de almacenamiento permite almacenar datos de cualquier tamaño, tipo y velocidad de ingesta para realizar análisis exploratorios y operativos. Además, Análisis de transmisiones debe tener autorización para acceder al Almacén de Data Lake. En el [artículo sobre la salida de Data Lake](stream-analytics-data-lake-output.md)se especifica información sobre la autorización y cómo registrarse en Data Lake Store (en caso necesario).

### <a name="authorize-an-azure-data-lake-store"></a>Creación de una instancia de Azure Data Lake Store
Al seleccionar Data Lake Storage como salida en Azure Portal, se le pedirá que autorice una conexión a una instancia existente de Data Lake Store.  

![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

A continuación, rellene las propiedades para la salida de Data Lake Store tal como se muestra a continuación:

![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

En la siguiente tabla podrá encontrar una lista de los nombres de propiedades y su descripción necesarios para crear una salida del Almacén de Data Lake.

<table>
<tbody>
<tr>
<td><B>NOMBRE DE LA PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo utilizado en las consultas para dirigir la salida de la consulta a este Almacén de Data Lake.</td>
</tr>
<tr>
<td>Nombre de cuenta</td>
<td>El nombre de la cuenta de Data Lake Storage donde va a enviar la salida. Se le mostrará una lista desplegable de cuentas del Almacén de Data Lake a las que el usuario cuya sesión se ha iniciado en el portal tiene acceso.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso</td>
<td>La nomenclatura de los archivos seguirá la convención siguiente: <BR>{Patrón del prefijo de la ruta de acceso}/schemaHashcode_Guid_Number.extension <BR> <BR>Archivos de salida de ejemplo:<BR>Myoutput/20170901/00/45434_gguid_1.csv <BR>Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR>Además, estas son las situaciones en las que se crea un archivo nuevo:<BR>1. Cambio en el esquema de salida <BR>2. Reinicio externo o interno de un trabajo<BR><BR>Además, si el patrón de la ruta de acceso al archivo no contiene un carácter "/" final, el patrón final de la ruta de acceso al archivo se considerará como un prefijo del nombre de archivo.<BR><BR>Ejemplo:<BR>Para el patrón de la ruta de acceso: folder1/logs/HH, el archivo generado puede verse así: folder1/logs/02_134343_gguid_1.csv</td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. Esta matriz se cerrará cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovación de la autorización del Almacén de Data Lake
Tendrá que volver a autenticar la cuenta de Data Lake Store si su contraseña ha cambiado desde que se creó o autenticó por última vez su trabajo.

![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Base de datos SQL
[Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) como salida de datos que son relacionales por naturaleza o de aplicaciones que dependen del contenido hospedado en una base de datos relacional. Los trabajos de Análisis de transmisiones se escribirán en una tabla existente en una Base de datos SQL de Azure.  Tenga en cuenta que el esquema de tabla debe coincidir exactamente con los campos y los tipos de salida del trabajo. También es posible especificar [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como la salida mediante la opción de salida de SQL Database (se trata de una característica de versión preliminar). En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de Base de datos SQL.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta base de datos. |
| Base de datos |El nombre de la base de datos a donde está enviando la salida |
| Nombre del servidor |El nombre del servidor Base de datos SQL de Azure |
| Nombre de usuario |El nombre del usuario con permiso para escribir en la base de datos |
| Password |La contraseña para conectarse a la base de datos |
| Tabla |El nombre de la tabla donde se escribirá la salida El nombre de tabla distingue mayúsculas de minúsculas y el esquema de esta tabla debe coincidir exactamente con el número de campos y tipos que va a generar la salida del trabajo. |

> [!NOTE]
> Actualmente, la oferta de Base de datos SQL de Azure se admite para una salida de trabajo de Análisis de transmisiones. Sin embargo, no se admite una máquina virtual de Azure que ejecute SQL Server con una base de datos asociada. Esto está sujeto a cambios en versiones futuras.
> 
> 

## <a name="blob-storage"></a>Almacenamiento de blobs
Almacenamiento de blobs ofrece una solución rentable y escalable para almacenar grandes cantidades de datos no estructurados en la nube.  Para obtener una introducción sobre el almacenamiento de blobs de Azure y su uso, consulte la documentación en [Uso de blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de blob.

<table>
<tbody>
<tr>
<td>Nombre de propiedad</td>
<td>Description</td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de blobs.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento a donde está enviando la salida</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso [opcional]</td>
<td>El patrón de la ruta de acceso al archivo para escribir los blobs dentro del contenedor especificado. <BR> En el patrón de la ruta de acceso, puede elegir usar una o más instancias de las dos variables siguientes para especificar la frecuencia con la que se escriben los blobs: <BR> {date}, {time} <BR> Ejemplo 1: cluster1/logs/{date}/{time} <BR> Ejemplo 2: cluster1/logs/{date} <BR> <BR> La nomenclatura de los archivos seguirá la convención siguiente: <BR> {Patrón del prefijo de la ruta de acceso}/schemaHashcode_Guid_Number.extension <BR> <BR> Archivos de salida de ejemplo: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Además, estas son las situaciones en las que se crea un archivo nuevo: <BR> 1. El archivo actual excede el número de bloques máximo permitido (actualmente, 50 000) <BR> 2. Cambio en el esquema de salida <BR> 3. Reinicio externo o interno de un trabajo  </td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. Esta matriz se cerrará cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Centro de eventos
[Centros de eventos](https://azure.microsoft.com/services/event-hubs/) es un servicio de introducción de eventos de publicación-suscripción altamente escalable. Puede recopilar millones de eventos por segundo.  Un uso del Centro de eventos como salida es cuando la salida de un trabajo de Análisis de transmisiones es la entrada de otro trabajo de streaming.

Hay unos cuantos parámetros que son necesarios para configurar los flujos de datos del Centro de eventos como salida.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este Centro de eventos. |
| Espacio de nombres de Bus de servicio |Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo Centro de eventos, también se crea un espacio de nombres del Bus de servicio. |
| Centro de eventos |El nombre de la salida del Centro de eventos |
| Nombre de la directiva del centro de eventos |La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de centro de eventos |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Columna Clave de partición [opcional] |Esta columna contiene la clave de partición para la salida del Centro de eventos. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
| Codificación |Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador |Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato |Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. Esta matriz se cerrará cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) como salida para un trabajo de Análisis de transmisiones a fin de ofrecer una amplia experiencia de visualización de los resultados del análisis. Esta capacidad puede usarse con paneles operativos, generación de informes e informes basados en métricas.

### <a name="authorize-a-power-bi-account"></a>Autorización de una cuenta de Power BI
1. Cuando se selecciona Power BI como salida en Azure Portal, se le pedirá que autorice a un usuario de Power BI o que cree una nueva cuenta de Power BI.  
   
   ![Autorización de un usuario de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Cree una cuenta nueva si no aún no tiene una y después haga clic en Autorizar ahora.  Aparecerá una pantalla similar a la siguiente.  
   
   ![Cuenta de Power BI de Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. En este paso, proporcione la cuenta profesional o educativa para la autorización de la salida de Power BI. Si aún no se ha registrado en Power BI, elija Registrarse ahora. La cuenta profesional o educativa que use para Power BI podría ser diferente de la cuenta de suscripción de Azure en la que ha iniciado sesión.

### <a name="configure-the-power-bi-output-properties"></a>Configuración de las propiedades de salida de Power BI
Cuando la cuenta de Power BI esté autenticada, puede configurar las propiedades de la salida de Power BI. La tabla siguiente muestra la lista de nombres de propiedad y su descripción para configurar la salida de Power BI.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta salida de Power BI. |
| Área de trabajo de grupo |Para habilitar el uso compartido de datos con otros usuarios de Power BI, puede seleccionar grupos dentro de su cuenta de Power BI o elegir "Mi área de trabajo" si no quiere escribir en un grupo.  Actualizar un grupo existente requiere renovar la autenticación de Power BI. |
| Nombre del conjunto de datos |Proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. |
| Nombre de la tabla |Proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Actualmente, la salida de Power BI de trabajos de Análisis de transmisiones solo puede tener una tabla en un conjunto de datos. |

Para visualizar un tutorial sobre la configuración de una salida de Power BI y un panel, consulte el artículo [Azure Stream Analytics y Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> No cree explícitamente el conjunto de datos y la tabla en el panel de Power BI. El conjunto de datos y la tabla se rellenarán automáticamente cuando se inicie el trabajo y este inicie el bombeo de la salida en Power BI. Observe que si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se creará. Tenga en cuenta asimismo que si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Análisis de transmisiones, se sobrescribirán los datos existentes.
> 
> 

### <a name="schema-creation"></a>Creación de esquemas
Azure Stream Analytics crea una tabla y un conjunto de datos de Power BI en nombre del usuario si todavía no existe. En todos los demás casos, la tabla se actualiza con los nuevos valores. Actualmente, existe la limitación de que solo puede existir una tabla dentro de un conjunto de datos.

### <a name="data-type-conversion-from-asa-to-power-bi"></a>Conversión de tipo de datos de ASA a Power BI
Azure Stream Analytics actualiza el modelo de datos dinámicamente en tiempo de ejecución si cambia el esquema de salida. Se realiza un seguimiento de los cambios de nombre de columna, los cambios de tipo de columna y la adición o eliminación de columnas.

Esta tabla cubre las conversiones de tipos de datos de [tipos de datos de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) a [tipos de Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) de Power BI si no existen una tabla y un conjunto de datos de POWER BI.


De Stream Analytics | A Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | string
datetime | DateTime
float | Doble
Matriz de registro | Tipo cadena, valor constante “IRecord” o “IArray”

### <a name="schema-update"></a>Actualización de esquema
Stream Analytics deduce el esquema de modelo de datos basándose en el primer conjunto de eventos en la salida. Más adelante, si es necesario, el esquema de modelo de datos se actualiza para dar cabida a los eventos entrantes que pueden encajar en el esquema original.

La consulta `SELECT *` debería evitarse para impedir la actualización de esquema dinámica en las filas. Además de las posibles implicaciones de rendimiento, también podría dar lugar a la indeterminación de la hora que se toma para los resultados. Se deben seleccionar los campos exactos que necesitan mostrarse en el panel de Power BI. Además, los valores de datos deben ser compatibles con el tipo de datos elegido.


Anterior o actual | Int64 | string | DateTime | Doble
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | string | Doble
Doble | Doble | string | string | Doble
string | String | String | String |  | string | 
DateTime | string | string |  DateTime | String


### <a name="renew-power-bi-authorization"></a>Renovación de la autorización de Power BI
Tendrá que volver a autenticar la cuenta de Power BI si su contraseña ha cambiado desde que se creó o autenticó por última vez su trabajo. Si Multi-Factor Authentication (MFA) se configura en el inquilino de Azure Active Directory (AAD), también debe renovar la autorización de Power BI cada 2 semanas. Un síntoma de este problema es la ausencia de salidas de trabajos y un "error de autenticación de usuario" en los registros de operaciones:

  ![Error de token de actualización de Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida de Power BI.  Haga clic en el vínculo "Renovar autorización" y reinicie el trabajo desde la hora en que se detuvo por última vez para evitar la pérdida de datos.

  ![Autorización de renovación de Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Almacenamiento de tablas
[Azure Table Storage](../storage/common/storage-introduction.md) ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Almacenamiento de tablas es un almacén de claves/atributos NoSQL de Microsoft que puede aprovechar para datos estructurados con menos restricciones en el esquema. El almacenamiento de tablas de Azure puede usarse para almacenar datos con de persistencia y recuperación eficaz.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de tablas. |
| Cuenta de almacenamiento |El nombre de la cuenta de almacenamiento a donde está enviando la salida |
| Clave de cuenta de almacenamiento |La clave de acceso asociada con la cuenta de almacenamiento. |
| Nombre de la tabla |El nombre de la tabla. La tabla se creará si no existe. |
| Partition Key |El nombre de la columna de salida que contiene la clave de partición. La clave de partición es un identificador único de la partición dentro de una tabla determinada que constituye la primera parte de la clave principal de la entidad. Es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Row Key |El nombre de la columna de salida que contiene la clave de la fila. La clave de fila es un identificador único de una identidad dentro de una partición determinada. Forma la segunda parte de la clave principal de la entidad. La clave de fila es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Tamaño de lote |El número de registros para una operación por lotes. Normalmente, el valor predeterminado es suficiente para la mayoría de los trabajos. Consulte [Especificaciones de la operación por lotes de tablas](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para más información sobre la modificación de esta configuración. |
 
## <a name="service-bus-queues"></a>Colas del Bus de servicio
[Colas del Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx) ofrecen una entrega de mensajes según el modelo El primero en entrar es el primero en salir (FIFO) a uno o más consumidores de la competencia. Normalmente, se espera que los receptores reciban y procesen los mensajes en el orden temporal en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de cola.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta cola del Bus de servicio. |
| Espacio de nombres de Bus de servicio |Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. |
| Nombre de la cola |El nombre de la cola del Bus de servicio |
| Nombre de la directiva de colas |Al crear una cola, también puede crear directivas de acceso compartidas en la pestaña Configurar cola. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de colas |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
| Codificación |Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador |Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato |Solo se aplica para el tipo JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. |

## <a name="service-bus-topics"></a>Temas de Bus de servicio
Mientras que las colas de Service Bus proporcionan un método de comunicación individual del remitente al receptor, los [temas de Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) proporcionan una forma de comunicación de uno a muchos.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | Description |
| --- | --- |
| Alias de salida |Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este tema de Service Bus. |
| Espacio de nombres de Bus de servicio |Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo Centro de eventos, también se crea un espacio de nombres del Bus de servicio. |
| Nombre del tema |Los temas son entidades de mensajería, similares a los centros de eventos y las colas. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Cuando se crea un tema, se proporciona también un nombre específico. Los mensajes enviados a un tema no estarán disponibles a menos que se cree una suscripción, así que asegúrese de que hay una o más suscripciones en el tema. |
| Nombre de la directiva de temas |Al crear un tema, también puede crear directivas de acceso compartido en la pestaña Configurar tema. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de temas |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
 | Codificación |Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible. |
| Delimitador |Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) es un servicio de base de datos multimodelo distribuido globalmente que ofrece un escalado ilimitado en todo el mundo, una avanzada funcionalidad de indexación automática y de consultas a partir de modelos de datos independientes del esquema, baja latencia garantizada y completos contratos de nivel de servicio líderes en el sector.

En la lista siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de Azure Cosmos DB.

* **Alias de salida** : un alias para hacer referencia a esta salida en la consulta ASA.  
* **Nombre de cuenta**: el nombre o el identificador URI del punto de conexión de la cuenta de Cosmos DB.  
* **Clave de cuenta**: la clave de acceso compartido para la cuenta de Cosmos DB.  
* **Base de datos**: nombre de la base de datos de Cosmos DB.  
* **Patrón de nombre de colección**: el patrón de nombre de colección para las colecciones que se usarán. El formato de nombre de la colección se pueden construir con el token opcional {partition}, donde las particiones comienzan desde 0. Las siguientes entradas de ejemplo son válidas:  
  1\) MyCollection: debe existir una colección denominada "MyCollection".  
  2\) MyCollection{partición}: deben existir esas colecciones: "MyCollection0", "MyCollection1", "MyCollection2", etc.  
* **Clave de partición**: opcional. Solo es necesario si usa un token {partition} en el patrón de nombre de la colección. Nombre del campo en los eventos de salida que se utiliza para especificar la clave de partición de salida entre colecciones. Para una salida de colección sencilla, se puede utilizar cualquier columna de salida arbitraria (por ejemplo, PartitionId).  
* **Identificador de documento** : opcional. Nombre del campo de los eventos de salida utilizado para especificar la clave principal en la que se basan las operaciones de inserción o actualización.  

## <a name="azure-functions-in-preview"></a>Azure Functions (en versión preliminar)
Azure Functions es un servicio de proceso sin servidor que le permite ejecutar código a petición sin necesidad de aprovisionar ni administrar explícitamente la infraestructura. Permite implementar el código desencadenado por los eventos que se producen en servicios de Azure o en servicios de terceros.  Esta capacidad que tiene Azure Functions para responder a los desencadenadores hace que sea una salida natural para una instancia de Azure Stream Analytics. Este adaptador de salida permite que los usuarios conecten Stream Analytics con Azure Functions y ejecuten un script o una parte de un código como respuesta a una variedad de eventos.

Azure Stream Analytics invoca a Azure Functions a través de desencadenadores HTTP. El nuevo adaptador de salida de Azure Functions está disponible con las propiedades configurables siguientes:

| Nombre de propiedad | Descripción |
| --- | --- |
| Function App |Nombre de la instancia de Azure Functions App |
| Función |Nombre de la función en la instancia de Azure Functions App |
| Tamaño máximo de lote |Esta propiedad se puede usar para establecer el tamaño máximo de cada lote de salida que se enviará a la instancia de Azure Functions. De manera predeterminada, este valor es 256 KB |
| Número máximo de lotes  |Tal como el nombre lo indica, esta propiedad permite especificar el número máximo de eventos en cada lote que se envía a Azure Functions. El valor predeterminado del número máximo de lotes es 100 |
| Clave |Si desea usar una instancia de Azure Functions desde otra suscripción, puede proporcionar la clave para acceder a la función |

Tenga en cuenta que cuando Azure Stream Analytics recibe una excepción 413 (La entidad de solicitud http es demasiado grande) de una función de Azure, disminuye el tamaño de los lotes que envía a Azure Functions. En el código de función de Azure, use esta excepción para asegurarse de que Azure Stream Analytics no envíe lotes demasiado grandes. Además, asegúrese de que los valores de tamaño y número máximo de lotes que se usan en la función sean coherentes con los valores que se ingresan en el portal de Stream Analytics. 

También tenga en cuenta que no se genera ninguna salida en una situación en la que no hay ningún aterrizaje de evento en una ventana de tiempo. Como resultado, no se llamará a la función computeResult. Este comportamiento es coherente con las funciones integradas de agregado en ventanas.


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce Análisis de transmisiones, un servicio administrado para el análisis del streaming de datos desde Internet de las cosas. Para obtener más información acerca de este servicio, consulte:

* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
