---
title: "Preguntas más frecuentes de Azure AD Connect Health"
description: "Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 2f990f4c888da91422a6a6e9873307cbb483e782
ms.openlocfilehash: b6687bc0ccde0e257338a939f39e343f75a6f5df


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Preguntas más frecuentes sobre Azure AD Connect Health
Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## <a name="general-questions"></a>Preguntas generales
**P: Administro varios directorios de Azure AD. ¿Cómo puedo cambiar al inquilino que tiene Azure Active Directory Premium?**

Puede alternar entre los distintos inquilinos de Azure AD seleccionando el nombre de usuario con el que se ha iniciado sesión actualmente en la esquina superior derecha y eligiendo la cuenta adecuada. Si la cuenta no aparece aquí, seleccione Cerrar sesión y luego use las credenciales de administrador global del directorio que tiene Azure Active Directory Premium habilitado para iniciar sesión.

## <a name="installation-questions"></a>Preguntas sobre la instalación
**P: ¿Qué impacto tiene la instalación del agente de Azure AD Connect Health en los servidores individuales?**

El impacto de la instalación de ADFS del agente de Microsoft Azure AD Connect Health, servidores proxy de aplicación web, servidores de Azure AD Connect (sincronización), controladores de dominio es mínimo con respecto a la CPU, al consumo de memoria, al ancho de banda y al almacenamiento.

Los números siguientes son una aproximación.

* Consumo de CPU: aumento de ~1%
* Consumo de memoria: hasta 10 % de la memoria total del sistema

> [!NOTE]
> En caso de que el agente no se pueda comunicar con Azure, el agente almacena los datos localmente, hasta un límite máximo definido. El agente sobrescribe los datos "en caché" en función de "los servidos menos recientemente".
> 
> 

* Almacenamiento en búfer local para agentes de Azure AD Connect Health: ~ 20 MB
* Para los servidores de AD FS, se recomienda que aprovisione un espacio en disco de 1024 MB (1 GB) para que el canal de auditoría de AD FS para agentes de Azure AD Connect Health procese todos los datos de auditoría antes de que se sobrescriban.

**P: ¿Tendré que reiniciar los servidores durante la instalación de los agentes de Azure AD Connect Health?**

No. La instalación de los agentes no requerirá que reinicie el servidor. Sin embargo, la instalación de algunos de los requisitos previos podría requerir reiniciar el servidor.

En Windows Server 2008 R2, por ejemplo, la instalación de .Net 4.5 Framework requiere un reinicio del servidor.

**P: ¿Los Servicios de Azure AD Connect Health funcionan mediante un proxy HTTP de paso a través?**

Sí.  Para las operaciones en curso, puede configurar el agente de mantenimiento para reenviar las solicitudes http de salida con un proxy HTTP.

Si necesita configurar a un proxy durante el registro del agente, debe modificar de antemano su configuración del proxy de Internet Explorer.

1. Abra Internet Explorer -> Configuración -> Opciones de Internet -> Conexiones -> Configuración de LAN.
2. Seleccione Usar un servidor proxy para la LAN.
3. Seleccione Opciones avanzadas si tiene distintos puertos de proxy para HTTP y HTTPS/Secure.

**P: ¿Servicios de Azure AD Connect Health admiten la autenticación básica cuando se conectan con servidores proxy de HTTP?**

No. Actualmente, no se admite un mecanismo para especificar un nombre de usuario/contraseña arbitrarios para la autenticación básica.

**P: ¿Qué versión de AD DS es compatible con Azure AD Connect Health para AD DS?**

Se admite la supervisión de AD DS mientras esté instalada en las siguientes versiones de sistema operativo:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>Preguntas sobre las operaciones
**P: ¿Es necesario habilitar la auditoría en los servidores proxy de aplicación de AD FS o de aplicación web?**

No, la auditoría no debe habilitarse en los servidores proxy de aplicación Web (WAP). Habilítela en los servidores de AD FS.

**P: ¿Cómo se resuelven las alertas de Azure AD Connect Health?**

Las alertas de Azure AD Connect Health se resuelven con una condición de acierto. Los agentes de Azure AD Connect Health detectan e informan las condiciones de acierto al servicio de manera periódica. En el caso de algunas alertas, la supresión depende del tiempo. En otras palabras, si la misma condición de error no se observa dentro de un plazo de 72 horas desde la generación de la alerta, esta se resuelve de forma automática.

**P: ¿Qué puertos de firewall debo abrir para que funcione el agente de Azure AD Connect Health?**

Debe abrir los puertos 443 y 5671 TCP/UDP para que el agente de Azure AD Connect Health pueda comunicarse con los puntos de conexión del servicio Azure AD Health.

**P: ¿Por qué veo dos servidores con el mismo nombre en el Portal de Azure AD Connect Health?**

Cuando quita un agente de un servidor, el servidor no se quita automáticamente del Portal de Azure AD Connect.  Si quita de forma manual un agente de un servidor o quita el propio servidor, necesita eliminar manualmente la entrada del servidor desde el Portal de Azure AD Connect Health. 

Si restableció la imagen inicial de un servidor o creó uno nuevo con los mismos detalles (como el nombre de la máquina), pero no quitó el servidor ya registrado del Portal de Azure AD Connect Health e instaló el agente en el servidor nuevo, es posible que vea dos entradas con el mismo nombre.  
En este caso, debería eliminar la entrada que pertenece al servidor anterior de forma manual. Los datos para este servidor deben estar obsoletos.

## <a name="related-links"></a>Vínculos relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


