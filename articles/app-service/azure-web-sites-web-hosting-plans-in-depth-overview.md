---
title: "Introducción detallada sobre los planes de Azure App Service | Microsoft Docs"
description: "Obtenga información acerca de cómo funcionan los planes del Servicio de aplicaciones de Azure y cómo benefician a su experiencia de administración."
keywords: servicio de aplicaciones, servicio de aplicaciones de azure, escala, escalable, plan del servicio de aplicaciones, costo del servicio de aplicaciones
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ceaf35eed16fda272e4b3c501e3e9ab570855101
ms.lasthandoff: 12/08/2016


---
# <a name="azure-app-service-plans-in-depth-overview"></a>Introducción detallada sobre los planes del Servicio de aplicaciones de Azure
Los planes de App Service representan la colección de recursos físicos usados para hospedar sus aplicaciones.

Los planes de App Service definen lo siguiente:

- Región (oeste de EE. UU., este de EE. UU., etc.)
- Recuento de escala (uno, dos, tres instancias, etc.)
- Tamaño de la instancia (pequeño, mediano, grande)
- SKU (Gratis, Compartido, Básico, Estándar y Premium)

Web Apps, Mobile Apps, Function Apps o API Apps, en [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) se ejecutan todas en un plan de App Service.  Las aplicaciones de la misma suscripción y región y grupo de recursos pueden compartir un plan de App Service. 

Todas las aplicaciones asignadas a un **plan de App Service** comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones.

Su **plan de App Service** puede escalarse de SKU **gratis** y **compartidas** a SKU **básicas**, **estándar** y **Premium**, lo que permite acceder a más recursos y características. 

Si su plan de App Service está establecido en **Básico** o superior, también puede controlar el **tamaño** y el recuento de escala de las máquinas virtuales.

Por ejemplo, si el plan está configurado para usar dos instancias "pequeñas" en el nivel de servicio estándar, todas las aplicaciones asociadas a este plan se ejecutarán en ambas instancias. Las aplicaciones también tienen acceso a las características del nivel de servicio estándar. Las instancias del plan en las que se ejecutan las aplicaciones están totalmente administradas y tienen una alta disponibilidad. 

El **SKU** y la **escala** del plan de App Service determina el costo, no el número de aplicaciones hospedadas en él.

En este artículo exploraremos las características clave, como el nivel y la escala de un plan de App Service y el papel que juegan mientras administra sus aplicaciones.

## <a name="apps-and-app-service-plans"></a>Aplicaciones y planes del Servicio de aplicaciones
Una aplicación del Servicio de aplicaciones se puede asociar a un solo plan de dicho servicio en un momento determinado.

Tanto las aplicaciones como los planes se incluyen en un grupo de recursos. Un grupo de recursos sirve como límite del ciclo de vida de cada uno de los recursos que contiene. Los grupos de recursos le permiten administrar todos los componentes de una aplicación conjuntamente.

Como un único grupo de recursos puede tener varios planes del Servicio de aplicaciones, se pueden asignar diferentes aplicaciones a diferentes recursos físicos. Por ejemplo, puede separar los recursos entre entornos de desarrollo, pruebas y producción. Tener entornos independientes para producción y desarrollo o pruebas le permite aislar los recursos. De esta forma, las pruebas de carga de una nueva versión de las aplicaciones no competirán por los mismos recursos que las aplicaciones de producción, que prestan servicio a clientes reales.

Al tener varios planes en un único grupo de recursos, también puede definir una aplicación que se extienda entre regiones geográficas. Por ejemplo, una aplicación de alta disponibilidad que se ejecute en dos regiones incluirá dos planes como mínimo, uno por cada región, y una aplicación asociada a cada plan. En tal situación, todas las copias de la aplicación estarán contenidas en un solo grupo de recursos. Al tener un grupo de recursos con varios planes y aplicaciones, la administración, el control y la visión del estado de la aplicación resultan más sencillos.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Creación de un plan de App Service o uso de uno ya existente
Al crear una aplicación, debe considerar la creación de un grupo de recursos. Por otro lado, si la aplicación que está a punto de crear es un componente de otra aplicación más grande, esta aplicación debería crearse dentro del grupo de recursos asignado a dicha aplicación de mayor tamaño.

Con independencia de que la aplicación sea totalmente nueva o parte de otra más grande, puede aprovechar un plan existente para hospedarla o crear uno nuevo. Esta decisión es más bien una cuestión de capacidad y de carga esperada.

Se recomienda aislar la aplicación en un nuevo plan de App Service en los siguientes casos:

- La aplicación consume muchos recursos. 
- La aplicación tiene factores de escalado diferentes de las otras aplicaciones hospedadas en un plan existente.
- La aplicación necesita recursos de una región geográfica diferente.

De esta forma, puede asignar un nuevo conjunto de recursos para la aplicación y tener un mayor control de las aplicaciones.

## <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones
> [!TIP]
> Si tiene un entorno de App Service, puede revisar la documentación específica para los entornos de App Service aquí: [Creación de un plan de App Service en un entorno de App Service](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).
> 
> 

Puede crear un plan del Servicio de aplicaciones vacío desde la experiencia de exploración del plan del Servicio de aplicaciones o como parte de la creación de la aplicación.

En [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** > **Web y móvil** y, a continuación, seleccione **Aplicación web** u otro tipo de aplicación de App Service.
![Creación de una aplicación de API en el Portal de Azure.][createWebApp]

A continuación, puede seleccionar o crear el plan del Servicio de aplicaciones para la nueva aplicación.

 ![Creación de un plan del Servicio de aplicaciones.][createASP]

Para crear un plan de App Service, haga clic en **[+] Crear nuevo**, escriba el nombre del **plan de App Service** y luego seleccione una **ubicación** adecuada. Haga clic en **Plan de tarifa**y seleccione un plan de tarifa adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. Una vez haya seleccionado el plan de tarifa, haga clic en el botón **Seleccionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Cambio de una aplicación a un plan del Servicio de aplicaciones diferente
Puede mover una aplicación a un plan distinto de App Service en [Azure Portal](https://portal.azure.com). Las aplicaciones pueden moverse entre los planes, siempre y cuando estos se encuentren en el mismo grupo de recursos y región geográfica.

Para mover una aplicación a otro plan, vaya a la aplicación que desea mover. En el menú **Configuración**, busque **Change App Service plan** (Cambiar plan de App Service).

Se abre el selector del **plan de App Service **. Llegados a este punto, puede elegir un plan existente o crear uno nuevo. Se muestran únicamente los planes válidos (es decir, los que están en el mismo grupo de recursos y ubicación geográfica).

![Selector de plan del Servicio de aplicaciones.][change]

Cada plan tiene su propio plan de tarifa. Por ejemplo, al mover un sitio de un nivel Gratis a un nivel Estándar, todas las aplicaciones asignadas puedan usar las características y los recursos del nivel Estándar.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonación de una aplicación en un plan del Servicio de aplicaciones diferente
Si desea mover la aplicación a una región diferente, una alternativa es clonar la aplicación. La clonación hará una copia de su aplicación en un plan de App Service nuevo o existente o en un entorno de App Service de cualquier región.

 ![Clonación de una aplicación.][appclone]

Encontrará la opción **Clonar aplicación** en el menú **Herramientas**.

La clonación presenta algunas limitaciones sobre las que puede leer en [Clonación de aplicaciones del Servicio de aplicaciones de Azure mediante el Portal de Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Escalación de un plan del Servicio de aplicaciones
Existen tres formas de escalar un plan:

* **Cambiar el nivel del plan de tarifa**. Un plan en el nivel Básico se puede convertir a Estándar y todas las aplicaciones asignadas a él usar las características del nivel Estándar.
* **Cambiar el tamaño de instancia del plan**. Por ejemplo, un plan en el nivel Básico que usa instancias pequeñas puede cambiarse que use instancias grandes. Todas las aplicaciones asociadas a dicho plan pueden usar la memoria y los recursos de CPU adicionales que ofrece el tamaño de instancia más grande.
* **Cambiar el recuento de instancias del plan**. Por ejemplo, un plan Estándar que se ha escalado horizontalmente a tres instancias se puede escalar hasta 10 instancias. Un plan Premium puede escalarse horizontalmente hasta a 20 instancias (según disponibilidad). Todas las aplicaciones asociadas a dicho plan pueden usar la memoria y los recursos de CPU adicionales que ofrece el mayor recuento de instancias.

Puede cambiar el plan de tarifa y el tamaño de la instancia haciendo clic en el elemento **Escalar verticalmente** en la configuración de la aplicación o del plan del Servicio de aplicaciones. Los cambios se aplicarán al plan de App Service y afectarán a todas las aplicaciones que hospede.

 ![Establecimiento de valores para escalar verticalmente una aplicación.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpieza del plan de App Service
Los **planes de App Service** que no tienen aplicaciones asociadas a ellos seguirán generando cargos ya que siguen reservando la capacidad de proceso configurada en las propiedades de escalado del plan de App Service.
Para evitar cargos imprevistos, cuando se elimina la última aplicación hospedada en un plan de App Service, también se elimina el plan de App Service vacío resultante.

## <a name="summary"></a>Resumen
Los planes del Servicio de aplicaciones representan un conjunto de características y capacidades que puede compartir entre las aplicaciones. Los planes del Servicio de aplicaciones ofrecen la flexibilidad necesaria para asignar aplicaciones específicas a un conjunto de recursos y optimizar aún más el uso de los recursos de Azure. De esta forma, si desea ahorrar gastos en el entorno de pruebas, puede compartir un plan entre varias aplicaciones. Asimismo, puede escalarlo entre varias regiones y planes para maximizar el rendimiento del entorno de producción.

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

