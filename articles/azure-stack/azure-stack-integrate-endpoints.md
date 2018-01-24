---
title: "Integración del centro de datos de Azure Stack: publicar puntos de conexión"
description: "Obtenga información sobre cómo publicar puntos de conexión de Azure Stack en su centro de datos."
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 01/16/2018
ms.author: victorh
keywords: 
ms.openlocfilehash: 1cc74cb2214918d6bfd0c0827cf5d9832b84f317
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Integración del centro de datos de Azure Stack: publicar puntos de conexión

*Se aplica a: sistemas integrados de Azure Stack*

Azure Stack configura varios puntos de conexión (VIP, es decir, direcciones IP virtuales) para sus roles de infraestructura. Estas VIP se asignan desde el grupo de direcciones IP públicas. Cada VIP está protegida con una lista de control de acceso (ACL) en el nivel de red definido por software. Las ACL también se usan en los conmutadores físicos (Tor y BMC) para proteger aún más la solución. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa que se haya especificado durante la implementación.


En el siguiente diagrama de arquitectura se muestran los diferentes niveles de red y ACL:

![Diagrama de arquitectura](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Puertos y protocolos (de entrada)

En la tabla siguiente se muestran las VIP de infraestructura necesarias para la publicación de puntos de conexión de Azure Stack en redes externas. En la lista se muestra cada punto de conexión, el puerto requerido y el protocolo. Los puntos de conexión necesarios para otros proveedores de recursos, como el proveedor de recursos SQL, se tratan en la documentación de implementación del proveedor de recursos específico.

No se indican las VIP de infraestructura interna porque no son necesarias para la publicación de Azure Stack.

> [!NOTE]
> Las VIP de usuario son dinámicas y están definidas por los propios usuarios, sin ningún control por parte del operador de Azure Stack.


|Punto de conexión (VIP)|Registro de host DNS A|Protocolo|Puertos|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (usuario)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (usuario)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Grafo|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de revocación de certificados|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP y UDP|53|
|Key Vault (usuario)|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Key Vault (administrador)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Cola de almacenamiento|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabla de almacenamiento|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Puertos y direcciones URL (de salida)

Azure Stack solo admite servidores proxy transparentes. En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir los siguientes puertos y direcciones URL para la comunicación saliente:


|Propósito|URL|Protocolo|Puertos|
|---------|---------|---------|---------|
|Identidad|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Redifusión de Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Revisión y actualización|https://&#42;.azureedge.net|HTTPS|443|
|Registro|https://management.azure.com|HTTPS|443|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|

## <a name="firewall-publishing"></a>Publicación de firewall

Los puertos indicados en la sección anterior se aplican a la comunicación entrante cuando se publican servicios de Azure Stack a través de un firewall existente.

Se recomienda que use un dispositivo de firewall para ayudar a proteger Azure Stack, pero no es un requisito estricto. Aunque los firewalls pueden ayudar en el caso de ataques por denegación de servicio distribuido (DDOS) y la inspección de contenidos, también pueden llegar a convertirse en un cuello de botella de rendimiento para servicios de almacenamiento de Azure como blobs, tablas y colas.

En función del modelo de identidad (Azure AD o AD FS), puede ser necesario o no publicar el punto de conexión de AD FS. Si se usa un modo de implementación desconectada, debe publicar el punto de conexión de AD FS. (Para obtener más información, vea el tema sobre la identidad de integración del centro de datos).

Los puntos de conexión de Azure Resource Manager (administrador), el portal de administrador y el almacén de claves (administrador) no requieren necesariamente publicación externa. Depende del escenario. Por ejemplo, como proveedor de servicios, podría interesarle limitar la superficie expuesta a ataques y administrar Azure Stack solo desde dentro de la red, y no desde Internet.

Para una organización, la red externa puede ser la red corporativa existente. En este caso, debe publicar esos puntos de conexión para trabajar con Azure Stack desde la red corporativa.

## <a name="edge-firewall-scenario"></a>Escenario de firewall perimetral

En una implementación perimetral, Azure Stack se implementa directamente detrás del enrutador perimetral (proporcionado por el ISP) con o sin un firewall delante.

![Diagrama de arquitectura de una implementación perimetral de Azure Stack](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

Por lo general, las direcciones IP enrutables públicas se especifican para el grupo de VIP públicas durante la implementación en una implementación perimetral. Este escenario permite que un usuario viva la experiencia completa en la nube controlada automáticamente como si se tratara de una nube pública como Azure.

### <a name="using-nat"></a>Uso de NAT

Aunque no se recomienda debido a la sobrecarga, podría usar la traducción de direcciones de red (NAT) para la publicación de puntos de conexión. Para una publicación de puntos de conexión controlada por completo por los usuarios, es necesaria una regla NAT por VIP de usuario que contenga todos los puertos que un usuario podría usar.

Otra consideración es que Azure no admite la configuración de un túnel VPN a un punto de conexión con NAT en un escenario de nube híbrida con Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Escenario de firewall de red empresarial/de intranet/perimetral

En una implementación empresarial/de intranet/perimetral, Azure Stack se implementa más allá de un segundo firewall, que normalmente forma parte de una red perimetral.

![Escenario de firewall de Azure Stack](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Si se han especificado direcciones IP enrutables públicas para el grupo de VIP públicas de Azure Stack, estas direcciones lógicamente pertenecen a la red perimetral y requieren reglas de publicación en el firewall principal.

### <a name="using-nat"></a>Uso de NAT

Si se usan direcciones IP enrutables no públicas para el grupo de VIP públicas de Azure Stack, se usa NAT en el firewall secundario para publicar puntos de conexión de Azure Stack. En este escenario, debe configurar las reglas de publicación en el firewall principal más allá del perímetro y en el firewall secundario. Tenga en cuenta lo siguiente si quiere usar NAT:

- NAT agrega sobrecarga al administrar las reglas de firewall, ya que los usuarios controlan sus propios puntos de conexión y sus propias reglas de publicación en la pila de redes definidas por software (SDN). Los usuarios deben ponerse en contacto con el operador de Azure Stack para que se publiquen sus VIP y para actualizar la lista de puertos.
- Aunque el uso de NAT limita la experiencia del usuario, ofrece un control total al operador de las solicitudes de publicación.
- En el caso de escenarios de nube híbrida con Azure, debe tener en cuenta que Azure no admite la configuración de un túnel VPN a un punto de conexión con NAT.


## <a name="next-steps"></a>pasos siguientes

[Integración del centro de datos de Azure Stack: Seguridad](azure-stack-integrate-security.md)