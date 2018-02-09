---
title: Planeamiento del firewall de Azure Stack para sistemas integrados de Azure Stack | Microsoft Docs
description: "Describe las consideraciones del firewall de Azure Stack para implementaciones de varios nodos con conexión a Azure en Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: f7b621312677c0b250e267770ae0c445ee9f083f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-firewall-integration"></a>Integración del firewall de Azure Stack
Se recomienda que use un dispositivo de firewall para ayudar a proteger Azure Stack. Aunque los firewalls pueden ayudar en el caso de ataques por denegación de servicio distribuido (DDOS), detección de intrusiones e inspección de contenidos, también pueden convertirse en un cuello de botella de rendimiento para servicios de almacenamiento de Azure como blobs, tablas y colas.

Según el modelo de identidad Azure Active Directory (Azure AD) o Windows Server Active Directory Federation Services (AD FS), puede que sea necesario que publique el punto de conexión de AD FS. Si se usa un modo de implementación desconectada, debe publicar el punto de conexión de AD FS. Para obtener más información, consulte el [artículo sobre la identidad de integración del centro de datos](azure-stack-integrate-identity.md).

Los puntos de conexión de Azure Resource Manager (administrador), el portal de administrador y el almacén de claves (administrador) no requieren necesariamente publicación externa. Por ejemplo, como proveedor de servicios, podría interesarle limitar la superficie expuesta a ataques y administrar Azure Stack solo desde dentro de la red, y no desde Internet.

Para organizaciones empresariales, la red externa puede ser la red corporativa existente. En este caso, debe publicar esos puntos de conexión para trabajar con Azure Stack desde la red corporativa.

### <a name="network-address-translation"></a>Traducción de direcciones de red
La traducción de direcciones de red (NAT) es el método recomendado para permitir que la máquina virtual de implementación (DVM) acceda a los recursos externos y a Internet durante la implementación, así como a las VM de la consola de recuperación de emergencia (ERCS) o al punto de conexión con privilegios (PEP) durante el registro y la solución de problemas.

NAT también puede ser una alternativa a las direcciones IP públicas de la red externa o VIP públicas. Sin embargo, no se recomienda, ya que limita la experiencia del usuario inquilino y aumenta la complejidad. Las dos opciones serían una NAT de 1:1 que aún necesitaría una IP pública por IP de usuario en el grupo o una NAT muchos:1 que requeriría una regla NAT por VIP de usuario que contenga las asociaciones para todos los puertos que un usuario podría utilizar.

Algunas de las desventajas del uso de NAT para una VIP pública son:
- NAT agrega sobrecarga al administrar las reglas de firewall, ya que los usuarios controlan sus propios puntos de conexión y sus propias reglas de publicación en la pila de redes definidas por software (SDN). Los usuarios deben ponerse en contacto con el operador de Azure Stack para que se publiquen sus VIP y para actualizar la lista de puertos.
- Aunque el uso de NAT limita la experiencia del usuario, ofrece un control total al operador de las solicitudes de publicación.
- En el caso de escenarios de nube híbrida con Azure, debe tener en cuenta que Azure no admite la configuración de un túnel VPN a un punto de conexión con NAT.

### <a name="ssl-decryption"></a>Descifrado SSL
Actualmente, nuestra recomendación respecto al descifrado SSL es deshabilitarlo en todo el tráfico de Azure Stack. En el futuro, proporcionaremos instrucciones acerca de cómo habilitar el descifrado SSL para Azure Stack.

## <a name="edge-firewall-scenario"></a>Escenario de firewall perimetral
En una implementación perimetral, Azure Stack se implementa directamente detrás del firewall o el enrutador perimetral. En estos casos, se admite que el firewall se encuentre por encima del borde o que actúe como dispositivo de borde, si admite varias rutas de acceso de igual costo (ECMP) con enrutamiento estático o BGP.

Por lo general, las direcciones IP enrutables públicas se especifican para el grupo de VIP públicas de la red externa en el momento de la implementación. En un escenario perimetral, no se recomienda utilizar direcciones IP enrutables públicas en ninguna otra red por motivos de seguridad. Este escenario permite que un usuario disfrute de la experiencia completa en la nube autocontrolada como si se tratara de una nube pública como Azure.  

![Ejemplo de firewall perimetral de Azure Stack](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Escenario de firewall de red perimetral o de intranet empresarial
En una implementación perimetral o de intranet empresarial, Azure Stack se implementa en un firewall de varias zonas o entre el firewall perimetral y el firewall de red corporativo interno. A continuación, se distribuye el tráfico entre la red perimetral (o DMZ) segura y las zonas no seguras como se describe a continuación:

- **Zona segura**: se trata de la red interna que usa las direcciones IP enrutables corporativas o internas. La red segura se puede dividir, puede tener acceso de salida a Internet a través de la NAT del firewall y, normalmente, se puede acceder a ella desde cualquier punto del centro de datos a través de la red interna. Todas las redes de Azure Stack deben residir en la zona segura, excepto el grupo de VIP públicas de la red externa.
- **Zona perimetral**. La red perimetral es el lugar en que se suelen implementar las aplicaciones externas o accesibles desde Internet, como los servidores web. Se suelen supervisar a través de un firewall para evitar ataques como DDoS e intrusión (acceso por parte de piratas informáticos) y permitir el tráfico entrante especificado de Internet. Solo debe residir en la zona DMZ el grupo de VIP públicas de redes externas de Azure Stack.
- **Zona no segura**. Se trata de la red externa: Internet. **No** se recomienda implementar Azure Stack en la zona no segura.

![Ejemplo de red perimetral de Azure Stack](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>Más información
Más información sobre los [puertos y protocolos que utilizan los puntos de conexión de Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>pasos siguientes
[Requisitos de PKI de Azure Stack](azure-stack-pki-certs.md)

