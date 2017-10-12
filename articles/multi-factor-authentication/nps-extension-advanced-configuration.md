---
title: "Configuración de la extensión NPS de Azure MFA | Microsoft Docs"
description: "Después de instalar la extensión NPS, siga estos pasos para la configuración avanzada como la creación de listas blancas de direcciones IP y el reemplazo de UPN."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: ef922668f080b8f02f07c2f9724f5a98171fb754
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opciones de configuración avanzada para la extensión NPS para Multi-Factor Authentication

La extensión Servidor de directivas de redes (NPS) amplía las características de Azure Multi-Factor Authentication basadas en la nube a la infraestructura local. En este artículo se supone que ya tiene la extensión instalada y ahora desea saber cómo personalizarla conforme a sus necesidades. 

## <a name="alternate-login-id"></a>Identificador de inicio de sesión alternativo

Puesto que la extensión NPS se conecta a los directorios locales y de la nube, puede tener problemas donde los nombres principales de usuario (UPN) locales no coincidan con los nombres existentes en la nube. Para solucionar este problema, use identificadores de inicio de sesión alternativos. 

Dentro de la extensión NPS, puede designar un atributo de Active Directory para que se use en lugar del UPN para Azure Multi-Factor Authentication. Esto le permite proteger los recursos locales con la verificación en dos pasos sin modificar los UPN locales. 

Para configurar identificadores de inicio de sesión alternativos, vaya a `HKLM\SOFTWARE\Microsoft\AzureMfa` y edite los siguientes valores del Registro:

| Nombre | Escriba | Valor predeterminado | Descripción |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | cadena | Vacío | Designe el nombre del atributo de Active Directory que desea usar en lugar del UPN. Este atributo se utiliza como el atributo AlternateLoginId. Si este valor del Registro se establece en un [atributo de Active Directory válido](https://msdn.microsoft.com/library/ms675090.aspx) (por ejemplo, correo electrónico o displayName), a continuación, el valor del atributo se utiliza en lugar del UPN del usuario para la autenticación. Si este valor del registro está vacío o no está configurado, AlternateLoginId se deshabilita y el UPN del usuario se utiliza para la autenticación. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | Use esta marca para exigir el uso del catálogo global para búsquedas LDAP al buscar AlternateLoginId. Configure un controlador de dominio como catálogo global, agregue el atributo AlternateLoginId a dicho catálogo y luego habilite esta marca. <br><br> Si LDAP_LOOKUP_FORESTS se ha configurado (no vacío), **se exige que esta marca sea true**, independientemente del valor de la configuración del Registro. En este caso, la extensión NPS requiere que el catálogo global esté configurado con el atributo AlternateLoginId para cada bosque. |
| LDAP_LOOKUP_FORESTS | cadena | Vacío | Proporcione una lista separada por puntos y coma de bosques para buscar. Por ejemplo, *contoso.com;foobar.com*. Si se configura este valor del Registro, la extensión NPS busca iterativamente en todos los bosques en el orden en el que se muestran y devuelve el primer valor AlternateLoginId correcto. Si este valor del Registro no está configurado, la búsqueda de AlternateLoginId se limita al dominio actual.|

Para solucionar problemas con identificadores de inicio de sesión alternativos, siga los pasos recomendados para [errores de identificadores de inicio de sesión alternativos](multi-factor-authentication-nps-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Excepciones de dirección IP

Si necesita supervisar la disponibilidad del servidor, como si los equilibradores de carga comprueban qué servidores se ejecutan antes de enviar cargas de trabajo, no deseará que las solicitudes de verificación bloqueen estas comprobaciones. En su lugar, cree una lista de direcciones IP que sepa que las cuentas de servicio las utilizan y deshabilite los requisitos de Multi-Factor Authentication en esa lista. 

Para configurar una lista banca de direcciones IP, vaya a `HKLM\SOFTWARE\Microsoft\AzureMfa` y configure el siguiente valor del Registro: 

| Nombre | Escriba | Valor predeterminado | Descripción |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | cadena | Vacío | Proporcione una lista separada por puntos y coma de direcciones IP. Incluya las direcciones IP de las máquinas donde se originan las solicitudes de servicio, como el servidor NAS/VPN. Los intervalos de direcciones IP y las subredes no se admiten. <br><br> Por ejemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

Cuando llega una solicitud procedente de una dirección IP que existe en la lista blanca, se omite la verificación en dos pasos. La lista blanca de direcciones IP se compara con la dirección IP que se proporciona en el atributo *ratNASIPAddress* de la solicitud RADIUS. Si llega una solicitud RADIUS sin el atributo ratNASIPAddress, se registra la siguiente advertencia similar a la siguiente: "La lista blanca P_WHITE_LIST_WARNING::IP se ha omitido porque la dirección IP de origen falta en la solicitud RADIUS en el atributo NasIpAddress".

## <a name="next-steps"></a>Pasos siguientes

[Resolución de mensajes de error de la extensión de NPS para Azure Multi-Factor Authentication](multi-factor-authentication-nps-errors.md)