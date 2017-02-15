---
title: "Introducción a las pruebas en producción para las aplicaciones web"
description: "Obtenga información acerca de la característica de prueba en producción (TiP) en Aplicaciones web del Servicio de aplicaciones."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 4623468d-886e-4203-8012-8f86deb2790b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f9d423e87938e55daeb07268b11c930fb62755b6
ms.openlocfilehash: 8fa7ca18fd2ea3a3f672854a0b2e0397cfdbb2bc


---
# <a name="get-started-with-test-in-production-for-web-apps"></a>Introducción a las pruebas en producción para las aplicaciones web
Las pruebas en producción o pruebas en directo de una aplicación web usando el tráfico de clientes, es una estrategia de prueba que los desarrolladores de aplicaciones integran cada vez más en sus metodologías de [desarrollo ágil](https://en.wikipedia.org/wiki/Agile_software_development) . Esta estrategia le permite probar la calidad de sus aplicaciones con un tráfico de usuarios activos en el entorno de producción, en lugar de con datos sintetizados en un entorno de prueba. Al exponer su nueva aplicación a usuarios reales, tiene la posibilidad de recibir información sobre problemas reales a los que se puede enfrentar la aplicación una vez que se implemente. Puede comprobar la funcionalidad, el rendimiento y el valor de las actualizaciones de la aplicación con el volumen, la velocidad y la variedad del tráfico real de usuarios , algo a lo que un entorno de prueba no llega a aproximarse.

## <a name="traffic-routing-in-app-service-web-apps"></a>Enrutamiento de tráfico en las aplicaciones web del Servicio de aplicaciones
Con la característica de enrutamiento del tráfico de [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), puede dirigir una parte del tráfico de usuario activo a una o varias [ranuras de implementación](web-sites-staged-publishing.md) y, después, analizar su aplicación con [Azure Application Insights](/services/application-insights/) o [Azure HDInsight](/services/hdinsight/), o con una herramienta de terceros como [New Relic](/marketplace/partners/newrelic/newrelic/) para validar el cambio. Por ejemplo, con el Servicio de aplicaciones puede implementar los siguientes escenarios:

* Detectar errores funcionales o identificar cuellos de botella en el rendimiento de las actualizaciones antes de realizar su implementación en todo el sitio
* Realizar "vuelos de prueba controlados" de los cambios realizando la medición de las métricas de facilidad de uso en la versión beta de la aplicación
* Cambiar gradualmente a una nueva actualización y volver sin problemas a la versión actual si se produce un error 
* Optimizar los resultados empresariales de su aplicación mediante la ejecución de [pruebas A/B](https://en.wikipedia.org/wiki/A/B_testing) o [pruebas sobre múltiples variables](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) en varias ranuras de implementación

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Requisitos para usar el enrutamiento del tráfico en las aplicaciones web
* La aplicación web se debe ejecutar en los niveles **Estándar** o **Premium**, ya que es lo que se requiere cuando hay varias ranuras de implementación.
* Para que funcione correctamente, el enrutamiento del tráfico las cookies tienen que estar habilitadas en el explorador del usuario. El enrutamiento de tráfico usa cookies para anclar un explorador cliente a una ranura de implementación mientras dura la sesión del cliente.
* El enrutamiento de tráfico admite escenarios avanzados de TiP mediante cmdlets de PowerShell de Azure.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Enrutamiento de un segmento de tráfico a una ranura de implementación
En el nivel básico en todas las situaciones TiP, se enruta un porcentaje predefinido del tráfico directo a una ranura de implementación que no sea de producción. Para ello, siga estos pasos:

> [!NOTE]
> En estos pasos se asume que ya tiene una [ranura de implementación que no es de producción](web-sites-staged-publishing.md) y que el contenido de la aplicación web deseado ya está [implementado](web-sites-deploy.md) en ella.
> 
> 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En la hoja de la aplicación web, haga clic en **Configuración** > **Enrutamiento de tráfico**.
   ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Seleccione la ranura a la que desea enrutar el tráfico y el porcentaje del tráfico total que desee, haga clic en **Guardar**.
   
    ![](./media/app-service-web-test-in-production/02-select-slot.png)
4. Vaya la hoja de la ranura de implementación. Debería ver tráfico real que se enruta hacia la ranura.
   
    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Una vez configurado el enrutamiento de tráfico, el porcentaje especificado de clientes se enrutará al azar a la ranura que no es de producción. De cualquier forma, es importante tener en cuenta que una vez que se enruta automáticamente a un cliente a una ranura específica, este cliente permanecerá "fijo" en esa ranura mientras dure su sesión. Esta operación se realiza mediante una cookie que ancla la sesión de usuario. Si inspecciona las solicitudes HTTP, encontrará una cookie `TipMix` en cada solicitud posterior.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Forzamiento de las solicitudes de cliente a una ranura específica
Además del enrutamiento automático del tráfico, el Servicio de aplicaciones puede enrutar solicitudes a una ranura específica. Esto es útil cuando desee que los usuarios puedan escoger el enrutamiento a la aplicación beta, o la exclusión voluntaria de la misma. Para ello use el parámetro de consulta `x-ms-routing-name` .

Para redirigir usuarios a una ranura específica mediante `x-ms-routing-name`, tiene que asegurarse de que la ranura se ha agregado a la lista de Enrutamiento de tráfico. Dado que desea enrutar a una ranura de forma explícita, no importa el porcentaje de enrutamiento que se establece. Si lo desea puede crear un "vínculo beta" en el que los usuarios pueden hacer clic para acceder a la aplicación beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Exclusión voluntaria de la aplicación beta para los usuarios
Para permitir que los usuarios opten por la exclusión del enrutamiento a la aplicación beta, por ejemplo, puede poner este vínculo en la página web:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

La cadena `x-ms-routing-name=self` especifica la ranura de producción. Una vez que el explorador del cliente accede al vínculo, no solo se redirige a la zona de producción, sino que cada solicitud posterior contendrá la cookie `x-ms-routing-name=self` que ancla la sesión a la ranura de producción.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Selección voluntaria de la aplicación beta para los usuarios
Para permitir a los usuarios participar en la aplicación de la versión beta, establezca el mismo parámetro de consulta con el nombre de la ranura que no es de producción, por ejemplo:

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Más recursos
* [Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md)
* [Implementación predecible de una aplicación compleja en Azure](app-service-deploy-complex-application-predictably.md)
* [Agile Software Development con el Servicio de aplicaciones de Azure](app-service-agile-software-development.md)
* [Uso eficaz de entornos DevOps para las aplicaciones web](app-service-web-staged-publishing-realworld-scenarios.md)




<!--HONumber=Dec16_HO2-->


