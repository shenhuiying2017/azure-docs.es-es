
<properties 
    pageTitle="Solución de problemas con las colecciones híbridas: creación"
    description="Obtenga información sobre cómo solucionar problemas con la creación de las colecciones híbridas de RemoteApp" 
    services="remoteapp" 
    solutions=""
    documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# Solución de problemas con las colecciones híbridas: creación

Antes de que pueda solucionar problemas que se producen durante la creación de colecciones híbridas, es importante comprender cómo se crean las colecciones híbridas. Una colección híbrida requiere que las instancias de RemoteApp estén unidas a un dominio; esto se hace durante la creación de la colección.  Cuando se inicia el proceso de creación de la colección, en la red virtual se crean copias de las imágenes de plantilla que cargó y se unen a un dominio a través del túnel VPN de sitio a sitio al dominio resuelto por el registro de IP de DNS especificado durante la creación de la red virtual.

Errores comunes vistos en el Portal de administración de Azure:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

Si ve alguno de los errores mencionados, revise lo siguiente:

- Compruebe que las configuraciones de IP de DNS son válidas
- Asegúrese de que los registros de IP de DNS son registros de IP pública o forman parte del "espacio de direcciones locales" que especificó durante la creación de la red virtual.
- Compruebe que el túnel de la red virtual esté activo o conectado 
- Asegúrese de que el lado local de la conexión VPN no esté bloqueando el tráfico de la red. Para comprobarlo, observe los registros del software o del dispositivo de VPN local.
- Asegúrese de que el dominio que especificó durante la creación de la colección esté activo y en ejecución.

	ProvisioningTimeout


Si ve este error, revise lo siguiente:

- Asegúrese de que el lado local de la conexión VPN no esté bloqueando el tráfico de la red. Para comprobarlo, observe los registros del software o del dispositivo de VPN local.
- Asegúrese de que la imagen de plantilla de RemoteApp que cargó esté preparada correctamente con sysprep. Puede revisar aquí los requisitos de imagen de RemoteApp.: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- Intente crear una máquina virtual con la imagen de plantilla que cargó y asegúrese de que arranca y se ejecuta de manera correcta (a) en un servidor Hyper-V local o (b) al crear una máquina virtual de IAAS de Azure en la suscripción de Azure. Si no es posible crear la máquina virtual o si esta no se inicia, normalmente se debe a que la imagen de plantilla no se preparó correctamente y será necesario corregirla.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

Si ve alguno de los errores mencionados, revise lo siguiente:

- Compruebe que las credenciales escritas para la unión a un dominio son válidas.
- Compruebe que las credenciales de unión a un dominio son correctas o que se cuenta con los permisos de unión a un dominio adecuados.
- Compruebe que la unidad organizativa tenga el formato correcto y que exista en Active Directory.


<!--HONumber=52--> 