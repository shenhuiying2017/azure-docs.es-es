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
ms.openlocfilehash: 64746f7b1a09e35b35e794f5a11d69bef39a03a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-an-app-service-environment"></a>Uso de una instancia de App Service Environment #

## <a name="overview"></a>Información general ##

Azure App Service Environment es una implementación de Azure App Service en una subred de Azure Virtual Network de un cliente. Consta de:

- **Servidores front-end**: en ellos finaliza HTTP/HTTPS en una instancia de App Service Environment (ASE).
- **Trabajos**: los trabajos son los recursos que hospedan las aplicaciones.
- **Base de datos**: la base de datos contiene información que define el entorno.
- **Almacenamiento**: el almacenamiento se usa para hospedar las aplicaciones publicadas del cliente.

> [!NOTE]
> Hay dos versiones de App Service Environment: ASEv1 y ASEv2. En ASEv1, debe administrar los recursos antes de que pueda usarlos. Para información sobre cómo configurar y administrar ASEv1, consulte [Configuración de App Service Environment v1][ConfigureASEv1]. El resto de este artículo se centra en ASEv2.
>
>

Puede implementar un entorno ASE (ASEv1 y ASEv2) con una VIP externa o interna para acceso a las aplicaciones. La implementación con una VIP externa suele llamarse ASE externo. La versión interna se llama ASE con un ILB porque usa un equilibrador de carga interno (ILB). Para más información sobre ASE con un ILB, consulte [Creación y uso de un ASE con un ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Creación de una aplicación web en ASE ##

Para crear una aplicación web en ASE, usa el mismo proceso que al crearla normalmente, pero con pequeñas diferencias. Al crear un nuevo plan de App Service:

- En lugar de elegir una ubicación geográfica en la que implementar la aplicación, elija un entorno ASE como su ubicación.
- Todos los planes de App Service que se creen en ASE deben tener un plan de tarifa aislado.

Si no tiene un entorno ASE, puede crear uno según las instrucciones que aparecen en [Creación de un entorno en App Service Environment][MakeExternalASE].

Para crear una aplicación web en ASE:

1. Seleccione **Nuevo** > **Web y móvil** > **Aplicación web**.

2. Escriba un nombre para la aplicación web. Si ya seleccionó un plan de App Service en ASE, el nombre de dominio de la aplicación refleja el nombre de dominio del entorno ASE.

    ![Selección del nombre de la aplicación web][1]

3. Seleccione una suscripción.

4. Especifique un nombre para un grupo de recursos nuevo o seleccione **Usar existente** y seleccione uno en la lista desplegable.

5. Seleccione un plan de App Service existente en ASE o cree uno nuevo siguiendo estos pasos:

    a. Seleccione **Crear nuevo**.

    b. Especifique el nombre del plan de App Service.

    c. Seleccione el entorno ASE en la lista desplegable **Ubicación**.

    d. Seleccione un plan de tarifa **Aislado**. Elija **Seleccionar**.

    e. Seleccione **Aceptar**.
    
    ![Planes de tarifa aislados][2]

6. Seleccione **Crear**.

## <a name="how-scale-works"></a>Cómo funciona escalar ##

Cada aplicación de App Service se ejecuta en un plan de App Service. El modelo de contenedor es entornos que contienen planes de App Service y planes de App Service que contienen aplicaciones. Cuando escala una aplicación, escala el plan de App Service y, por tanto, escala todas las aplicaciones en el mismo plan.

En un entorno de ASEv2, cuando escala un plan de App Service, se agrega automáticamente la infraestructura necesaria. Hay un retraso para escalar las operaciones mientras se agrega la infraestructura. En ASEv1, se debe agregar la infraestructura necesaria antes de poder crear o escalar horizontalmente el plan de App Service. 

En App Service multiinquilino, habitualmente el escalado es inmediato porque un grupo de recursos ya está disponible para admitirlo. En un entorno ASE, no hay ningún búfer de este tipo y los recursos se asignan según sea necesario.

En un entorno ASE, puede escalar hasta 100 instancias. Todas esas 100 instancias pueden estar en un solo plan de App Service o estar distribuidos en varios planes de App Service.

## <a name="ip-addresses"></a>Direcciones IP ##

App Service tiene la capacidad de asignar una dirección IP dedicada a una aplicación. Esta funcionalidad está disponible después de configurar un SSL basado en dirección IP, tal como se describe en [Enlazar un certificado SSL personalizado a Azure Web Apps][ConfigureSSL]. Sin embargo, en ASE, hay una excepción importante. No puede agregar direcciones IP adicionales para usarlas para un SSL basado en dirección IP en ASE con un ILB.

En ASEv1, debe asignar las direcciones IP como recursos antes de poder usarlos. En ASEv2, las usa desde la aplicación tal como lo hace en App Service multiinquilino. Siempre hay una dirección de reserva en un entorno ASEv2 para hasta 30 direcciones IP. Cada vez que usa una, se agrega otra, para que siempre haya una dirección disponible lista para su uso. Un retraso es necesario para asignar otra dirección IP, que evita agregar direcciones IP en una sucesión rápida.

## <a name="front-end-scaling"></a>Escalado de front-end ##

En un entorno ASEv2, cuando escala horizontalmente los planes de App Service, los trabajos se agregan automáticamente para admitirlos. Cada ASE se crea con dos servidores front-end. Además, los servidores front-end se escalan horizontalmente a un ritmo de un servidor front-end por cada 15 instancias en los planes de App Service. Por ejemplo, si tiene 15 instancias, tiene tres Front-Ends. Si escala a 30 instancias, tendrá cuatro Front-Ends, y así sucesivamente.

Este número de servidores front-end debe ser más que suficiente en la mayoría de los casos. Sin embargo, puede escalar horizontalmente a mayor velocidad. Puede cambiar la frecuencia de modo que sea tan lenta como un servidor front-end por cada cinco instancias. Hay cargos por cambiar la frecuencia. Para más información, consulte [Precios de Azure App Service][Pricing].

Los recursos de Front-End son el punto de conexión HTTP/HTTPS para el entorno ASE. Con la configuración predeterminada de Front-End, el uso de memoria por Front-End se sitúa sistemáticamente en torno al 60 %. Las cargas de trabajo no se ejecutan en Front-End. El factor clave de un Front-End con respecto a escalar es la CPU, que depende principalmente del tráfico HTTPS.

## <a name="app-access"></a>Acceso a las aplicaciones ##

En un entorno ASE externo, el dominio que se usa al crear aplicaciones es distinto de App Service multiinquilino. Incluye el nombre del entorno ASE. Para más información sobre cómo crear un entorno ASE externo, consulte [Creación de App Service Environment][MakeExternalASE]. El nombre de dominio de un entorno ASE externo es similar a *.&lt;asename&gt;.p.azurewebsites.net*. Por ejemplo, si el entorno ASE se denomina _external-ase_ y hospeda una aplicación llamada _contoso_ en ese ASE, puede llegar a él en las siguientes direcciones URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

La dirección URL contoso.scm.external-ase.p.azurewebsites.net se usa para obtener acceso a la consola Kudu o para publicar la aplicación mediante implementación web. Para información sobre la consola Kudu, consulte el artículo sobre la [consola Kudu para Azure App Service][Kudu]. La consola Kudu ofrece una UI web para depurar, cargar archivos, editar archivos y mucho más.

En un entorno ASE con un ILB, se determina el dominio en el momento de la implementación. Para más información sobre cómo crear un entorno ASE con un ILB, consulte [Creación y uso de un ASE con un ILB][MakeILBASE]. Si especifica el nombre de dominio _ilb-ase.info_, las aplicaciones de ese ASE usan ese dominio durante la creación de las aplicaciones. Para la aplicación denominada _contoso_, las direcciones URL son:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicación ##

Al igual que lo que ocurre con App Service multiinquilino, en un entorno ASE puede publicar con:

- Implementación web.
- FTP.
- Integración continua.
- Operación de arrastrar y soltar en la consola Kudu.
- Un IDE como Visual Studio, Eclipse o IntelliJ IDEA.

Con un entorno ASE externo, todas estas opciones de publicación se comportan del mismo modo. Para más información, consulte [Implementación en Azure App Service][AppDeploy]. 

La gran diferencia con la publicación tiene que ver con un ASE con un ILB. Con un ASE con un ILB, los puntos de conexión de publicación solo están disponibles a través de ILB. El ILB está en una dirección IP privada en la subred de ASE en la red virtual. Si no tiene acceso de red al ILB, no puede publicar ninguna aplicación en ese entorno ASE. Tal como se indica en [Creación y uso de un ASE con un ILB][MakeILBASE], debe configurar DNS para las aplicaciones del sistema. Eso incluye el punto de conexión de SCM. Si no se han definido correctamente, no puede llevar a cabo la publicación. Los IDE también deben tener acceso de red al ILB para publicar directamente en él.

Los sistemas de CI basados en Internet, como GitHub y Visual Studio Team Services, no funcionan con un ASE con un ILB ya que el punto de conexión de publicación no es accesible desde Internet. En lugar de eso, necesita usar un sistema de CI que use un modelo de extracción, como Dropbox.

Los puntos de conexión de publicación para las aplicaciones en un ASE con un ILB usan el dominio con el que se creó el ASE con un ILB. Puede verlo en el perfil de publicación de la aplicación y en la hoja del portal de la aplicación (en **Información general** > **Información esencial** y también en **Propiedades**). 

## <a name="pricing"></a>Precios ##

La SKU de precios llamada **Isolated** se creó para su uso únicamente con ASEv2. Todos los planes de App Service que se hospedan en un entorno ASEv2 están en el SKU de precios Aislado. Las tarifas del plan Isolated de App Service pueden variar por región. 

Además del precio de los planes de App Service, hay una tarifa fija para ASE mismo. La velocidad sin formato no cambia con el tamaño de su ASE y paga por la infraestructura de ASE a una tarifa de escalado predeterminada de 1 front-end adicional por cada 15 instancias del plan de App Service.  

Si la tarifa de escalado predeterminada de 1 front-end por cada 15 instancias del plan de App Service no es lo suficientemente rápida, puede ajustar la proporción a la que se agregan los front-end o el tamaño de estos.  Al ajustar la proporción o el tamaño, paga por los núcleos de front-end que no se agregarían de forma predeterminada.  

Por ejemplo, si ajusta el factor de escala en 10, se agrega un Front-End por cada 10 instancias en los planes de App Service. La tarifa fija cubre una escala de un Front-End por cada 15 instancias. Con un factor de escala de 10, paga una tarifa por el tercer servidor front-end que se agrega para las 10 instancias del plan de App Service. No es necesario que pague por él si llega a 15 instancias, ya que se agregó automáticamente.

Si ajusta el tamaño de los front-end a 2 núcleos, pero no se ajusta la proporción se pagará por los núcleos adicionales.  Se crea un ASE con 2 front-end, por lo que incluso estando por debajo del umbral de escalado automático, pagaría por 2 núcleos adicionales si aumentó el tamaño a 2 front-end de núcleo.

Para más información, consulte [Precios de Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Eliminar un entorno ASE ##

Para eliminar un entorno ASE: 

1. Use **Eliminar** en la parte superior de la hoja **App Service Environment**. 

2. Escriba el nombre de su ASE para confirmar que desea eliminarlo. Cuando se elimina un entorno ASE, se elimina también todo su contenido. 

    ![Eliminación de ASE][3]

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
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
