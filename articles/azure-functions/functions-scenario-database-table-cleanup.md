---
title: Uso de Azure Functions para realizar una tarea de limpieza de base de datos | Microsoft Docs
description: "Use Azure Functions para programar una tarea que se conecte a Azure SQL Database a fin de limpiar filas de forma periódica."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 9d8261a22f5ea9ce61bcdc79d24a6c054597039b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Uso de Azure Functions para conectarse a una base de datos de Azure SQL Database
En este tema se indica cómo usar Azure Functions para crear un trabajo programado que limpie hileras de una tabla en Azure SQL Database. La nueva función C# se crea a partir de una plantilla de desencadenador de temporizador predefinida de Azure Portal. Para que este escenario sea posible, también debe establecer una cadena de conexión de base de datos como una configuración de aplicación en la aplicación de función. En este escenario se utiliza una operación masiva en la base de datos. 

Para que la función procese operaciones individuales de creación, lectura, actualización y eliminación (CRUD) en una tabla de Mobile Apps, debería usar en su lugar [enlaces de Mobile Apps](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Requisitos previos

+ En este tema se usa una función desencadenada por un temporizador. Efectúe los pasos del tema [Cree una función en Azure que se desencadena mediante un temporizador](functions-create-scheduled-function.md) para crear una versión en C# de esta función.   

+ En este tema se realiza una demostración de un comando de Transact-SQL que ejecuta una operación de limpieza masiva en la tabla **SalesOrderHeader** de la base de datos AdventureWorksLT de ejemplo. Para crear la base de datos de ejemplo AdventureWorksLT, efectúe los pasos indicados en el tema [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Deberá obtener la cadena de conexión de la base de datos que creó una vez concluidos los pasos de [Creación de una instancia de Azure SQL Database en Azure Portal](../sql-database/sql-database-get-started-portal.md).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
 
3. Seleccione **SQL Database** en el menú de la izquierda y seleccione la base de datos en la página **SQL Database**.

4. Seleccione **Mostrar las cadenas de conexión de la base de datos** y copie la cadena de conexión de **ADO.NET** completa.

    ![Copie la cadena de conexión de ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Establecimiento de la cadena de conexión 

Una aplicación de función hospeda la ejecución de sus funciones en Azure. Se recomienda almacenar las cadenas de conexión y otros secretos en la configuración de la aplicación de función. El uso de la configuración de la aplicación impide divulgaciones accidentales de la cadena de conexión con el código. 

1. Vaya a la aplicación de función que creó en [Cree una función en Azure que se desencadena mediante un temporizador](functions-create-scheduled-function.md).

2. Seleccione **Características de la plataforma** > **Configuración de la aplicación**.
   
    ![Configuración de la aplicación para la aplicación de función.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Desplácese hacia abajo hasta **Cadenas de conexión** y agregue una cadena de conexión con los valores de configuración especificados en la tabla.
   
    ![Agregue una cadena de conexión a la configuración de la aplicación de función.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Configuración       | Valor sugerido | Descripción             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name**  |  sqldb_connection  | Se usa para acceder a la cadena de conexión almacenada en el código de función.    |
    | **Valor** | Cadena copiada  | Pegue la cadena de conexión que copió en la sección anterior y reemplace los marcadores de posición `{your_username}` y `{your_password}` por valores reales. |
    | **Tipo** | SQL Database | Use la conexión predeterminada de SQL Database. |   

3. Haga clic en **Guardar**.

Ahora, puede agregar el código de función de C# que conecta con SQL Database.

## <a name="update-your-function-code"></a>Actualización del código de función

1. En la aplicación de función, seleccione la función desencadenada por temporizador.
 
3. Agregue las siguientes referencias de ensamblado en la parte superior del código de función existente:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. Agregue las siguientes instrucciones `using` a la función:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Reemplace la función `Run` existente por el código siguiente:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Este comando de ejemplo actualiza la columna `Status` en función de la fecha de envío. Debería actualizar 32 filas de datos.

5. Haga clic en **Guardar**, inspeccione la ventana **Registros** durante la ejecución de la siguiente función y, a continuación, anote el número de filas actualizadas en la tabla **SalesOrderHeader**.

    ![Vista de los registros de función.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Pasos siguientes

A continuación, obtenga información sobre cómo usar Functions con Logic Apps para la integración con otros servicios.

> [!div class="nextstepaction"] 
> [Creación de una función que se integre con Logic Apps](functions-twitter-email.md)

Para obtener más función sobre Functions, consulte los siguientes temas:

* 
              [Referencia para desarrolladores de Azure Functions](functions-reference.md)  
contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Prueba de Azure Functions](functions-test-a-function.md)  
  describe las diversas herramientas y técnicas para probar sus funciones.  
