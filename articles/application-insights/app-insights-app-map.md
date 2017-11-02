---
title: "Mapa de aplicación en Azure Application Insights | Microsoft Docs"
description: "Una presentación visual de las dependencias entre los componentes de la aplicación, con etiquetas para KPI y alertas."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: e1eb2177d6032142781e6e31af6c7f6313d38f4d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="application-map-in-application-insights"></a>Mapa de aplicación en Application Insights
En [Azure Application Insights](app-insights-overview.md), el mapa de aplicación es un diseño visual de las relaciones de dependencia de los componentes de aplicación. Cada componente muestra indicadores clave de rendimiento (KPI), como carga, rendimiento, errores y alertas, para ayudarle a detectar los componentes que provocan problemas o errores de rendimiento. Puede hacer clic a través de cualquier componente para ver un diagnóstico más detallado, como los eventos de Application Insights. Si su aplicación usa los servicios de Azure, también puede hacer clic por los diagnósticos de Azure, como las recomendaciones SQL Database Advisor.

Al igual que otros gráficos, puede anclar un mapa de aplicación al panel de Azure, donde es totalmente funcional. 

## <a name="open-the-application-map"></a>Apertura del mapa de aplicación
Abra el mapa desde la hoja de información general de su aplicación:

![abrir el mapa de aplicación](./media/app-insights-app-map/01.png)

![mapa de aplicación](./media/app-insights-app-map/02.png)

El mapa muestra:

* Pruebas de disponibilidad
* Componente del lado cliente (que se supervisa con el SDK de JavaScript)
* Componente del lado servidor
* Dependencias de los componentes cliente y servidor

Puede expandir y contraer los grupos de vínculos de dependencia:

![contraer](./media/app-insights-app-map/03.png)

Si tiene un gran número de dependencias de un tipo (SQL, HTTP, etc.), aparecen agrupadas. 

![dependencias agrupadas](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Detección de problemas
Cada nodo cuenta con indicadores de rendimiento relacionados, como los porcentajes de error, rendimiento y carga de ese componente. 

Los iconos de advertencia resaltan los posibles problemas. Una advertencia naranja significa que hay errores en las solicitudes, las vistas de página o las llamadas de dependencia. Rojo significa un porcentaje de error superior al 5 %. Si quiere ajustar estos umbrales, abra Opciones.

![iconos de error](./media/app-insights-app-map/04.png)

También se muestran las alertas activas: 

![alertas activas](./media/app-insights-app-map/05.png)

Si usa SQL Azure, hay un icono que se muestra cuando hay recomendaciones sobre cómo mejorar el rendimiento. 

![recomendación de Azure](./media/app-insights-app-map/06.png)

Haga clic en cualquier icono para más detalles:

![recomendación de Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Recorrido por los diagnósticos mediante clic
Cada uno de los nodos del mapa ofrece un recorrido dirigido mediante clic por la información de diagnóstico. Las opciones varían según el tipo del nodo.

![opciones de servidor](./media/app-insights-app-map/09.png)

En el caso de los componentes que se hospedan en Azure, las opciones incluyen vínculos directos a ellos.

## <a name="filters-and-time-range"></a>Filtros e intervalo de tiempo
De forma predeterminada, el mapa resume todos los datos disponibles para el intervalo de tiempo seleccionado. Sin embargo, puede filtrar para incluir solo nombres de operaciones o dependencias específicos.

* Nombre de la operación: incluye vistas de página y tipos de solicitud del lado servidor. Con esta opción, el mapa muestra el KPI en el nodo del lado cliente o servidor solo de las operaciones seleccionadas. Muestra las dependencias que se invocan en el contexto de esas operaciones específicas.
* Nombre base de dependencia: incluye las dependencias del explorador de AJAX y las dependencias del lado servidor. Si utiliza la API TrackDependency para notificar telemetría de dependencias personalizada, esta información también se muestra aquí. Puede seleccionar las dependencias que se mostrarán en el mapa. Actualmente esta selección no filtra las solicitudes del lado servidor ni las vistas de página del lado cliente.

![Establecer filtros](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Guardado de los filtros
Para guardar los filtros aplicados, ancle la vista filtrada a un [panel](app-insights-dashboards.md).

![Anclar al panel](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Panel de errores
Al hacer clic en un nodo del mapa, se muestra un panel de error se muestra en el lado derecho con un resumen de los errores de ese nodo. Los errores se agrupan en primer lugar por identificador de operación y, luego, por identificador de problema.

![Panel de errores](./media/app-insights-app-map/error-pane.png)

Al hacer clic en un error, se le redirige a la instancia más reciente de ese error.

## <a name="resource-health"></a>Estado de los recursos
En algunos tipos de recursos, se muestra el estado en la parte superior del panel de errores. Por ejemplo, al hacer clic en un nodo de SQL se mostrará el estado de la base de datos y las alertas activadas.

![Estado de los recursos](./media/app-insights-app-map/resource-health.png)

Puede hacer clic en el nombre del recurso para ver las métricas de información general estándar de ese recurso.

## <a name="end-to-end-system-app-maps"></a>Mapas de la aplicación del sistema completo

*Se requiere la versión 2.3 o posterior del SDK*

Si la aplicación tiene varios componentes, por ejemplo, un servicio back-end además de la aplicación web, puede mostrarlos todos en un mapa de la aplicación integrado.

![Establecer filtros](./media/app-insights-app-map/multi-component-app-map.png)

El mapa de aplicación busca los nodos de servidor siguiendo las llamadas de una dependencia HTTP entre los servidores con el SDK de Application Insights instalado. Se da por hecho que cada recurso de Application Insights contiene un servidor.

### <a name="multi-role-app-map-preview"></a>Mapa de aplicación de varios roles (versión preliminar)

La característica de mapa de aplicación de varios roles en versión preliminar permite usar el mapa de aplicación con varios servidores que envían datos al mismo recurso o la misma clave de instrumentación de Application Insights. Los servidores del mapa están segmentados por la propiedad cloud_nombreDelRol en elementos de telemetría. Establezca el *mapa de aplicación de varios roles* en *Activado* en la hoja de versiones preliminares para habilitar esta configuración.

Este enfoque puede ser útil en una aplicación de microservicios o en otros escenarios en los que desea correlacionar eventos de varios servidores de un único recurso de Application Insights.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Comentarios
Proporcione sus comentarios mediante la opción de comentarios del portal.

![Imagen MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Pasos siguientes

* [Portal de Azure](https://portal.azure.com)
