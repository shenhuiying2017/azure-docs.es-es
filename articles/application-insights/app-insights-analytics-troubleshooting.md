---
title: "Diagnóstico de problemas de Analytics de Azure Application Insights | Microsoft Docs"
description: "¿Problemas con Analytics de Application Insights? Comience aquí. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: 3ef9150cad62e9f9fc43f7afcbbbc01d89884a12
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Solución de problemas de Analytics en Application Insights
¿Problemas con [Analytics de Application Insights](app-insights-analytics.md)? Comience aquí. Analytics es la herramienta eficaz de búsqueda de Application Insights.

## <a name="limits"></a>límites
* En la actualidad, los resultados de consulta están limitados exclusivamente a solo una semana de datos antiguos.
* Exploradores en los que la hemos probado: últimas ediciones de Chrome, Edge e Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Extensiones de explorador incompatibles conocidas
* Ghostery

Deshabilite la extensión o use un explorador diferente.

## <a name="e-a"></a> "Error inesperado"
![Pantalla de error inesperado](./media/app-insights-analytics-troubleshooting/010.png)

Error interno durante el tiempo de ejecución del portal: excepción no controlada.

* Limpie la caché del explorador. 

## <a name="e-b"></a>403... intente volver a cargar
![403... intente volver a cargar](./media/app-insights-analytics-troubleshooting/020.png)

Se ha producido un error relacionado con la autenticación (durante la autenticación o durante la generación del token de acceso). Puede que el portal no tenga forma de recuperarse sin cambiar la configuración del explorador.

* Compruebe que las [cookies de terceros estén habilitadas](#cookies) en el explorador. 

## <a name="authentication"></a>403... compruebe la zona de seguridad
![403... compruebe la zona de seguridad](./media/app-insights-analytics-troubleshooting/030.png)

Se ha producido un error relacionado con la autenticación (durante la autenticación o durante la generación del token de acceso). Puede que el portal no tenga forma de recuperarse sin cambiar la configuración del explorador.

1. Compruebe que las [cookies de terceros estén habilitadas](#cookies) en el explorador. 
2. ¿Ha utilizado un favorito, un marcador o un vínculo guardado para abrir el portal de Analytics? ¿Ha iniciado sesión con credenciales diferentes a las que utilizó cuando guardó el vínculo?
3. Pruebe a usar una ventana de exploración privada o de incógnito (después de cerrar todas estas ventanas). Tendrá que proporcionar sus credenciales. 
4. Abra otra ventana de explorador (ordinaria) y vaya a [Azure](https://portal.azure.com). Cierre la sesión. A continuación, abra el vínculo e inicie sesión con las credenciales correctas.
5. Los usuarios de Edge y de Internet Explorer también pueden recibir este error cuando no se admite la configuración de zona Sitios de confianza.
   
    Compruebe que tanto el [portal de Analytics](https://analytics.applicationinsights.io) como el [portal de Azure Active Directory](https://portal.azure.com) se encuentren en la misma zona de seguridad:
   
   * En Internet Explorer, abra **Opciones de Internet**, **Seguridad**, **Sitios de confianza**, **Sitios**:
     
     ![Cuadro de diálogo Opciones de Internet, agregar un sitio a Sitios de confianza](./media/app-insights-analytics-troubleshooting/033.png)
     
     En la lista de sitios web, si se incluye alguna de las siguientes direcciones URL, asegúrese de que las otras se incluyan también:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Recurso no encontrado
![404 ... recurso no encontrado](./media/app-insights-analytics-troubleshooting/040.png)

Los recursos de la aplicación se eliminaron de Application Insights y ya no están disponibles. Esto puede suceder si guardó la dirección URL en la página de Analytics.

## <a name="e-e"></a>403 ... Sin autorización
![403 ... no autorizado](./media/app-insights-analytics-troubleshooting/050.png)

No tiene permiso para abrir esta aplicación en Analytics.

* ¿Recibió el vínculo de otra persona? Pregúnteles para asegurarse de que se encuentra en los roles de [lector o colaborador en este grupo de recursos](app-insights-resources-roles-access-control.md).
* ¿Guardó el vínculo mediante credenciales diferentes? Abra el [Portal de Azure](https://portal.azure.com), cierre sesión y vuelva a probar este vínculo con las credenciales correctas.

## <a name="html-storage"></a>403 ... Almacenamiento de HTML5
Nuestro portal utiliza localStorage y sessionStorage de HTML5.

* Chrome: Configuración, Privacidad, Configuración de contenido.
* Internet Explorer: Opciones de Internet, pestaña Opciones avanzadas, Seguridad, Habilitar el almacenamiento DOM

![403... pruebe a habilitar el almacenamiento de HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Suscripción no encontrada
![404 ... Suscripción no encontrada](./media/app-insights-analytics-troubleshooting/070.png)

La dirección URL no es válida. 

* Abra el recurso de aplicación en el [portal de Application Insights](https://portal.azure.com). A continuación, utilice el botón de Analytics.

## <a name="e-h"></a>404... la página no existe
![404 ... La página no existe](./media/app-insights-analytics-troubleshooting/080.png)

La dirección URL no es válida.

* Abra el recurso de aplicación en el [portal de Application Insights](https://portal.azure.com). A continuación, utilice el botón de Analytics.

## <a name="cookies"></a>Habilitar cookies de terceros
  Vea [How to disable third-party cookies in all major web browsers](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers)(Cómo deshabilitar las cookies de terceros en todos los principales exploradores), pero tenga en cuenta que es necesario **habilitarlas** .


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

