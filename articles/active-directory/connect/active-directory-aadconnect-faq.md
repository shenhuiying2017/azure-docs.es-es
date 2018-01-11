---
title: "Azure Active Directory Connect: Preguntas más frecuentes (P+F) | Microsoft Docs"
description: "Esta página contiene las preguntas más frecuentes sobre Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: billmath
ms.openlocfilehash: 13f12f39fb72bd7d4e32056753a012c38034f807
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Preguntas más frecuentes acerca de Azure Active Directory Connect

## <a name="general-installation"></a>Instalación general
**P: ¿Funcionará la instalación si el administrador global de Azure AD tiene 2FA habilitado?**  
Sí, con las compilaciones de febrero de 2016.

**P: ¿Existe alguna manera de instalar Azure AD Connect de manera desatendida?**  
Solo se admite la instalación de Azure AD Connect mediante el Asistente para instalación. No se admite la instalación silenciosa y desatendida.

**P: Tengo un bosque en el que no se puede contactar con un dominio. ¿Cómo se puede instalar Azure AD Connect?**  
Sí, con las compilaciones de febrero de 2016.

**P.: ¿El agente de mantenimiento de AD DS funciona en Server Core?**  
Sí. Después de instalar el agente, puede completar el proceso de registro mediante el siguiente cmdlet de PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: ¿AADConnect admite la sincronización de dos dominios en Azure AD?**</br>
Sí, este procedimiento se admite. Consulte [Varios dominios](active-directory-aadconnect-multiple-domains.md).
 
**P: ¿se admiten varios conectores para el mismo dominio de Active Directory en Azure AD Connect?**</br> No, no se admite. 

## <a name="network"></a>Red
**P: Tengo un firewall, dispositivo de red o algo más que limita las conexiones de tiempo máximo que pueden permanecer abiertas en mi red. ¿Cuál debería ser mi umbral de tiempo de espera del lado de cliente al usar Azure AD Connect?**  
Todo el software de redes, dispositivos físicos o cualquier otra cosa que limite el tiempo máximo en que las conexiones permanecen abiertas deben usar un umbral de un mínimo de 5 minutos (300 segundos) para conectividad entre el servidor donde está instalado el cliente de Azure AD Connect y Azure Active Directory. Esto también se aplica a todas las herramientas de sincronización de Microsoft Identity publicadas anteriormente.

**P: ¿Se admiten los dominios de una sola etiqueta (SLD)?**  
No, Azure AD Connect no admite bosques/dominios locales con dominios de una sola etiqueta.

**P: ¿Se admiten los bosques con dominios de AD no contiguos?**  
No, Azure AD Connect no admite bosques locales con espacios de nombres no contiguos.

**P: ¿Se admiten los nombres de NetBios con puntos?**  
No, Azure AD Connect no admite bosques/dominios locales en los que el nombre de NetBios contenga un punto "." en el nombre.

**P: ¿Se admiten entornos puros de IPv6?**  
No, Azure AD Connect no admite entornos puros de IPv6.

## <a name="federation"></a>Federación
**P.: ¿Qué debo hacer si recibo un correo electrónico que me pide que renueve el certificado de Office 365?**  
Siga las instrucciones que se describen en el tema de [renovación de certificados](active-directory-aadconnect-o365-certs.md) .

**P.: Tengo establecido Actualizar automáticamente el usuario de confianza para el usuario de confianza de O365. ¿Es necesario realizar alguna acción cuando se implemente automáticamente mi certificado de firma de tokens?**  
Siga las instrucciones que se describen en el artículo sobre la [renovación de certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Environment
**P: ¿Se admite cambiar el nombre del servidor después de haber instalado Azure AD Connect?**  
No. Cambiar el nombre del servidor hará que el motor de sincronización no pueda conectarse a la Base de datos SQL y el servicio no podrá iniciarse.

## <a name="identity-data"></a>Datos de identidad
**P: El atributo UPN (userPrincipalName) de Azure AD no coincide con el UPN local, ¿por qué?**  
Consulte estos artículos:

* [Los nombres de usuario de Office 365, Azure o Intune no coinciden con el UPN local o el identificador de inicio de sesión alternativo](https://support.microsoft.com/en-us/kb/2523192)
* [Los cambios no son sincronizados por la herramienta de sincronización de Azure Active Directory después de cambiar el UPN de una cuenta de usuario para usar un dominio federado diferente](https://support.microsoft.com/en-us/kb/2669550)

También puede configurar Azure AD para permitir que el motor de sincronización actualice userPrincipalName como se describe en [Azure AD Connect sync service features](active-directory-aadconnectsyncservice-features.md)(Características del servicio de sincronización de Azure AD Connect).

**P: ¿Se admiten las coincidencias parciales de objetos de contacto/grupo de AD local con objetos de contacto/grupo de Azure AD existentes?**  
No, actualmente no se admite.

**P: ¿Se admite configurar manualmente el atributo ImmutableId en objetos de contacto/grupo de Azure AD existentes para conseguir una coincidencia exacta con los objetos de contacto/grupo de AD local?**  
No, actualmente no se admite.

## <a name="custom-configuration"></a>Configuración personalizada
**P: ¿Dónde se documentan los cmdlets de PowerShell para Azure AD Connect?**  
A excepción de los cmdlets documentados en este sitio, el resto de cmdlets de PowerShell que se encuentran en Azure AD Connect no se admiten para uso del cliente.

**P.: ¿Puedo usar la característica exportación e importación del servidor de *Synchronization Service Manager* para mover la configuración entre servidores?**  
No. Esta opción no recuperará todas las opciones de configuración y no debe usarse. En su lugar, debe usar al Asistente para crear la configuración base en el segundo servidor y utilizar el editor de reglas de sincronización para generar scripts de PowerShell para mover cualquier regla personalizada entre servidores. Consulte [Migración oscilante](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**P: ¿Se pueden almacenar en caché las contraseñas de la página de inicio de sesión y se puede evitar esto dado que contiene un elemento de entrada de contraseña con el atributo de autocompletar = "false"?**</br>
Actualmente no admitimos la modificación de los atributos HTML del campo de entrada de contraseña, incluida la etiqueta de autocompletar. Estamos trabajando en una característica que permitirá Javascript personalizado, por lo que podrá agregar cualquier atributo al campo de contraseña. Esta solución debería estar disponible a finales de 2017.

**P: En la página de inicio de sesión de Azure, se muestran nombres de usuario de usuarios que han iniciado sesión anteriormente de forma correcta.  ¿Este comportamiento se puede desactivar?**</br>
Actualmente no admitimos la modificación de los atributos HTML de la página de inicio de sesión. Estamos trabajando en una característica que permitirá Javascript personalizado, por lo que podrá agregar cualquier atributo al campo de contraseña. Esta solución debería estar disponible a finales de 2017.

**P: ¿Existe alguna manera de evitar las sesiones simultáneas?**</br>
No.

## <a name="troubleshooting"></a>Solución de problemas
**P: ¿Cómo puedo obtener ayuda con Azure AD Connect?**

[Buscar en Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Busque en Microsoft Knowledge Base (KB) soluciones técnicas a los problemas de break-fix más comunes sobre soporte técnico de Azure AD Connect.

[Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Puede buscar y examinar preguntas técnicas y sus respuestas en la comunidad o realizar su propia pregunta haciendo clic [aquí](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Obtención de soporte técnico para Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Use este vínculo para obtener soporte técnico mediante el Portal de Azure.

