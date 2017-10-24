---
title: "Novedades de las colección de áreas de trabajo de Power BI"
description: "Obtenga la información más reciente sobre las novedades en las colecciones de áreas de trabajo de Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: a2faf610ca50acdb54353ade7c7a4ecabd314347
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Novedades de las colección de áreas de trabajo de Power BI

De forma periódica se publican actualizaciones a las **colecciones de áreas de trabajo de Power BI**. Sin embargo, no todas las versiones incluirán nuevas características de cara al usuario; algunas se centran en las funcionalidades del servicio back-end. Analizamos aquí las nuevas funcionalidades para el usuario.

> [!IMPORTANT]
> Las colecciones de áreas de trabajo de Power BI están en desuso y estarán disponibles hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de Colección de áreas de trabajo de Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Marzo de 2017

**Funcionalidades de autoservicio**

* [Creación de un informe nuevo](create-report-from-dataset.md)
* [Informe SaveAs](save-reports.md)
* Insertar informe en modo de lectura/edición/creación de nuevo 
* [Toggle report between edit/read modes](toggle-mode.md) (Alternancia de informe entre los modos de edición/lectura)

**Conectividad a datos con las API de REST**

* [Creación de un conjunto de datos](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Inserción de datos 

**API de administración**

* Clonación de informe y conjunto de datos
* Enlace de un informe a otro conjunto de datos

**Ejemplos**

* Actualización del [ejemplo de inserción de informe de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Diciembre de 2016

* [Nuevo ejemplo de inserción de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Octubre de 2016

* [Análisis avanzado con colecciones de área de trabajo de Power BI y R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 de agosto de 2016
En esta versión se incluyen:

* Todos los nuevos SDK de JavaScript que admita el [filtro avanzado y la exploración de páginas](interact-with-reports.md).
* Las colecciones de áreas de trabajo de Power BI ahora son compatibles con el centro de datos de Canadá Central. Compruebe el [estado del centro de datos](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 de julio de 2016
En esta versión se incluyen:

* **Buenas noticias** El servicio Colección de áreas de trabajo de Power BI ya no está en versión preliminar: ahora ya está disponible de manera general.  
* Todas las API de REST han pasado de **/beta** a **/v1.0**.
* Los SDK de JavaScript y .NET se han actualizado a **v1.0**.
* Ahora se pueden autenticar las llamadas de API de BI Power directamente mediante claves de API. Los tokens de aplicación solo se necesitan para la inserción. Como parte de esto, los tokens de aprovisionamiento y desarrollo han quedado en desuso en la versión v1.0 API, pero seguirán funcionando en la versión beta hasta el 30 de diciembre de 2016. Para más información, consulte [Authenticating and authorizing with Power BI Workspace Collections](app-token-flow.md) (Autenticación y autorización en las colecciones de áreas de trabajo de Power BI).
* Compatibilidad de la seguridad de nivel de fila (RLS) con tokens de aplicación e informes insertados. Para aprender más, consulte [Seguridad de nivel de fila de las colecciones de áreas de trabajo de Power BI](row-level-security.md).
* Aplicación de ejemplo actualizada para todas las llamadas de API **v1.0** .
* Compatibilidad de las colecciones de áreas de trabajo de Power BI con SDK de Azure, PowerShell y CLI.
* Los usuarios pueden exportar datos de visualización a un archivo **.csv**.
* Las colecciones de áreas de trabajo de Power BI ahora son compatibles con los mismos idiomas o configuraciones regionales que Microsoft Azure. Para obtener más información, consulte [Azure - Idiomas](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

