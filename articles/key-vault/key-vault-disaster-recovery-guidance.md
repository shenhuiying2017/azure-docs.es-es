<properties
	pageTitle="Qué hacer si se produce una interrupción del servicio de Azure que afecta al Almacén de claves de Azure | Microsoft Azure"
	description="Obtenga información acerca de qué hacer si se produce una interrupción del servicio de Azure que afecta al Almacén de claves de Azure."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="danpl"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="sumedhb;aglick"/>
	

# Redundancia y disponibilidad del Almacén de claves de Azure

El Almacén de claves de Azure tiene varias capas integradas de redundancia con el fin de garantizar la disponibilidad de las claves y los secretos para su aplicación, incluso cuando se produzcan errores de componentes individuales del servicio.

El contenido del almacén de claves se replica dentro de la región, así como en una región secundaria que se encuentra a una distancia mínima de 241 km dentro de la misma ubicación geográfica. Esto garantiza una durabilidad muy elevada de las claves y los secretos.

Si se produce un error en algún componente individual dentro del servicio del almacén de claves, los componentes alternativos de la región se encargan de atender la solicitud. En este caso, no hay ninguna degradación de la funcionalidad. No es necesario efectuar ninguna acción, ya que este proceso se realizará automáticamente y sin que se dé cuenta.

En el caso excepcional de que toda una región de Azure pase a estar no disponible, las solicitudes efectuadas al almacén de claves de esa región se enrutarán automáticamente (un proceso conocido como "conmutación por error") a una región secundaria. Cuando la región primaria vuelva a estar disponible, las solicitudes se enrutarán a ella nuevamente ("conmutación por recuperación"). Conviene insistir en que no es necesario que efectúe ninguna acción, ya que este proceso se realizará automáticamente y sin que se dé cuenta. Hay algunas advertencias que deben tenerse en cuenta:

  * La conmutación por error de región, en caso de producirse, puede tardar varios minutos en completarse. Las solicitudes realizadas durante este intervalo podrían no efectuarse correctamente mientras no se complete la conmutación .

  * Una vez finalizada una conmutación por error, el almacén de claves se encontrará en modo de ___solo lectura___. Las solicitudes compatibles con este modo son las siguientes:
    * Enumeración de almacenes de claves
    * Obtención de propiedades de los almacenes de claves
    * Enumeración de secretos 
    * Obtención de secretos
    * Enumeración de claves
    * Obtención de (propiedades de) claves
    * encrypt
    * Descifrado
    * Encapsulado
    * Desencapsulado
    * Comprobación
    * Firma
    * backup

  * Cuando tenga lugar la conmutación por recuperación, todos los tipos de solicitud (es decir, de lectura ___y___ escritura) pasarán a estar disponibles.

<!---HONumber=AcomDC_0525_2016-->