---
title: "Diagnóstico de problemas en tiempo de ejecución mediante Azure Application Insights | Microsoft Docs"
description: "Tutorial para buscar y diagnosticar excepciones en tiempo de ejecución en un aplicación mediante Azure Application Insights."
services: application-insights
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 58c69e9c75f3a97c04b2c5b2e3b9acfc7065c658
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Búsqueda y diagnóstico de excepciones en tiempo de ejecución con Azure Application Insights

Azure Application Insights recopila datos de telemetría de cualquier aplicación para ayudarle a identificar y diagnosticar excepciones en tiempo de ejecución.  Este tutorial le guiará por este proceso en la aplicación.  Aprenderá a:

> [!div class="checklist"]
> * Modifica el proyecto para habilitar el seguimiento de excepciones
> * Identificar excepciones en los distintos componentes de una aplicación
> * Ver los detalles de una excepción
> * Descargar una instantánea de la excepción en Visual Studio para su depuración
> * Analizar los detalles de las solicitudes con errores mediante un lenguaje de consulta
> * Crear un nuevo elemento de trabajo para corregir el código con errores


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
    - ASP.NET y desarrollo web
    - Desarrollo de Azure
- Descargue e instale [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger).
- Implemente una aplicación de .NET en Azure y [habilite el SDK de Application Insights](app-insights-asp-net.md). 
- El tutorial realiza un seguimiento de la identificación de una excepción en la aplicación, así que modifique el código en los entornos de desarrollo o prueba para generar una excepción. 

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Análisis de errores
Application Insights recopila los errores de la aplicación y permite ver su frecuencia en las diferentes operaciones, lo que le ayudarán a centrar sus esfuerzos en los que tienen el mayor impacto.  Más adelante puede profundizar en los detalles de dichos errores para identificar la causa principal.   

1. Seleccione **Application Insights** y, después, la suscripción.  
1. Para abrir el panel **Errores**, seleccione **Errores** en el menú **Investigar** o haga clic en el grafo **Solicitudes con errores**.

    ![Error en las solicitudes](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. El panel **Solicitudes con errores** muestra el número de solicitudes con errores y el número de usuarios afectados para cada operación en la aplicación.  Si ordenar esta información por usuario, puede identificar los errores que más afectan a los usuarios.  En este ejemplo, **GET Employees/Create** y **GET Customers/Details** son probables candidatos a los que investigar debido al gran número de errores y a la gran cantidad de usuarios afectados.  Al seleccionar cualquier operación, se muestra más información acerca de la misma en el panel derecho.

    ![Panel Solicitudes con errores](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Reduzca la ventana de tiempo para acercar el periodo en el que la frecuencia de errores muestra un pico.

    ![Ventana Solicitudes con errores](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Haga clic en **Ver detalles** para ver los detalles de la operación.  Aquí se incluye un gráfico de Gantt que muestra dos dependencias con errores que, en conjunto, tardaron casi medio segundo en completarse.  Para encontrar más información acerca del análisis de problemas de rendimiento, complete el tutorial [Búsqueda y diagnóstico de problemas de rendimiento con Azure Application Insights](app-insights-tutorial-performance.md).

    ![Detalles de solicitudes con errores](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. Los detalles de las operaciones también muestran la excepción FormatException, que parece que es lo que ha generado el error.  Haga clic en la excepción o en **Top 3 exception types** (Tres tipos de excepción más habituales) para ver sus detalles.  Puede ver que se debe a un código postal no válido.

    ![Detalles de la excepción](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Identificación de código con errores
Snapshot Debugger recopila instantáneas de las excepciones que se producen con más frecuencia en la aplicación para ayudarle a diagnosticar su causa principal en producción.  Puede ver las instantáneas de depuración en el portal para examinar la pila de llamadas e inspeccionar las variables en cada marco de pila de llamadas. Posteriormente, puede depurar el código fuente, para lo que debe descargar la instantánea y abrirla en Visual Studio 2017.

1. En las propiedades de la excepción, haga clic en **Abrir instantánea de depuración**.
2. Se abre el panel **Depurar instantánea** con la pila de llamadas de la solicitud.  Haga clic en cualquiera de los métodos para ver los valores de todas las variables locales en el momento de la solicitud.  Si empezamos por el primero de los métodos de este ejemplo, podemos ver variables locales que no tienen ningún valor.

    ![Depurar instantánea](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. La primera llamada que tiene valores válidos es **ValidZipCode** y podemos ver que se especificó un código postal con letras que no se puede convertir en un número entero.  Parece que este es el error que hay en el código y que debe corregirse.

    ![Depurar instantánea](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Para descargar esta instantánea en Visual Studio, donde se puede buscar el código que se debe corregir, haga clic en **Descargar instantáneas**.
6. La instantánea se carga en Visual Studio.
7. A partir de ese momento se puede ejecutar una sesión de depuración en Visual Studio que identifique rápidamente la línea de código que provocó la excepción.

    ![Excepción en el código](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Uso de datos de análisis
Todos los datos que recopila Application Insights se almacenan en Azure Log Analytics, lo que proporciona un lenguaje de consulta completo que permite analizar los datos de varias formas.  Dichos datos se pueden usar para analizar las solicitudes que generaron la excepción que investigamos. 

8. Haga clic en la información de CodeLens que encontrará encima del código para ver los datos de telemetría que proporciona Application Insights.

    ![Código](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Haga clic en **Analizar impacto** para abrir Application Insights Analytics.  Contiene varias consultas que proporcionan detalles acerca de las solicitudes con errores, como los usuarios afectados, los exploradores usados y las regiones.<br><br>![Analytics](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Incorporación de elemento de trabajo
Si conecta Application Insights a un sistema de seguimiento, como GitHub y Visual Studio Team Services, puede crear un elemento de trabajo directamente desde Application Insights.

1. Vuelva al panel **Exception Properties** (Propiedades de la excepción) de Application Insights.
2. Haga clic en **Nuevo elemento de trabajo**.
3. Se abre el panel **Nuevo elemento de trabajo** con detalles acerca la excepción ya rellenados.  Además, antes de guardarlo puede agregar toda la información adicional que desee.

    ![Nuevo elemento de trabajo](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a identificar las excepciones en tiempo de ejecución, pase al siguiente tutorial, donde aprenderá a identificar y diagnosticar problemas de rendimiento.

> [!div class="nextstepaction"]
> [Identificar problemas de rendimiento](app-insights-tutorial-performance.md)
