---
title: "Creación y uso de un equilibrador de carga interno con App Service Environment | Microsoft Docs"
description: "Creación y uso de ASE con un ILB"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: c0f942cb11edc6d6212914b3134e25c3fbc3d3ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Uso de un equilibrador de carga interno con un entorno del Servicio de aplicaciones

> [!NOTE] 
> Este artículo trata sobre App Service Environment v1. Hay una versión más reciente que resulta más fácil de usar y se ejecuta en una infraestructura más eficaz. Para aprender más sobre la nueva versión, empiece por consultar la [Introducción a App Service Environment](intro.md).
>

La característica App Service Environment (ASE) es una opción del nivel Premium de Azure App Service que ofrece una mejor funcionalidad de configuración que no está disponible en las marcas de varios inquilinos. Básicamente, la característica de ASE implementa el Servicio de aplicaciones de Azure en la red virtual de esta plataforma. Para comprender mejor las funciones que ofrecen los entornos de App Service, lea el artículo donde[que explica en qué consiste un entorno de App Service][WhatisASE]. Si no conoce las ventajas de utilizar una red virtual, consulte las [preguntas más frecuentes sobre Azure Virtual Network][virtualnetwork]. 

## <a name="overview"></a>Información general
Un ASE puede implementarse con un punto de conexión accesible por Internet o con una dirección IP en la red virtual. Para establecer la dirección IP en una dirección de red virtual, tiene que implementar el ASE con un equilibrador de carga interno (ILB). Cuando el ASE se configura con un ILB, tiene que proporcionar lo siguiente:

* Su propio dominio o subdominio. Para que sea más sencillo, en este documento se da por hecho que utilizará un subdominio, pero también puede configurarse con un dominio. 
* El certificado usado para las conexiones HTTPS.
* La administración de DNS del subdominio. 

A cambio, podrá realizar todo esto:

* Hospedar aplicaciones de intranet (por ejemplo, aplicaciones de línea de negocio) de forma segura en la nube a las que se accede a través de una VPN de ExpressRoute o de sitio a sitio.
* Hospedar aplicaciones en la nube que no se muestran en los servidores DNS públicos
* Crear aplicaciones de back-end sin conexión a Internet con las que puedan integrarse de forma segura sus aplicaciones de front-end.

#### <a name="disabled-functionality"></a>Funcionalidad deshabilitada
Sin embargo, cuando utilice un ASE con un ILB, no podrá realizar algunas operaciones; estas son algunas de ellas:

* Utilizar una SSL de IP.
* Asignar direcciones IP a aplicaciones específicas.
* Comprar y usar un certificado con una aplicación a través del Portal. Evidentemente, puede seguir obteniendo los certificados directamente de una entidad de certificación y utilizarlos con sus aplicaciones.

## <a name="creating-an-ilb-ase"></a>Creación de un ASE con un ILB
Apenas hay diferencias entre crear un ASE con un ILB y del modo habitual. Para ver una explicación exhaustiva sobre cómo crear un ASE, consulte [Creación de un App Service Environment][HowtoCreateASE]. Con independencia de que se genere una red virtual durante la creación del ASE con un ILB o de que se seleccione una que ya exista, el proceso será el mismo. Pasos para crear un ASE con un ILB: 

1. En el Azure Portal, elija **Nuevo -> Web y móvil -> App Service Environment**
2. Seleccione su suscripción.
3. Elija un grupo de recursos o cree uno.
4. Cree una red virtual o seleccione una.
5. Crear una subred si se selecciona una VNet
6. Elija **Ubicación/red virtual -> VNet Configuration (Configuración de red virtual)** y establezca el valor de VIP Type (Tipo de dirección VIP) en Interna.
7. Proporcione el nombre del subdominio (será el que utilice para las aplicaciones creadas en este ASE).
8. Seleccione Aceptar y, después, Crear.

![][1]

En la hoja Red virtual, verá una opción llamada "VNet Configuration" (Configuración de red virtual). Aquí podrá seleccionar una dirección VIP externa o interna. El valor predeterminado es Externa. En este caso, el ASE utilizará una dirección VIP accesible por Internet. Si selecciona Interna, el ASE se configurará con un ILB en una dirección IP de su red virtual. 

Después de elegir este valor, no podrá agregar más direcciones IP al ASE y, en su lugar, tendrá que proporcionar el subdominio del ASE. En un ASE con una dirección VIP externa, se usa el nombre del ASE en el subdominio para las aplicaciones creadas en dicho ASE. Si denomina al ASE ***contosotest*** y a la aplicación en ese ASE, ***mytest***, el subdominio tendría el formato ***contosotest.p.azurewebsites.net*** y la dirección URL de dicha aplicación sería ***mytest.contosotest.p.azurewebsites.net***. Si establece el valor de VIP Type (Tipo de dirección VIP) en Interna, el nombre del ASE no se utilizará en el subdominio de dicho ASE. En este caso, especifique expresamente el subdominio. Si el subdominio fuera ***contoso.corp.net*** y creara una aplicación en dicho ASE denominada ***timereporting***, la dirección URL de dicha aplicación sería ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicaciones en un ASE con un ILB
En un ASE con un ILB, las aplicaciones se crean de la misma forma que en un ASE sin equilibrador de carga. 

1. En el Azure Portal, seleccione **Nuevo -> Web y móvil -> Web**, o bien **Móvil** o **Aplicación de API**.
2. Escriba el nombre de la aplicación.
3. Seleccione la suscripción.
4. Elija un grupo de recursos o cree uno.
5. Seleccione o cree un plan del Servicio de aplicaciones (ASP). Si va a crear un nuevo ASP, seleccione el ASE como la ubicación y elija el grupo de trabajo en el que quiera que se cree. Cuando cree el ASP, seleccione el ASE como la ubicación y el grupo de trabajo. Al especificar el nombre de la aplicación, verá que el subdominio que se encuentra bajo el nombre de la aplicación se reemplazará por el subdominio del ASE. 
6. Seleccione Crear. Debe activar la casilla **Anclar a panel** si quiere que la aplicación se muestre en el panel. 

![][2]

Debajo del nombre de la aplicación, el nombre del subdominio se actualiza para reflejar el subdominio del ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validación posterior a la creación del ASE con un ILB
Un ASE con un ILB es ligeramente diferente a un ASE sin ILB. Como ya se ha indicado antes, tiene que administrar su propio DNS y proporcionar también su propio certificado para las conexiones HTTPS. 

Cuando cree el ASE, observará que el subdominio muestra el subdominio que especificó y que hay un nuevo elemento en el menú **Configuración** llamado **Certificado de ILB**. El ASE se crea con un certificado autofirmado, lo que facilita la prueba HTTPS. El portal le indicará que debe proporcionar su propio certificado para HTTPS pero se trata de unidad que tenga un certificado que vaya con el subdominio. 

![][3]

Si solo está probando las características y no sabe cómo crear un certificado, puede utilizar la aplicación de consola de MMC en IIS para crear un certificado autofirmado. Cuando se crea, puede exportarlo como archivo .pfx y, luego, cargarlo en la interfaz de usuario del certificado de ILB. Al acceder a un sitio protegido con un certificado autofirmado, el explorador le mostrará una advertencia que indica que el sitio al que está accediendo no es seguro debido a que no se puede validar el certificado. Si quiere evitar que aparezca esa advertencia, necesita un certificado firmado correctamente que coincida con el subdominio y que tenga una cadena de confianza que reconozca el explorador.

![][6]

Si desea probar el flujo con sus propios certificados y probar el acceso HTTP y HTTPS en su ASE:

1. Cuando cree el ASE, vaya a su interfaz de usuario: **ASE -> Configuración -> Certificados de ILB**.
2. Establezca el certificado de ILB seleccionando el archivo de certificado .pfx y proporcione la contraseña. Este paso tarda unos minutos en completarse. Luego, se mostrará un mensaje que indica que se está realizando una operación de escalado.
3. Obtenga la dirección del ILB del ASE (**ASE -> Propiedades -> Dirección IP virtual**).
4. Genere una aplicación web en ASE cuando lo haya creado. 
5. Cree una máquina virtual (en caso de que no tenga ninguna) en esa red virtual (no en la misma subred que el ASE, ya que se producirían errores).
6. Establezca el DNS del subdominio. Puede utilizar un carácter comodín con el subdominio en el DNS o, si quiere realizar algunas pruebas sencillas, editar el archivo de hosts de la máquina virtual para establecer el nombre de la aplicación web en la dirección IP virtual. Si el nombre de subdominio del ASE era .ilbase.com y el de la aplicación web, miaplicaciónprueba, y la dirección, en consecuencia, miaplicaciónprueba.ilbase.com, establezca dicha dirección en el archivo de hosts (en Windows, el archivo de hosts se encuentra en C:\Windows\System32\drivers\etc\).
7. Utilice un explorador en esa VM y obtenga acceso a http://mytestapp.ilbase.com (o a la dirección que corresponda según el nombre de su aplicación web en el subdominio).
8. Utilice un explorador en esa máquina virtual y obtenga acceso a https://mytestapp.ilbase.com. Si utiliza un certificado autofirmado, tendrá que aceptar que la seguridad será deficiente. 

La dirección IP del ILB aparece en la sección Propiedades como la dirección IP virtual.

![][4]

## <a name="using-an-ilb-ase"></a>Uso de un ASE con un ILB
#### <a name="network-security-groups"></a>Grupos de seguridad de red
Un ASE con un ILB permite aislar a las aplicaciones de la red; es decir, permanecerán ocultas y no se podrá acceder a ellas desde Internet. Esta posibilidad es perfecta para hospedar sitios de intranet como aplicaciones de línea de negocio. Cuando tenga que restringir más aún el acceso, puede seguir utilizando los grupos de seguridad de red (NSG) para controlar el acceso en la red. 

Si quiere usar los NSG con este fin, tendrá que asegurarse de no interrumpir la comunicación que necesita el ASE para poder funcionar. Aunque el acceso HTTP y HTTPS solo se realiza a través del ILB que utiliza el ASE, este sigue dependiendo de recursos externos a la red virtual. Para consultar qué acceso de red sigue siendo necesario, busque la información en los documentos [Cómo controlar el tráfico de entrada a un App Service Environment][ControlInbound] y [Detalles de configuración de red para los App Service Environment con ExpressRoute][ExpressRoute]. 

Para configurar los NSG, debe conocer la dirección IP que usa Azure para administrar el ASE. Esa dirección IP también es la dirección IP saliente del ASE en caso de que realice solicitudes de Internet. La dirección IP saliente para su ASE permanecerá estática durante la vida de su ASE. Si elimina y vuelve a crear el ASE, recibirá una nueva dirección IP. Para encontrar esta dirección IP, vaya a **Configuración -> Propiedades** y busque **Dirección IP saliente**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Administración general de ASE con un ILB
Apenas hay diferencias entre administrar un ASE con un ILB y del modo habitual. Debe escalar verticalmente los grupos de trabajo para hospedar varias instancias de ASP, además de los servidores de front-end para administrar una mayor cantidad de tráfico HTTP y HTTPS. Para obtener información general sobre cómo administrar la configuración de un ASE, lea el documento [Configuración de un App Service Environment][ASEConfig]. 

Los certificados y los DNS son los otros recursos de ASE con un ILB que se administran. Tendrá que obtener y cargar el certificado utilizado para las conexiones HTTPS después de crear el ASE con un ILB y, luego, reemplazarlo antes de que expire. Dado que Azure posee el dominio base, podemos proporcionar certificados para ASE con una dirección VIP externa. Como el subdominio que utiliza un ASE con un ILB no es fijo, debe proporcionar su propio certificado para las conexiones HTTPS. 

#### <a name="dns-configuration"></a>Configuración de DNS
Cuando se utiliza una dirección VIP externa, Azure se encarga de administrar el DNS. Todas las aplicaciones que cree en el ASE se agregan automáticamente a DNS de Azure, que es un DNS público. En los ASE con un ILB tiene que administrar su propio DNS. Para un subdominio concreto como contoso.corp.net, debe crear registros A de DNS que apunten a la dirección del ILB de:

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>Introducción
Para empezar a trabajar con entornos de App Service, consulte [Introducción al entorno de App Service][WhatisASE].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
