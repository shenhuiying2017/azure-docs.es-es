---
title: Desarrollo de Azure Functions con Visual Studio | Microsoft Docs
description: "Obtenga información sobre cómo desarrollar y probar Azure Functions mediante Herramientas de Azure Functions para Visual Studio 2017."
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: 5f0b346ec1c4a42d11ef74ab8ac8701dd45b00eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-tools-for-visual-studio"></a>Herramientas de Azure Functions para Visual Studio  

Herramientas de Azure Functions para Visual Studio 2017 es una extensión para Visual Studio que le permite desarrollar, probar e implementar funciones de C# en Azure. Si esta es su primera experiencia con Azure Functions, puede obtener más información en [Introducción a Azure Functions](functions-overview.md).

Herramientas de Azure Functions proporciona los siguientes beneficios: 

* Editar, compilar y ejecutar funciones en el equipo de desarrollo local. 
* Publicar su proyecto de Azure Functions directamente en Azure. 
* Usar atributos de WebJobs para declarar los enlaces de función directamente en el código C# en lugar de mantener un function.json separado para enlazar definiciones.
* Desarrollar e implementar funciones de C# compiladas previamente. Las funciones compiladas previamente proporcionan un mejor rendimiento de arranque en frío que las funciones basadas en scripts de C#. 
* Programar las funciones en C# a la vez que se tienen todos los beneficios del desarrollo de Visual Studio. 

En este tema se muestra cómo usar Herramientas de Azure Functions para Visual Studio 2017 a fin de desarrollar las funciones en C#. También puede obtener información sobre cómo publicar el proyecto en Azure como un ensamblado .NET.

## <a name="prerequisites"></a>Requisitos previos

Las herramientas de Azure Functions forman parte de la carga de trabajo de desarrollo de Azure de la [versión 15.3 de Visual Studio 2017](https://www.visualstudio.com/vs/) y las posteriores. Asegúrese de incluir la carga de trabajo de **desarrollo Azure** en la instalación de la versión 15.3 de Visual Studio 2017:

![Instalar Visual Studio 2017 con la cargas de trabajo Desarrollo de Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

>[!NOTE]  
>Después de instalar Visual Studio 2017 versión 15.3 o actualizar a esta versión, también debe actualizar manualmente las herramientas de Visual Studio 2017 para Azure Functions. Puede actualizar las herramientas del menú **Herramientas** situado bajo **Extensiones y actualizaciones...**  > **Actualizaciones** > **Visual Studio Marketplace** > **Herramientas de Azure Functions y Web Jobs** > **Actualizar**. 

Para crear e implementar funciones, también necesita:

* Una suscripción de Azure activa. Si no tiene una suscripción de Azure, hay disponibles [cuentas gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Una cuenta de Almacenamiento de Azure. Para crear una cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account).  
## <a name="create-an-azure-functions-project"></a>Creación de un proyecto de Azure Functions 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>Configuración del proyecto para el desarrollo local

Cuando crea un proyecto nuevo con la plantilla de Azure Functions, obtiene un proyecto C# vacío que contiene los archivos siguientes:

* **host.json**: permite configurar el host de Functions. Esta configuración se aplica tanto cuando se ejecuta localmente como en Azure. Para más información, consulte la [referencia sobre host.json](functions-host-json.md).
    
* **local.settings.json**: mantiene la configuración que se usa cuando se ejecutan localmente las funciones. Azure no usa estas configuraciones, sino que las usa [Azure Functions Core Tools](functions-run-local.md). Use este archivo para especificar la configuración, tal como las cadenas de conexión a otros servicios de Azure. Agregue una clave nueva a la matriz de **valores** para cada conexión que requieren las funciones de su proyecto. Para más información, consulte [Archivo de configuración local](functions-run-local.md#local-settings-file) en el tema Azure Functions Core Tools.

El entorno de tiempo de ejecución de Functions usa internamente una cuenta de Azure Storage. Para todos los tipos de desencadenadores distintos de HTTP y webhooks, debe establecer la clave **Values.AzureWebJobsStorage** en una cadena de conexión de cuenta de Azure Storage válida.

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 Para establecer la cadena de conexión de cuenta de almacenamiento:

1. En Visual Studio, abra **Cloud Explorer**, expanda **Cuenta de almacenamiento** > **Su cuenta de almacenamiento** y seleccione **Propiedades** y copie el valor **Cadena de conexión principal**.   

2. En el proyecto, abra el archivo del proyecto local.settings.json y establezca el valor de la clave **AzureWebJobsStorage** en la cadena de conexión que copió.

3. Repita el paso anterior para agregar claves únicas a la matriz de **valores** para cualquier otra conexión que requieran sus funciones.  

## <a name="create-a-function"></a>Creación de una función

En las funciones compiladas previamente, los enlaces que la función usa se definen mediante la aplicación de atributos en el código. Cuando usa Herramientas de Azure Functions para crear las funciones a partir de las plantillas proporcionadas, estos atributos se aplican automáticamente. 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nodo del proyecto y seleccione **Agregar** > **Nuevo elemento**. Seleccione **Función de Azure**, escriba un **nombre** para la clase y, luego, haga clic en **Agregar**.

2. Elija el desencadenador, establezca las propiedades de enlace y haga clic en **Crear**. En el ejemplo siguiente se muestra la configuración cuando se crea una función desencadenada de Queue Storage. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Se suministra una clave de cadena de conexión de nominada **QueueStorage**, que se define en el archivo local.settings.json. 
 
3. Examine la clase recién agregada. Verá un método **Run** estático, que cuenta con el atributo **FunctionName**. Este atributo indica que el método es el punto de entrada de la función. 

    Por ejemplo, la clase C# siguiente representa una función desencadenada de Queue Storage:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    Se aplica un atributo específico de enlace a cada parámetro de enlace que se suministra al método de punto de entrada. El atributo toma la información de enlace como parámetros. En el ejemplo anterior, el primer parámetro tiene aplicado un atributo **QueueTrigger**, que indica la función desencadenada de cola. El nombre de la configuración de cadena de conexión y el nombre de cola se pasan como parámetros.  

## <a name="testing-functions"></a>Funciones de prueba

Azure Functions Core Tools le permite ejecutar un proyecto de Azure Functions en el equipo de desarrollo local. Se le solicita que instale estas herramientas la primera vez que inicie una función de Visual Studio.  

Para probar la función, presione F5. Si se le solicita, acepte la solicitud de Visual Studio para descargar e instalar las herramientas de Azure Functions Core (CLI).  También es preciso que habilite una excepción de firewall para que las herramientas para controlen las solicitudes de HTTP.

Con el proyecto en ejecución, puede probar el código tal como probaría la función implementada. Para más información, consulte [Estrategias para probar el código en Azure Functions](functions-test-a-function.md). Cuando se ejecuta en modo de depuración, los puntos de interrupción se alcanzan en Visual Studio tal como se esperaba. 

Para un ejemplo de cómo probar una función desencadenada de cola, consulte el [tutorial de inicio rápido de la función desencadenada de cola](functions-create-storage-queue-triggered-function.md#test-the-function).  

Para más información sobre cómo usar Azure Functions Core Tools, consulte [Codificación y comprobación de las funciones de Azure en un entorno local](functions-run-local.md).

## <a name="publish-to-azure"></a>Publicación en Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>Cualquier configuración que agregue en local.settings.json debe agregarse a la aplicación de función en Azure. Esta configuración no se agrega automáticamente. Puede agregar la configuración necesaria a la aplicación de función de alguna de estas maneras:
>
>* [Uso de Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
>* [Uso de la opción de publicación de `--publish-local-settings` en Herramientas principales de Azure Functions](functions-run-local.md#publish).
>* [Uso de la CLI de Azure](/cli/azure/functionapp/config/appsettings#set). 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Herramientas de Azure Functions, consulte la sección Preguntas comunes de la entrada de blog [Herramientas de Visual Studio 2017 para Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

Para más información sobre Azure Functions Core Tools, consulte [Codificación y comprobación de las funciones de Azure en un entorno local](functions-run-local.md).  
Para más información sobre el desarrollo de funciones como bibliotecas de clases .NET, consulte [Utilizar bibliotecas de clases de .NET con Azure Functions](functions-dotnet-class-library.md). En este tema también se ofrecen ejemplos sobre cómo usar atributos para declarar varios tipos de vínculos admitidos por Azure Functions.    
