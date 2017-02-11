---
title: "Conceptos de diseño de Azure AD Connect | Microsoft Docs"
description: "En este tema se detallan algunas áreas de diseño de implementación"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 09/13/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 116ef0786c9c45b549311a59615d06f194093bc2


---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: conceptos de diseño
El propósito de este tema es describir las áreas que se deben tener en cuenta durante el diseño de implementación de Azure AD Connect. Este tema trata de una profundización en determinadas áreas, y estos conceptos se describen también brevemente en otros temas.

## <a name="sourceanchor"></a>sourceAnchor
El atributo sourceAnchor se define como *un atributo inmutable durante la vigencia de un objeto*. Identifica de forma única que un objeto es el mismo objeto en el entorno local y en Azure AD. El atributo también se denomina **immutableId** y los dos nombres se usan indistintamente.

La palabra inmutable, es decir, no se puede cambiar, es importante en este tema. Puesto que el valor de este atributo no se puede cambiar después de que se haya establecido, es importante elegir un diseño que respalde su caso.

El atributo se usa en las situaciones siguientes:

* Cuando un nuevo servidor de motor de sincronización se genera, o regenera, después de una situación de recuperación ante desastres, este atributo vincula los objetos existentes en Azure AD con los objetos locales.
* Si se mueve de una identidad solo de nube a un modelo de identidad sincronizada, este atributo permitirá que los objetos existentes en Azure AD coincidan exactamente con los objetos locales.
* Si usa federación, este atributo junto con el **userPrincipalName** se usan en la notificación para identificar de forma exclusiva un usuario.

En este tema solo se habla de sourceAnchor, puesto que está relacionado con los usuarios. Las mismas reglas se aplican a todos los tipos de objeto, pero es solo este problema el que supone una preocupación para los usuarios.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selección de un buen atributo sourceAnchor
El valor de atributo debe seguir las reglas siguientes:

* Debe tener una longitud de menos de 60 caracteres.
  * Los caracteres que no estén entre a-z, A-Z o 0-9 se codifican y cuentan como 3 caracteres.
* No debe contener caracteres especiales: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Debe ser único globalmente.
* Debe ser una cadena, un entero o un número binario.
* No se debe basar en el nombre del usuario, estos cambios
* No debe distinguir mayúsculas y minúsculas; evite valores que pueden variar según el caso
* Debe asignarse cuando se crea el objeto.

Si el atributo sourceAnchor seleccionado no es de tipo cadena, Azure AD Connect aplica Base64Encode al valor de atributo para asegurarse de que no se muestre ningún carácter especial. Si usa otro servidor de federación distinto de ADFS, asegúrese de que también pueda aplicar Base64Encode al atributo.

El atributo sourceAnchor distingue mayúsculas de minúsculas. El valor "JohnDoe" no es igual que "johndoe". Pero no debería tener dos objetos diferentes con solo una diferencia en mayúsculas o minúsculas.

Si tiene un solo bosque local, el atributo que debe usar es **objectGUID**. También es el atributo que se usa con la configuración rápida en Azure AD Connect y el atributo de sincronización de directorios.

Si tiene varios bosques y no mueve usuarios entre bosques y dominios, **objectGUID** es un buen atributo para usar incluso en este caso.

Si mueve usuarios entre bosques y dominios, debe buscar un atributo que no cambie o se puede mover con los usuarios durante el movimiento. Un enfoque recomendado es introducir un atributo sintético. Un atributo que pueda contener algo parecido a un GUID sería adecuado. Durante la creación del objeto se crea un nuevo GUID y se marca en el usuario. Puede crear una regla de sincronización en el servidor del motor de sincronización para crear este valor según el **objectGUID** y actualizar el atributo seleccionado en ADDS. Al mover el objeto, asegúrese de copiar también el contenido de este valor.

Otra solución consiste en seleccionar un atributo existente que sepa que no va a cambiar. Uno de los atributos de uso más común es **employeeID**. Si está considerando el uso de un atributo que contenga letras, asegúrese de que no haya ninguna posibilidad de que la distinción de mayúsculas y minúsculas pueda cambiar el valor del atributo. Entre los atributos incorrectos que no deben usarse se incluyen aquellos con el nombre del usuario. En un matrimonio o divorcio se espera que cambie el nombre, lo que no está permitido para este atributo. Este también es uno de los motivos de que atributos como **userPrincipalName**, **mail** y **targetAddress** no se puedan seleccionar en el asistente de instalación de Azure AD Connect. Esos atributos también contienen el carácter @-character,, que no está permitido en sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Cambio del atributo sourceAnchor
No se puede cambiar el valor del atributo sourceAnchor después de que el objeto se ha creado en Azure AD y la identidad se ha sincronizado.

Por este motivo, se aplican las restricciones siguientes a Azure AD Connect:

* El atributo sourceAnchor solo puede establecerse durante la instalación inicial. Si vuelve a ejecutar el asistente de instalación, esta opción es de solo lectura. Si necesita cambiar esto, debe desinstalar y reinstalar la aplicación.
* Si instala otro servidor de Azure AD Connect, debe seleccionar el mismo atributo sourceAnchor usado anteriormente. Si anteriormente ha estado usando la sincronización de directorios y se pasa a Azure AD Connect, debe usar **objectGUID** ya que es el atributo de sincronización de directorios.
* Si se cambia el valor de sourceAnchor después de que el objeto se ha exportado a Azure AD, Azure AD Connect Sync produce un error y no permite más cambios en ese objeto antes de que el problema se haya corregido y el atributo sourceAnchor cambie de nuevo en el directorio de origen.

## <a name="azure-ad-sign-in"></a>Inicio de sesión de Azure AD
Al integrar su directorio local con Azure AD, es importante entender cómo la configuración de sincronización puede afectar a la forma en la que los usuarios se autentican. Azure AD usa userPrincipalName (UPN) para autenticar al usuario. Sin embargo, al sincronizar los usuarios tiene que elegir el atributo que se utilizará para el valor userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Selección del atributo para userPrincipalName
Cuando se selecciona el atributo para proporcionar el valor de UPN que se va a usar en Azure es necesario asegurarse de lo siguiente:

* Los valores de atributo se ajustan a la sintaxis UPN (RFC 822), es decir, debe tener el formato username@domain
* El sufijo en los valores coincide con uno de los dominios personalizados comprobados en Azure AD

En la configuración rápida, la opción que se presupone para el atributo es userPrincipalName. Si el atributo userPrincipalName no contiene el valor que quiera que los usuarios inicien sesión en Azure, debe elegir la **instalación personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de dominio personalizado y UPN
Es importante asegurarse de que hay un dominio comprobado para el sufijo UPN.

John es un usuario de "contoso.com". Desea que John use el UPN local john@contoso.com para iniciar sesión en Azure después haber sincronizado a los usuarios con el directorio de Azure AD azurecontoso.onmicrosoft.com. Para ello, tiene que agregar y comprobar "contoso.com" como un dominio personalizado en Azure AD antes de sincronizar a los usuarios. Si el sufijo UPN de John, por ejemplo, contoso.com, no coincide con un dominio comprobado en Azure AD, Azure AD reemplaza el sufijo UPN con contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Dominios locales no enrutables y UPN para Azure AD
Algunas organizaciones tienen dominios no enrutables, como "contoso.local" o dominios de etiqueta única simple como "contoso". No podrá comprobar un dominio no enrutable en Azure AD. Azure AD Connect puede sincronizarse solo con un dominio comprobado en Azure AD. Cuando se crea un directorio de Azure AD, se crea un dominio enrutable que se convierte en el dominio predeterminado para Azure SD, por ejemplo, "contoso.onmicrosoft.com". Por lo tanto, es necesario comprobar cualquier otro dominio enrutable en este escenario, en caso de que no desee sincronizar con el valor predeterminado ".onmicrosoft.com".

Consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](active-directory-add-domain.md) para más información sobre cómo agregar y comprobar dominios.

Azure AD Connect detecta si está ejecutando en un entorno de dominio no enrutable y le advertirá adecuadamente antes de que prosiga con la configuración rápida. Si está trabajando en un dominio no enrutable, es probable que el UPN de los usuarios tenga también un sufijo no enrutable. Por ejemplo, si se ejecuta bajo "contoso.local", Azure AD Connect le sugiere que utilice la configuración personalizada en lugar de la configuración rápida. Al usar una configuración personalizada, podrá especificar el atributo que debe usarse como UPN para iniciar sesión en Azure después de que los usuarios se sincronicen con Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


