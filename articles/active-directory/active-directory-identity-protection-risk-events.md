---
title: Eventos de riesgo de Azure Active Directory | Microsoft Docs
description: "En este tema se explica detalladamente qué son los eventos de riesgo."
services: active-directory
keywords: azure active directory identity protection, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad
author: MarkusVi
manager: femila
ms.assetid: ce3b054c-7772-401f-9b06-3d24f6e7ca86
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: e37f1716b459ea8eb0f2d0a5a12e7a3a8ab2c083
ms.openlocfilehash: e2ec0cefb6e009f22c3406ccd1570748dc467ed0
ms.lasthandoff: 02/22/2017


---
# <a name="azure-active-directory-risk-events"></a>Eventos de riesgo de Azure Active Directory

Azure Active Directory le ayuda a proteger sus identidades. Una parte del proceso de protección es la detección de acciones sospechosas que están relacionadas con sus cuentas de usuario. La detección se basa en algoritmos y heurística de aprendizaje automático adaptable. Cada acción sospechosa detectada se almacena en un registro denominado *evento de riesgo*. 


![Evento de riesgo](./media/active-directory-identity-protection-risk-events/91.png)


En la actualidad, Azure Active Directory detecta seis tipos de eventos de riesgo.

| Tipo de evento de riesgo | Nivel de riesgo | Tipo de detección |
| :-- | --- | --- |
| [Usuarios con credenciales perdidas](#leaked-credentials) | Alto | Sin conexión |
| [Inicios de sesión desde direcciones IP anónimas](#sign-ins-from-anonymous-ip-addresses) | Mediano | Tiempo real |
| [Viaje imposible a ubicaciones inusuales](#impossible-travel-to-atypical-locations) | Mediano | Sin conexión |
| [Inicios de sesión desde ubicaciones desconocidas](#sign-in-from-unfamiliar-locations) | Mediano | Tiempo real |
| [Inicios de sesión desde dispositivos infectados](#sign-ins-from-infected-devices) | Bajo | Sin conexión |
| [Inicios de sesión desde direcciones IP con actividad sospechosa](#sign-ins-from-ip-addresses-with-suspicious-activity) | Mediano | Sin conexión|

## <a name="risk-level"></a>Nivel de riesgo

La propiedad de nivel de riesgo es un indicador (alto, medio o bajo) de la gravedad de un evento de riesgo. Esta propiedad le ayuda a clasificar por orden de prioridad las acciones que debe realizar. 

La gravedad del evento de riesgo representa la fuerza de la señal como predicción del riesgo de la identidad, combinada con la cantidad de ruido que proporciona normalmente.

* **Alto**: evento de riesgo de gravedad alta y de alta confianza. Estos eventos son buenos indicadores de que se ha puesto en peligro la identidad del usuario; las cuentas de usuario afectadas deben corregirse inmediatamente.

* **Medio**: evento de riesgo de gravedad alta, pero confianza inferior, o viceversa. Estos eventos son potencialmente peligrosos y las cuentas de usuario afectadas deben corregirse.

* **Bajo**: evento de riesgo de baja confianza y gravedad baja. Este evento puede no requerir una acción inmediata, pero cuando se combina con otros eventos de riesgo, puede proporcionar una indicación clara de que la identidad está en peligro.

![Nivel de riesgo](./media/active-directory-identity-protection-risk-events/01.png)


## <a name="detection-type"></a>Tipo de detección

La propiedad de tipo de detección es un indicador (en tiempo real, sin conexión) del período de tiempo de detección de un evento de riesgo.  
Actualmente, la mayoría de los eventos de riesgo se detectan sin conexión en una operación posterior de procesamiento una vez que ya se ha producido el evento de riesgo.

En la tabla siguiente se muestra la cantidad de tiempo que tarda un tipo de detección en aparecer en un informe relacionado.

| Tipo de detección | Informes de latencia |
| --- | --- |
| Tiempo real | 5 a 10 minutos |
| Sin conexión | 2 a 4 horas |



## <a name="risk-event-types"></a>Tipo de evento de riesgo

La propiedad de tipo de evento de riesgo es un identificador de la acción sospechosa para la que se ha creado un registro de evento de riesgo.  
En la actualidad, Azure Active Directory detecta seis tipos de eventos.

Las continuas inversiones de Microsoft en procesos de detección conducirán a:

- Mejoras en la precisión de la detección de los eventos de riesgo ya existentes 
- Nuevos tipos de eventos de riesgo que se agregarán en el futuro

### <a name="leaked-credentials"></a>Credenciales con fugas
Los investigadores de seguridad de Microsoft han encontrado credenciales con fugas publicadas en la Web oscura. Estas credenciales se encuentran normalmente en texto sin formato. Se comprueban con credenciales de Azure AD y, si hay una coincidencia, se notifican como "Credenciales con fugas" en Identity Protection.

Los eventos de riesgo de credenciales con fugas se clasifican con gravedad "Alta", ya que proporcionan una indicación clara de que el nombre de usuario y la contraseña están a disposición de un atacante.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inicios de sesión desde direcciones IP anónimas
Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónima. A menudo, estos servidores proxy los usan los usuarios que desean ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados.

Se recomienda ponerse en contacto inmediatamente con el usuario para comprobar si utilizaba direcciones IP anónimas. El nivel de riesgo de este tipo de evento es ""**Medio**" porque, en sí misma, una IP anónima no es una indicación clara de una cuenta en riesgo.


### <a name="impossible-travel-to-atypical-locations"></a>Viaje imposible a ubicaciones inusuales
Este tipo de evento de riesgo identifica dos inicios de sesión procedentes de ubicaciones geográficamente distantes, donde al menos una de las ubicaciones puede también ser inusual para el usuario, según su comportamiento anterior. Además, el tiempo entre los dos inicios de sesión es menor que el tiempo que habría necesitado el usuario para viajar de la primera ubicación a la segunda, lo que indica que otro usuario utiliza las mismas credenciales. 

Este algoritmo de aprendizaje automático omite*falsos positivos*obvios que contribuyen a la condición de viaje imposible, como las VPN y las ubicaciones que usan con regularidad otros usuarios de la organización.  El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicios de sesión del nuevo usuario.

Un viaje imposible suele ser un buen indicador de que un hacker logró iniciar sesión correctamente. Sin embargo, pueden producirse falsos positivos cuando un usuario viaja con un nuevo dispositivo o usa una VPN que normalmente no utilizan otros usuarios de la organización. Otra fuente de falsos positivos son las aplicaciones que pasan incorrectamente direcciones IP del servidor como IP de cliente, lo que puede dar la impresión de que los inicios de sesión tienen lugar desde el centro de datos en el que está hospedado el back-end de esa aplicación (a menudo son centros de datos de Microsoft, por lo que parece que los inicios de sesión tienen lugar en direcciones IP propiedad de Microsoft). Como resultado de estos falsos positivos, el nivel de riesgo de estos eventos de riesgo es "**Medio**".

### <a name="sign-in-from-unfamiliar-locations"></a>Inicio de sesión desde ubicaciones desconocidas
Este tipo de evento de riesgo es un mecanismo de evaluación de inicio de sesión en tiempo real que tiene en cuenta las ubicaciones de inicio de sesión anteriores (IP, latitud/longitud y ASN) para determinar las ubicaciones nuevas o desconocidas. El sistema almacena información acerca de las ubicaciones anteriores utilizadas por un usuario y considera estas ubicaciones "conocidas". El evento de riesgo se desencadena cuando el inicio de sesión se produce desde una ubicación que no está en la lista de ubicaciones conocidas. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual no marca ninguna nueva ubicación como ubicación desconocida. El sistema también ignora los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación conocida. 

Las ubicaciones desconocidas pueden proporcionar una indicación clara de que un atacante está intentando utilizar una identidad robada. Se pueden producir falsos positivos cuando un usuario está viajando o probando un nuevo dispositivo, o bien utiliza una VPN nueva. Como resultado de estos falsos positivos, el nivel de riesgo para este tipo de evento es "**Medio**".



### <a name="sign-ins-from-infected-devices"></a>Inicios de sesión desde dispositivos infectados
Este tipo de evento de riesgo identifica los inicios de sesión desde dispositivos infectados con malware, que se sabe que se comunican activamente con un servidor bot. Esto se determina mediante la correlación de direcciones IP de dispositivos de usuarios con direcciones IP que estuvieron en contacto con un servidor bot. 

Este evento de riesgo identifica las direcciones IP, no los dispositivos de usuarios. Si hay varios dispositivos detrás de una única dirección IP, y solo algunos son controlados por una red bot, los inicios de sesión desde otros dispositivos pueden desencadenar este evento innecesariamente, por lo que se clasifica este evento de riesgo como "**Bajo**".  

Se recomienda ponerse en contacto con el usuario y examinar todos sus dispositivos. También es posible que el dispositivo personal de un usuario esté infectado o, como se ha mencionado antes, que otra persona estaba usando un dispositivo infectado desde la misma dirección IP que el usuario. A menudo, los dispositivos infectados son infectados por malware que todavía no ha sido identificado por el software antivirus, y también pueden indicar unos hábitos del usuario incorrectos que pueden haber hecho que el dispositivo se infecte.

Para obtener más información acerca de cómo tratar infecciones de malware, consulte el [Centro de protección contra malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inicios de sesión desde direcciones IP con actividad sospechosa
Este tipo de evento de riesgo identifica las direcciones IP desde las que se ha producido un gran número de intentos fallidos de inicio de sesión, en varias cuentas de usuario, durante un corto período de tiempo. Esto compara los patrones de tráfico de direcciones IP usadas por atacantes y es un claro indicador de que las cuentas están ya o van a estar en peligro. Se trata de un algoritmo de aprendizaje automático que omite "*falsos positivos*" obvios, como las direcciones IP que utilizan con regularidad otros usuarios de la organización.  El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicio de sesión de un nuevo usuario y un nuevo inquilino.

Se recomienda ponerse en contacto con el usuario para comprobar si realmente iniciaron sesión desde una dirección IP marcada como sospechosa. El nivel de riesgo de este tipo de evento es "**Medio**" porque varios dispositivos pueden encontrarse detrás de la misma dirección IP, mientras que solo algunos pueden ser responsables de la actividad sospechosa. 





## <a name="azure-ad-anomalous-activity-reports"></a>Informes de actividades anómalas de Azure AD

En el Portal de Azure clásico, algunos de los eventos de riesgo ya están disponibles en los informes de actividades anómalas de Azure AD. 

En la tabla siguiente se enumeran los distintos tipos de eventos de riesgo y el correspondiente informe de **actividades anómalas de Azure AD** . 

| Tipo de evento de riesgo de Identity Protection | Informe de actividades anómalas de Azure AD correspondiente |
|:--- |:--- |
| Credenciales con fugas |Usuarios con credenciales perdidas |
| Viaje imposible a ubicaciones inusuales |Actividad de inicio de sesión irregular |
| Inicios de sesión desde dispositivos infectados |Inicios de sesión desde dispositivos posiblemente infectados |
| Inicios de sesión desde direcciones IP anónimas |Inicios de sesión desde orígenes desconocidos |
| Inicios de sesión desde direcciones IP con actividad sospechosa |Inicios de sesión desde direcciones IP con actividad sospechosa |
| Inicios de sesión desde ubicaciones desconocidas |- |


Los siguientes informes de actividades anómalas de Azure AD no se incluyen como eventos de riesgo en Azure AD Identity Protection y, por tanto, no estarán disponibles mediante Identity Protection. Estos informes siguen estando disponibles en el Portal de Azure clásico. Sin embargo, dejarán de estar en uso en el futuro, ya que se están reemplazando por los eventos de riesgo de Identity Protection.

* Inicios de sesión tras varios errores
* Inicios de sesión desde varias ubicaciones geográficas






## <a name="next-steps"></a>Pasos siguientes
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


