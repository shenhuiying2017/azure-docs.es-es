---
title: "Creación de una función de Azure que se enlaza a un servicio de Azure | Microsoft Azure"
description: "Cree una función de Azure, una aplicación sin servidor, que interactúa con otros servicios de Azure."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Creación de una función de Azure que se enlaza a un servicio de Azure
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

En este vídeo corto, obtendrá información sobre cómo crear una función de Azure que escucha los mensajes en una cola de Azure y copia los mensajes en un blob de Azure.

## <a name="watch-the-video"></a>Visualización del vídeo
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Creación de una función de activación de cola de entrada
El objetivo de esta función consiste en escribir un mensaje en una cola cada 10 segundos. Para ello, debe crear las colas de función y mensaje y agregar el código para escribir mensajes en las colas recién creadas.

1. Vaya a Azure Portal y busque la aplicación Azure Function.
2. Haga clic en **Nueva función** > **TimerTrigger: - Node** (TimeTrigger - Node). Asigne a la función el nombre **FunctionsBindingsDemo1**
3. Escriba un valor de "0/10 * * * * *" para la programación. Este valor está en forma de una expresión cron. Programa el temporizador para que se ejecute cada 10 segundos.
4. Haga clic en el botón **Crear** para crear la función.
   
    ![Adición de una función de temporizador de desencadenador](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Compruebe que la función funciona; para ello, examine la actividad del registro. Puede que tenga que hacer clic en el vínculo **Registros** en la esquina superior derecha para mostrar el panel de registro.
   
   ![Compruebe que la función funciona; para ello, examine el registro.](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Adición de una cola de mensajes
1. Vaya a la ficha **Integrar**.
2. Elija **Nueva salida** > **Cola de Azure Storage** > **Seleccionar**.
3. Escriba **myQueueItem** en el cuadro de texto **Nombre de parámetro de mensaje**.
4. Seleccione una cuenta de almacenamiento, o haga clic en **Nueva** para crear una cuenta de almacenamiento si no tiene ya una.
5. Escriba **functions-bindings** en el cuadro de texto **Nombre de la cola**.
6. Haga clic en **Guardar**.  
   
   ![Adición de una función de temporizador de desencadenador](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Escritura en la cola de mensajes
1. Vuelva a la ficha **Desarrollar** y agregue el código siguiente a la función detrás del código existente:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modifique el código de función existente para llamar al código que agregó en el paso 1. Inserte el siguiente código alrededor de la línea 9 de la función, después de la instrucción *if*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Este código crea un elemento **myQueueItem** y establece su propiedad **time** en la marca de tiempo actual. A continuación, agrega el nuevo elemento de cola al enlace de myQueue del contexto.
3. Haga clic en **Guardar y ejecutar**.
4. Compruebe que el código funciona; para ello, examine la cola en Visual Studio.
   
   * Abra Visual Studio y vaya a **Ver** > **Cloud** **Explorer**.
   * Busque la cuenta de almacenamiento y la cola **functions-bindings** que usó para crear la cola myQueue. Verá filas de datos de registro. Puede que deba iniciar sesión en Azure mediante Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Creación de una función de desencadenador de cola de salida
1. Haga clic en **Nueva función** > **QueueTrigger - C#**. Asigne a la función el nombre **FunctionsBindingsDemo2**. Tenga en cuenta que se pueden mezclar lenguajes en la misma aplicación de función (en este caso Node y C#).
2. Escriba **functions-bindings** en el campo **Nombre de la cola**.
3. Seleccione una cuenta de almacenamiento para usar o cree una nueva.
4. Haga clic en **Crear**
5. Compruebe que la nueva función funciona; para ello, examine el registro de la función y Visual Studio en busca de actualizaciones. El registro de la función muestra que la función se está ejecutando y que los elementos se han quitado de la cola. Como la función está enlazada con la cola de salida **functions-bindings** como desencadenador de entrada, al actualizar la cola **functions-bindings** en Visual Studio se mostrará que los elementos han desaparecido. Se han quitado de la cola.   
   
   ![Adición de funciones de temporizador de cola de salida](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Modificación del tipo de elemento de cola de JSON a objeto
1. Reemplace el código de **FunctionsBindingsDemo2** por el código siguiente:    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Este código agrega dos clases, **TableItem** y **QItem**, que se usan para leer y escribir en las colas. Además, la función **Run** se ha modificado para aceptar el parámetro **QItem** y **TraceWriter**, en lugar de una **cadena** y un elemento **TraceWriter**. 
2. Haga clic en el botón **Save** (Guardar).
3. En el registro puede comprobar que el código funciona. Tenga en cuenta que Azure Functions serializa y deserializa automáticamente el objeto, lo que permite que sea fácil acceder a la cola en un modo orientado a objetos para pasar datos. 

## <a name="store-messages-in-an-azure-table"></a>Almacenamiento de mensajes en una tabla de Azure
Ahora que tiene a las colas funcionando juntas, es hora de agregar una tabla de Azure para el almacenamiento permanente de los datos de la cola.

1. Vaya a la ficha **Integrar**.
2. Cree una tabla de Azure Storage para la salida y asígnele el nombre **myTable**.
3. Cuando se pregunte en qué tabla se deben escribir los datos, responda **functionsbindings**.
4. Cambie el valor de **PartitionKey** de **{project-id}** a **{partition}**.
5. Elija una cuenta de almacenamiento o cree una nueva.
6. Haga clic en **Guardar**.
7. Vaya a la ficha **Desarrollar**.
8. Cree una clase **TableItem** para representar una tabla de Azure y modifique la función Run para aceptar el objeto TableItem recién creado. Tenga en cuenta que debe usar las propiedades **PartitionKey** y **RowKey** para que funcione.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Haga clic en **Guardar**.
10. Compruebe que el código funciona; para ello, examine los registros de la función y Visual Studio. En Visual Studio, use **Cloud Explorer** para ir a la tabla de Azure **functionbindings** y compruebe que contenga filas.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


