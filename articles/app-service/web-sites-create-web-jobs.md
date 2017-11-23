---
title: "Ejecución de tareas en segundo plano con WebJobs en Azure App Service"
description: "Obtenga información sobre cómo usar WebJobs para ejecutar tareas en segundo plano en aplicaciones web, aplicaciones de API o aplicaciones móviles de Azure App Service."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 91839d8f547340d55f6badb3350a393a48a13c7d
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Ejecución de tareas en segundo plano con WebJobs en Azure App Service

## <a name="overview"></a>Información general
WebJobs es una característica de [Azure App Service](https://docs.microsoft.com/azure/app-service/) que le permite ejecutar un programa o script en el mismo contexto que una aplicación web, una aplicación de API o una aplicación móvil. No hay ningún coste adicional en el uso de trabajos web.

En este artículo se muestra cómo implementar WebJobs con [Azure Portal](https://portal.azure.com) para cargar un ejecutable o un script. Para información sobre cómo desarrollar e implementar WebJobs mediante Visual Studio, consulte [Implementación de WebJobs con Visual Studio](websites-dotnet-deploy-webjobs.md).

El SDK de Azure WebJobs se puede usar con WebJobs para simplificar muchas tareas de programación. Para obtener más información, consulte [¿Qué es el SDK de Webjobs?](https://github.com/Azure/azure-webjobs-sdk/wiki)

Azure Functions proporciona otra manera de ejecutar programas y scripts. Para una comparación entre WebJobs y Functions, consulte [Elección entre Flow, Logic Apps, Functions y WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de WebJob

En la tabla siguiente se describen las diferencias entre los WebJobs *continuo* y *desencadenado*.


|Continuo  |Desencadenado  |
|---------|---------|
| Se inicia inmediatamente cuando se crea el WebJob. Para evitar que el trabajo finalice, el programa o script habitualmente funciona dentro de un bucle sin fin. Si el trabajo finaliza, es posible reiniciarlo. | Se inicia solo cuando se desencadena manualmente o de acuerdo con una programación. |
| Se ejecuta en todas las instancias en que se ejecuta la aplicación web. También puede restringir el WebJob a una sola instancia. |Se ejecuta en una sola instancia que Azure selecciona para el equilibrio de carga.|
| Admite la depuración remota. | No admite la depuración remota.|

> [!NOTE]
> Una aplicación web puede agotar el tiempo de espera después de 20 minutos de inactividad. Solo las solicitudes al sitio de scm (implementación) o a las páginas de la aplicación web en el portal restablecen el temporizador. Las solicitudes al sitio real no restablecen el temporizador. Si la aplicación ejecuta WebJobs continuos o programados, habilite **AlwaysOn** para asegurarse de que los WebJobs se ejecuten de manera confiable. Esta característica solo está disponible en los [planes de tarifa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Estándar y Premium.

## <a name="acceptablefiles"></a>Tipos de archivo admitidos para scripts o programas

Se admiten los tipos de archivo siguientes:

* .cmd, .bat, .exe (con cmd de Windows)
* .ps1 (con PowerShell)
* .sh (con Bash)
* .php (con PHP)
* .py (con Python)
* .js (con Node.js)
* .jar (con Java)

## <a name="CreateContinuous"></a> Creación de un WebJob continuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. En [Azure Portal](https://portal.azure.com), vaya a la página **App Service** de la aplicación web, la aplicación de API o la aplicación móvil de App Service.

2. Seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. En la página **WebJobs**, seleccione **Agregar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use la configuración **Agregar WebJob** que se especifica en la tabla.

   ![Página Agregar WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myContinuousWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_". |
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Continuo | Los [tipos de WebJob](#webjob-types) aparecen descritos anteriormente en este artículo. |
   | **Escala** | Instancias múltiples | Solo disponible para WebJobs continuos. Determina si el programa o el script se ejecuta en todas las instancias o solo en una. La opción para que se ejecute en varias instancias no se aplica a los [planes de tarifa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratis o Compartido. | 

4. Haga clic en **Aceptar**.

   El WebJob nuevo aparece en la página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para detener o reiniciar un WebJob continuo, haga clic con el botón derecho en el WebJob en la lista y haga clic en **Detener** o **Iniciar**.

    ![Detención de un WebJob continuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Creación de un WebJob desencadenado de manera manual

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. En [Azure Portal](https://portal.azure.com), vaya a la página **App Service** de la aplicación web, la aplicación de API o la aplicación móvil de App Service.

2. Seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. En la página **WebJobs**, seleccione **Agregar**.

    ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use la configuración **Agregar WebJob** que se especifica en la tabla.

   ![Página Agregar WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myTriggeredWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_".|
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Desencadenado | Los [tipos de WebJob](#webjob-types) aparecen descritos anteriormente en este artículo. |
   | **Desencadenadores** | Manual | |

4. Haga clic en **Aceptar**.

   El WebJob nuevo aparece en la página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para ejecutar el trabajo web, haga clic en su nombre en la lista y haga clic en **Ejecutar**.
   
    ![Ejecución de WebJobs](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Creación de un WebJob programado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. En [Azure Portal](https://portal.azure.com), vaya a la página **App Service** de la aplicación web, la aplicación de API o la aplicación móvil de App Service.

2. Seleccione **WebJobs**.

   ![Selección de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. En la página **WebJobs**, seleccione **Agregar**.

   ![Página WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use la configuración **Agregar WebJob** que se especifica en la tabla.

   ![Página Agregar WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Configuración      | Valor de ejemplo   | Descripción  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myScheduledWebJob | Nombre único dentro de una aplicación de App Service. Debe comenzar con una letra o un número y no puede contener caracteres especiales salvo "-" y "_". |
   | **Carga de archivos** | ConsoleApp.zip | Archivo *ZIP* que contiene el archivo de script o el archivo ejecutable así como cualquier archivo complementario necesario para ejecutar el programa o script. Los tipos de archivo de script o de archivo ejecutable compatibles aparecen en la sección [Tipos de archivo admitidos](#acceptablefiles). |
   | **Tipo** | Desencadenado | Los [tipos de WebJob](#webjob-types) aparecen descritos anteriormente en este artículo. |
   | **Desencadenadores** | Programado | Para que la programación funcione de manera confiable, habilite la característica AlwaysOn. AlwaysOn solo está disponible en los planes de tarifa Básico, Estándar y Premium.|
   | **Expresión CRON** | 0 0/20 * * * * | Las [expresiones CRON](#cron-expressions) se describen en la sección siguiente. |

4. Haga clic en **Aceptar**.

   El WebJob nuevo aparece en la página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Expresiones CRON

Una [expresión CRON](https://en.wikipedia.org/wiki/Cron) se compone de seis campos: `{second} {minute} {hour} {day} {month} {day of the week}`.  Estos son algunos ejemplos:

* Cada 15 minutos: `0 */15 * * * *`
* Cada hora (es decir, siempre que el número de minutos sea 0): `0 0 * * * *` 
* Cada hora de 9 a 17 horas: `0 0 9-17 * * *` 
* A las 9:30 todos los días: `0 30 9 * * *`
* A las 9:30 cada día de lunes a viernes: `0 30 9 * * 1-5`

Puede escribir la expresión CRON en el portal o incluir un archivo `settings.job` en la raíz del archivo *ZIP* del WebJob, como en el ejemplo siguiente:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Cuando implemente un WebJob desde Visual Studio, marque las propiedades del archivo `settings.job` como **Copiar si es posterior**.

## <a name="ViewJobHistory"></a> Visualización del historial de trabajos

1. Seleccione el WebJob cuyo historial desea ver y, luego, seleccione el botón **Registros**.
   
   ![Botón Registros](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. En la página **Detalles del WebJob**, seleccione una hora para ver los detalles de una ejecución.
   
   ![Detalles del WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. En la página **Detalles de ejecución del WebJob**, seleccione **Alternar salida** para ver el texto del contenido del registro.
   
    ![Detalles de ejecución del trabajo web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver el texto de salida en otra ventana del explorador, seleccione **Descargar**. Para descargar el texto propiamente tal, haga clic con el botón derecho en **Descargar** y use las opciones del explorador para guardar el contenido del archivo.
   
5. Seleccione el vínculo de la ruta de navegación de los **WebJobs** que se encuentra en la parte superior de la página para ir a una lista de los WebJobs.

    ![Ruta de navegación de WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de WebJobs el panel del historial](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Pasos siguientes

El SDK de Azure WebJobs se puede usar con WebJobs para simplificar muchas tareas de programación. Para obtener más información, consulte [¿Qué es el SDK de Webjobs?](https://github.com/Azure/azure-webjobs-sdk/wiki)
