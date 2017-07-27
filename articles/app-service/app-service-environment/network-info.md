---
title: Consideraciones de red con una instancia de Azure App Service Environment
description: "Explica el tráfico de red de ASE y cómo establecer los NSG y las UDR con el ASE"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d19590c23da43e08ea42cd278347e01d87433a58
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>Consideraciones de red para una instancia de App Service Environment #

## <a name="overview"></a>Información general ##

[App Service Environment][Intro] (ASE) es una implementación de Azure App Service en una subred de Azure Virtual Network (VNet).  Hay dos tipos de implementación para el ASE:

- _ASE externo_: expone las aplicaciones hospedadas en ASE en una dirección IP accesible a través de Internet. Para más información, consulte [Creación de una instancia externa de App Service Environment][MakeExternalASE].
- _ASE de ILB_: expone las aplicaciones hospedadas en ASE en una dirección IP dentro de su instancia de Azure Virtual Network (VNet).  El punto de conexión interno es un equilibrador de carga interno (ILB), y esta es la razón por la que se denomina ASE de ILB. Para más información, consulte [Creación y uso de un ASE de ILB][MakeILBASE].

Existen en la actualidad dos versiones de ASE. La versión inicial del ASE se denomina ASEv1 y la más reciente ASEv2. Para información sobre ASEv1, consulte [Introducción a App Service Environment v1][ASEv1Intro]. Una instancia de ASEv1 se puede implementar en una red virtual clásica o en una de Resource Manager. En el caso de ASEv2, la implementación solo se puede realizar en una red virtual de Resource Manager.

Todas las llamadas que vayan a Internet desde una instancia de ASE salen de la red virtual a través de una IP virtual asignada para el ASE. La dirección IP pública de esta IP virtual es la dirección IP de origen para todas las llamadas desde el ASE que vayan a Internet.  Si las aplicaciones en la instancia de ASE realizan llamadas a los recursos de la red virtual o a través de una VPN, la IP de origen será una de las direcciones IP en la subred usada por su ASE.  Dado que el ASE está dentro de la red virtual, también puede tener acceso a los recursos dentro de la red virtual sin ninguna configuración adicional. Si la red virtual está conectada a la red local, también tiene acceso a los recursos en la misma sin necesidad de ninguna configuración adicional del ASE o de la aplicación.

![][1] 

Si tiene un ASE externo, entonces esa IP virtual pública es también el punto de conexión que las aplicaciones de ASE resolverán para HTTP/S, FTP/S, implementación web y depuración remota.

![][2]

Si tiene un ASE de ILB, entonces la dirección IP del ILB es el punto de conexión para HTTP/S, FTP/S, implementación web y depuración remota.

Los puertos de acceso de aplicación normales son:

| Uso | De | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurable por el usuario |  80, 443 |
|  FTP/FTPS    | Configurable por el usuario |  21, 990, 10001-10020 |
|  Depuración remota en Visual Studio  |  Configurable por el usuario |  4016, 4018, 4020, 4022 |

Esto es así tanto si está en un ASE externo como en un ASE de ILB. Si se encuentra en un ASE externo, estos son los puertos que se alcanzan en la IP virtual pública. Si se encuentra en un ASE de ILB, estos son los puertos que se alcanzan en el ILB. Debe tenerse en cuenta que si bloquea el puerto 443, puede producirse un impacto a algunas características que se exponen en el portal. Para más información, consulte [Dependencias del portal](#portaldep).

## <a name="ase-dependencies"></a>Dependencias de ASE ##

Un dependencia de acceso de entrada de ASE es:

| Uso | De | Para |
|-----|------|----|
| Administración | Internet | Subred de ASE: 454, 455 |
|  Comunicación interna ASE | Subred de ASE: todos los puertos | Subred de ASE: todos los puertos
|  Permitir entrada de Azure Load Balancer | Azure Load Balancer | Cualquiera

El tráfico entrante proporciona el comando y control del ASE además de supervisión del sistema. La direcciones IP de origen para este tráfico no son constantes. Esto significa que la configuración de seguridad de red tiene que permitir el acceso desde todas las direcciones IP en los puertos 454 y 455.

Para el acceso de salida, un ASE depende de varios sistemas externos. Esas dependencias del sistema se definen con nombres DNS y no se asignan a un conjunto fijo de direcciones IP. Esto significa que el ASE requiere acceso de salida desde la subred de ASE a todas las direcciones IP externas en una variedad de puertos. Un ASE presenta las siguientes dependencias de salida:

| Uso | De | Para |
|-----|------|----|
| Almacenamiento de Azure | Subred de ASE | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 se necesita solo para ASEv1) |
| SQL Database | Subred de ASE | database.windows.net: 1433, 11000-11999, 14000-14999 (Para más información, consulte [uso de los puertos de SQL Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md))|
| Administración de Azure | Subred de ASE | management.core.windows.net, management.azure.com: 443 
| Comprobación de certificado SSL |  Subred de ASE            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | Subred de ASE            |  Internet: 443
| Administración de App Service        | Subred de ASE            |  Internet: 443
| DNS de Azure                     | Subred de ASE            | Internet: 53
| Comunicación interna ASE    | Subred de ASE: todos los puertos | Subred de ASE: todos los puertos

Si el ASE pierde el acceso a estas dependencias, deja de funcionar. Cuando esto ocurre durante un tiempo suficientemente prolongado, el ASE se suspende.

**Cliente DNS**

Si la red virtual se configura con un servidor DNS definido por el cliente, las cargas de trabajo del inquilino lo utilizarán. El ASE todavía tiene que comunicarse con Azure DNS para fines de administración. 

Si la red virtual se configura con un cliente DNS en el otro lado de una VPN, el servidor DNS tiene que ser accesible desde la subred que contiene el ASE.

<a name="portaldep"></a>
## <a name="portal-dependencies"></a>Dependencias del portal ##

Además de las dependencias funcionales que tiene un ASE, hay algunos elementos adicionales relacionados con la experiencia del portal. Algunas de las funcionalidades en Azure Portal dependen de un acceso directo al _sitio SCM_. Para cada aplicación en Azure App Service, hay dos direcciones URL. La primera dirección URL es para acceder a la aplicación. La segunda dirección URL es para el acceso al sitio SCM, que también se denomina el _consola Kudu_. Algunas de las características que usan el sitio SCM incluyen:

-   Trabajos web
-   Functions
-   Logstream
-   Kudu
-   Extensiones
-   Explorador de procesos
-   Consola

Esto es más difícil cuando se usa un ASE de ILB, ya que el sitio SCM no es accesible para Internet fuera de la red virtual. Por esta razón, estas funcionalidades que dependen del acceso al sitio SCM aparecen atenuadas en Azure Portal cuando la aplicación se hospeda en un ASE de ILB.

Muchas de estas funcionalidades que dependen del sitio SCM también están disponibles directamente en la consola Kudu. Puede conectarse directamente en lugar de utilizar el portal. Si la aplicación se hospeda en un ASE de ILB, tiene que iniciar sesión con las credenciales de publicación. La dirección URL para acceder al sitio SCM de una aplicación hospedada en un ASE de ILB tiene el formato siguiente: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Por lo tanto, si su ASE de ILB es el nombre de dominio *contoso.net* y el nombre de la aplicación es *testapp*, se accederá a la aplicación en *testapp.contoso.net* y al sitio SCM que la acompaña en *testapp.scm.contoso.net*.

## <a name="ase-ip-addresses"></a>Direcciones IP de ASE ##

Un ASE tiene unas cuantas direcciones IP que es necesario tener en cuenta. Son las siguientes:

- Dirección IP pública de entrada: utilizada para el tráfico de la aplicación en un ASE externo y para el tráfico de administración tanto en un ASE externo como en un ASE de ILB.
- Dirección IP pública de salida: utilizada como la dirección IP "desde" para las conexiones de salida desde el ASE que dejan la red virtual y que no se enrutan hacia una VPN.
- Dirección IP del ILB: si está utilizando una ASE de ILB.
- Direcciones SSL basadas en IP asignadas a la aplicación: solo son posibles con un ASE externo y cuando hay configurada una SSL basada en IP.

Todas estas direcciones IP son fácilmente visibles en un ASEv2 en Azure Portal desde la interfaz de usuario de ASE. Si tiene un ASE de ILB, aparecerá la dirección IP para el ILB.

![][3]

**Direcciones IP asignadas a las aplicaciones**

Con un ASE externo, puede asignar direcciones IP a las aplicaciones individuales. Esto no se puede hacer con un ASE de ILB. Para más información acerca de cómo configurar la aplicación para que tenga su propia dirección IP, consulte [Enlace de un certificado SSL personalizado a Azure Web Apps](../../app-service-web/app-service-web-tutorial-custom-ssl.md).

Cuando una aplicación tiene su propia dirección SSL basada en IP, el ASE reserva dos puertos para asignar a esa dirección IP. Un puerto es para el tráfico HTTP y el otro es para HTTPS. Estos puertos se muestran en la interfaz de usuario de ASE en la sección de direcciones IP. El tráfico tiene que poder conectar con esos puertos desde la dirección IP virtual o las aplicaciones no serán accesibles. Esta es una cuestión importante que se debe tener en cuenta al configurar los Grupos de seguridad de red.

## <a name="network-security-groups"></a>Grupos de seguridad de red ##

Los [Grupos de seguridad de red][NSGs] (NSG) proporcionan la capacidad de controlar el acceso a la red dentro de una red virtual. Cuando usa el portal, hay una regla de denegación implícita en la prioridad más baja que deniega todo, por ello lo que va a crear son sus propias reglas de permiso.

En un ASE, no tiene acceso a las máquinas virtuales que se utilizan para hospedar el propio ASE. Estas se encuentran en una suscripción administrada por Microsoft. Si desea restringir el acceso a las aplicaciones en el ASE, tiene que establecer los NSG en la subred de ASE. Al hacerlo, tiene que prestar mucha atención a las dependencias de ASE. Si se bloquea alguna de las dependencias, el ASE dejará de funcionar.

Los NSG pueden configurarse mediante Azure Portal o a través de PowerShell. Para simplificar, la información aquí muestra el método con Azure Portal. Puede crear y administrar los NSG en el portal como un recurso de nivel superior en **Redes**.

Teniendo en cuenta los requisitos de entrada y salida, los NSG deben ser similares a lo que se muestra a continuación. En este ejemplo, el intervalo de direcciones de red virtual es _192.168.250.0/16_ y la subred a la que pertenece el ASE es _192.168.251.128/25_.

Los dos primeros requisitos de entrada para que el ASE funcione están en la parte superior en este ejemplo. Estos habilitan la administración del ASE y permiten que el ASE se comunique consigo mismo. Las demás entradas son todas configurables por inquilino y pueden controlar el acceso por red a las aplicaciones hospedadas en el ASE.   

![][4]

Además de una regla predeterminada para habilitar las direcciones IP en la red virtual para que se comuniquen con la subred de ASE, también hay una regla predeterminada que permite que el equilibrador de carga, también conocido como la IP virtual pública, se comunique con el ASE.  Puede ver las reglas predeterminadas haciendo clic en *Reglas predeterminadas* junto al icono *Agregar*. Si colocase una regla para denegar todo lo demás después de las reglas de NSG que se muestran, evitaría el tráfico entre la IP virtual y el ASE. Si desea evitar el tráfico que proceda de dentro de la red virtual, no olvide agregar su propia regla para permitir entradas con un origen igual a AzureLoadBalancer con un destino Any (Cualquiera) y un intervalo de puertos de \*.  Puesto que la regla NSG se aplica solo a la subred de ASE, no es necesario que sea específico en el destino.

Si ha asignado una dirección IP a la aplicación, entonces tendrá que asegurarse también de que los puertos utilizados para ello permanezcan abiertos. Puede ver los puertos utilizados en la interfaz de usuario de **App Service Environment** > **Direcciones IP**.  

Todo lo que aparece a continuación es necesario, con la excepción del último elemento que se muestra en las reglas de salida. Estos elementos habilitan el acceso de red a las dependencias de ASE que se han registrado anteriormente en este documento. Si bloquea cualquiera de ellos, su ASE dejará de funcionar. El último elemento de la lista habilita al ASE para que se comunique con otros recursos de la red virtual.

![][5]

Una vez que haya definido los NSG, tiene que asignarlos a la subred en la que se encuentra el ASE. Si no recuerda la red virtual o la subred de ASE, puede verlo desde el portal de administración de ASE. Para asignar el NSG a la subred, vaya a la interfaz de usuario de la subred y seleccione el NSG.

## <a name="routes"></a>Rutas ##

Las rutas presentan problemas sobre todo cuando la red virtual se configura con ExpressRoute. Hay tres tipos de rutas en una red virtual de Azure. Estas son:

-   Rutas del sistema
-   Rutas BGP
-   Rutas definidas por el usuario (UDR)

Las rutas BGP reemplazan a las rutas del sistema. Las UDR reemplazan a las rutas BGP. Para más información acerca de las rutas en las redes virtuales de Azure, consulte la [introducción a las rutas definidas por el usuario][UDRs]

La instancia de SQL Database que utiliza el ASE para administrar el sistema tiene una lista de permitidos y exige que la comunicación se origine desde la IP virtual pública del ASE. Si las respuestas a solicitudes entrantes de administración se envían a través de ExpressRoute, la dirección de respuesta es distinta al destino, lo que interrumpe la comunicación TCP.

Todo esto significa que, para que su ASE funcione cuando la red virtual se configure con ExpressRoute, lo más fácil es:

-   Configurar ExpressRoute para anunciar _0.0.0.0/0_, lo que de manera predeterminada fuerza la tunelización de todo el tráfico saliente a local.
-   Crear una UDR y aplicarla a la subred que contiene la instancia de App Service Environment, con un prefijo de dirección de _0.0.0.0/0_ y un tipo de próximo salto de _Internet_.

Si realiza estos dos cambios el tráfico destinado a Internet, que se origina en la subred ASE, no se forzará hacia ExpressRoute y el ASE podrá funcionar. 

> [!IMPORTANT]
> Las rutas definidas en una UDR tienen que ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo anterior se usa el intervalo de direcciones 0.0.0.0/0 amplio y como tal puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.
>

Las instancias de App Service Environment no son compatibles con las configuraciones de ExpressRoute que anuncian rutas entre la ruta de acceso de los pares públicos y la ruta de acceso de los pares privados. Las configuraciones de ExpressRoute con el emparejamiento público configurado recibirán anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian en la ruta de acceso de los pares privados, el resultado final es que se forzará la tunelización de todos los paquetes de red salientes desde la subred de App Service Environment a la infraestructura de red local de un cliente. Actualmente, este flujo de red no es compatible con las instancias de App Service Environment. La solución a este problema es dejar de anunciar rutas entre la ruta de acceso de los pares públicos y la ruta de acceso de los pares privados.

Para crear una UDR tal como se describe:

1.  Vaya a Azure portal > **Redes** > **Tablas de rutas**.
2.  Cree una nueva tabla de rutas en la misma región que la red virtual.
3.  En la interfaz de usuario de la tabla de ruta, seleccione **Rutas** > **Agregar**.
4.  Establezca **Tipo del  próximo salto** en **Internet** y **Prefijo de dirección** en _0.0.0.0/0_ y haga clic en **Guardar**.

Verá algo parecido a lo siguiente:

![][6]

Después de haber creado la nueva tabla de rutas, vaya a la subred que contiene el ASE. Seleccione la tabla de rutas de la lista que obtenga en el portal. Después de guardar el cambio, debería ver los NSG y las rutas anotadas con la subred.

![][7]

### <a name="deploying-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Implementación en redes virtuales de Azure existentes que están integradas con ExpressRoute ###

Si desea implementar su ASE en una red virtual que ya está integrada con ExpressRoute, necesita realizar una configuración previa en la subred en la que desea implementar el ASE y, a continuación, realizar la implementación con una plantilla de Resource Manager. Para crear un ASE en una red virtual que ya tiene configurado ExpressRoute:

- Cree una subred para hospedar el ASE.

    > [!NOTE]
    > En la subred no puede haber nada más que el ASE, asegúrese de elegir un espacio de direcciones lo suficientemente grande como para permitir el crecimiento futuro, ya que no lo podrá cambiar más adelante. Un `/25` con 128 direcciones es el tamaño recomendado.
- Cree las UDR (es decir, tablas de rutas) tal y como se ha descrito anteriormente y establezca esto en la subred.
- Cree el ASE mediante una plantilla de Resource Manager como se describe en el artículo sobre [Creación de un ASE mediante una plantilla de Azure Resource Manager][MakeASEfromTemplate].

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

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

