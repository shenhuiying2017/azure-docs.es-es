---
title: "Creación de una instancia externa de Azure App Service Environment"
description: "Explica cómo crear una instancia de Azure App Service Environment al crear una aplicación o una instancia independiente"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>Creación de una instancia externa de App Service Environment #

Una instancia de Azure App Service Environment (ASE) es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet). Hay dos modos de implementación de un ASE:

- con una dirección IP virtual en una dirección IP externa, a menudo denominado un _ASE externo_.
- con la dirección VIP en una dirección IP interna, llamada a menudo un _ASE con un ILB_ porque el punto de conexión interno es un equilibrador de carga interno (ILB).

En este artículo se muestra cómo crear un ASE externo. Para obtener información general sobre el ASE, empiece con una [introducción a App Service Environment][Intro] Para más información sobre cómo crear una ASE con un ILB, consulte el artículo sobre cómo [crear y usar un ASE con un ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de crear su ASE ##

Es importante tener presente todo lo que no se puede cambiar después de la creación de ASE, que es:

- La ubicación
- La suscripción
- Grupo de recursos
- Red virtual usada
- La subred usada
- Tamaño de la subred

> [!NOTE]
> Al seleccionar una red virtual y especificar una subred, asegúrese de que es lo suficientemente grande para adaptarse al crecimiento futuro. El tamaño recomendado es un `/25` con 128 direcciones.
>

## <a name="three-ways-to-create-an-ase"></a>Tres formas de crear un ASE ##

Hay tres formas de crear un ASE. Puede crear un ASE:

- Al crear un plan de App Service, que crea el ASE y el plan de App Service en un solo paso.
- Desde la interfaz de usuario de la creación de ASE independiente, que crea un ASE sin nada en él. Esto constituye una experiencia de la interfaz de usuario de creación de ASE más avanzada y es donde se va a crear un ASE con un equilibrador de carga interno (ILB).
- Desde una plantilla de Resource Manager. Este procedimiento es para los usuarios avanzados y se trata en el artículo [How To Create an ASE Using Azure Resource Manager Templates][MakeASEfromTemplate] (Creación de un ASE mediante plantillas de Resource Manager).

Un ASE creado sin un ILB tiene una VIP pública. Esto significa que todo el tráfico HTTP a las aplicaciones del ASE accederá a una dirección IP accesible de Internet. Un ASE con un ILB tiene un punto de conexión en una dirección IP de Virtual Network. Esas aplicaciones no se exponen directamente a Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Creación de un ASE y de un plan de App Service juntos ##

El plan de App Service es un contenedor de aplicaciones. Cuando se crea una aplicación en App Service, siempre debe seleccionar o crear un plan de App Service. El modelo de contenedor es el siguiente: entornos que contienen planes de App Service y planes de App Service que contienen aplicaciones.

Para crear un ASE durante la creación del plan de App Service:

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo &gt; Web y móvil &gt; Aplicación web**.

    ![][1]
1. Seleccione su suscripción. Si tiene varias suscripciones, tenga en cuenta que para crear una aplicación en el ASE, debe usar la misma suscripción que usó para crearlo.
1. Seleccione o cree un grupo de recursos. *Los grupos de recursos* le permiten administrar los recursos de Azure relacionados como una unidad y resultan útiles al establecer las *reglas del control de acceso basado en rol* (RBAC) para las aplicaciones. Para más información, consulte [Información general de Azure Resource Manager][ARMOverview].
1. Haga clic en el plan de App Service y, a continuación, seleccione **Crear nuevo**.

    ![][2]
1. En la lista desplegable **Ubicación**, elija la región donde desea crear el ASE. Si se elige un ASE existente, no se habilitará la creación de un nuevo ASE, sino que simplemente se creará el plan de App Service en el ASE seleccionado.
1. Haga clic en la interfaz de usuario **Plan de precios** y elija una de las SKU de precios **aislado**. La selección de una tarjeta de SKU **aislada** y una ubicación que no sea un ASE significa que quiere crear un nuevo ASE en esa ubicación. De esta forma se muestra la interfaz de usuario de creación del ASE tras hacer clic en **Seleccionar** en la página de la tarjeta de precios. La SKU **aislada** solo está disponible junto con un ASE. Tampoco puede utilizar cualquier otro SKU de precios en un ASE distinto del **aislado**.

    ![][3]
1. Escriba el nombre del ASE. El nombre del ASE se utiliza en el nombre direccionable para las aplicaciones. Si el nombre del ASE es _appsvcenvdemo_, el nombre del subdominio será *.appsvcenvdemo.p.azurewebsites.net*. Por tanto, si crea una aplicación llamada *mytestapp*, se obtiene acceso a ella en *mytestapp.appsvcenvdemo.p.azurewebsites.net*. No puede usar espacios en blanco en el nombre del ASE. Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre.

    ![][4]
1. Elija **Crear nuevo** o **Seleccionar existente**. La opción de seleccionar una red virtual existente solo está disponible si tiene una red virtual en la región seleccionada. Si selecciona **Crear nuevo**, proporcione un nombre para la red virtual y se creará una nueva red virtual de Resource Manager con ese nombre con el espacio de direcciones `192.168.250.0/23` en la región seleccionada. Si selecciona **Seleccionar existente**, tiene que:
    1. Seleccionar el bloque de direcciones de Virtual Network si tiene más de uno.
    2. Especificar un nuevo nombre de subred.
    3. Seleccionar el tamaño de la subred. **Recordatorio: Debe ser lo suficientemente grande para adaptarse al crecimiento futuro del ASE.** El tamaño recomendado es un `/25`, que tiene 128 direcciones y puede controlar los ASE de tamaño máximo. `/28` no se recomienda, por ejemplo, porque solo tiene 16 direcciones disponibles. Las necesidades de infraestructura utilizarían al menos 5 direcciones, dejándole solo con una escala máxima de 11 instancias en una subred `/28`.
    4. Seleccione el intervalo IP de subred.

El proceso de creación de ASE se iniciará después de seleccionar **Crear**. Esto también creará el plan de App Service y la aplicación. Tanto el ASE como el plan de App Service y la aplicación estarán en la misma suscripción y también en el mismo grupo de recursos. Si necesita que el ASE esté en un grupo de recursos independiente del plan de App Service y de la aplicación, o si necesita un ASE con un ILB, utilice la experiencia de creación de ASE independiente.

## <a name="create-an-ase-by-itself"></a>Creación de un ASE por sí mismo ##

El recorrido por el flujo de creación de un ASE independiente creará un ASE sin nada en él. Un ASE vacío seguirá generando un cargo mensual para la infraestructura. Las razones principales para recorrer este flujo de trabajo son crear un ASE con un ILB o crear un ASE en su propio grupo de recursos. Después de crear el ASE, puede crear aplicaciones en el ASE mediante las experiencias de creación de aplicaciones habituales y la selección de la nueva ASE como la ubicación.

Acceda a la interfaz de usuario de creación del ASE mediante la búsqueda de ***App Service Environment*** en Azure Marketplace o a través de Nuevo -&gt; Web y móvil -&gt; App Service Environment. Para crear un ASE con la experiencia de creación independiente:

1. Proporcione el nombre de su ASE. El nombre especificado para el ASE se usará en las aplicaciones creadas en él. Si el nombre del ASE es *appsvcenvdemo*, el nombre del subdominio será *.mynewdemoase.p.azurewebsites.net*. Por tanto, para crear una aplicación llamada *mytestapp*, se obtiene acceso a ella en *mytestapp.mynewdemoase.p.azurewebsites.net*. No puede usar espacios en blanco en el nombre del ASE. Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre. Si usa un ILB, el nombre del ASE no se utiliza en su subdominio, sino que se indica explícitamente durante la creación del ASE.

    ![][5]
1.  Seleccione su suscripción. La suscripción utilizada para su ASE también es aquella con la que se crearán todas las aplicaciones en ese ASE. No se puede colocar su ASE en una red virtual que se encuentra en otra suscripción.
1.  Seleccione o especifique un nuevo grupo de recursos. El grupo de recursos que se usa para su ASE debe ser el mismo que se utiliza para la red virtual. Si selecciona una red virtual existente, la selección del grupo de recursos para su ASE se actualizará para reflejar el de la red virtual.

    ![][6]
1. Realice las selecciones de red virtual y ubicación. Puede optar por crear una nueva red virtual o seleccionar una red virtual existente. Si selecciona una red virtual nueva, puede especificar un nombre y una ubicación. La red virtual nueva tendrá el intervalo de dirección 192.168.250.0/23 y una subred denominada **predeterminada** que se define como 192.168.250.0/24. Solo se puede seleccionar una red virtual de Resource Manager. La selección del **tipo de VIP** determina si es posible obtener acceso directo a su ASE desde Internet (externo) o si usa un equilibrador de carga interno (ILB). Para más información sobre ellos, consulte [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Crear y usar un equilibrador de carga interno con una instancia de App Service Environment). Si selecciona un tipo de VIP **externo**, puede seleccionar la cantidad de direcciones IP externas que crea el sistema con fines de SSL basados en IP. Si selecciona **Interno**, debe especificar el subdominio que utilizará el ASE. Se pueden implementar los ASE en redes virtuales que usen *intervalos* de direcciones públicas *o* espacios de direcciones de RFC1918 (es decir, direcciones privadas). Para usar una red virtual con un intervalo de direcciones públicas, es necesario crear la red virtual por adelantado. Cuando seleccione una red virtual existente, debe crear una nueva subred durante la creación de ASE. **No se puede usar una subred creada previamente en el portal. Puede crear un ASE con una subred existente si crea su ASE con una plantilla de Resource Manager.** Para crear un ASE desde una plantilla, consulte [How To Create an ASE Using Azure Resource Manager Templates][MakeASEfromTemplate] (Creación de un ASE con plantillas de Azure Resource Manager).

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Todavía puede crear instancias de la primera versión de la característica de ASE (ASEv1). Para llegar a esta experiencia de búsqueda en Marketplace para **App Service Environment v1.** La experiencia de creación es la misma que al crear un ASE independiente. Cuando haya finalizado, se creará el ASEv1 con dos servidores front-end y dos roles de trabajo. Con ASEv1 necesita administrar los servidores front-end y los roles de trabajo. No se agregan de forma automática al crear los planes de App Service. Los front-end actúan como los puntos de conexión HTTP/HTTPS y envían tráfico a los trabajos que son los roles que hospedan sus aplicaciones. Puede ajustar la cantidad después de la creación del ASE. Para más información sobre ASEv1, consulte [Introducción a App Service Environment v1][ASEv1Intro]. Para más información sobre el escalado manual, la administración y la supervisión del ASEv1, consulte [Configuración de App Service Environment][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

