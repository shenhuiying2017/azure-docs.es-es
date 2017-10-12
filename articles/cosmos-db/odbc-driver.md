---
title: "Conexión a Azure Cosmos DB mediante herramientas de análisis de BI | Microsoft Docs"
description: "Obtenga información sobre cómo usar el controlador ODBC de Azure Cosmos DB para crear tablas y vistas de forma que los datos normalizados puedan verse en el software de análisis de datos y BI."
keywords: odbc, controlador odbc
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 2df792c00b7a789dbefa64bfe0245f1ad73c3faa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Conexión a Azure Cosmos DB mediante herramientas de análisis de BI con el controlador ODBC

El controlador ODBC de Azure Cosmos DB le permite conectarse a Azure Cosmos DB mediante herramientas de análisis de BI, como SQL Server Integration Services, Power BI Desktop y Tableau de forma que pueda analizar y crear visualizaciones de los datos de Azure Cosmos DB en esas soluciones.

El controlador ODBC de Azure Cosmos DB es compatible con ODBC 3.8 y admite la sintaxis de ANSI SQL-92. El controlador ofrece características enriquecidas que le ayudarán a volver a normalizar datos en Azure Cosmos DB. Mediante el controlador, puede representar datos en Azure Cosmos DB como tablas y vistas. El controlador permite realizar operaciones de SQL en las tablas y vistas, así como agrupar por consultas, inserciones, actualizaciones y eliminaciones.

## <a name="why-do-i-need-to-normalize-my-data"></a>¿Por qué es necesario normalizar los datos?
Azure Cosmos DB es una base de datos sin esquema, por lo que habilita el desarrollo rápido de aplicaciones al permitir que estas iteren sobre la marcha su modelo de datos sin limitarlos a un esquema estricto. Una sola base de datos de Azure Cosmos DB puede contener documentos JSON de varias estructuras. Esto es ideal para el desarrollo rápido de aplicaciones, pero cuando desea analizar y crear informes de los datos mediante análisis de datos y herramientas de BI, suele ser normal eliminar el formato de los datos y que estos se ajusten a un esquema específico.

Aquí es donde entra en juego el controlador ODBC. Mediante el controlador ODBC, ahora puede volver a normalizar datos en Azure Cosmos DB en tablas y vistas que se ajusten a sus necesidades de informes y análisis de datos. Los esquemas renormalizados no tienen ningún impacto en los datos subyacentes y no limitan a los desarrolladores a ajustarse a ellos, simplemente permiten aprovechar las herramientas compatibles con ODBC para acceder a los datos. Por tanto, la base de datos de Azure Cosmos DB no solo será ahora la favorita del equipo de desarrollo, sino que a los analistas de datos también les encantará.

Ahora empecemos con el controlador ODBC.

## <a id="install"></a>Paso 1: Instalación del controlador ODBC de Azure Cosmos DB

1. Descargue los controladores para su entorno:

    * [Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) para Windows de 64 bits
    * [Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64) para Windows de 32 bits en 64 bits
    * [Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) para Windows de 32 bits

    Ejecute el archivo msi localmente, lo que iniciará el **Asistente para instalación del controlador ODBC de Microsoft Azure Cosmos DB**. 
2. Complete el asistente para instalación con la entrada predeterminada para instalar el controlador ODBC.
3. Abra la aplicación **Administrador de orígenes de datos ODBC** en el equipo. Puede hacerlo escribiendo **orígenes de datos ODBC** en el cuadro de búsqueda de Windows. 
    Puede confirmar que el controlador se ha instalado haciendo clic en la pestaña **Controladores** y asegurándose de que **el controlador ODBC de Microsoft Azure Cosmos DB** aparece en la lista.

    ![Administrador de orígenes de datos ODBC de Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Paso 2: Conexión a su cuenta de Azure Cosmos DB

1. Después de [instalar el controlador ODBC de Azure Cosmos DB](#install), en la ventana **Administrador de orígenes de datos ODBC**, haga clic en **Agregar**. Puede crear un DSN de usuario o de sistema. En este ejemplo, vamos a crear un DSN de usuario.
2. En la ventana **Crear un nuevo origen de datos**, seleccione **Microsoft Azure Cosmos DB ODBC Driver** (Controlador ODBC de Microsoft Azure Cosmos DB) y haga clic en **Finalizar**.
3. En la ventana **Azure Cosmos DB ODBC Driver DSN Setup** (Configuración de DSN del controlador ODBC de Azure Cosmos DB), rellene lo siguiente: 

    ![Ventana Azure Cosmos DB ODBC Driver DSN Setup (Configuración de DSN del controlador ODBC de Azure Cosmos DB)](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nombre del origen de datos**: su propio nombre descriptivo para el DSN de ODBC. Este nombre es único en su cuenta de Azure Cosmos DB, por lo que debe ser apropiado si tiene varias cuentas.
    - **Descripción**: breve descripción del origen de datos.
    - **Host**: URI de la cuenta de Azure Cosmos DB. Puede recuperar esta información de la hoja Claves de Azure Cosmos DB en Azure Portal, como se muestra en la captura de pantalla siguiente. 
    - **Clave de acceso**: clave principal o secundaria, de solo escritura o solo lectura de la hoja Claves de Azure Cosmos DB de Azure Portal, como se muestra en la captura de pantalla siguiente. Se recomienda usar la clave de solo lectura si DSN se utiliza para procesamiento e informes de datos de solo lectura.
    ![Hoja Claves de Azure Cosmos DB](./media/odbc-driver/odbc-driver-keys.png)
    - **Encrypt Access Key for** (Cifrar clave de acceso para): seleccione la mejor opción en función de los usuarios de esta máquina. 
4. Haga clic en el botón **Probar** para asegurarse de que puede conectarse a su cuenta de Azure Cosmos DB. 
5. Haga clic en **Opciones avanzadas** y establezca los siguientes valores:
    - **Query Consistency** (Coherencia de consultas): seleccione el [nivel de coherencia](consistency-levels.md) para las operaciones. El valor predeterminado es Sesión.
    - **Número de reintentos**: escriba el número de veces que se volverá a intentar una operación si la solicitud inicial no se completa debido a una limitación de servicio.
    - **Archivo de esquema**: dispone de varias opciones.
        - De forma predeterminada, dejando esta entrada tal cual (en blanco), el controlador busca todas las coleccionas en los datos de la primera página para determinar el esquema de cada colección. Esto se conoce como asignación de colección. Sin un archivo de esquema definido, el controlador tiene que realizar el examen para cada sesión de controlador y podría dar lugar a un tiempo de inicio mayor de una aplicación con DSN. Se recomienda asociar siempre un archivo de esquema para un DSN.
        - Si ya tiene un archivo de esquema (posiblemente uno que creó mediante el [Editor de esquemas](#schema-editor)), puede hacer clic en **Examinar**, navegar al archivo, hacer clic en **Guardar** y, luego, hacer clic en **Aceptar**.
        - Si desea crear un nuevo esquema, haga clic en **Aceptar**y luego en **Editor de esquemas** en la ventana principal. Después, continúe con la información del [Editor de esquemas](#schema-editor). Cuando cree el nuevo archivo de esquema, recuerde que debe volver a la ventana **Opciones avanzadas** para incluir el archivo de esquema recién creado.

6. Una vez que complete y cierre la ventana **Azure Cosmos DB ODBC Driver DSN Setup** (Configuración de DSN del controlador ODBC de Azure Cosmos DB), el nuevo DSN de usuario se agregará a la pestaña DSN de usuario.

    ![Nuevo DSN de ODBC de Azure Cosmos DB en la pestaña DSN de usuario](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Paso 3: Creación de una definición de esquema mediante el método de asignación de colección

Hay dos tipos de métodos de muestreo que puede usar: **asignación de colección** o **delimitadores de tabla**. Una sesión de muestreo puede utilizar ambos métodos de muestreo, pero cada colección solo puede usar un método de muestreo específico. Los pasos siguientes crean un esquema para los datos de una o varias colecciones mediante el método de asignación de colección. Este método de muestreo recupera los datos de la página de una colección para determinar la estructura de dichos datos. Transpone una colección a una tabla en el lado ODBC. Este método de muestreo es rápido y eficaz cuando los datos de una colección son homogéneos. Si una colección contiene un tipo heterogénea de datos, es recomendable usar el [método de asignación de delimitadores de tabla ](#table-mapping), ya que proporciona un método de muestreo más robusto para determinar las estructuras de datos de la colección. 

1. Después de completar los pasos 1 a 4 de [Conexión a la base de datos de Azure Cosmos DB](#connect), haga clic en **Editor de esquemas** en la ventana **Azure Cosmos DB ODBC Driver DSN Setup** (Configuración de DSN del controlador ODBC de Azure Cosmos DB).

    ![Botón Editor de esquemas en la ventana Azure Cosmos DB ODBC Driver DSN Setup (Configuración de DSN del controlador ODBC de Azure Cosmos DB)](./media/odbc-driver/odbc-driver-schema-editor.png)
2. En la ventana **Editor de esquemas**, haga clic en **Crear nuevo**.
    La ventana **Generate Schema** (Generar esquema) muestra todas las colecciones de la cuenta de Azure Cosmos DB. 
3. Seleccione una o varias recopilaciones para muestrear y luego haga clic en **Muestra**. 
4. En la pestaña **Vista de diseño**, se representan la base de datos, el esquema y la tabla. En la vista de tabla, el examen muestra el conjunto de propiedades asociadas a los nombres de columna (Nombre SQL, Nombre de origen, etc.).
    Para cada columna, puede modificar el nombre SQL de la columna, el tipo de SQL, la longitud SQL (si procede), la escala (si procede), la precisión (si procede) y si acepta valores NULL.
    - Puede establecer **Ocultar columna** en **true** si desea excluir esa columna de los resultados de la consulta. Las columnas marcadas con Ocultar columna = true no se devuelven para la selección y proyección, aunque aún forman parte del esquema. Por ejemplo, puede ocultar todas las propiedades necesarias del sistema de Azure Cosmos DB a partir de “_”.
    - La columna **identificador** es el único campo que no se puede ocultar, ya que se usa como la clave principal en el esquema normalizado. 
5. Cuando haya terminado de definir el esquema, haga clic en **Archivo** | **Guardar**, navegue hasta el directorio para guardar el esquema y, luego, haga clic en **Guardar**.

    Si en el futuro desea utilizar este esquema con un DSN, abra la ventana Azure Cosmos DB ODBC Driver DSN Setup (Configuración de DSN del controlador ODBC de Azure Cosmos DB) a través del Administrador de orígenes de datos ODBC, haga clic en Opciones avanzadas y, luego, en el cuadro Archivo de esquema, desplácese al esquema guardado. Al guardar un archivo de esquema en un DSN existente se modifica la conexión de DSN para tener como ámbito los datos y la estructura definidos por el esquema.

## <a id="table-mapping"></a>Paso 4: Creación de una definición de esquema mediante el método de asignación de delimitadores de tabla

Hay dos tipos de métodos de muestreo que puede usar: **asignación de colección** o **delimitadores de tabla**. Una sesión de muestreo puede utilizar ambos métodos de muestreo, pero cada colección solo puede usar un método de muestreo específico. 

Los pasos siguientes crean un esquema para los datos de una o varias colecciones mediante el método de asignación de **delimitadores de table**. Se recomienda usar este método de muestreo cuando las colecciones contengan un tipo heterogéneo de datos. Puede usar este método para limitar el muestreo a un conjunto de atributos y sus valores correspondientes. Por ejemplo, si un documento contiene una propiedad "Tipo", puede limitar el muestreo a los valores de esta propiedad. El resultado final del muestreo sería un conjunto de tablas para cada uno de los valores para el tipo especificado. Por ejemplo, Tipo = Vehículo generará una tabla de vehículos, mientras que Tipo = Plano generaría una tabla de planos.

1. Después de completar los pasos 1 a 4 de [Conexión a la base de datos de Azure Cosmos DB](#connect), haga clic en **Editor de esquemas** en la ventana Azure Cosmos DB ODBC Driver DSN Setup (Configuración de DSN del controlador ODBC de Azure Cosmos DB).
2. En la ventana **Editor de esquemas**, haga clic en **Crear nuevo**.
    La ventana **Generate Schema** (Generar esquema) muestra todas las colecciones de la cuenta de Azure Cosmos DB. 
3. Seleccione una colección en la pestaña **Vista de ejemplo**, en la columna **Mapping Definition** (Definición de asignación) para la colección y haga clic en **Editar**. A continuación, en la ventana **Mapping Definition** (Definición de asignación), seleccione el método **Table Delimiters** (Delimitadores de tabla). A continuación, haga lo siguiente:

    a. En el cuadro **Atributos**, escriba el nombre de una propiedad de delimitador. Se trata de una propiedad del documento a la que desea limitar el ámbito del muestreo, por ejemplo, Ciudad; luego, presione Intro. 

    b. Si solo desea definir el ámbito del muestreo a ciertos valores para el atributo que acaba de escribir, seleccione dicho atributo en el cuadro de selección, escriba un valor en el cuadro **Valor** (por ejemplo, Seattle) y presione Intro. Puede seguir agregando más valores para los atributos. Solo tiene que asegurarse de que se selecciona el atributo correcto cuando está escribiendo valores.

    Por ejemplo, si incluye Ciudad como un valor de **Atributos** y desea limitar la tabla para incluir solo filas con Nueva York y Dubai como valores de ciudad, tendría que escribir Ciudad en el cuadro Atributos, y Nueva York y luego Dubái en el cuadro **Valores**.
4. Haga clic en **Aceptar**. 
5. Después de completar las definiciones de asignación para las colecciones que desea muestrear, en la ventana **Editor de esquemas**, haga clic en **Ejemplo**.
     Para cada columna, puede modificar el nombre SQL de la columna, el tipo de SQL, la longitud SQL (si procede), la escala (si procede), la precisión (si procede) y si acepta valores NULL.
    - Puede establecer **Ocultar columna** en **true** si desea excluir esa columna de los resultados de la consulta. Las columnas marcadas con Ocultar columna = true no se devuelven para la selección y proyección, aunque aún forman parte del esquema. Por ejemplo, puede ocultar todas las propiedades necesarias del sistema de Azure Cosmos DB a partir de “_”.
    - La columna **identificador** es el único campo que no se puede ocultar, ya que se usa como la clave principal en el esquema normalizado. 
6. Cuando haya terminado de definir el esquema, haga clic en **Archivo** | **Guardar**, navegue hasta el directorio para guardar el esquema y, luego, haga clic en **Guardar**.
7. De vuelta a la ventana **Azure Cosmos DB ODBC Driver DSN Setup** (Configuración de DSN del controlador ODBC de Azure Cosmos DB), haga clic en **Opciones avanzadas**. A continuación, en el cuadro **Archivo de esquema** , navegue hasta el archivo de esquema guardado y haga clic en **Aceptar**. Haga clic en **Aceptar** de nuevo para guardar el DSN. Esto guarda el esquema que ha creado en el DSN. 

## <a name="optional-creating-views"></a>(Opcional) Creación de vistas
Puede definir y crear vistas como parte del proceso de muestreo. Estas vistas son equivalentes a las vistas SQL. Son de solo lectura y con el ámbito de las selecciones y proyecciones de SQL de Azure Cosmos DB definido. 

Para crear una vista para los datos, en la ventana **Editor de esquemas**, en la columna **View Definitions** (Definiciones de vista), haga clic en **Agregar** en la fila de la colección en el ejemplo. A continuación, en la ventana **View Definitions** (Definiciones de vista), haga lo siguiente:
1. Haga clic en **Nuevo**, escriba un nombre para la vista, por ejemplo, EmployeesfromSeattleView y luego haga clic en **Aceptar**.
2. En la ventana **Editar vista**, escriba una consulta de Azure Cosmos DB. Debe tratarse de una consulta SQL de Azure Cosmos DB, por ejemplo`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”` y, luego, haga clic en **Aceptar**.

Puede crear tantas vistas como desee. Una vez que haya terminado de definir las vistas, puede muestrear los datos. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Paso 5: Visualización de los datos en herramientas de BI como Power BI Desktop

Puede utilizar el nuevo DSN para conectar DocumentADB con cualquier herramienta compatible con ODBC; este paso simplemente muestra cómo conectarse a Power BI Desktop y crear una visualización de Power BI.

1. Abra Power BI Desktop.
2. Haga clic en **Obtener datos**.
3. En la ventana **Obtener datos**, haga clic en **Otros** | **ODBC** | **Conectar**.
4. En la ventana **Desde ODBC**, seleccione el nombre del origen de datos que ha creado el nombre y luego haga clic en **Aceptar**. Puede dejar las entradas **Opciones avanzadas** en blanco.
5. En la ventana **Acceda a un origen de datos a través de un controlador ODBC**, seleccione **Predeterminado o personalizado** y luego haga clic en **Conectar**. No es necesario incluir **Propiedades de la cadena de conexión de credenciales**.
6. En la ventana **Navegador**, en el panel izquierdo, expanda la base de datos, el esquema y luego seleccione la tabla. El panel de resultados incluye los datos que usan el esquema que ha creado.
7. Para visualizar los datos en Power BI Desktop, active la casilla junto al nombre de tabla y luego haga clic en **Cargar**.
8. En Power BI Desktop, en el extremo izquierdo, seleccione la pestaña Datos ![Pestaña Datos en Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) para confirmar que los datos se importaron.
9. Ahora puede crear objetos visuales con Power BI haciendo clic en la pestaña Informe ![Pestaña Informe en Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), haciendo clic en **Nuevo objeto visual** y, por último, personalizando el icono. Para más información sobre cómo crear visualizaciones en Power BI Desktop, consulte [Tipos de visualización en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Solución de problemas

Si recibe el siguiente error, asegúrese de que los valores de **Host** y **Clave de acceso** copiados de Azure Portal en el [paso 2](#connect) son correctos y luego vuelva a intentarlo. Utilice los botones de copia situados a la derecha de los valores de **Host** y **Clave de acceso** en Azure Portal para copiar los valores sin errores.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Azure Cosmos DB, consulte [¿Qué es Azure Cosmos DB?](introduction.md).
