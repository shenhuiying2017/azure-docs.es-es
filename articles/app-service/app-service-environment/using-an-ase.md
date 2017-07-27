---
title: Uso de Azure App Service Environment
description: "Cómo crear, publicar y escalar aplicaciones en Azure App Service Environment"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>Uso de App Service Environment #

## <a name="overview"></a>Información general ##

Un entorno App Service Environment (ASE) es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet) de un cliente. Consta de:

- Front-ends: donde HTTP/HTTPS finaliza en un entorno ASE
- Trabajos: los recursos que hospedan las aplicaciones
- Base de datos: contiene información que define el entorno
- Almacenamiento: el almacenamiento se usa para hospedar las aplicaciones publicadas del cliente

> [!NOTE]
> Hay dos versiones de App Service Environment: ASEv1 y ASEv2. En ASEv1, debe administrar los recursos antes de que pueda usarlos. Para información sobre cómo configurar y administrar ASEv1, consulte [Configuración de App Service Environment v1][ConfigureASEv1]. El resto de este documento se centra en ASEv2.
>
>

Puede implementar un entorno ASE (ya sea ASEv1 o ASEv2) con una VIP externa para acceso a las aplicaciones o una VIP interna para acceso a las aplicaciones. La implementación con una VIP externa habitualmente se denomina ASE externo y la versión interna, ASE con un ILB, porque usa un equilibrador de carga interno (ILB). Para más información sobre ASE con un ILB, consulte [Creación y uso de un ASE con un ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Creación de una aplicación web en ASE ##

Crear una aplicación web en ASE es el mismo proceso para crearla normalmente, con solo pequeñas diferencias. Cuando se crea un nuevo plan de App Service:

- En lugar de seleccionar una ubicación geográfica para implementar la aplicación, seleccione un entorno ASE como su ubicación
- Todos los planes de App Service que se creen en ASE deben tener un plan de tarifa aislado.

Si no tiene un entorno ASE, puede crear uno según las instrucciones que aparecen en [Creación de un entorno en App Service Environment][MakeExternalASE].

Para crear una aplicación web en ASE:

1. Haga clic en **Nuevo &gt; Uso web y móvil** y seleccione **Aplicación web**.
2. Proporcione un nombre para la aplicación web. Si ya seleccionó un plan de App Service en ASE, el nombre de dominio de la aplicación reflejará el nombre de dominio del entorno ASE.

    ![][1]

1. Seleccione una suscripción.
2. Proporcione un nombre para un grupo de recursos nuevo o seleccione **Usar existente** y seleccione uno en la lista desplegable.
3. Seleccione un plan de App Service existente en ASE o cree uno nuevo mediante los pasos siguientes:
    1. Seleccione **Crear nuevo**.
    2. Proporcione el nombre del plan de App Service.
    3. Seleccione el entorno ASE en la lista desplegable **Ubicación**.
    4. Seleccione un plan de tarifa **Aislado**. Haga clic en **Seleccionar**.
    5. Haga clic en **Aceptar**.
    
    ![][2]
1. Haga clic en **Crear**.

## <a name="how-scale-works"></a>Cómo funciona escalar ##

Cada aplicación de App Service se ejecuta en un plan de App Service. El modelo de contenedor es: los entornos contienen planes de App Service y los planes de App Service contienen aplicaciones. Cuando escala una aplicación, escala el plan de App Service y, por tanto, escala todas las aplicaciones en el mismo plan.

En un entorno de ASEv2, cuando escala un plan de App Service, se agrega automáticamente la infraestructura necesaria. Esto es distinto de ASEv1, donde se debe agregar la infraestructura necesaria antes de poder crear o escalar horizontalmente el plan de App Service. En ASEv2, esto significa que hay un retraso para escalar las operaciones debido a que se agrega la infraestructura.

En App Service multiinquilino, habitualmente el escalado es inmediato porque hay un grupo de recursos ya disponible que se puede usar para admitir la escalabilidad horizontal. En un entorno ASE no hay ningún búfer de este tipo y los recursos se asignan según sea necesario.

En un entorno ASE puede escalar hasta 100 instancias. Todas esas 100 instancias pueden estar en un solo plan de App Service o estar distribuidos en varios planes de App Service.

## <a name="ip-addresses"></a>Direcciones IP ##

App Service tiene la capacidad de asignar una dirección IP dedicada a una aplicación. Esta funcionalidad está disponible mediante la configuración de un SSL basado en dirección IP tal como se describe aquí: [Enlazar un certificado SSL personalizado a Azure Web Apps][ConfigureSSL]. Sin embargo, en un entorno ASE, existe una excepción notable: no puede agregar direcciones IP adicionales para que las use un SSL basado en dirección IP en un ASE con un ILB.

En ASEv1, debe asignar las direcciones IP como recursos antes de poder usarlos. En ASEv2, simplemente se usa desde la aplicación tal como lo haría en App Service multiinquilino. Siempre hay una dirección de reserva en un entorno ASEv2 para hasta 30 direcciones IP. Cada vez que usa una, se agrega otra, para que siempre haya una dirección disponible lista para su uso. Hay un retraso necesario para asignar otra dirección IP que evita agregar direcciones IP en una sucesión rápida.

## <a name="front-end-scaling"></a>Escalado de front-end ##

En un entorno ASEv2, cuando escala horizontalmente los planes de App Service, los trabajos se agregan automáticamente para admitirlos. Además de los dos Front-Ends con que sea crea cada entorno ASE, los Front-ends también se escalan horizontalmente a una velocidad de un Front-End por cada 15 instancias en los planes de App Service. Eso significa que si tiene 15 instancias, tiene tres Front-Ends. Si escala a 30 instancias, tendrá cuatro Front-Ends, y así sucesivamente.

Esto debiera más que suficiente para la mayoría de los escenarios, pero si es necesario escalar horizontalmente a mayor velocidad, puede cambiar la velocidad a un Front-End por cada cinco instancias en los planes de App Service. Hay un cobro por cambiar la velocidad, tal como se indica en [Precios de Azure App Service][Pricing].

Los recursos de Front-End son el punto de conexión HTTP/HTTPS para el entorno ASE. Con la configuración predeterminada de Front-End, el uso de memoria por Front-End se sitúa sistemáticamente en torno al 60 %. Las cargas de trabajo no se ejecutan en Front-End. El factor clave de un Front-End con respecto a escalar es la CPU, que depende principalmente del tráfico HTTPS.

## <a name="app-access"></a>Acceso a las aplicaciones ##

En un ASE externo, el dominio que se usa cuando se crean aplicaciones es distinto de App Service multiinquilino e incluye el nombre del ASE. Para más información sobre cómo crear un entorno ASE externo, consulte [Creación de App Service Environment][MakeExternalASE]. El nombre de dominio de un entorno ASE externo es similar a *.&lt;asename&gt;.p.azurewebsites.net*. Esto significa que si el entorno ASE se denomina _external-ase_ y hospeda una aplicación llamada _contoso_ en ese ASE, podría llegar a él en las siguientes direcciones URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

La dirección URL *contoso.scm.external-ase.p.azurewebsites.net* se usa para obtener acceso a la consola Kudu o para publicar la aplicación mediante implementación web. Para información sobre la consola Kudu, consulte el artículo sobre la [consola Kudu para Azure App Service][Kudu]. La consola Kudu ofrece una UI web para depurar, cargar archivos, editar archivos y mucho más.

En un entorno ASE con un ILB, se determina el dominio en el momento de la implementación. Para más información sobre cómo crear un entorno ASE con un ILB, consulte [Creación y uso de un ASE con un ILB][MakeILBASE]. Si especifica el nombre de dominio _ilb-ase.info_, las aplicaciones de ese ASE usan ese dominio durante la creación de las aplicaciones. Para la aplicación denominada _contoso_, las direcciones URL serían:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicación ##

Al igual que lo que ocurre con App Service multiinquilino, en un entorno ASE, puede publicar con:

- Implementación web
- FTP
- Integración continua
- Operación de arrastrar y soltar en la consola Kudu
- Un IDE como Visual Studio, Eclipse o Intellij IDEA

Con un entorno ASE externo, todos tienen el mismo comportamiento. Para información, consulte [Implementación en Azure App Service][AppDeploy]. 

La gran diferencia con la publicación tiene que ver con un ASE con un ILB. Con un ASE con un ILB, los puntos de conexión de publicación solo están disponibles a través de ILB. El ILB está en una dirección IP privada en la subred de ASE en la red virtual. Si no tiene acceso de red al ILB, no puede publicar ninguna aplicación en ese entorno ASE. Tal como se indica en [Creación y uso de un ASE con un ILB][MakeILBASE], debe configurar DNS para las aplicaciones del sistema. Eso incluye el punto de conexión de SCM. Si no están correctamente definidos, no podrá publicar. Los IDE también deben tener acceso de red al ILB para publicar directamente en él.

Los sistemas de CI basados en Internet, como GitHub y VSTS, funcionan con un entorno ASE con un ILB si el punto de conexión de publicación no es accesible a través de Internet. En lugar de eso, necesita usar un sistema de CI que use un modelo de extracción, como Dropbox.

Los puntos de conexión de publicación para las aplicaciones en un entorno ASE con un ILB usan el dominio con el que se creó el entorno ASE con un ILB. Esto puede verse en el perfil de publicación de la aplicación y en la hoja del portal de la aplicación (en **Información general** > **Información esencial** y también en **Propiedades**). 

## <a name="pricing"></a>Precios ##

Con ASEv2, hay un nuevo SKU de precios que solo se usa con ASEv2 llamado **Aislado**. Todos los planes de App Service que se hospedan en un entorno ASEv2 están en el SKU de precios Aislado. Además del precio de los planes de App Service, hay una tarifa fija para ASE mismo. Este precio no cambia con el tamaño del entorno ASE. 

Las otras posibles tarifas son por el ajuste del factor de escala de Front-End o del tamaño de Front-End. Si ajusta el factor de escala para que los Front-Ends se agreguen más rápidamente, pagará los núcleos adicionales que no se agregarían automáticamente al sistema. Del mismo modo, si selecciona un tamaño mayor para los Front-Ends, pagará los núcleos que no se pudieron asignar automáticamente. Por ejemplo, si ajusta el factor de escala en 10, significa que se agrega un Front-End por cada 10 instancias en los planes de App Service. La tarifa fija cubre una escala de un Front-End por cada 15 instancias. Con un factor de escala de 10, pagará una tarifa por el tercer Front-End que se agrega para las 10 instancias de ASP, pero no deberá pagar si alcanza las 15 instancias, ya que se agregaría automáticamente.

Para más información, consulte [Precios de Azure App Service][Pricing].

## <a name="deleting-an-ase"></a>Eliminación de un ASE ##

Si desea eliminar un entorno de App Service Environment simplemente use la acción **Eliminar** que se encuentra en la parte superior de la hoja App Service Environment. Al hacerlo, se le pedirá que escriba el nombre del entorno del Servicio de aplicaciones para confirmar que realmente quiere hacerlo. Tenga en cuenta que cuando se elimina un entorno del Servicio de aplicaciones, se elimina también todo su contenido. 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

