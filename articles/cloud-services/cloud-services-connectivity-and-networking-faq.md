---
title: P+F sobre los problemas de conectividad y redes en Microsoft Azure Cloud Services | Microsoft Docs
description: "En este artículo se enumeran las preguntas frecuentes sobre conectividad y redes para Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: d86f3ec043c504c9d79b18f1f0b4c9cf0adb115b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de conectividad y redes en Azure Cloud Services: preguntas más frecuentes (P+F)

Este artículo incluye las preguntas frecuentes sobre conectividad y redes para [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). También puede consultar la [página Tamaños de Cloud Services](cloud-services-sizes-specs.md) para obtener información de tamaño.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>No se puede reservar una dirección IP en un servicio en la nube con varias direcciones IP virtuales
En primer lugar, asegúrese de que la instancia de máquina virtual para la que está intentando reservar la dirección IP está activada. En segundo lugar, asegúrese de que utiliza direcciones IP reservadas para las implementaciones de ensayo y de producción. **No** cambie la configuración mientras se está actualizando la implementación.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>¿Cómo se usa el escritorio remoto con un NSG?
Agregue reglas al NSG que permitan el tráfico en los puertos **3389** y **20000**.  El Escritorio remoto usa el puerto **3389**.  Las instancias del servicio en la nube tienen la carga equilibrada, por lo que no se puede controlar directamente a qué instancia conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* administran el tráfico RDP y permiten al cliente enviar una cookie de RDP y especificar una instancia concreta a la que conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* requieren que ese puerto **20000** se abra (puede estar bloqueado si tiene un NSG).

## <a name="can-i-ping-a-cloud-service"></a>¿Se puede hacer ping a un servicio de nube?

No, no usando un "ping" normal / protocolo ICMP. No se permite el protocolo ICMP a través de Azure Load Balancer.

Para probar la conectividad, se recomienda que realice un ping de puerto. Mientras que Ping.exe usa ICMP, otras herramientas, como PSPing, Nmap, y telnet permiten probar la conectividad con un puerto TCP específico.

Para más información, consulte [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (uso de pings de puerto en lugar de ICMP para probar la conectividad de la máquina virtual de Azure).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>¿Cómo se puede evitar la recepción de miles de aciertos de direcciones IP desconocidas que indican algún tipo de ataque malintencionado al servicio de nube?
Azure implementa una seguridad de red multicapa para proteger sus servicios de plataforma contra los ataques de denegación de servicio distribuido (DDoS). El sistema de defensa DDoS de Azure forma parte del proceso de supervisión continuo de Azure, que mejora continuamente a través de pruebas de penetración. Este sistema de defensa DDoS está diseñado para resistir ataques no solo desde el exterior, sino también de otros inquilinos de Azure. Para información más detallada, consulte el documento sobre [Seguridad de red de Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

También puede crear una tarea de inicio para bloquear de manera selectiva algunas direcciones IP específicas. Para más información, consulte [Bloqueo de una dirección IP específica](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Cuando intento ejecutar el protocolo de escritorio remoto (RDP) para la instancia de servicio de nube, obtengo el mensaje, "La cuenta de usuario ha expirado".
Puede obtener el mensaje de error "Esta cuenta de usuario expiró" cuando ignora la fecha de expiración que está configurada en los ajustes de RDP. Puede cambiar la fecha de expiración desde el portal siguiendo estos pasos:
1. Inicie sesión en [Azure Portal](https://portal.azure.com), vaya a su servicio en la nube y seleccione la pestaña **Escritorio remoto**.
2. Seleccione la ranura de implementación **Producción** o **Ensayo**.
3. Cambie la fecha "Expira en" y, a continuación, guarde la configuración.

Ahora podrá ejecutar el RDP en el equipo.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>¿Por qué LoadBalancer no equilibra el tráfico de forma equitativa?
Para información acerca de cómo funciona de equilibrador de carga interno, consulte [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nuevo modo de distribución de Azure Load Balancer).

El algoritmo de distribución usado para asignar el tráfico a los servidores disponibles es una tupla de 5 componentes (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo). Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el extremo con equilibrio de carga. Cuando el cliente se cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia y provoca que el tráfico vaya hacia otro extremo DIP.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>¿Cómo puedo redirigir el tráfico que entra en mi dirección URL predeterminada de un servicio en la nube a una dirección URL personalizada predeterminada? 

El módulo URL Rewrite de IIS podría usarse para redirigir el tráfico que entra en la dirección URL predeterminada del servicio en la nube (por ejemplo, \*.cloudapp.net) a alguna dirección URL o nombre DNS personalizados. Dado que el módulo URL Rewrite está habilitado de forma predeterminada en los roles web y sus reglas se configuran en el archivo web.config de la aplicación, siempre estará disponible en la máquina virtual con independencia de los reinicios o los restablecimientos de la imagen inicial. Para más información, consulte:

- [Creating Rewrite Rules for the URL Rewrite Module](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module) (Creación de reglas de reescritura para el módulo URL Rewrite)
- [How to remove default link](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top) (Eliminación de un vínculo predeterminado)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>¿Cómo puedo bloquear o deshabilitar el tráfico entrante a la dirección URL predeterminada de mi servicio en la nube? 

Puede impedir la entrada de tráfico en la dirección URL o nombre predeterminados de su servicio en la nube (por ejemplo, \*.cloudapp.net) si establece el encabezado host en un nombre DNS personalizado (por ejemplo, www.MyCloudService.com) en la configuración de enlace de sitio en el archivo de definición de servicio en la nube (*.csdef), como se indica a continuación: 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
Dado que este enlace de encabezado host se aplica mediante un archivo csdef, el servicio solo será accesible mediante el nombre personalizado "www.MyCloudService.com", siempre y cuando todas las solicitudes entrantes al dominio "*.cloudapp.net" produzcan error en todo momento. Dicho esto, si usa un sondeo de SLB personalizado o un equilibrador de carga interno en el servicio, el bloqueo de la dirección URL o el nombre predeterminados del servicio puede interferir con el comportamiento de sondeo. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>¿Cómo se puede tener la seguridad de que la dirección IP de acceso público de un servicio en la nube (también conocida como VIP) no cambie nunca para que algunos clientes concretos puedan incluirla habitualmente en la lista de permitidos?

Para incluir la dirección IP de sus servicios en la nube en la lista de permitidos, se recomienda tener una IP reservada asociada a ella, si no, la IP virtual que proporciona Azure se desasignará de su suscripción si elimina la implementación. Tenga en cuenta que para que la operación de intercambio de direcciones IP virtuales se realice con éxito, necesitará direcciones IP reservadas para los espacios de producción y ensayo, o dicha operación dará error. Siga estos artículos para reservar una dirección IP y asociarla a su instancia de Cloud Services:  
 
- [Reserva de la dirección IP de un servicio en la nube existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Asociación de una dirección IP reservada a un servicio en la nube mediante un archivo de configuración de servicio](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Siempre que tenga más de una instancia de los roles, la asociación de RIP a su servicio en la nube no debería producir tiempo de inactividad. Como alternativa, puede incluir en la lista de permitidos el intervalo IP de su centro de datos de Azure. Puede encontrar todos los intervalos IP de Azure [aquí](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Este archivo contiene los intervalos de direcciones IP (incluidos los intervalos de Compute, SQL y Storage) utilizados en los centros de datos de Microsoft Azure. Semanalmente, se publica un archivo actualizado que refleja los intervalos implementados actualmente y los próximos cambios en los intervalos de direcciones IP. Los nuevos intervalos que aparecen en el archivo no se utilizarán en los centros de datos durante al menos una semana. Descargue el nuevo archivo XML cada semana y realizar los cambios necesarios en su sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Express Route observarán que este archivo se usa para actualizar la publicidad de BGP del espacio de Azure en la primera semana de cada mes. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>¿Cómo puedo usar redes virtuales de Azure Resource Manager con Cloud Services? 

Los servicios en la nube no se pueden colocar en redes virtuales de Azure Resource Manager, pero es posible conectar una red virtual de Azure Resource Manager y una red virtual clásica mediante emparejamiento. Para más información, consulte [Emparejamiento de redes virtuales](../virtual-network/virtual-network-peering-overview.md).