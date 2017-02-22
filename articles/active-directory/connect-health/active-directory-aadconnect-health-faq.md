---
title: "Preguntas más frecuentes (P+F) sobre Azure Active Directory Connect Health: Azure | Microsoft Docs"
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
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Preguntas más frecuentes sobre Azure AD Connect Health
Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## <a name="general-questions"></a>Preguntas generales
**P: Administro varios directorios de Azure AD. ¿Cómo cambio entre los diferentes directorios en Azure Portal?**

Puede alternar entre los distintos inquilinos de Azure AD seleccionando el nombre de usuario con el que se ha iniciado sesión actualmente en la esquina superior derecha y eligiendo la cuenta adecuada. Si la cuenta no aparece en la lista, seleccione Cerrar sesión y, a continuación, use las credenciales adecuadas del directorio para iniciar sesión.

**P: ¿Qué versión de los roles de identidad es compatible con Azure AD Connect Health?**

En la tabla siguiente se enumeran los roles y las versiones de sistema operativo compatibles.

|Rol| Sistema operativo / Versión|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Versión 1.0.9125 o posterior.|
|AD DS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Tenga en cuenta que las características proporcionadas por el servicio pueden diferir según el rol y el sistema operativo. En otras palabras, todas las características pueden no estar disponibles para todas las versiones del SO. Vea las descripciones de las características para obtener más información.

**P: ¿Cuántas licencias necesito para supervisar mi infraestructura?**

* El primer agente de Connect Health requiere al menos una licencia de Azure AD Premium.
* Cada agente adicional registrado requiere 25 licencias de AADP más. 
* El número de agentes es equivalente al número total de agentes registrados en todos los roles supervisados (AD FS, Azure AD Connect o AD DS).

La información de licencia se encuentra también en la [página de precios de Azure AD](https://aka.ms/aadpricing).

Ejemplo:

| Agentes registrados | Licencias necesarias | Ejemplo de configuración de supervisión |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor de AAD Connect |
| 2 | 26| 1 servidor de AAD Connect y 1 controlador de dominio |
| 3 | 51 | 1 servidor de AD FS, 1 proxy de AD FS y 1 controlador de dominio |
| 4 | 76 | 1 servidor de AD FS, 1 proxy de AD FS y 2 controladores de dominio |
| 5 | 101 | 1 servidor de AAD Connect, 1 servidor de AD FS, 1 proxy de AD FS y 2 controladores de dominio |
## <a name="installation-questions"></a>Preguntas sobre la instalación
**P: ¿Qué impacto tiene la instalación del agente de Azure AD Connect Health en los servidores individuales?**

El impacto de instalar ADFS del agente de Microsoft Azure AD Connect Health, los servidores proxy de aplicación web, los servidores de Azure AD Connect (sincronización) y los controladores de dominio es mínimo en la CPU, el consumo de memoria, el ancho de banda y el almacenamiento.

Los números siguientes son una aproximación.

    * Consumo de CPU: aumento de ~1-5%
    * Consumo de memoria: hasta 10 % de la memoria total del sistema

> [!NOTE]
> Si el agente de mantenimiento pierde la conexión con el servicio Connect Health, almacena los datos localmente, hasta un límite máximo definido. El agente sobrescribe los datos "en caché" en función de "los servidos menos recientemente".
>
>

* Almacenamiento en búfer local para agentes de Azure AD Connect Health: ~&20; MB
* Para los servidores de AD FS, se recomienda que aprovisione un espacio en disco de 1024 MB (1 GB) para que el canal de auditoría de AD FS para agentes de Azure AD Connect Health procese todos los datos de auditoría antes de que se sobrescriban.

**P: ¿Tendré que reiniciar los servidores durante la instalación de los agentes de Azure AD Connect Health?**

No. La instalación de los agentes no requerirá que reinicie el servidor.

La instalación de algunos de los requisitos previos podría requerir reiniciar el servidor. En Windows Server 2008 R2, por ejemplo, la instalación de .Net 4.5 Framework requiere un reinicio del servidor.

**P: ¿Los Servicios de Azure AD Connect Health funcionan mediante un proxy HTTP de paso a través?**

Sí.  Para las operaciones en curso, puede configurar el agente de mantenimiento para reenviar las solicitudes http de salida con un proxy HTTP. Obtenga más información sobre cómo [configurar el proxy HTTP para los agentes de mantenimiento](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

Si necesita configurar a un proxy durante el registro del agente, debe modificar de antemano su configuración del proxy de Internet Explorer.

1. Abra Internet Explorer -> Configuración -> Opciones de Internet -> Conexiones -> Configuración de LAN.
2. Seleccione Usar un servidor proxy para la LAN.
3. Seleccione Opciones avanzadas si tiene distintos puertos de proxy para HTTP y HTTPS/Secure.

**P: ¿Servicios de Azure AD Connect Health admiten la autenticación básica cuando se conectan con servidores proxy de HTTP?**

No. Actualmente, no se admite un mecanismo para especificar un nombre de usuario/contraseña arbitrarios para la autenticación básica.

**P: ¿Qué puertos de firewall debo abrir para que funcione el agente de Azure AD Connect Health?**

Vea la [sección Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para obtener la lista de puertos del firewall y otros requisitos de conectividad.


**P: ¿Por qué veo dos servidores con el mismo nombre en el Portal de Azure AD Connect Health?**

Cuando quita un agente de un servidor, el servidor no se quita automáticamente del Portal de Azure AD Connect.  Si quita de forma manual un agente de un servidor o quita el propio servidor, necesita eliminar manualmente la entrada del servidor desde el Portal de Azure AD Connect Health.

Si restableció la imagen inicial de un servidor o creó uno nuevo con los mismos detalles (como el nombre de la máquina), pero no quitó el servidor ya registrado del Portal de Azure AD Connect Health e instaló el agente en el servidor nuevo, es posible que vea dos entradas con el mismo nombre.  
En este caso, debería eliminar la entrada que pertenece al servidor anterior de forma manual. Los datos para este servidor deben estar obsoletos.

## <a name="health-agent-registration-and-data-freshness"></a>Registro del agente de mantenimiento y actualización de datos

**P: ¿Cuáles son las causas comunes de los errores de registro del agente de mantenimiento y cómo puedo solucionarlos?**

El agente de mantenimiento puede presentar errores de registro debido a las siguientes razones posibles
    * El agente no puede comunicarse con los puntos de conexión necesarios porque el firewall bloquea el tráfico. Esto es especialmente frecuente en los servidores proxy de aplicación web. Asegúrese de que haya permitido la comunicación de salida con los puertos y puntos finales necesarios. Consulte la [sección Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para más información.
    * La comunicación de salida se somete a una inspección de SSL mediante el nivel de red. Esto da lugar a que el certificado utilizado por el agente se reemplace por la entidad/servidor de inspección, por lo que no puede realizar los pasos necesarios para completar el registro del agente.
    * El usuario no tiene acceso para realizar el registro del agente. Los administradores globales tienen acceso de forma predeterminada. Puede usar el [control de acceso basado en roles](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) para delegar el acceso a otros usuarios.

**P: Me aparece la alerta "Los datos del servicio de mantenimiento no están actualizados". ¿Cómo puedo solucionar el problema?**

El Servicio de mantenimiento genera esta alerta si no recibe todos los puntos de datos del servidor en las últimas 2 horas. Puede haber varios motivos por los que se activa esta alerta.
    * El agente no puede comunicarse con los puntos de conexión necesarios porque el firewall bloquea el tráfico. Esto es especialmente frecuente en los servidores proxy de aplicación web. Asegúrese de que haya permitido la comunicación de salida con los puertos y puntos finales necesarios. Consulte la [sección Requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para más información.
    * La comunicación de salida se somete a una inspección de SSL mediante el nivel de red. Esto da lugar a que el certificado utilizado por el agente se reemplace por la entidad/servidor de inspección, por lo que no puede cargar los datos en el servicio Connect Health.
    * Puede usar el comando de conectividad integrado en el agente. [Más información](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service).
    * Los agentes también admiten la conectividad saliente a través de un servidor proxy HTTP no autenticado. [Más información](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy).


## <a name="operations-questions"></a>Preguntas sobre las operaciones
**P: ¿Necesita habilitar la auditoría en los servidores proxy de aplicación web?**

No, la auditoría no debe habilitarse en los servidores proxy de aplicación Web (WAP).

**P: ¿Cómo se resuelven las alertas de Azure AD Connect Health?**

Las alertas de Azure AD Connect Health se resuelven con una condición de acierto. Los agentes de Azure AD Connect Health detectan e informan las condiciones de acierto al servicio de manera periódica. En el caso de algunas alertas, la supresión depende del tiempo. En otras palabras, si la misma condición de error no se observa dentro de un plazo de 72 horas desde la generación de la alerta, esta se resuelve de forma automática.


## <a name="migration-questions"></a>Preguntas sobre la migración

Esta sección solo se aplica a los clientes a los que se notificó la migración futura de sus datos de servicio de mantenimiento de Azure AD Connect Health.

**P: ¿Tengo que volver a registrar mis agentes o volver a configurar las notificaciones después de la migración?**

No, la información del registro del agente y la configuración de las notificaciones se trasladarán como parte de la migración.

**P: Después de la migración ¿Cuándo empezaré a ver datos en el portal?**

Los datos empezarán a aparecer en el portal en un plazo de una hora después de la migración.

**P: ¿Qué ocurre con mis alertas activas?**

Las alertas aplicables se volverán a activar en un plazo de una hora después de la migración.


## <a name="related-links"></a>Vínculos relacionados
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


