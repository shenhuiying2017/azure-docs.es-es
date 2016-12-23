---
title: Uso de Azure Functions para realizar una tarea de limpieza programada | Microsoft Docs
description: "Utilice Funciones de Azure para crear una función de C# que se ejecuta según un temporizador de eventos."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: b873a7d0ef9efa79c9a173a8bfd3522b12522322
ms.openlocfilehash: c0b4a963275dae5bbf203388cb61086393803b15


---
# <a name="use-azure-functions-to-perform-a-scheduled-clean-up-task"></a>Uso de funciones de Azure para realizar una tarea de limpieza programada
En este tema se muestra cómo utilizar Azure Functions para crear una nueva función de C# que se ejecuta según un temporizador de eventos para limpiar filas de una tabla de base de datos. La función nueva se crea a partir de una plantilla predefinida del portal de Funciones de Azure. Para que este escenario sea posible, también debe establecer una cadena de conexión de base de datos como un valor de configuración del Servicio de aplicaciones en la aplicación de función. 

## <a name="prerequisites"></a>Requisitos previos
Para poder crear una función, tiene que tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [puede crear una gratuita](https://azure.microsoft.com/free/).

En este tema se describe un comando de Transact-SQL que ejecuta una operación de limpieza masiva en la tabla denominada *TodoItems* en SQL Database. Esta misma tabla TodoItems se crea cuando se completa el [tutorial de inicio rápido de Aplicaciones móviles del Servicio de aplicaciones de Azure](../app-service-mobile/app-service-mobile-ios-get-started.md). También puede usar una base de datos de ejemplo. Si decide utilizar una tabla diferente, deberá modificar el comando.

Puede obtener la cadena de conexión que usa un back-end de aplicación móvil en el portal, en **Toda la configuración** > **Configuración de la aplicación** > **Cadenas de conexión** > **Mostrar valores de la cadena de conexión** > **MS_TableConnectionString**. También puede obtener la cadena de conexión directamente de una base de datos SQL en el portal en **Toda la configuración** > **Propiedades** > **Mostrar las cadenas de conexión de la base de datos** > **ADO.NET (autenticación de SQL)**.

En este escenario se utiliza una operación masiva en la base de datos. Para que la función procese operaciones CRUD individuales en una tabla de Aplicaciones móviles, utilice en su lugar el enlace de la tabla de servicios móviles.

## <a name="set-a-sql-database-connection-string-in-the-function-app"></a>Establecimiento de una cadena de conexión de Base de datos SQL en la aplicación de función
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Se recomienda almacenar las cadenas de conexión y otros secretos en la configuración de la aplicación de función. De esta manera, se evita la revelación accidental cuando el código de la función acaba en un repositorio en alguna parte. 

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.
2. Si tiene una aplicación de función existente, selecciónela en **Your function apps** (Sus aplicaciones de función) y haga clic en **Abrir**. Para crear una aplicación de función, escriba un **nombre** único para ella o acepte el que se genera, seleccione su **región** preferida y haga clic en **Create + get started** (Crear y comenzar). 
3. En la aplicación de función, haga clic en **Function app settings (Configuración de Function App)** > **Go to App Service settings** (Ir a la configuración del Servicio de aplicaciones). 
   
    ![Hoja Configuración de Function App](./media/functions-create-an-event-processing-function/functions-app-service-settings.png)
4. En la aplicación de función, haga clic en **Toda la configuración**, desplácese a **Configuración de la aplicación**, en **Cadenas de conexión** escriba `sqldb_connection` para **Nombre**, pegue la cadena de conexión en **Valor**, haga clic en **Guardar** y luego cierre la hoja de la aplicación de función para volver al portal de Funciones.
   
    ![Cadena de conexión de configuración del Servicio de aplicaciones](./media/functions-create-an-event-processing-function/functions-app-service-settings-connection-strings.png)

Ahora, puede agregar el código de función de C# que conecta con la base de datos SQL.

## <a name="create-a-timer-triggered-function-from-the-template"></a>Creación de una función desencadenada por un temporizador a partir de la plantilla
1. En la aplicación de función, haga clic en **+ New Function (+ Nueva función)** > **TimerTrigger - C#** > **Create (Crear)**. Esto crea una función con un nombre predeterminado que se ejecuta en la programación predeterminada de una vez cada minuto. 
   
    ![Creación de una nueva función desencadenada por un temporizador](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
2. En el panel **Código** de la pestaña **Desarrollar**, agregue las siguientes referencias de ensamblado en la parte superior del código de función existente:
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

4. Reemplace la función **Run** existente por el siguiente código:
    ```cs
        public static async Task Run(TimerInfo myTimer, TraceWriter log)
        {
            var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
            using (SqlConnection conn = new SqlConnection(str))
            {
                conn.Open();
                var text = "DELETE from dbo.TodoItems WHERE Complete='True'";
                using (SqlCommand cmd = new SqlCommand(text, conn))
                {
                    // Execute the command and log the # rows deleted.
                    var rows = await cmd.ExecuteNonQueryAsync();
                    log.Info($"{rows} rows were deleted");
                }
            }
        }
    ```

5. Haga clic en **Guardar**, inspeccione la ventana **Registros** durante la ejecución de la siguiente función y luego anote el número de filas eliminadas de la tabla TodoItems.
6. (Opcional) Mediante la [aplicación de inicio rápido de Aplicaciones móviles](../app-service-mobile/app-service-mobile-ios-get-started.md), marque los elementos adicionales como "completados", vuelva a la ventana **Registros** y vea que la función elimina el mismo número de filas durante la próxima ejecución. 

## <a name="next-steps"></a>Pasos siguientes
Consulte estos temas para más información sobre Azure Functions.

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)  
   contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Prueba de Azure Functions](functions-test-a-function.md)  
   describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
  Trata los planes de servicio disponibles con Azure Functions, incluido el plan de consumo, y cómo elegir el plan adecuado.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO5-->


