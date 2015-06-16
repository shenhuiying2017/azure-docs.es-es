<properties 
	pageTitle="Cómo controlar el tráfico de entrada a un entorno del Servicio de aplicaciones " 
	description="Obtenga información acerca de cómo configurar las reglas de seguridad de red para controlar el tráfico entrante a un entorno del Servicio de aplicaciones." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Cómo controlar el tráfico de entrada a un entorno del Servicio de aplicaciones 

## Información general ##
Siempre se crea un entorno del Servicio de aplicaciones en una subred de una [red virtual][virtualnetwork] regional. Una nueva red virtual regional y una nueva subred pueden definirse en el momento en que se crea un entorno del Servicio de aplicaciones. También puede crearse un entorno del Servicio de aplicaciones en una red virtual regional y subred preexistentes. Para obtener más detalles sobre la creación de un entorno del Servicio de aplicaciones, consulte [Creación de un entorno del Servicio de aplicaciones][HowToCreateAnAppServiceEnvironment].

Siempre debe crearse un entorno del Servicio de aplicaciones dentro de una subred al proporcionar esta un límite de red que puede utilizarse para bloquear el tráfico entrante tras dispositivos y servicios ascendentes de forma que solo se acepta el tráfico HTTP y HTTPS de determinadas direcciones IP ascendentes.

El tráfico de red entrante y saliente de una subred se controla mediante un [grupo de seguridad de red][NetworkSecurityGroups]. Para controlar el tráfico entrante, se requiere la creación de reglas de seguridad de red en un grupo de seguridad de red y, a continuación, la asignación a este de la subred que contiene el entorno del Servicio de aplicaciones.

Una vez que un grupo de seguridad de red se asigna a una subred, el tráfico entrante a aplicaciones en el entorno del Servicio de aplicaciones se permite o bloquea según las reglas de permiso y denegación definidas en el grupo de seguridad de red.

## Puertos de red usados en un entorno del Servicio de aplicaciones ##
Antes de bloquear el tráfico de red entrante con un grupo de seguridad de red, es importante conocer el conjunto de puertos de red necesarios y opcionales utilizados por un entorno del Servicio de aplicaciones. El bloqueo accidental del tráfico a algunos puertos puede producir la pérdida de funcionalidad en un entorno del Servicio de aplicaciones.

A continuación se muestra una lista de puertos utilizados por un entorno del Servicio de aplicaciones:

- 454: **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener entornos del Servicio de aplicaciones. No bloquee el tráfico a este puerto.
- 455: **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener entornos del Servicio de aplicaciones. No bloquee el tráfico a este puerto.
- 80: puerto predeterminado para el tráfico HTTP entrante a aplicaciones que se ejecutan en planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones
- 443: puerto predeterminado para el tráfico SSL entrante a aplicaciones que se ejecutan en planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones
- 21: canal de control para FTP. Este puerto se puede bloquear de forma segura si no se utiliza FTP.
- 10001-10020: canales de datos para FTP. Al igual que ocurre con el canal de control, estos puertos se pueden bloquear de forma segura si no se utiliza FTP (**Nota:** los canales de datos FTP pueden cambiar durante la vista previa).
- 4016: usado para la depuración remota con Visual Studio 2012. Este puerto se puede bloquear de forma segura si no se utiliza la característica.
- 4018: usado para la depuración remota con Visual Studio 2013. Este puerto se puede bloquear de forma segura si no se utiliza la característica.
- 4020: usado para la depuración remota con Visual Studio 2015. Este puerto se puede bloquear de forma segura si no se utiliza la característica.

## Creación de un grupo de seguridad de red ##
Para obtener los detalles completos de cómo funcionan los grupos de seguridad de red, consulte la siguiente [información][NetworkSecurityGroups]. Los detalles siguientes hacen referencia a los puntos destacados de los grupos de seguridad de red, centrándose en la configuración y aplicación de un grupo de seguridad de red a una subred que contiene un entorno del Servicio de aplicaciones.

Los grupos de seguridad de red se crean por primera vez como una entidad independiente asociada a una suscripción. Puesto que los grupos de seguridad de red se crean en una región de Azure, asegúrese de que el grupo de seguridad de red se crea en la misma región que el entorno del Servicio de aplicaciones.

Lo siguiente muestra la creación de un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Una vez creado un grupo de seguridad de red, una o varias reglas de seguridad de red se agregan a él. Dado que el conjunto de reglas puede cambiar con el tiempo, se recomienda espaciar el esquema de numeración usado para las prioridades de reglas para facilitar la inserción de reglas adicionales con el tiempo.

En el ejemplo siguiente se muestra una regla que concede acceso de forma explícita a los puertos de administración necesarios para que la infraestructura de Azure administre y mantenga un entorno del Servicio de aplicaciones. Tenga en cuenta que todo el tráfico de administración fluye a través de SSL y se protege mediante certificados de cliente, por lo que aunque estén abiertos los puertos, son inaccesibles para cualquier entidad que no sea la infraestructura de administración de Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Al bloquear el acceso a los puertos 80 y 443 para "ocultar" un entorno del Servicio de aplicaciones tras dispositivos o servicios ascendentes, tendrá que conocer la dirección IP ascendente. Por ejemplo, si usa un firewall de aplicaciones web (WAF), este tendrá su propia dirección (o direcciones) IP, que usa al transmitir el tráfico a un entorno del Servicio de aplicaciones de nivel auxiliar. Deberá usar esta dirección IP en el parámetro *SourceAddressPrefix* de una regla de seguridad de red.

En el ejemplo siguiente, se permite explícitamente el tráfico entrante de una dirección IP ascendente específica. La dirección *1.2.3.4* se usa como marcador de posición para la dirección IP de un WAF ascendente. Cambie el valor para que coincida con la dirección usada por su dispositivo o servicio ascendente.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Si se desea compatibilidad con FTP, las reglas siguientes pueden utilizarse como plantilla para conceder acceso al puerto de control FTP y los puertos del canal de datos. Puesto que FTP es un protocolo con estado, es posible que no pueda enrutar el tráfico FTP a través de un dispositivo de firewall o proxy HTTP/HTTPS. En este caso deberá establecer *SourceAddressPrefix* en un valor diferente: por ejemplo, el intervalo de direcciones IP del desarrollador o los equipos de implementación en los que se ejecutan los clientes FTP.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** puede cambiar el intervalo de puertos del canal de datos durante el período de vista previa).

Si se usa la depuración remota con Visual Studio, las reglas siguientes muestran cómo conceder acceso. Puesto que cada versión usa un puerto diferente para la depuración remota, hay una regla distinta para cada versión compatible de Visual Studio. Al igual que ocurre con el acceso FTP, es posible que el tráfico de depuración remoto no fluya correctamente a través de un dispositivo de proxy o WAF tradicional. En su lugar, *SourceAddressPrefix* se puede establecer en el intervalo de direcciones IP de los equipos del desarrollador que ejecutan Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Asignación de un grupo de seguridad de red a una subred ##
Un grupo de seguridad de red tiene una regla de seguridad predeterminada que deniega el acceso a todo el tráfico externo. El resultado de la combinación de las reglas de seguridad de red descritas anteriormente y la regla de seguridad predeterminada que bloquea el tráfico entrante es que solo el tráfico de intervalos de direcciones de origen asociado a una acción *Permitir* podrá enviar tráfico a aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones.

Después de rellenarse un grupo de seguridad de red con las reglas de seguridad, debe asignarse a la subred que contiene el entorno del Servicio de aplicaciones. El comando de asignación hace referencia tanto al nombre de la red virtual donde reside el entorno del Servicio de aplicaciones como al nombre de la subred donde se creó este.

En el ejemplo siguiente se muestra la asignación de un grupo de seguridad de red a una subred y una red virtual:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Cuando se realice correctamente la asignación del grupo de seguridad de red (la asignación es una operación de ejecución prolongada y puede tardar unos minutos en completarse), solo el tráfico entrante que coincida con las reglas de *permisión* llegará correctamente a las aplicaciones del entorno del Servicio de aplicaciones.

Por razones de integridad, en el ejemplo siguiente se muestra cómo quitar y, por tanto, desasociar el grupo de seguridad de red de la subred:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Consideraciones especiales para IP-SSL explícito ##
Si una aplicación se configura con una dirección IP explícita en lugar de la dirección IP predeterminada del entorno del Servicio de aplicaciones, tanto el tráfico HTTP como el tráfico HTTPS fluyen en la subred a través de un conjunto de puertos distintos de los puertos 80 y 443.

Durante la vista previa inicial de los entornos del Servicio de aplicaciones, no es posible determinar los puertos específicos usados por IP-SSL. Sin embargo, una vez que esta información se expone a través del portal, las herramientas de línea de comandos y las API de REST, los desarrolladores podrán configurar los grupos de seguridad de red para controlar también el tráfico a través de estos puertos.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, consulte [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment]

Para obtener detalles en torno a las aplicaciones que se conectan de forma segura al recurso de back-end desde un entorno del Servicio de aplicaciones, consulte [Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones][SecurelyConnecttoBackend]

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!--HONumber=52-->
 