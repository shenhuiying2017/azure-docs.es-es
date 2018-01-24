---
title: "Integración de una aplicación con una instancia de Azure Virtual Network"
description: "Muestra cómo conectar una aplicación de Azure App Service a una red virtual de Azure nueva o existente"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: b755197af7e8791e01273bcc25f72c0d92ef6bc2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integración de su aplicación con una instancia de Azure Virtual Network
En este documento, se describe la característica Integración con redes virtuales de Azure App Service y se muestra cómo configurarla con aplicaciones en el [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Si no conoce las redes virtuales de Azure, se trata de una funcionalidad que le permite colocar muchos de sus recursos de Azure en una red no enrutable sin conexión a Internet cuyo acceso se controla. Después, estas redes se pueden conectar a sus redes locales mediante diversas tecnologías de VPN. Para más información sobre las redes virtuales de Azure, consulte: [Información general sobre Azure Virtual Network][VNETOverview]. 

Azure App Service adopta dos formas. 

1. Los sistemas multiinquilino que admiten la gama completa de planes de precios.
2. La característica premium App Service Environment (ASE), que se implementa en su red virtual. 

Este documento pasa a través de Integración con red virtual y no de App Service Environment. Si desea obtener más información sobre la característica de ASE, comience con la información que se presenta aquí: [Introducción a App Service Environment][ASEintro].

Integración con red virtual ofrece a su aplicación web acceso a los recursos de su red virtual, pero no concede acceso privado a su aplicación web desde la red virtual. El acceso al sitio privado se refiere a que la aplicación solo es accesible desde una red privada, como desde dentro de una red virtual de Azure. El acceso privado al sitio solo está disponible con un ASE configurado con un Equilibrador de carga interno (ILB). Para obtener más información sobre el uso de un ASE con un ILB, comience por leer el artículo aquí indicado: [Creación y uso de un ASE con un ILB][ILBASE]. 

Un escenario habitual en el que usaría Integración con red virtual es para permitir el acceso desde su aplicación web a una base de datos o a servicios web que se ejecutan en una máquina virtual de la red virtual de Azure. Con Integración con red virtual, no es necesario exponer un punto de conexión público para las aplicaciones en su máquina virtual, sino que puede usar las direcciones privadas no enrutables sin conexión a Internet en su lugar. 

La característica Integración con red virtual:

* requiere un plan de precios Premium, Estándar o Aislado; 
* funciona con una red virtual clásica o de Resource Manager; 
* es compatible con TCP y UDP;
* funciona con aplicaciones web, móviles, de API e instancias de Function App;
* permite que una aplicación se conecte solo a una red virtual a la vez;
* permite la integración con hasta cinco redes virtuales dentro de un plan de App Service; 
* permite que varias aplicaciones de un plan de App Service usen la misma red virtual;
* admite un SLA del 99,9 % debido al SLA de la puerta de enlace de red virtual.

Hay algunos aspectos que Integración con red virtual no admite, como:

* montar una unidad;
* la integración de AD; 
* NetBios;
* el acceso privado a sitios.

### <a name="getting-started"></a>Introducción
Le recordamos algunos aspectos que debe tener en cuenta antes de conectar su aplicación web a una red virtual:

* Integración con red virtual solo funciona con aplicaciones en un plan de precios **Estándar**, **Premium** o **Aislado**. Si habilita la característica y después escala su plan de App Service a un plan de precios no admitido, las aplicaciones pierden la conexión a las redes virtuales que estén usando. 
* Si su red virtual de destino ya existe, debe tener habilitada la VPN de punto a sitio con una puerta de enlace de enrutamiento dinámico para que se pueda conectar a una aplicación. Si la puerta de enlace está configurada con enrutamiento estático, no puede habilitar una red privada virtual (VPN) de punto a sitio.
* La red virtual debe compartir la misma suscripción que el plan de App Service (ASP). 
* Las aplicaciones que se integran con una red virtual usarán el DNS que se especifique para esa red virtual.
* De forma predeterminada, las aplicaciones que se integran solo enrutan el tráfico hacia la red virtual según las rutas definidas en la red virtual. 

## <a name="enabling-vnet-integration"></a>Habilitación de Integración con red virtual

Puede conectar una aplicación a una red virtual nueva o existente. Si crea una nueva red como parte de la integración, además de crearse la red virtual, se preconfigura de forma automática una puerta de enlace de enrutamiento dinámico y se habilita la VPN de punto a sitio. 

> [!NOTE]
> Se puede tardar unos minutos en configurar una nueva integración con redes virtuales. 
> 
> 

Para habilitar Integración con red virtual, abra la configuración de la aplicación y después seleccione Redes. La interfaz de usuario que se abre ofrece tres opciones de redes. En esta guía solo se va a tratar Integración con red virtual, aunque más adelante en este documento se explicarán Conexiones híbridas y a instancias de App Service Environment. 

Si la aplicación no pertenece al plan de precios correcto, la interfaz de usuario le permite escalarlo a otro superior de su elección.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Habilitación de Integración con red virtual con una red virtual existente
La interfaz de usuario de Integración con red virtual le permite seleccionar en una lista de redes virtuales. Las redes virtuales clásicas indican su pertenencia a esta clase con la palabra "Clásica" entre paréntesis junto al nombre de la red virtual. La lista está ordenada de tal forma que las redes virtuales de Resource Manager se muestran primero. En la imagen siguiente, se ve que solo se puede seleccionar una red virtual. Existen varias razones por las que una red virtual estará atenuada, entre otras:

* la red virtual pertenece a otra suscripción a la que su cuenta tiene acceso;
* la red virtual no tiene la conectividad de punto a sitio habilitada;
* la red virtual carece de una puerta de enlace de enrutamiento dinámico.

![][2]

Para habilitar la integración, basta con hacer clic en la red virtual con la que se desee integrar la aplicación. Después de seleccionar la red virtual, la aplicación se reinicia automáticamente para que los cambios surtan efecto. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Habilitación de una conectividad de punto a sitio en una red virtual clásica
Si la red virtual carece de una puerta de enlace o de conectividad de punto a sitio, primero debe configurarlas. Para hacerlo con una red virtual clásica, vaya a [Azure Portal][AzurePortal] y muestre la lista de redes virtuales (clásicas). Desde aquí, haga clic en la red con la que desee integrar la aplicación y haga clic en el cuadro grande bajo Essentials llamado Conexiones VPN. Aquí puede crear la VPN de punto a sitio e incluso hacer que se cree una puerta de enlace. Después de completar la experiencia de creación de la conexión de punto a sitio y la puerta de enlace, pasan unos 30 minutos hasta que todo esté listo. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Habilitación de punto a sitio en una red virtual de Resource Manager
Para configurar una red virtual de Resource Manager con una puerta de enlace y la conexión de punto a sitio, puede usar PowerShell tal y como se describe aquí ([Configuración de una conexión punto a sitio a una red virtual mediante PowerShell][V2VNETP2S]) o utilizar Azure Portal tal como se explica aquí ([Configuración de una conexión de punto a sitio a una red virtual mediante Azure Portal][V2VNETPortal]). La interfaz de usuario que debe realizar esta funcionalidad no está aún disponible. Tenga en cuenta que no tiene que crear certificados para la configuración de punto a sitio. Esto se configura automáticamente cuando se conecte la aplicación web a la red virtual. 

### <a name="creating-a-pre-configured-vnet"></a>Creación de una red virtual preconfigurada
Si desea crear una nueva red virtual que esté configurada con una puerta de enlace y una conexión de punto a sitio, la interfaz de usuario de redes de App Service dispone de la funcionalidad para hacerlo, pero solo para una red virtual de Resource Manager. Si desea crear una red virtual clásica con una puerta de enlace y una conexión de punto a sitio, deberá hacerlo manualmente a través de la interfaz de usuario de redes. 

Para crear una red virtual de Resource Manager a través de la interfaz de usuario de Integración con red virtual, seleccione **Crear una red virtual nueva** y especifique:

* El nombre de la red virtual
* El bloque de direcciones de la red virtual
* Nombre de subred
* El bloque de direcciones de la subred
* El bloque de direcciones de la puerta de enlace
* El bloque de direcciones de la conexión de punto a sitio

Si desea que esta red virtual se conecte a cualquiera de sus otras redes, no debe elegir un espacio de direcciones IP que se solape con esas redes. 

> [!NOTE]
> La creación de una red virtual de Resource Manager con una puerta de enlace tarda unos 30 minutos y actualmente no integra la red virtual con la aplicación. Una vez creada la red virtual con la puerta de enlace, debe volver a la interfaz de usuario de Integración con red virtual de la aplicación y seleccionar la nueva red virtual.
> 
> 

![][3]

Las redes virtuales de Azure normalmente se crean dentro de direcciones de red privadas. De forma predeterminada, la característica Integración con red virtual enrutará el tráfico destinado a esos intervalos de direcciones IP hacia la red virtual. Los intervalos de direcciones IP privadas son:

* 10.0.0.0/8: es igual que 10.0.0.0 - 10.255.255.255.
* 172.16.0.0/12: es igual que 172.16.0.0 - 172.31.255.255. 
* 192.168.0.0/16: es igual que 192.168.0.0 - 192.168.255.255.

El espacio de direcciones de red virtual debe especificarse con la notación CIDR. Si desconoce la notación CIDR, es un método para especificar bloques de direcciones con una dirección IP y un entero que representa la máscara de red. Como referencia rápida, tenga en cuenta que 10.1.0.0/24 corresponde a 256 direcciones y 10.1.0.0/25, a 128. Una dirección IPv4 con un /32 sería simplemente una dirección. 

Si establece aquí la información del servidor DNS, quedará establecida para la red virtual. Después de crearse la red virtual, puede editar esta información desde las experiencias de usuario de red virtual. Si cambia el DNS de la red virtual, debe realizar una operación de sincronización de red.

Cuando se crea una red virtual clásica mediante la interfaz de usuario de Integración con red virtual, se crea una red virtual en el mismo grupo de recursos que la aplicación. 

## <a name="how-the-system-works"></a>Funcionamiento del sistema
Esta característica se basa en la tecnología de VPN de punto a sitio para conectar la aplicación a la red virtual. Las aplicaciones de Azure App Service tienen una arquitectura de sistema de varios inquilinos que impide el aprovisionamiento de una aplicación directamente en una red virtual como se hace con las máquinas virtuales. Al basarse en tecnología de punto a sitio, se limita el acceso a la red a únicamente la máquina virtual que hospeda la aplicación. El acceso a la red se restringe aún más en esos hosts de aplicaciones para que sus aplicaciones solo puedan acceder a las redes que se configuren para ese fin. 

![][4]

Si no ha configurado un servidor DNS con su red virtual, la aplicación necesitará usar direcciones IP para acceder al recurso de la red virtual. Cuando use direcciones IP, recuerde que la principal ventaja de esta característica es que le permite usar las direcciones privadas dentro de la red privada. Si ha configurado la aplicación para que use direcciones IP públicas para una de las máquinas virtuales, no está usando la característica Integración con red virtual, sino que se está comunicando a través de Internet.

## <a name="managing-the-vnet-integrations"></a>Administración de las integraciones con redes virtuales
La capacidad de conectarse a una red virtual y desconectarse de ella se encuentra en un nivel de aplicación. Las operaciones que pueden afectar a la integración con red virtual en varias aplicaciones se encuentran en un nivel del plan de App Service. Desde la interfaz de usuario que se muestra en el nivel de aplicación, puede obtener detalles sobre la red virtual. Gran parte de esta misma información también se muestra en el nivel de plan de servicio de aplicaciones. 

![][5]

En la página Estado de característica de red, puede ver si la aplicación está conectada a la red virtual. Si la puerta de enlace de red virtual no está disponible por cualquier razón, aparecerá como no conectada. 

La información que tiene ahora disponible en la interfaz de usuario de Integración con red virtual en el nivel de aplicación es igual que la información detallada que se obtiene desde el plan de App Service. Esto es lo que cubre:

* Nombre de red virtual: este vínculo abre la interfaz de usuario de red virtual de Azure.
* Ubicación: esto refleja la ubicación de la red virtual. Es posible realizar la integración con una red virtual en otra ubicación.
* Estado de certificado: hay certificados que se usan para proteger la conexión VPN entre la aplicación y la red virtual. Esto refleja una prueba para asegurarse de que estén sincronizados.
* Estado de la puerta de enlace: si las puertas de enlace estuvieran inactivas por algún motivo, la aplicación no podrá acceder a los recursos de la red virtual. 
* Espacio de direcciones de red virtual: se trata del espacio de direcciones IP para la red virtual. 
* Espacio de direcciones de punto a sitio: es el espacio de direcciones IP de punto a sitio para la red virtual. La aplicación muestra la comunicación como procedente de una de las direcciones IP de este espacio de direcciones. 
* Espacio de direcciones de sitio a sitio: puede usar VPN de sitio a sitio para conectar la red virtual a sus recursos locales o a otras redes virtuales. Si lo tiene configurado, aquí se muestran los intervalos de direcciones IP definidos para esa conexión VPN.
* Servidores DNS: si tiene servidores DNS configurados con la red virtual, aparecen aquí.
* Direcciones IP enrutadas a la red virtual: hay una lista de direcciones IP para las que la red virtual tiene definido el enrutamiento y esas direcciones se muestran aquí. 

La única operación que puede realizar en la vista de aplicación de Integración con red virtual es desconectar la aplicación de la red virtual si está conectada a ella. Para hacerlo, basta con hacer clic en Desconectar arriba. Esta acción no cambia la red virtual. La red virtual y su configuración, incluidas las puertas de enlace, permanecen intactas. Si después desea eliminar la red virtual, debe eliminar primero los recursos que hay en ella, incluidas las puertas de enlace. 

La vista del plan de App Service ofrece varias operaciones adicionales. Además, también se accede a ella de forma diferente desde la aplicación. Para llegar a la interfaz de usuario de Redes del plan de servicio de aplicaciones, abra la interfaz de usuario del plan de servicio de aplicaciones y desplácese hacia abajo. Hay un elemento de interfaz de usuario llamado Estado de característica de red. Ofrece varios detalles menores sobre Integración con red virtual. Si hace clic en este elemento, se abre la interfaz de usuario de Estado de característica de red. Si después hace clic en "Haga clic aquí para administrar", se abrirá la interfaz de usuario que enumera las integraciones con redes virtuales en este plan de App Service.

![][6]

Está bien recordar la ubicación del plan de App Service cuando se observan las ubicaciones de las redes virtuales con las que va a realizar la integración. Cuando la red virtual está en otra ubicación, es mucho más probable que se experimenten problemas de latencia. 

Las redes virtuales integradas constituyen un recordatorio de con cuántas redes virtuales están integradas sus aplicaciones en este plan de App Service y cuántas puede tener. 

Para ver más detalles sobre cada red virtual, haga clic en la red virtual que le interese. Además de los detalles que se indicaron antes, también verá una lista de las aplicaciones de este plan de App Service que usan esa red virtual. 

Con respecto a las acciones, existen dos principales. La primera es la capacidad de agregar rutas que controlan el tráfico que sale de la aplicación hacia la red virtual. La segunda acción es la capacidad de sincronizar los certificados y la información de la red.

![][7]

**Enrutamiento** Como se indicó antes, las rutas definidas en la red virtual son lo que se usa para dirigir el tráfico desde la aplicación hacia la red virtual. Sin embargo, en algunos casos, los clientes desean enviar más tráfico saliente desde una aplicación hacia la red virtual y para ellos se proporciona esta capacidad. Lo que ocurra con el tráfico después de eso depende de cómo el cliente configure su red virtual. 

**Certificados** El campo Estado de certificado refleja que App Service está llevando a cabo una comprobación para confirmar que los certificados que se estén usando para la conexión VPN sigan siendo válidos. Cuando se habilita Integración con red virtual, si es la primera integración con dicha red virtual desde las aplicaciones de este plan de App Service, se produce un intercambio obligatorio de certificados para garantizar la seguridad de la conexión. Junto con los certificados, se obtienen la configuración de DNS, las rutas y otros elementos similares que describen la red.
Si cambian los certificados o la información de red, es necesario hacer clic en "Sincronizar red". **NOTA**: Al hacer clic en "Sincronizar red", se producirá una breve interrupción en la conectividad entre la aplicación y la red virtual. Aunque no se reinicia la aplicación, la pérdida de conectividad podría hacer que su sitio no funcione correctamente. 

## <a name="accessing-on-premises-resources"></a>Obtener acceso a recursos locales
Una de las ventajas de la característica Integración con red virtual es que, si la red virtual está conectada a la red local con una VPN de sitio a sitio, las aplicaciones pueden acceder a los recursos locales desde su aplicación. Para que esto funcione, puede que necesite actualizar la puerta de enlace de VPN local con las rutas de su intervalo de direcciones IP de punto a sitio. En la configuración inicial de la VPN de sitio a sitio, los scripts que se usan para configurarla deberían también configurar las rutas, incluida la VPN de punto a sitio. Si agrega la VPN de punto a sitio después de crear la VPN de sitio a sitio, deberá actualizar las rutas manualmente. El procedimiento varía según la puerta de enlace y no se describe aquí. 

> [!NOTE]
> La característica Integración con red virtual no integra una aplicación con una red virtual que tiene una puerta de enlace de ExpressRoute. Incluso si la puerta de enlace de ExpressRoute está configurada en [modo de coexistencia][VPNERCoex], Integración con red virtual no funcionará. Si necesita tener acceso a los recursos mediante una conexión de ExpressRoute, puede utilizar una instancia de [App Service Environment][ASE] que se ejecute en la red virtual.
> 
> 

## <a name="pricing-details"></a>Detalles de precios
Hay algunos matices sobre los precios que se deben tener en cuenta al usar la característica Integración con red virtual. El uso de esta característica comporta tres cargos:

* Requisitos del plan de tarifa para el plan de servicio de aplicaciones
* Costos de la transferencia de datos
* Costos de VPN Gateway

Para que las aplicaciones puedan usar esta característica, deben pertenecer a un plan de App Service Premium o Estándar. Puede ver más detalles sobre esos costos aquí: [Precios de App Service][ASPricing]. 

Debido a la forma en que se controlan las VPN de punto a sitio, siempre tiene un cargo por los datos salientes a través de la conexión de Integración con red virtual, incluso si la red virtual está en el mismo centro de datos. Para ver cuáles son dichos cargos, consulte: [Detalles de precios de transferencia de datos][DataPricing]. 

El último elemento es el costo de las puertas de enlace de red virtual. Si no necesita las puertas de enlace para algo diferente, como las VPN de sitio a sitio, va a pagar para que las puertas de enlace admitan la característica Integración con red virtual. Los detalles sobre esos costos están aquí: [Precios de VPN Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>solución de problemas
El que una característica sea fácil de configurar no quiere decir que no presente problemas con el uso. Si encuentra problemas para acceder al punto de conexión que desee, existen varias utilidades que sirven para probar la conectividad desde la consola de la aplicación. Puede usar dos experiencias de consola. Una es la consola Kudu y la otra es la consola a la que se accede en Azure Portal. Para llegar a la consola Kudu desde la aplicación, vaya a Herramientas -> Kudu. Esto equivale a ir a [nombreDeSitio].scm.azurewebsites.net. Cuando se abra, simplemente vaya a la pestaña de consola Debug (Depurar). Para llegar a la consola hospedada en el Portal de Azure, desde su aplicación, vaya a Herramientas -> Consola. 

#### <a name="tools"></a>Herramientas
Las herramientas ping, nslookup y tracert no funcionarán a través de la consola por restricciones de seguridad. Para suplir esta falta, se han agregado dos herramientas independientes. Para probar la funcionalidad de DNS, agregamos una herramienta denominada nameresolver.exe. La sintaxis es:

    nameresolver.exe hostname [optional: DNS Server]

Puede usar nameresolver para comprobar los nombres de host de los que depende la aplicación. De este modo, puede probar si hay algo mal configurado en el DNS o si no tiene acceso al servidor DNS.

La siguiente herramienta permite probar la conectividad de TCP para una combinación de host y puerto. Esta herramienta se llama tcpping.exe y la sintaxis es:

    tcpping.exe hostname [optional: port]

La utilidad tcpping indica si se puede llegar a un host y a puerto específicos. Solo puede mostrar correcto si hay una aplicación que escucha en la combinación de host y puerto, y no hay acceso a la red de la aplicación para el host especificado y el puerto.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depuración del acceso a recursos hospedados en la red virtual
Hay varias situaciones que pueden impedir que la aplicación llegue a un host y un puerto específicos. La mayoría de las veces se debe a una de estas tres causas:

* **Hay un firewall en la comunicación**: si tiene un firewall en la comunicación, se agotará el tiempo de espera de TCP. En este caso, es de 21 segundos. Utilice la herramienta tcpping para probar la conectividad. Los tiempos de espera agotados de TCP pueden deberse a muchas causas, además de los firewalls, pero comience por comprobar los firewalls. 
* **El DNS no está accesible**: el tiempo de espera del DNS es de tres segundos por cada servidor DNS. Si tiene dos servidores DNS, el tiempo de espera es de seis segundos. Utilice nameresolver para ver si funciona el DNS. Recuerde que no puede usar nslookup ya que este no utiliza el DNS con el que está configurada su red virtual.
* **Intervalo IP de P2S no válido**  El intervalo IP de punto a sitio debe estar en los intervalos IP privado de RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Si el intervalo utiliza direcciones IP externas, las cosas no funcionarán. 

Si estos elementos no resuelven su problema, busque primero elementos sencillas como: 

* ¿Aparece la puerta de enlace como activa en el Portal?
* ¿Indican los certificados que están sincronizados?
* ¿Cambió alguien la configuración de la red sin hacer clic en "Sincronizar red" en los planes de servicio de aplicaciones afectados? 

Si la puerta de enlace está inactiva, vuelva a activarla. Si los certificados no están sincronizados, vaya a la vista del plan de App Service en Integración con red virtual y haga clic en "Sincronizar red". Si sospecha que se realizó un cambio en la configuración de la red virtual y no se sincronizó con el plan de App Service, vaya a la vista del plan de App Service de Integración con red virtual y haga clic en "Sincronizar red". Recuerde que esto causará una breve interrupción en la conexión entre la red virtual y sus aplicaciones. 

Si todo eso está bien, debe profundizar un poco más:

* ¿Hay alguna otra aplicación que utilice Integración con red virtual para tener acceso a recursos en la misma red virtual? 
* ¿Puede ir a la consola de la aplicación y usar tcpping para llegar a cualquier otro recurso de la red virtual? 

Si la respuesta a cualquiera de las anteriores preguntas es afirmativa, Integración con red virtual es correcta y el problema reside en otro lugar. Aquí la situación se complica porque no existe una forma sencilla de ver por qué no se llega a un host:puerto. Algunas de las causas son:

* tiene un firewall en el host que impide el acceso al puerto de la aplicación desde su intervalo de direcciones IP de punto a sitio. Para cruzar subredes, se requiere a menudo acceso público.
* el host de destino está inactivo;
* la aplicación está inactiva;
* la dirección IP o el nombre de host son incorrectos;
* la aplicación escucha en un puerto diferente del que se esperaba. Para comprobarlo, vaya a ese host y use "netstat -aon" en el símbolo del sistema. Esto le muestra qué identificador de proceso está escuchando en qué puerto; 
* los grupos de seguridad de la red están configurados de tal manera que impiden el acceso al host y al puerto de la aplicación desde su intervalo de direcciones IP de punto a sitio.

Recuerde que no sabe qué dirección IP del intervalo de direcciones IP de punto a sitio usará la aplicación, así que necesita permitir el acceso a todo el intervalo. 

Otros pasos de depuración son:

* iniciar sesión en otra máquina virtual de la red virtual e intentar acceder al host:puerto de recursos desde ella. Hay algunas utilidades ping basadas en TCP que sirven para este propósito o incluso puede usar telnet si es necesario. El objetivo es solamente determinar si existe conectividad desde esta otra máquina virtual; 
* abrir una aplicación en otra máquina virtual y probar el acceso a ese host y ese puerto desde la consola de la aplicación.

#### <a name="on-premises-resources"></a>Recursos locales ####
Si la aplicación no puede acceder a los recursos locales, lo primero que debe comprobar es si puede llegar a un recurso de la red virtual. Si funciona, intente conectarse a la aplicación local desde una máquina virtual de la red virtual. Puede usar telnet o una utilidad ping basada en TCP. Si la máquina virtual no puede acceder al recurso local, asegúrese de que funciona la conexión VPN de sitio a sitio. Si funciona, compruebe lo mismo que se indicó antes, así como el estado y la configuración de la puerta de enlace local. 

Ahora bien, si la máquina virtual hospedada en la red virtual puede acceder a su sistema local pero la aplicación no puede, es probable que el motivo sea uno de los siguientes:

* Las rutas no están configuradas con los intervalos IP de punto a sitio en la puerta de enlace local
* Los grupos de seguridad de red están bloqueando el acceso del intervalo IP de punto a sitio
* Los firewalls locales están bloqueando el tráfico procedente del intervalo IP de punto a sitio
* Tiene una ruta definida por el usuario (UDR) en la red virtual que impide que el tráfico basado en la conectividad de punto a sitio llegue a su red local

## <a name="powershell-automation"></a>Automatización de PowerShell

Puede integrar App Service con Azure Virtual Network mediante PowerShell. Para obtener un script que esté listo para ejecutarse, consulte [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3) (Conectar una aplicación de Azure App Service a una red de Azure Virtual Network).

## <a name="hybrid-connections-and-app-service-environments"></a>Conexiones híbridas y entornos de App Service
Existen tres características que permiten el acceso a los recursos hospedados en la red virtual. Son las siguientes:

* Integración con red virtual
* conexiones híbridas
* Entornos de App Service

Conexiones híbridas requiere que se instale un agente de retransmisión llamado administrador de conexiones híbridas (HCM) en la red. El HCM debe ser capaz de conectarse a Azure y también a la aplicación. Esta solución es especialmente atractiva desde una red remota como su red local o incluso otra red hospedada en la nube porque no requiere un punto de conexión accesible por Internet. El HCM solamente se ejecuta en Windows y puede tener hasta cinco instancias en ejecución para proporcionar alta disponibilidad. Sin embargo, Conexiones híbridas solo admite TCP y cada uno de sus puntos de conexión tiene que coincidir con una combinación de host:puerto específica. 

La característica App Service Environment permite ejecutar una instancia de Azure App Service en la red virtual. Esto permite que sus aplicaciones accedan a recursos de la red virtual sin ningún paso adicional. Algunas de las otras ventajas de un entorno de App Service Environment es que se pueden usar trabajos dedicados Dv2 con hasta 14 GB de RAM. Otra ventaja es que se puede escalar el sistema para que satisfaga sus necesidades. A diferencia de los entornos de varios inquilinos en los que el plan de App Service se limita a 20 instancias, en un ASE puede escalar hasta 100 instancias del plan de App Service. Una de las cosas que ofrece un entorno de App Service Environment que no encontrará en Integración con red virtual es que funciona con una VPN de ExpressRoute. 

Aunque algunos casos de uso se solapan, ninguna de estas características puede reemplazar a las otras. Saber qué característica emplear depende de sus necesidades. Por ejemplo: 

* Si es un desarrollador y simplemente desea ejecutar un sitio en Azure y acceder a la base de datos en la estación de trabajo que suele usar, lo más fácil de usar es Conexiones híbridas. 
* Si es una gran organización que desea colocar un gran número de propiedades web en la nube pública y administrarlas en su propia red, entonces le interesará App Service Environment. 
* Si tiene varias aplicaciones hospedadas con App Service y simplemente desea acceder a recursos de la red virtual, Integración con red virtual es la mejor opción. 

Más allá de los casos de uso, hay diversos aspectos relacionados con la simplicidad. Si la red virtual ya está conectada a la red local, usar Integración con red virtual o una instancia de App Service Environment es una manera fácil de consumir recursos locales. Por otro lado, si la red virtual no está conectada a la red local, resulta mucho más costoso configurar una VPN de sitio a sitio con la red virtual en comparación con instalar el HCM. 

Además de las diferencias funcionales, existen también diferencias de precio. La característica Entorno de App Service es un servicio premium, pero ofrece la mayor variedad de configuraciones de red, además de otras características interesantes. Integración con red virtual se puede usar con el plan de App Service Estándar o Premium y es ideal para consumir de forma segura recursos de la red virtual desde App Service con varios inquilinos. Actualmente, Conexiones híbridas depende de un cuenta de BizTalk con niveles de precios que comienzan de forma gratuita y progresivamente van aumentando según la cantidad que necesite. Sin embargo, cuando se necesita trabajar en muchas redes, no hay ninguna característica como Conexiones híbridas, que permite acceder a recursos de más de 100 redes distintas. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
