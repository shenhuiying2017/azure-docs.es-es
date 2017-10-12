---
title: Microsoft Dynamics CRM y Azure Application Insights | Microsoft Docs
description: "Obtenga la telemetría de Microsoft Dynamics CRM Online con Application Insights. Tutorial sobre configuración, obtención de datos, visualización y exportación."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: bwren
ms.openlocfilehash: a9593d5f198e05db80451a599428a296ed02e781
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Tutorial: Habilitar la telemetría para Microsoft Dynamics CRM Online con Application Insights
En este artículo se muestra cómo obtener datos de telemetría de [Microsoft Dynamics CRM Online](https://www.dynamics.com/) con [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Analizaremos el proceso completo para agregar scripts de Application Insights a la aplicación, capturar datos y visualizar datos.

> [!NOTE]
> [Examine la solución de ejemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Incorporación de Application Insights a la instancia de CRM Online nueva o existente
Para supervisar la aplicación, agregue un SDK de Application Insights a la aplicación. El SDK envía telemetría al [portal de Application Insights](https://portal.azure.com), donde puede usar nuestras potentes herramientas de análisis y diagnóstico o exportar los datos al almacenamiento.

### <a name="create-an-application-insights-resource-in-azure"></a>Creación de un recurso de Application Insights en Azure
1. Obtenga una [cuenta en Microsoft Azure](http://azure.com/pricing). 
2. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y agregue un nuevo recurso de Application Insights. Aquí es donde se procesarán y mostrarán los datos.
   
    ![Haga clic en +, Servicios para desarrolladores, Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Elija ASP.NET como el tipo de aplicación.
3. Abra la página de inicio y abra "Supervisar y diagnosticar la aplicación del lado cliente".
   
    ![Fragmento de código para inserción en la página web](./media/app-insights-sample-mscrm/03.png)

**Mantenga abierta la página de códigos** mientras lleva a cabo el siguiente paso en otra ventana del explorador. Necesitará el código pronto. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Creación de un recurso web de JavaScript en Microsoft Dynamics CRM
1. Abra la instancia de CRM Online e inicie sesión con privilegios de administrador.
2. Abra Configuración de Microsoft Dynamics CRM, personalizaciones, personalizar el sistema
   
    ![Configuración de Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/04.png)
   
    ![Configuración > Personalizaciones](./media/app-insights-sample-mscrm/05.png)

    ![Personalizar la opción del sistema](./media/app-insights-sample-mscrm/06.png)

1. Cree un recurso de JavaScript.
   
    ![Cuadro de diálogo de nuevo recurso web](./media/app-insights-sample-mscrm/07.png)
   
    Asígnele un nombre, seleccione **Script (JScript)** y abra el editor de texto.
   
    ![Abrir el editor de texto](./media/app-insights-sample-mscrm/08.png)
2. Copie el código de Application Insights. Al copiar, asegúrese de que ignora las etiquetas de script. Consulte la siguiente captura de pantalla:
   
    ![Establecer la clave de instrumentación](./media/app-insights-sample-mscrm/09.png)
   
    El código contiene la clave de instrumentación que identifica al recurso de Application Insights.
3. Guarde y publique.
   
    ![Guardar y publicar](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formularios de instrumentos
1. En Microsoft CRM Online, abra el formulario Cuenta.
   
    ![Formulario de la cuenta](./media/app-insights-sample-mscrm/11.png)
2. Abra el formulario Propiedades.
   
    ![Propiedades del formulario](./media/app-insights-sample-mscrm/12.png)
3. Agregue el recurso web de JavaScript que ha creado.
   
    ![Menú Agregar](./media/app-insights-sample-mscrm/13.png)
   
    ![Agregar el recurso web](./media/app-insights-sample-mscrm/14.png)
4. Guarde y publique las personalizaciones de formulario.

## <a name="metrics-captured"></a>Métricas capturadas
Ya ha configurado la captura de telemetría para el formulario. Siempre que se utilice, los datos se enviarán al recurso de Application Insights.

A continuación se ofrecen ejemplos de los datos que verá.

#### <a name="application-health"></a>Estado de la aplicación
![Ejemplo de tiempo de carga de página](./media/app-insights-sample-mscrm/15.png)

![Ejemplo de gráfico de vistas de página](./media/app-insights-sample-mscrm/16.png)

Excepciones de explorador:

![Gráfico de excepciones de explorador](./media/app-insights-sample-mscrm/17.png)

Haga clic en el gráfico para obtener más detalles.

![Lista de excepciones](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso
![Sesiones, usuarios y vistas de página](./media/app-insights-sample-mscrm/19.png)

![Gráficos de sesión](./media/app-insights-sample-mscrm/20.png)

![Versiones de explorador](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Exploradores
![Desglose de tiempo de carga de página](./media/app-insights-sample-mscrm/22.png)

![Recuento de sesiones por versión de explorador](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalización
![Recuento de sesiones por país](./media/app-insights-sample-mscrm/24.png)

![Sesiones y usuarios por país](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitud de la vista de página interior
![Resumen de vistas de página](./media/app-insights-sample-mscrm/26.png)

![Buscar en eventos de vista de página](./media/app-insights-sample-mscrm/27.png)

![Vistas de página similares](./media/app-insights-sample-mscrm/28.png)

![Propiedades de la vista de página](./media/app-insights-sample-mscrm/29.png)

![Páginas por sesión](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Código de ejemplo
[Examine el código de ejemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Puede realizar un análisis todavía más exhaustivo si [exporta los datos a Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solución de ejemplo de Microsoft Dynamics CRM
[A continuación se muestra la solución de ejemplo implementada en Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Más información
* [¿Qué es Application Insights?](app-insights-overview.md)
* [Application Insights para páginas web](app-insights-javascript.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)
