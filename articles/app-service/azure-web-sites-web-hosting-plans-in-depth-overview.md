---
title: Introducción detallada sobre los planes del Servicio de aplicaciones de Azure | Microsoft Docs
description: Obtenga información acerca de cómo funcionan los planes del Servicio de aplicaciones de Azure y cómo benefician a su experiencia de administración.
keywords: servicio de aplicaciones, servicio de aplicaciones de azure, escala, escalable, plan del servicio de aplicaciones, costo del servicio de aplicaciones
services: app-service
documentationcenter: ''
author: btardif
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2016
ms.author: byvinyal

---
# Introducción detallada sobre los planes del Servicio de aplicaciones de Azure
Un plan del Servicio de aplicaciones representa un conjunto de características y funcionalidades que puede compartir entre múltiples aplicaciones, que incluyen aplicaciones web, aplicaciones móviles, Logic Apps o aplicaciones de API en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Estos planes admiten cinco planes de tarifa: *Gratis*, *Compartido*, *Basic*, *Standard* y *Premium*. Cada plan tiene sus propias funcionalidades y capacidades. Las aplicaciones de una misma suscripción y ubicación geográfica pueden compartir un plan. Todas las aplicaciones que comparten un plan pueden utilizar todas las funcionalidades y características que están definidas en el plan de tarifa. Todas las aplicaciones que están asociadas a un plan se ejecutan en los recursos que define el plan.

Por ejemplo, si el plan está configurado para usar dos instancias "pequeñas" en el nivel de servicio estándar, todas las aplicaciones asociadas a este plan se ejecutarán en ambas instancias con la funcionalidad de dicho nivel de servicio. Las instancias del plan en las que se ejecutan las aplicaciones están totalmente administradas y tienen una alta disponibilidad.

En este artículo exploraremos las características clave, como el nivel y la escala de un plan del Servicio de aplicaciones y el papel que juegan mientras administra sus aplicaciones.

## Aplicaciones y planes del Servicio de aplicaciones
Una aplicación del Servicio de aplicaciones se puede asociar a un solo plan de dicho servicio en un momento determinado.

Tanto las aplicaciones como los planes se incluyen en un grupo de recursos. Un grupo de recursos sirve como límite del ciclo de vida de cada uno de los recursos que contiene. Los grupos de recursos le permiten administrar todos los componentes de una aplicación conjuntamente.

Como un único grupo de recursos puede tener varios planes del Servicio de aplicaciones, se pueden asignar diferentes aplicaciones a diferentes recursos físicos. Por ejemplo, puede separar los recursos entre entornos de desarrollo, pruebas y producción. Un posible escenario se daría si desea asignar un plan con su propio conjunto de recursos dedicado para las aplicaciones de producción y un segundo plan para los entornos de desarrollo y prueba. De esta forma, las pruebas de carga de una nueva versión de las aplicaciones no competirán por los mismos recursos que las aplicaciones de producción, que prestan servicio a clientes reales.

Al tener varios planes en un único grupo de recursos, también puede definir una aplicación que se extienda entre regiones geográficas. Por ejemplo, una aplicación de alta disponibilidad que se ejecute en dos regiones incluirá dos planes, uno por cada región, y una aplicación asociada a cada plan. En tal situación, todas las copias de la aplicación se asociarán a un solo grupo de recursos. Al tener una única vista de un grupo de recursos con varios planes y aplicaciones, la administración, el control y la visión del estado de la aplicación resultan más sencillos.

## Creación de un nuevo plan del Servicio de aplicaciones frente al uso de un plan existente
Al crear una nueva aplicación, considere la posibilidad de generar un nuevo grupo de recursos. Por otro lado, si la aplicación que está a punto de crear es un componente de otra aplicación más grande, esta aplicación debería crearse dentro del grupo de recursos asignado a dicha aplicación de mayor tamaño.

Con independencia de que la nueva aplicación sea totalmente nueva o parte de otra más grande, puede aprovechar un plan existente del Servicio de aplicaciones para hospedarla o crear uno nuevo. Es más bien una cuestión de capacidad y de la carga esperada.

Si esta nueva aplicación va a utilizar muchos recursos y tiene factores de escala diferentes a los de las otras aplicaciones hospedadas en un plan existente, se recomienda aislarla en su propio plan.

Cuando crea un nuevo plan, puede asignar un nuevo conjunto de recursos para la aplicación y conseguir un mayor control sobre la asignación de recursos, dado que cada plan recibe su propio conjunto de instancias.

Como las aplicaciones se pueden mover entre los planes, es posible cambiar la forma en que se asignan los recursos en la aplicación más grande.

Por último, si desea crear una nueva aplicación en otra región distinta y dicha región no tiene un plan existente, tendrá que crear un nuevo plan en esta región para poder hospedar la aplicación en ella.

## Creación de un plan del Servicio de aplicaciones
Puede crear un plan del Servicio de aplicaciones vacío desde la experiencia de exploración del plan del Servicio de aplicaciones o como parte de la creación de la aplicación.

En el [Portal de Azure](https://portal.azure.com), haga clic en **Nuevo**, **Web y móvil**, y luego en **Aplicación web**, **Aplicación móvil**, **Aplicación de API** o **Aplicación lógica**. ![Creación de una aplicación de API en el Portal de Azure.][createWebApp]

A continuación, puede seleccionar o crear el plan del Servicio de aplicaciones para la nueva aplicación.

 ![Creación de un plan del Servicio de aplicaciones.][createASP]

Para crear un nuevo plan del Servicio de aplicaciones, haga clic en **[+] Crear nuevo**, escriba el nombre del **plan del Servicio de aplicaciones** y luego seleccione una **ubicación** adecuada. Haga clic en **Plan de tarifa** y seleccione un plan de tarifa adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. Una vez haya seleccionado el plan de tarifa, haga clic en el botón **Seleccionar**.

## Cambio de una aplicación a un plan del Servicio de aplicaciones diferente
Puede mover una aplicación a un plan distinto del Servicio de aplicaciones en el [Portal de Azure](https://portal.azure.com). Las aplicaciones pueden moverse entre los planes, siempre y cuando estos se encuentren en el mismo grupo de recursos y región geográfica.

Para mover una aplicación a otro plan, vaya a la aplicación que desea mover. En el menú **Configuración**, busque **Cambiar plan de servicio de aplicaciones**.

De esta forma se abrirá el selector del **plan del Servicio de aplicaciones**. Llegados a este punto, puede elegir un plan existente o crear uno nuevo. Se muestran únicamente los planes válidos (es decir, los que están en el mismo grupo de recursos y ubicación geográfica).

![Selector de plan del Servicio de aplicaciones.][change]

Tenga en cuenta que cada plan tiene su propio nivel de precios. Por ejemplo, cuando mueve un sitio de un nivel Gratis a un nivel Estándar, la aplicación podrá aprovechar todas las características y recursos del nivel Estándar.

## Clonación de una aplicación en un plan del Servicio de aplicaciones diferente
Si desea mover la aplicación a una región diferente, una alternativa es clonar la aplicación. La clonación hará una copia de su aplicación en un plan del Servicio de aplicaciones nuevo o existente o en un entorno del Servicio de aplicaciones de cualquier región.

 ![Clonación de una aplicación.][appclone]

Encontrará la opción **Clonar aplicación** en el menú **Herramientas**.

La clonación presenta algunas limitaciones sobre las que puede leer en [Clonación de aplicaciones del Servicio de aplicaciones de Azure mediante el Portal de Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## Escalación de un plan del Servicio de aplicaciones
Existen tres formas de escalar un plan:

* **Cambiar el nivel del plan de tarifa**. Por ejemplo, un plan del nivel Básico puede convertirse al nivel Estándar o Premium y todas las aplicaciones asociadas a dicho plan podrán aprovechar las características que se ofrecen en el nuevo nivel de servicio.
* **Cambiar el tamaño de instancia del plan**. Por ejemplo, un plan en el nivel Básico que usa instancias pequeñas puede cambiarse que use instancias grandes. Todas las aplicaciones asociadas a dicho plan podrán usar la memoria y los recursos de CPU adicionales que ofrece el tamaño de instancia más grande.
* **Cambiar el recuento de instancias del plan**. Por ejemplo, un plan Estándar que se ha escalado horizontalmente a tres instancias se puede escalar hasta 10 instancias. Un plan Premium puede escalarse horizontalmente hasta a 20 instancias (según disponibilidad). Todas las aplicaciones asociadas a dicho plan podrán usar la memoria y los recursos de CPU adicionales que ofrece el recuento de instancia más grande.

Puede cambiar el plan de tarifa y el tamaño de la instancia haciendo clic en el elemento **Escalar verticalmente** en la configuración de la aplicación o del plan del Servicio de aplicaciones. Los cambios se aplicarán al plan del Servicio de aplicaciones y afectarán a todas las aplicaciones que hospede.

 ![Establecimiento de valores para escalar verticalmente una aplicación.][pricingtier]

## Resumen
Los planes del Servicio de aplicaciones representan un conjunto de características y capacidades que puede compartir entre las aplicaciones. Los planes del Servicio de aplicaciones ofrecen la flexibilidad necesaria para asignar aplicaciones específicas a un conjunto de recursos y optimizar aún más el uso de los recursos de Azure. De esta forma, si desea ahorrar gastos en el entorno de pruebas, puede compartir un plan entre varias aplicaciones. Asimismo, puede escalarlo entre varias regiones y planes para maximizar el rendimiento del entorno de producción.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

<!---HONumber=AcomDC_0817_2016-->