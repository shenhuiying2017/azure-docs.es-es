<properties
	pageTitle="Tipos de eventos de riesgo que detecta Azure Active Directory Identity Protection | Microsoft Azure"
	description="En este tema se ofrece información detallada sobre los tipos de eventos de riesgo disponibles en Azure Active Directory Identity Protection."
	services="active-directory"
	keywords="azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="markvi"/>

#Tipos de eventos de riesgo que detecta Azure Active Directory Identity Protection 

En Azure Active Directory Identity Protection, los eventos de riesgo son aquellos que:

- se han marcado como sospechosos,

- indican que es posible que se haya puesto en peligro una identidad.

En este tema se ofrece información detallada sobre los tipos de eventos de riesgo disponibles.


## Credenciales con fugas

Los investigadores de seguridad de Microsoft han encontrado credenciales con fugas publicadas en la Web oscura. Estas credenciales se encuentran normalmente en texto sin formato. Se comprueban con credenciales de Azure AD y, si hay una coincidencia, se notifican como "Credenciales con fugas" en Identity Protection.

Los eventos de riesgo de credenciales con fugas se clasifican con gravedad "Alta", ya que proporcionan una indicación clara de que el nombre de usuario y la contraseña están a disposición de un atacante.

## Viaje imposible a ubicaciones inusuales

Este tipo de evento de riesgo identifica dos inicios de sesión procedentes de ubicaciones geográficamente distantes, donde al menos una de las ubicaciones puede también ser inusual para el usuario, según su comportamiento anterior. Además, el tiempo entre los dos inicios de sesión es menor que el tiempo que habría necesitado el usuario para viajar de la primera ubicación a la segunda, lo que indica que otro usuario utiliza las mismas credenciales.

Este algoritmo de aprendizaje automático omite *falsos positivos* obvios que contribuyen a la condición de viaje imposible, como las VPN y las ubicaciones que usan con regularidad otros usuarios de la organización. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicios de sesión del nuevo usuario.

Un viaje imposible suele ser un buen indicador de que un hacker logró iniciar sesión correctamente. Sin embargo, pueden producirse falsos positivos cuando un usuario viaja con un nuevo dispositivo o usa una VPN que normalmente no utilizan otros usuarios de la organización. Otra fuente de falsos positivos son las aplicaciones que pasan incorrectamente direcciones IP del servidor como IP de cliente, lo que puede dar la impresión de que los inicios de sesión tienen lugar desde el centro de datos en el que está hospedado el back-end de esa aplicación (a menudo son centros de datos de Microsoft, por lo que parece que los inicios de sesión tienen lugar en direcciones IP propiedad de Microsoft). Como resultado de estos falsos positivos, el nivel de riesgo de estos eventos de riesgo es "**Medio**".

##Inicios de sesión desde dispositivos infectados

Este tipo de evento de riesgo identifica los inicios de sesión desde dispositivos infectados con malware, que se sabe que se comunican activamente con un servidor bot. Esto se determina mediante la correlación de direcciones IP de dispositivos de usuarios con direcciones IP que estuvieron en contacto con un servidor bot.

Este evento de riesgo identifica las direcciones IP, no los dispositivos de usuarios. Si hay varios dispositivos detrás de una única dirección IP, y solo algunos son controlados por una red bot, los inicios de sesión desde otros dispositivos pueden desencadenar este evento innecesariamente, por lo que se clasifica este evento de riesgo como "**Bajo**".

Se recomienda ponerse en contacto con el usuario y examinar todos sus dispositivos. También es posible que el dispositivo personal de un usuario esté infectado o, como se ha mencionado antes, que otra persona estaba usando un dispositivo infectado desde la misma dirección IP que el usuario. A menudo, los dispositivos infectados son infectados por malware que todavía no ha sido identificado por el software antivirus, y también pueden indicar unos hábitos del usuario incorrectos que pueden haber hecho que el dispositivo se infecte.

Para obtener más información acerca de cómo tratar infecciones de malware, consulte el [Centro de protección contra malware](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


## Inicios de sesión desde direcciones IP anónimas

Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónima. A menudo, estos servidores proxy los usan los usuarios que desean ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados.

Se recomienda ponerse en contacto inmediatamente con el usuario para comprobar si utilizaba direcciones IP anónimas. El nivel de riesgo de este tipo de evento es ""**Medio**" porque, en sí misma, una IP anónima no es una indicación clara de una cuenta en riesgo.

## Inicios de sesión desde direcciones IP con actividad sospechosa

Este tipo de evento de riesgo identifica las direcciones IP desde las que se ha producido un gran número de intentos fallidos de inicio de sesión, en varias cuentas de usuario, durante un corto período de tiempo. Esto compara los patrones de tráfico de direcciones IP usadas por atacantes y es un claro indicador de que las cuentas están ya o van a estar en peligro. Se trata de un algoritmo de aprendizaje automático que omite "*falsos positivos*" obvios, como las direcciones IP que utilizan con regularidad otros usuarios de la organización. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual aprende el comportamiento de inicio de sesión de un nuevo usuario y un nuevo inquilino.

Se recomienda ponerse en contacto con el usuario para comprobar si realmente iniciaron sesión desde una dirección IP marcada como sospechosa. El nivel de riesgo de este tipo de evento es "**Medio**" porque varios dispositivos pueden encontrarse detrás de la misma dirección IP, mientras que solo algunos pueden ser responsables de la actividad sospechosa.


## Inicio de sesión desde ubicaciones desconocidas

Este tipo de evento de riesgo es un mecanismo de evaluación de inicio de sesión en tiempo real que tiene en cuenta las ubicaciones de inicio de sesión anteriores (IP, latitud/longitud y ASN) para determinar las ubicaciones nuevas o desconocidas. El sistema almacena información acerca de las ubicaciones anteriores utilizadas por un usuario y considera estas ubicaciones "conocidas". El evento de riesgo se desencadena cuando el inicio de sesión se produce desde una ubicación que no está en la lista de ubicaciones conocidas. El sistema tiene un período de aprendizaje inicial de 14 días, durante el cual no marca ninguna nueva ubicación como ubicación desconocida. El sistema también ignora los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación conocida. <br> Las ubicaciones desconocidas pueden proporcionar una indicación clara de que un atacante está intentando utilizar una identidad robada. Se pueden producir falsos positivos cuando un usuario está viajando o probando un nuevo dispositivo, o bien utiliza una VPN nueva. Como resultado de estos falsos positivos, el nivel de riesgo para este tipo de evento es "**Medio**".





## Consulte también

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0803_2016-->