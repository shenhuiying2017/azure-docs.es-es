---
title: 'Azure Active Directory Connect: Preguntas más frecuentes (P+F) | Microsoft Docs'
description: Esta página contiene las preguntas más frecuentes sobre Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: billmath
ms.openlocfilehash: 46a9bf47b4998c4d5be47f67556fbdb3ba7b71db
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054101"
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
Nº Cambiar el nombre del servidor hará que el motor de sincronización no pueda conectarse a la Base de datos SQL y el servicio no podrá iniciarse.

## <a name="identity-data"></a>Datos de identidad
**P: El atributo UPN (userPrincipalName) de Azure AD no coincide con el UPN local, ¿por qué?**  
Consulte estos artículos:

* [Los nombres de usuario de Office 365, Azure o Intune no coinciden con el UPN local o el identificador de inicio de sesión alternativo](https://support.microsoft.com/en-us/kb/2523192)
* [Los cambios no son sincronizados por la herramienta de sincronización de Azure Active Directory después de cambiar el UPN de una cuenta de usuario para usar un dominio federado diferente](https://support.microsoft.com/en-us/kb/2669550)

También puede configurar Azure AD para permitir que el motor de sincronización actualice userPrincipalName como se describe en [Azure AD Connect sync service features](active-directory-aadconnectsyncservice-features.md)(Características del servicio de sincronización de Azure AD Connect).

**P: ¿Se admiten las coincidencias parciales de objetos de contacto/grupo de AD local con objetos de contacto/grupo de Azure AD existentes?**  
Sí, se basarán en la dirección de proxy.  No se admite la coincidencia parcial en grupos no habilitados para correo.

**P: ¿Se admite configurar manualmente el atributo ImmutableId en objetos de contacto/grupo de Azure AD existentes para conseguir una coincidencia exacta con los objetos de contacto/grupo de AD local?**  
No, actualmente no se admite.

## <a name="custom-configuration"></a>Configuración personalizada
**P: ¿Dónde se documentan los cmdlets de PowerShell para Azure AD Connect?**  
A excepción de los cmdlets documentados en este sitio, el resto de cmdlets de PowerShell que se encuentran en Azure AD Connect no se admiten para uso del cliente.

**P.: ¿Puedo usar la característica exportación e importación del servidor de *Synchronization Service Manager* para mover la configuración entre servidores?**  
Nº Esta opción no recuperará todas las opciones de configuración y no debe usarse. En su lugar, debe usar al Asistente para crear la configuración base en el segundo servidor y utilizar el editor de reglas de sincronización para generar scripts de PowerShell para mover cualquier regla personalizada entre servidores. Consulte [Migración oscilante](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**P: ¿Se pueden almacenar en caché las contraseñas de la página de inicio de sesión y se puede evitar esto dado que contiene un elemento de entrada de contraseña con el atributo de autocompletar = "false"?**</br>
Actualmente no admitimos la modificación de los atributos HTML del campo de entrada de contraseña, incluida la etiqueta de autocompletar. Estamos trabajando en una característica que permitirá Javascript personalizado, por lo que podrá agregar cualquier atributo al campo de contraseña. Esta solución debería estar disponible a finales de 2017.

**P: En la página de inicio de sesión de Azure, se muestran nombres de usuario de usuarios que han iniciado sesión anteriormente de forma correcta.  ¿Este comportamiento se puede desactivar?**</br>
Actualmente no admitimos la modificación de los atributos HTML de la página de inicio de sesión. Estamos trabajando en una característica que permitirá Javascript personalizado, por lo que podrá agregar cualquier atributo al campo de contraseña. Esta solución debería estar disponible a finales de 2017.

**P: ¿Existe alguna manera de evitar las sesiones simultáneas?**</br>
Nº

## <a name="auto-upgrade"></a>Actualización automática

**P: ¿Cuáles son las ventajas y consecuencias de usar la actualización automática?**</br>
Se recomienda a todos los clientes que habiliten la actualización automática para la instalación de Azure AD Connect. Estas son las ventajas de las que siempre se beneficiarán las revisiones más recientes, incluidas las actualizaciones de seguridad de las vulnerabilidades que se detectaron en Azure AD Connect. El proceso de actualización es sencillo y se realizará automáticamente en cuanto se encuentre disponible una versión nueva. Abastecemos a muchos miles de clientes de Azure AD Connect mediante la actualización automática con cada nueva versión.

El proceso de actualización automática siempre determina primero si una instalación es apta para la actualización automática (esto incluye, entre otros aspectos, buscar cambios personalizados en las reglas y factores específicos del entorno) y, de ser así, la actualización se realiza y se prueba. Si las pruebas revelan que una actualización no se realizó correctamente, automáticamente se restaurará la versión anterior.

Dependiendo del tamaño del entorno, el proceso puede tardar un par de horas y, mientras se produce la actualización, no se realizará ninguna sincronización entre Windows Server AD y Azure AD.

**P: Recibí un correo electrónico para comunicarme que mi versión de actualización automática ya no funciona y que tengo que instalar la nueva. ¿Por qué tengo que hacerlo?**</br>
El año pasado se publicó una versión de Azure AD Connect que, en determinadas circunstancias, podría haber deshabilitado la característica de actualización automática en el servidor. Se corrigió este problema en la versión 1.1.750.0 de Azure AD Connect, que se publicó al final del mes pasado. Necesitamos que los clientes afectados por este problema actualicen manualmente a la última versión de Azure AD Connect para mitigar el problema. Para actualizar manualmente, debe descargar y ejecutar la última versión del archivo AADConnect.msi.
 
-  Si su versión actual es anterior a la 1.1.750.0, debe actualizar a la última versión, [que puede descargarse aquí](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Si su versión de Azure AD Connect es la 1.1.750.0 o posterior, no es necesario realizar ninguna acción para mitigar el problema de actualización automática, puesto que dispone de la versión en la que se ha aplicado la corrección. 

**P: Recibí un correo electrónico donde se me pide que actualice a la última versión para volver a habilitar la actualización automática. Tengo la versión 1.1.654.0, ¿tengo que actualizarla?** </br>    
Sí, debe actualizar a la versión 1.1.750 o posteriores para volver a habilitar la actualización automática. Aquí encontrará el vínculo en que se explica cómo actualizar a una versión más reciente.

**P: Recibí un correo electrónico donde se me pide que actualice a la última versión para volver a habilitar la actualización automática. Usé PowerShell para habilitar la actualización automática, ¿también necesito instalar la última versión?**</br>    
Sí, deberá actualizar a la versión 1.1.750.0 o posterior. Habilitar el servicio de actualización automática con PowerShell no mitiga el problema de actualización automática detectado en las versiones anteriores a la 1.1.750.

**P: ¿Quiero actualizar a una versión más reciente, pero no estoy seguro de quién instaló Azure AD Connect y no disponemos del nombre de usuario y la contraseña.  ¿Necesito estos datos?**</br>
No necesita conocer el nombre de usuario y la contraseña utilizados inicialmente para actualizar Azure AD Connect; se puede usar cualquier cuenta de Azure AD que tenga el rol de administrador global.

**P: ¿Cómo puedo saber qué versión de Azure AD Connect tengo?**</br>   
Para comprobar qué versión de Azure AD Connect está instalada en el servidor, vaya al Panel de control y busque la versión instalada de Microsoft Azure AD Connect en "Programas > Programas y características":

![version](media/active-directory-aadconnect-faq/faq1.png)

**P: ¿Cómo se realiza la actualización a la última versión de AADConnect?**</br>    
En este [artículo](active-directory-aadconnect-upgrade-previous-version.md) se explica cómo actualizar a una versión más reciente. 

**P: Ya actualizamos a la última versión de AADConnect el año pasado, ¿tenemos que volver a actualizar?**</br> Los equipos de Azure AD Connect realizan actualizaciones frecuentes del servicio, y es importante que el servidor esté actualizado con la última versión para beneficiarse de correcciones de errores y actualizaciones de seguridad, así como de las nuevas características. Si habilita la actualización automática, se actualizará automáticamente la versión del software. Para buscar el historial de versiones de Azure AD Connect, siga este [vínculo](active-directory-aadconnect-version-history.md).

**P: ¿Cuánto tarda en realizarse la actualización y en qué afectará a mis usuarios?**</br>    
El tiempo necesario para actualizar depende del tamaño de los inquilinos, y para las organizaciones grandes puede ser preferible hacerlo por la noche o durante el fin de semana. Tenga en cuenta que, durante la actualización, no se realiza ninguna actividad de sincronización.

**P: ¿Creo que actualicé a AADConnect, pero en el portal de Office todavía se menciona DirSync.  ¿Qué significa?**</br>    
El equipo de Office está trabajando para que las actualizaciones del portal de Office reflejen el nombre de producto actual; no refleja qué herramienta de sincronización se usa.

**P: Comprobé el estado de la actualización automática y es "Suspendida". ¿Por qué está suspendida? ¿Debo habilitarla?**</br>     
Se introdujo un error en una versión anterior que, en determinadas circunstancias, podría hacer que el estado de la actualización automática esté establecido en "Suspendida". Habilitarla manualmente es posible desde el punto de vista técnico, pero conllevaría varios pasos complejos; por ello, lo mejor es instalar la última versión de Azure AD Connect.

**P: Mi empresa tiene requisitos de administración de cambios estrictos y me gustaría estar al tanto de los nuevos lanzamientos. ¿Puedo controlar cuándo se lanza la actualización automática?**</br> No, actualmente no existe esta característica, la estamos evaluando para una futura versión.

**P: ¿Recibiré un correo electrónico si la actualización automática presenta algún error? ¿Cómo puedo saber si la actualización se realizó correctamente?**</br>     
No se le notificará el resultado de la actualización, es algo que estamos valorando para una futura versión.

**P: ¿Se publica alguna escala de tiempo de la previsión de lanzamiento de actualizaciones automáticas?**</br>    
La actualización automática es el primer paso de nuestro proceso de lanzamiento de una versión más reciente, así que, cada vez que haya una nueva versión, lanzaremos las actualizaciones automáticas. Las versiones más recientes de Azure AD Connect se anuncian con anticipación en la [hoja de ruta de Azure AD](../../active-directory/whats-new.md).

**P: ¿La actualización automática actualiza AAD Connect Health?**</br>   Sí, la actualización automática también actualiza AAD Connect Health.

**P: ¿También se actualizan automáticamente los servidores de AAD Connect en modo de almacenamiento provisional?**</br>   
No, no se puede actualizar automáticamente un servidor de Azure AD Connect que se encuentra en modo de almacenamiento provisional.

**P: Si la actualización automática genera algún error y mi servidor de AAD Connect no se inicia, ¿qué debo hacer?**</br>   
En raras ocasiones, el servicio Azure AD Connect no se inicia después de realizar la actualización. En esos casos, reinicie el servidor, ya que así suele corregirse el problema. Si el servicio Azure AD Connect sigue sin iniciarse, abra un vale de soporte. En este [vínculo](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) se explica cómo hacerlo. 

**P: No estoy seguro de los riesgos derivados de la actualización a una versión más reciente de Azure AD Connect. ¿Puede llamarme para ayudarme con la actualización?**</br>
Si necesita actualizar a una versión más reciente de Azure AD Connect, abra un vale de soporte; en este [vínculo](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) se explica cómo hacerlo.

## <a name="troubleshooting"></a>solución de problemas
**P: ¿Cómo puedo obtener ayuda con Azure AD Connect?**

[Buscar en Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Busque en Microsoft Knowledge Base (KB) soluciones técnicas a los problemas de break-fix más comunes sobre soporte técnico de Azure AD Connect.

[Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Puede buscar y examinar preguntas técnicas y sus respuestas en la comunidad o realizar su propia pregunta haciendo clic [aquí](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Obtención de soporte técnico para Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Use este vínculo para obtener soporte técnico mediante el Portal de Azure.

