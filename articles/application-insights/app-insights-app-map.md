---
title: "Mapa de aplicación en Azure Application Insights | Microsoft Docs"
description: "Supervisión de topologías de aplicaciones complejas con el mapa de aplicación"
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
ms.openlocfilehash: 3bbed59bf93eab5e729fbdd3ccae04599ac47081
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="application-map-triage-distributed-applications"></a>Mapa de aplicación: evaluación de prioridades de aplicaciones distribuidas
El mapa de aplicación le ayuda a detectar los cuellos de botella en el rendimiento o las zonas activas con error en todos los componentes de la aplicación distribuida. Cada nodo del mapa representa un componente de aplicación o sus dependencias, e incluye el KPI de mantenimiento y el estado de alerta. Puede hacer clic a través de cualquier componente para ver un diagnóstico más detallado, como los eventos de Application Insights. Si su aplicación usa los servicios de Azure, también puede hacer clic por los diagnósticos de Azure, como las recomendaciones SQL Database Advisor.

## <a name="what-is-a-component"></a>¿Qué es un componente?

Los componentes son partes que se pueden implementar independientemente de su aplicación de microservicios o distribuida. Los equipos de operaciones y los desarrolladores pueden ver el código o acceder a la telemetría que generan estos componentes de la aplicación. 

* Los componentes son diferentes de las dependencias externas "observadas", como SQL, EventHub, etc. a las que su equipo u organización no pueden acceder (código o telemetría).
* Los componentes se ejecutan en cualquier número de instancias de rol, servidor o contenedor.
* Los componentes pueden ser claves de instrumentación de Application Insights independientes (incluso aunque las suscripciones sean diferentes) o diferentes roles que informan a una única clave de instrumentación de Application Insights. La experiencia de mapa de versión preliminar muestra los componentes con independencia de cómo están configurados.

## <a name="composite-application-map-preview"></a>Mapa de aplicación compuesta (versión preliminar)
*Esta es una primera versión preliminar y se irán agregando más características a este mapa. Nos gustaría conocer su opinión sobre la nueva experiencia. Puede cambiar fácilmente entre la experiencia clásica y de versión preliminar.*

Habilite "Mapa de aplicación compuesta" en la [lista de versiones preliminares](app-insights-previews.md) o haga clic en "Preview map" (Mapa de versión preliminar) en la opción de conmutación de la esquina superior derecha. Puede usar esta opción de conmutación para volver a la experiencia clásica.
![Habilitar el mapa de versión preliminar](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Esta versión preliminar reemplaza a la versión preliminar anterior de "Mapa de aplicación de varios roles". En este momento, úsela para ver la topología entera a lo largo de varios niveles de dependencias de componentes de aplicación. Envíenos sus comentarios. Iremos agregando más funcionalidades similares a las que admite el mapa clásico.

Puede ver la topología de aplicación completa a lo largo de varios niveles de componentes de aplicación relacionados. Los componentes podrían ser diferentes recursos de Application Insights o distintos roles de un único recurso. Para encontrar componentes, el mapa de aplicación sigue las llamadas de dependencia HTTP entre los servidores con el SDK de Application Insights instalado. 

Esta experiencia comienza con la detección progresiva de los componentes. La primera vez que carga la versión preliminar, se desencadena un conjunto de consultas para detectar los componentes relacionados con este componente. Un botón en la esquina superior izquierda se actualiza con el número de componentes de la aplicación a medida que se detectan. 
![Mapa de versión preliminar](media/app-insights-app-map/preview.png)

Al hacer clic en "Update map components" (Actualizar componentes del mapa), el mapa se actualiza con todos los componentes detectados hasta ese momento.
![Mapa de versión preliminar cargado](media/app-insights-app-map/components-loaded-hierarchical.png)

Si todos los componentes son roles dentro de un único recurso de Application Insights, este paso de detección no es necesario. La carga inicial para este tipo de aplicación tendrá todos sus componentes.

Uno de los objetivos principales de la nueva experiencia es poder visualizar topologías complejas con cientos de componentes. La nueva experiencia admite zoom y puede acercar para agregar detalles. O puede alejar para ver más componentes de un vistazo, y aun así detectar los componentes con mayores tasas de error. 

![Niveles de zoom](media/app-insights-app-map/zoom-levels.png)

Haga clic en cada componente para ver información detallada relacionada e ir a la experiencia de evaluación de prioridades de rendimiento y errores de ese componente.

![Control flotante](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Mapa de aplicación clásico

El mapa muestra:

* Pruebas de disponibilidad
* Componente del lado cliente (que se supervisa con el SDK de JavaScript)
* Componente del lado servidor
* Dependencias de los componentes cliente y servidor

![mapa de aplicación](./media/app-insights-app-map/02.png)

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

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Comentarios
Proporcione sus comentarios mediante la opción de comentarios del portal.

![Imagen MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>pasos siguientes

* [portal de Azure](https://portal.azure.com)
