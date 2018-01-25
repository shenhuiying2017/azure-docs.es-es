---
title: "Atributos paralelos del servicio de sincronización de Azure AD Connect | Microsoft Docs"
description: "Describe cómo funcionan los atributos paralelos en el servicio de sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 19d5ae46955ecc094c340d141485d3eb54c8e9b2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos paralelos del servicio de sincronización de Azure AD Connect
La mayoría de los atributos se representan del mismo modo en Azure AD, ya que se encuentran en el Active Directory local. Pero algunos atributos tienen algún tratamiento especial y el valor del atributo en Azure AD puede ser distinto de lo que sincroniza Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introducción a los atributos paralelos
Algunos atributos tienen dos representaciones en Azure AD. Se almacenan el valor local y un valor calculado. Estos atributos adicionales se denominan atributos paralelos. Los dos atributos más comunes donde verá este comportamiento son **userPrincipalName** y **proxyAddress**. El cambio de los valores de atributo se produce cuando hay valores de estos atributos que representan los dominios no comprobados. Pero el motor de sincronización de Connect lee el valor del atributo paralelo, por lo que desde su perspectiva, el atributo se ha confirmado por Azure AD.

No se pueden ver los atributos paralelos mediante Azure Portal o con PowerShell. Pero entender el concepto le ayudará a solucionar los problemas de determinados escenarios donde el atributo tiene valores diferentes tanto en local como en la nube.

Para comprender mejor el comportamiento, vea este ejemplo de Fabrikam:  
![Dominios](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Tienen varios sufijos UPN en su instancia de Active Directory local, pero solo se ha comprobado uno.

### <a name="userprincipalname"></a>userPrincipalName
Un usuario tiene los siguientes valores de atributo en un dominio no comprobado:

| Atributo | Valor |
| --- | --- |
| userPrincipalName local | lee.sperry@fabrikam.com |
| shadowUserPrincipalName de Azure AD | lee.sperry@fabrikam.com |
| userPrincipalName de Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

El atributo userPrincipalName es el valor que aparece cuando se usa PowerShell.

Puesto que el valor del atributo local real se almacena en Azure AD, al comprobar el dominio fabrikam.com, Azure AD actualiza el atributo userPrincipalName con el valor de shadowUserPrincipalName. No es necesario sincronizar los cambios de Azure AD Connect para que se actualicen estos valores.

### <a name="proxyaddresses"></a>proxyAddresses
El mismo proceso para incluir solo dominios comprobados también se produce para proxyAddresses, pero con alguna lógica adicional. La comprobación de dominios comprobados solo se produce para los usuarios de buzón. Un usuario habilitado para correo o un contacto representa un usuario de otra organización de Exchange y puede agregar los valores de proxyAddresses en estos objetos.

Para un usuario del buzón, de forma local o en Exchange Online, aparecen únicamente los valores para los dominios comprobados. Debería ser parecido a esto:

| Atributo | Valor |
| --- | --- |
| proxyAddresses local | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| ProxyAddresses de Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

En este caso  **smtp:abbie.spencer@fabrikam.com**  se quitó porque no se ha comprobado el dominio. Pero Exchange también agregó **SIP:abbie.spencer@fabrikamonline.com**. Fabrikam no ha usado Lync o Skype local, pero Azure AD y Exchange Online se preparan para ello.

Esta lógica para proxyAddresses se conoce como **ProxyCalc**. ProxyCalc se llama con cada cambio en un usuario cuando:

- Al usuario se ha asignado un plan de servicio que incluye Exchange Online, incluso si el usuario no se tiene licencia para Exchange. Por ejemplo, si al usuario se le asigna la SKU de Office E3, pero solo se asignó SharePoint Online. Esto ocurre incluso si el buzón sigue siendo local.
- El atributo msExchRecipientTypeDetails tiene un valor.
- Realiza un cambio en proxyAddresses o userPrincipalName.

ProxyCalc puede tardar algún tiempo en procesar un cambio en un usuario y no está sincronizado con el proceso de exportación de Azure AD Connect.

> [!NOTE]
> La lógica de ProxyCalc tiene algunos comportamientos adicionales para escenarios avanzados que no se documentan en este tema. Este tema se proporciona para que pueda entender el comportamiento y no tener que documentar toda la lógica interna.

### <a name="quarantined-attribute-values"></a>Valores de atributo en cuarentena
Los atributos paralelos también se utilizan cuando hay valores de atributo duplicados. Para más información, consulte [Resistencia de atributos duplicados](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Otras referencias
* [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
