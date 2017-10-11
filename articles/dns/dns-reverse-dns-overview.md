---
title: "Introducción al DNS inverso en Azure | Microsoft Docs"
description: "Aprenda cómo funciona DNS inverso y cómo se puede usar en Azure"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 70a1ad070e812951fca3d2b19da12c67f0725dd0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Introducción a DNS inverso y compatibilidad en Azure

Este artículo proporciona información general sobre cómo funciona DNS inverso y escenarios de DNS inverso admitidos en Azure.

## <a name="what-is-reverse-dns"></a>¿Qué es un DNS inverso?

Los registros de DNS convencionales permiten la asignación de un nombre de DNS (por ejemplo, "www.contoso.com") a una dirección IP (por ejemplo, 64.4.6.100).  Un DNS inverso permite traducir una dirección IP (64.4.6.100) a un nombre ("www.contoso.com").

Los registros de DNS inversos se utilizan en distintas situaciones, entre ellas, en la validación de servidores y en la autenticación de solicitudes de servidor. Por ejemplo, los registros de DNS inverso se usan mucho en la lucha contra el correo basura; para ello, se comprueba el remitente del mensaje de correo electrónico.  El servidor de correo receptor recupera el registro de DNS inverso de la dirección IP del servidor de envío y comprueba si ese host está autorizado para enviar un correo electrónico desde el dominio de origen. 

## <a name="how-reverse-dns-works"></a>Cómo funciona un DNS inverso

Los registros de DNS inverso se hospedan en zonas DNS especiales conocidas como zonas “ARPA”.  Estas zonas forman una jerarquía de DNS independiente paralela a la jerarquía normal que hospeda dominios tales como "contoso.com".

Por ejemplo, el registro de DNS “www.contoso.com” se implementa mediante un registro “A” de DNS con el nombre "www" en la zona “contoso.com”.  Este registro A apunta a la dirección IP correspondiente, en este caso 64.4.6.100.  La búsqueda inversa se implementa por separado, usando un registro “PTR” llamado “100” en la zona “6.4.64.in-addr.arpa” (tenga en cuenta que las direcciones IP se invierten en las zonas ARPA).  Este registro PTR, si se ha configurado correctamente, apunta al nombre "www.contoso.com".

Cuando se asigna un bloque de direcciones IP a una organización, adquiere también el derecho a administrar la zona ARPA correspondiente. Microsoft hospeda y administra las zonas ARPA correspondientes a los bloques de direcciones IP usados por Azure. Su ISP puede hospedar la zona ARPA para sus propias direcciones IP automáticamente o le puede permitir hospedar la zona ARPA en un servicio DNS de su elección, por ejemplo, el DNS de Azure.

> [!NOTE]
> Las búsquedas DNS directas e inversas se implementan en jerarquías de DNS independientes y paralelas. La búsqueda inversa de "www.contoso.com" **no** se hospeda en la zona "contoso.com"; en su lugar se hospeda en la zona ARPA para el bloque de direcciones IP correspondiente. Se utilizan zonas independientes para los bloques de direcciones IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

El nombre de una zona de búsqueda inversa IPv4 debe tener el formato siguiente: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por ejemplo, al crear una zona inversa para hospedar registros para hosts con direcciones IP que están en el prefijo 192.0.2.0/24, el nombre de zona se crearía aislando el prefijo de red de la dirección (192.0.2) e invirtiendo luego el orden (2.0.192) y agregando el sufijo `.in-addr.arpa`.

|Clase de subred|Prefijo de red  |Prefijo de red invertido  |Sufijo estándar  |Nombre de zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Clase A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Clase B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Clase C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegación IPv4 sin clases

En algunos casos, el intervalo IP asignado a una organización es menor que un intervalo de Clase C (/24). En este caso, el intervalo IP no entra dentro de un límite de zona en la zona de jerarquía `.in-addr.arpa` y, por lo tanto, no se puede delegar como una zona secundaria.

En su lugar, se utiliza un mecanismo diferente para transferir el control de los registros de búsqueda inversa individuales (PTR) a una zona DNS dedicada. Este mecanismo delega una zona secundaria para cada intervalo IP y asigna cada dirección IP del intervalo individualmente a esa zona secundaria mediante registros CNAME.

Por ejemplo, suponga que un ISP concede a una organización el intervalo IP 192.0.2.128/26. Esto representa 64 direcciones IP, de 192.0.2.128 a 192.0.2.191. El DNS inverso de este intervalo se implementa del modo siguiente:
- La organización crea una zona de búsqueda inversa denominada 128-26.2.0.192.in-addr.arpa. El prefijo ' 128-26' representa el segmento de red asignado a la organización dentro del intervalo de la Clase C (/24).
- El ISP crea registros NS a fin de configurar la delegación de DNS para la zona anterior desde la zona primaria de la Clase C. También crea registros CNAME en la zona de búsqueda inversa primaria (clase C), asignando cada dirección IP en el intervalo IP a la nueva zona creada por la organización:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- A continuación, la organización administra los registros PTR individuales dentro de su zona secundaria.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Una búsqueda inversa para la dirección IP '192.0.2.129' consulta para un registro PTR denominado '129.2.0.192.in-addr.arpa'. Esta consulta se resuelve a través del registro CNAME en la zona primaria hacia el registro PTR en la zona secundaria.

### <a name="ipv6"></a>IPv6

El nombre de una zona de búsqueda inversa IPv6 debe tener el formato siguiente: `<IPv6 network prefix in reverse order>.ip6.arpa`

Por ejemplo: Al crear una zona inversa para hospedar registros para hosts con direcciones IP que están en el prefijo 2001:db8:1000:abdc::/64, el nombre de zona se crearía aislando el prefijo de red de la dirección (2001:db8:abdc::). A continuación, expanda el prefijo de red IPv6 para quitar la [compresión de ceros](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), si se usa para acortar el prefijo de dirección IPv6 (2001:0db8:abdc:0000::). Invierta el orden, con un punto como delimitador entre cada número hexadecimal en el prefijo, para generar el prefijo de red invertido (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) y agregar el sufijo `.ip6.arpa`.


|Prefijo de red  |Prefijo de red expandido e invertido |Sufijo estándar |Nombre de zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Soporte técnico de Azure para DNS inverso

Azure admite dos escenarios independientes con relación al DNS inverso:

**El hospedaje de la zona de búsqueda inversa correspondiente a su bloque de direcciones IP.**
Se puede usar el DNS de Azure para [hospedar las zonas de búsqueda inversa y administrar los registros PTR para cada búsqueda inversa DNS](dns-reverse-dns-hosting.md), tanto para IPv4 como para IPv6.  El proceso de creación de la zona (ARPA) de búsqueda inversa, configuración de la delegación y configuración de los registros PTR es el mismo que para las zonas DNS normales.  Las únicas diferencias son que la delegación se debe configurar mediante su ISP en lugar del registrador de DNS, y solo se debe usar el tipo de registro PTR.

**Configuración del registro de DNS inverso para la dirección IP asignada a su servicio de Azure.** Azure le permite [configurar la búsqueda inversa de las direcciones IP asignadas a su servicio de Azure](dns-reverse-dns-for-azure-services.md).  Azure configura esta búsqueda inversa como un registro PTR en la zona ARPA correspondiente.  Microsoft hospeda y administra estas zonas ARPA correspondientes a todos los intervalos IP usados por Azure.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los registros de DNS inverso, consulte información sobre la [búsqueda de DNS inverso en Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Aprenda cómo [hospedar la zona de búsqueda inversa para el intervalo IP asignado por el ISP en DNS de Azure](dns-reverse-dns-for-azure-services.md).
<br>
Aprenda a [administrar registros de DNS inversos para servicios de Azure](dns-reverse-dns-for-azure-services.md).

