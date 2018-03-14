---
title: Conectarse a Azure Databricks desde Excel, Python o R | Microsoft Docs
description: Aprenda a usar el controlador Simba para conectar Azure Databricks con Excel, Python o R.
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 9daa7d30036d0a0f98d079e03a69c29d11e49664
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Conectarse a Azure Databricks desde Excel, Python o R

En este artículo, aprenderá a usar el controlador ODBC de Databricks para conectar Azure Databricks con Microsoft Excel, Python o el lenguaje R. Una vez establecida la conexión, puede acceder a los datos de Azure Databricks desde los clientes de Excel, Python o R. También puede usar los clientes para analizar con más detalle los datos. 

## <a name="prerequisites"></a>requisitos previos

* Debe tener un área de trabajo de Azure Databricks, un clúster de Spark y datos de ejemplo asociados al clúster. Si no dispone de estos requisitos previos, complete la guía de inicio rápido de [Ejecución de un trabajo de Spark en Azure Databricks con Azure Portal](quickstart-create-databricks-workspace-portal.md).

* Descargue el controlador ODBC de Databricks desde la [página de descarga de controladores de Databricks](https://databricks.com/spark/odbc-driver-download). Instale la versión de 32 bits o la de 64 bits dependiendo de la aplicación desde la que desea conectarse a Azure Databricks. Por ejemplo, para conectarse desde Excel, instale la versión de 32 bits del controlador. Para conectarse desde R y Python, instale la versión de 64 bits del controlador.

* Configure un token de acceso personal en Databricks. Para obtener instrucciones, consulte [Administración de tokens](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Configuración de un DSN

Un nombre de origen de datos (DSN) contiene la información sobre un origen de datos específico. Un controlador ODBC necesita este DSN para conectarse a un origen de datos. En esta sección, configurará un DSN que se puede usar con el controlador ODBC de Databricks para conectarse a Azure Databricks desde clientes como Microsoft Excel, Python o R.

1. Desde el área de trabajo de Azure Databricks, vaya al clúster de Databricks.

    ![Abrir clúster de Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Abrir clúster de Databricks")

2. En la pestaña **Configuración**, haga clic en la pestaña **JDBC/ODBC** y copie los valores de **Nombre de host del servidor** y **Ruta de acceso HTTP**. Necesitará estos valores para completar los pasos de este artículo.

    ![Obtener configuración de Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obtener configuración de Databricks")

3. En el equipo, inicie la aplicación **Orígenes de datos ODBC** (32 bits o 64 bits), dependiendo de la aplicación. Para conectarse desde Excel, use la versión de 32 bits. Para conectarse desde R y Python, use la versión de 64 bits.

    ![Iniciar ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Iniciar aplicación ODBC")

4. En la pestaña **DSN de usuario**, haga clic en **Agregar**. En el cuadro de diálogo **Crear un nuevo origen de datos**, seleccione **Simba Spark ODBC Driver** (Controlador ODBC de Simba Spark) y haga clic en **Finalizar**.

    ![Iniciar ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Iniciar aplicación ODBC")

5. En el cuadro de diálogo **Simba Spark ODBC Driver** (Controlador ODBC de Simba Spark), proporcione los siguientes valores:

    ![Configurar DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurar DSN")

    La tabla siguiente proporciona información sobre los valores que debe proporcionar en el cuadro de diálogo.
    
    |Campo  | Valor  |
    |---------|---------|
    |**Data Source Name**     | Proporcione un nombre para el origen de datos.        |
    |**Host(s)**     | Proporcione el valor que copió del área de trabajo de Databricks en *Nombre de host del servidor*.        |
    |**Puerto**     | Escriba *443*.        |
    |**Authentication** > **Mechanism**     | Seleccione *User name and password* (Nombre de usuario y contraseña).        |
    |**Nombre de usuario**     | Escriba *token*.        |
    |**Password**     | Escriba el valor de token que copió del área de trabajo de Databricks. |
    
    Realice los siguientes pasos adicionales en el cuadro de diálogo de configuración de DSN.
    
    * Haga clic en **Opciones de HTTP**. En el cuadro de diálogo que se abre, pegue el valor de *Ruta de acceso HTTP* que copió del área de trabajo de Databricks. Haga clic en **OK**.
    * Haga clic en **Opciones SSL**. En el cuadro de diálogo que se abre, seleccione la casilla **Habilitar SSL**. Haga clic en **OK**.
    * Haga clic en **Probar** para probar la conexión a Azure Databricks. Haga clic en **Aceptar** para guardar la configuración.
    * En el cuadro de diálogo **Administrador de orígenes de datos ODBC**, haga clic en **Aceptar**.

Ahora ya tiene el DSN configurado. En las secciones siguientes, use este DSN para conectarse a Azure Databricks desde Excel, Python o R.

## <a name="connect-from-microsoft-excel"></a>Conexión desde Microsoft Excel

En esta sección, va a extraer los datos de Azure Databricks en Microsoft Excel mediante el DSN que creó anteriormente. Antes de comenzar, asegúrese de que tiene instalado Microsoft Excel en el equipo. Puede usar una versión de prueba de Excel desde este [vínculo de prueba de Microsoft Excel](https://products.office.com/excel).

1. Abra un libro en blanco en Microsoft Excel. En la cinta **Datos**, haga clic en **Obtener datos**. Haga clic en **Desde otras fuentes** y, luego, en **Desde ODBC**.

    ![Iniciar ODBC desde Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Iniciar ODBC desde Excel")

2. En el cuadro de diálogo **De ODBC**, seleccione el DSN que creó anteriormente y haga clic en **Aceptar**.

    ![Seleccionar DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Seleccionar DSN")

3. Si se le piden credenciales, en el nombre de usuario escriba **token**. Como contraseña, proporcione el valor del token que recuperó del área de trabajo de Databricks.

    ![Proporcionar credenciales de Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Seleccionar DSN")

4. En la ventana del navegador, seleccione la tabla en Databricks que desea cargar en Excel y, a continuación, haga clic en **Cargar**. 

    ![Cargar dta en Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Cargar dta en Excel")

Una vez que tenga los datos en el libro de Excel, podrá realizar operaciones analíticas en él.

## <a name="connect-from-r"></a>Conexión desde R

En esta sección va a usar un IDE de lenguaje R para hacer referencia a datos disponibles en Azure Databricks. Antes de empezar, debe tener lo siguiente instalado en el equipo.

* Un IDE para el lenguaje R. En este artículo se usa RStudio for Desktop. Puede instalarlo desde las [descargas de R Studio](https://www.rstudio.com/products/rstudio/download/).
* Si usa RStudio for Desktop como su IDE, instale también Microsoft R Client desde [http://aka.ms/rclient/](http://aka.ms/rclient/). 

Abra RStudio y realice los pasos siguientes:

- Haga referencia al paquete `RODBC`. Esto le permitirá conectarse a Azure Databricks mediante el DSN que creó anteriormente.
- Establezca una conexión mediante el DSN.
- Ejecute una consulta SQL en los datos de Azure Databricks. En el siguiente fragmento de código, *radio_sample_data* es una tabla que ya existe en Azure Databricks.
- Realice algunas operaciones en la consulta para comprobar la salida. 

El fragmento de código siguiente realiza estas tareas:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Conexión desde Python

En esta sección va a usar un IDE de Python (como IDLE) para hacer referencia a datos disponibles en Azure Databricks. Antes de empezar, debe cumplir los siguientes requisitos previos:

* Instale Python desde [aquí](https://www.python.org/downloads/). La instalación de Python desde este vínculo también instala IDLE.

* Desde un símbolo del sistema del equipo, instale el paquete `pyodbc`. Ejecute el siguiente comando:

      pip install pyodbc

Abra IDLE y realice los pasos siguientes:

- Importe el paquete `pyodbc`. Esto le permitirá conectarse a Azure Databricks mediante el DSN que creó anteriormente.
- Establezca una conexión mediante el DSN que creó anteriormente.
-  Ejecute una consulta SQL mediante la conexión que creó. En el siguiente fragmento de código, *radio_sample_data* es una tabla que ya existe en Azure Databricks.
- Realice operaciones en la consulta para comprobar la salida.

El fragmento de código siguiente realiza estas tareas:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de los orígenes desde donde se pueden importar datos a Azure Databricks, consulte los [orígenes de datos de Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


