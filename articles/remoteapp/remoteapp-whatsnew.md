
<properties 
    pageTitle="Novedades en RemoteApp de Azure"
    description="Obtenga información sobre los cambios y las mejoras realizados en RemoteApp de Azure" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# Novedades de RemoteApp

Una de las ventajas de RemoteApp es que siempre trabajamos para mejorarlo. Cada vez que lo hagamos, anunciaremos aquí esos cambios.


## Junio de 2015

¡Hay muchos cambios! El equipo ha estado muy ocupado en junio:

- Se ha rediseñado la [página de aterrizaje](https://www.remoteapp.windowsazure.com/) de RemoteApp de Azure, compruébelo. 
- Se ha actualizado el software en todas las imágenes disponibles como parte de la suscripción.
- Se han realizado mejoras en las colecciones híbridas, incluida la compatibilidad con la tunelización forzada y la comprobación del tamaño de la subred IP antes de intentar crear la colección.
- Se ha detectado que el carácter comodín * no funciona con las cámaras web. Es necesario especificar el identificador de instancia o el GUID. Actualizaremos la información de redirección para reflejar este hecho.
- Esto permite agregar software antivirus personalizado a la imagen al crear una imagen de plantilla desde la galería de Azure.

En julio habrá nuevos cambios, por lo que pronto publicaremos otra actualización.

## Mayo de 2015

Han tenido lugar varias adiciones y han pasado varios meses desde que creamos este tema, por lo que esta lista en realidad corresponde al período entre principios de marzo y mayo. Revise las nuevas características:

- Automatización completa: ahora RemoteApp de Azure incluye [cmdlets en el módulo de Azure PowerShell](remoteapp-tutorial-arawithpowershell.md). 
- [Creación de una imagen de RemoteApp de Azure basada en una máquina virtual de Azure](remoteapp-image-on-azurevm.md). Consigue que la carga de la imagen personalizada en Azure se realice mucho más rápido.
- Uso de una red virtual de Azure en lugar de una red virtual de RemoteApp para conectar los recursos de la red corporativa a Azure. Hemos actualizado las [instrucciones de la colección híbrida](remoteapp-create-hybrid-deployment.md) para guiarle en el proceso de creación de una red virtual de Azure (el paso 1).
- Y hablando de redes virtuales, consulte [la nueva guía](remoteapp-vnetsizing.md) sobre los límites de tamaño y las limitaciones de la red virtual.
- Y ya que hablamos de límites, ¿cuáles son los [límites de servicio y los valores predeterminados](remoteapp-servicelimits.md)?

¿Desea obtener más información acerca de RemoteApp de Azure? El equipo de RemoteApp estuvo en Ignite hace unas semanas. Vea el vídeo de Eric, [The Fundamentals of Microsoft Azure RemoteApp Management and Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868) (Aspectos básicos de la gestión y administración de RemoteApp de Microsoft Azure).

¿Necesita ver RemoteApp de Azure en el mundo real? Vea el tutorial [Ejecución de cualquier aplicación en cualquier dispositivo con RemoteApp](remoteapp-anyapp.md): en él se muestra cómo compartir el acceso con los usuarios, incluido el uso compartido de los archivos de base de datos. También disponemos de un tutorial para [conseguir que Office 365](remoteapp-tutorial-o365anywhere.md) se ejecute del mismo modo en cualquier dispositivo.

Gracias por seguir con nosotros, volveremos el próximo mes con más novedades.

<!---HONumber=July15_HO1-->