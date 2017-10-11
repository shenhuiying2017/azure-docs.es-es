---
title: "No se encontró ningún grupo de conectores en funcionamiento para una aplicación de proxy de aplicación | Microsoft Docs"
description: "Solucione los problemas que puedan surgir cuando no haya ningún conector en funcionamiento en un grupo de conectores para su aplicación con el proxy de aplicación de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4945958deedc8a1d9989ff901192c03a5363b4dc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>No se encontró ningún grupo de conectores en funcionamiento para una aplicación de proxy de aplicación

Este artículo lo ayuda a solucionar problemas habituales que surgen cuando no se detecta ningún conector para una aplicación de proxy de aplicación integrada con Azure Active Directory.

## <a name="overview-of-steps"></a>Introducción a los pasos
Si no hay ningún conector en funcionamiento en un grupo de conectores para la aplicación, hay varias maneras de resolver el problema:

-   Si no tiene ningún conector en el grupo, puede hacer lo siguiente:

    -   Descargue un nuevo conector en el servidor local correcto y asígnelo a este grupo.

    -   Mueva un conector activo al grupo.

-   Si no tiene ningún conector activo en el grupo, puede hacer lo siguiente:

    -   Identifique el motivo por el que el conector está inactivo y resuélvalo.

    -   Mueva un conector activo al grupo.

Para saber cuál de estos es el problema, abra el menú "Proxy de la aplicación" en la aplicación y vea el mensaje de advertencia del grupo de conectores. Especifica si el grupo necesita al menos un conector (no hay ninguno en el grupo) o si no tiene ninguno activo (es probable que tenga conectores inactivos).

   ![Selección de grupos de conectores en Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Para ver información detallada sobre cada una de estas opciones, consulte la sección correspondiente a continuación. En cada una, se da por supuesto que empieza desde la página de administración del conector. Si ve el mensaje de error anterior, puede ir a esta página si hace clic en el mensaje de advertencia. De lo contrario, para encontrarlo, vaya a **Azure Active Directory** y haga clic en **Aplicaciones empresariales** y en **Proxy de la aplicación**.

   ![Administración de grupos de conectores en Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Descarga de un nuevo conector

Para descargar un conector nuevo, use el botón "Descargar conector" en la parte superior de la página.

Tenga en cuenta que el conector debe instalarse en una máquina con línea de visión directa a la aplicación back-end y se suele colocar en el mismo servidor que la aplicación. Después de la descarga, el conector debería aparecer en este menú. Haga clic en el conector y compruebe en la lista desplegable "Grupo de conectores" que pertenece al grupo adecuado. Guarde el cambio.

   ![Descarga del conector de Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Movimiento de un conector activo

Si tiene un conector activo que debería pertenecer a este grupo y tiene línea de visión a la aplicación back-end de destino, puede moverlo al grupo asignado. Para ello, haga clic en el conector. En el campo "Grupo de conectores", seleccione el grupo correcto en la lista desplegable y haga clic en Guardar.

## <a name="resolve-an-inactive-connector"></a>Resolución de un conector inactivo

Si los únicos conectores del grupo están inactivos, es probable que estén en una máquina donde no están desbloqueados todos los puertos necesarios.

Consulte el documento de solución de problemas de puertos para más información sobre cómo investigar este problema.

## <a name="next-steps"></a>Pasos siguientes
[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)


