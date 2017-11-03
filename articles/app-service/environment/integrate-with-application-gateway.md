---
title: "Integración del ASE de ILB con una instancia de Azure Application Gateway"
description: "Tutorial sobre cómo integrar una aplicación en el ASE de ILB mediante Azure Application Gateway"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Integración del ASE de ILB con una instancia de Application Gateway #

[Azure App Service Environment (ASE)](./intro.md) es una implementación de Azure App Service en la subred de Azure Virtual Network de un cliente. Se puede implementar con un punto de conexión público o privado para el acceso a las aplicaciones. La implementación del ASE con un punto de conexión privado se denomina un ASE de ILB.  
Azure Application Gateway es una aplicación virtual que ofrece un equilibrio de carga de nivel 7, descarga de SSL y protección WAF. Puede escuchar en una dirección IP pública y enrutar el tráfico hacia el punto de conexión de la aplicación. La siguiente información describe cómo integrar una instancia de Application Gateway, configurada con firewall de aplicaciones web, con una aplicación en un ASE de ILB.  

La integración de la instancia de Application Gateway con el ASE de ILB se produce en el nivel de la aplicación.  Al configurar la instancia de Application Gateway con el ASE de ILB, lo hace para aplicaciones específicas de este. Esto permite hospedar aplicaciones multiinquilino seguras en un solo ASE de ILB.  

![Instancia de Application Gateway que apunta a la aplicación de un ASE de ILB][1]

En este tutorial realizará lo siguiente:

* Creación de una instancia de Application Gateway
* Configuración de la instancia de Application Gateway para que apunte a una aplicación del ASE de ILB
* Configuración de la aplicación para que admita el nombre de dominio personalizado
* Edición del nombre de host público del DNS que apunta a la instancia de Application Gateway

Para integrar la instancia de Application Gateway con el ASE de ILB, necesita:

* Un ASE de ILB
* Una aplicación que se ejecute en el ASE de ILB
* Un nombre de dominio enrutable de Internet para su uso con la aplicación en el ASE de ILB
* La dirección ILB utilizada por el ASE de ILB (esta se encuentra en el portal de ASE en **Configuración -> Direcciones IP**)

    ![Las direcciones IP usadas por el ASE de ILB][9]
    
* Un nombre DNS público que se utilizará posteriormente para que apunte a la instancia de Application Gateway 

Para más información sobre cómo crear un ASE de ILB lea el documento [Creación y uso de un ASE de ILB][ilbase]

En esta guía se da por supuesto que desea una instancia de Application Gateway en la misma instancia de Azure Virtual Network en la que está implementado el ASE. Antes de iniciar la creación de la instancia de Application Gateway, elija o cree la subred que utilizará para hospedarla. Debe usar una subred que no sea la que se llama GatewaySubnet, ni la subred usada por el ASE de ILB.
Si coloca la instancia de Application Gateway en GatewaySubnet no podrá crear ninguna puerta de enlace de Virtual Network posteriormente. Tampoco puede colocarla en la subred que usa el ASE de ILB ya que el ASE es la única cosa que puede estar en su subred.

## <a name="steps-to-configure"></a>Pasos de configuración ##

1. En Azure Portal, vaya a **Nuevo > Red > Application Gateway** 
    1. Proporcione:
        1. Nombre de la instancia de Application Gateway
        1. Seleccione el firewall de aplicaciones web
        1. Seleccione la misma suscripción utilizada para la red virtual de ASE
        1. Cree o seleccione el grupo de recursos
        1. Seleccione la ubicación en la que está la red virtual de ASE

    ![Conceptos básicos de la creación de instancias de Application Gateway][2]   
    1. En el área Configuración, establezca:
        1. La red virtual de ASE
        1. La subred en la que se debe implementar la instancia de Application Gateway. No utilice GatewaySubnet ya que esto impediría la creación de instancias de VPN Gateway
        1. Seleccione Pública
        1. Seleccione una dirección IP pública. Si no tiene ninguna, créela en este momento
        1. Configure para HTTP o HTTPS. Si configura para HTTPS deberá proporcionar un certificado PFX
        1. Seleccione la configuración del firewall de aplicaciones web. Aquí puede habilitar el firewall y también establecerlo en Detección o en Prevención como lo estime oportuno.

    ![Configuración de creación de nueva instancia de Application Gateway][3]
    
    1. En la revisión de la sección de resumen, seleccione **Aceptar**. Puede que tarde algo más de 30 minutos en completarse la instalación de la instancia de Application Gateway.  

2. Una vez completada la instalación, vaya al portal de Application Gateway. Seleccione **Grupo back-end**.  Agregue la dirección de ILB para el ASE.

    ![Configuración del grupo back-end][4]

3. Una vez completado el procesamiento para configurar el grupo back-end, seleccione **Sondeos de estado**. Cree un sondeo de estado para el nombre de dominio que desea usar para la aplicación. 

    ![Configuración de sondeos de mantenimiento][5]
    
4. Después de que se complete el procesamiento para configurar los sondeos de estado, seleccione **Configuración HTTP**.  Edite la configuración existente allí, seleccione **Usar sondeo personalizado** y elija el sondeo que configuró.

    ![Configuración de HTTP][6]
    
5. Vaya a la **Introducción** de Application Gateway y copie la dirección IP pública usada por esta.  Establezca esa dirección IP como registro A del nombre de dominio de la aplicación, o utilice el nombre DNS para esa dirección en un registro CNAME.  Es más fácil seleccionar la dirección IP pública y copiarla desde la interfaz de usuario de dirección IP pública que desde el vínculo de la sección Introducción de Application Gateway. 

    ![Portal de Application Gateway][7]

6. Establezca el nombre de dominio personalizado para la aplicación en el ASE de ILB.  Vaya a la aplicación en el portal y en Configuración, seleccione **Dominios personalizados**

![Establecer el nombre de dominio personalizado en la aplicación][8]

Aquí puede encontrar información sobre la [configuración de nombres de dominio personalizados para su aplicación web][custom-domain]. La diferencia entre una aplicación en un ASE de ILB y ese documento, es que no hay ninguna validación en el nombre de dominio.  Como usted es el propietario del DNS que administra los puntos de conexión de la aplicación, puede colocar todo lo que desee allí. No es necesario que el nombre de dominio personalizado que agregue en este caso esté en el DNS, pero deberá configurarlo con la aplicación. 

Después de completar la instalación y de haber permitido un breve período de tiempo para que los cambios de DNS se propaguen, puede acceder a la aplicación mediante el nombre de dominio personalizado que creó. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
