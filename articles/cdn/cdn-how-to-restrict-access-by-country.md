<properties 
	pageTitle="Cómo restringir el acceso a contenidos por país" 
	description="Cuando un usuario solicita su contenido, de forma predeterminada, el contenido se proporciona sin tener en cuenta el lugar desde el que el usuario realizó esta solicitud. En algunos casos, puede que desee restringir el acceso al contenido por país. En este tema se explica cómo configurar el servicio para permitir o bloquear el acceso por país." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="mazha"/>

#Cómo restringir el acceso a contenidos por país
 
 
Cuando un usuario solicita su contenido, de forma predeterminada, el contenido se proporciona sin tener en cuenta el lugar desde el que el usuario realizó esta solicitud. En algunos casos, puede que desee restringir el acceso al contenido por país. En este tema se explica cómo configurar el servicio para permitir o bloquear el acceso por país.

>[AZURE.NOTE]Una vez efectuada la configuración, esta se aplicará a todos los extremos de CDN de su suscripción.

##Paso 1: Definir la ruta de acceso al directorio 

Al configurar un filtro de país, debe especificar la ruta de acceso relativa a la ubicación a la que se les permitirá o denegará el acceso a los usuarios. Puede aplicar el filtrado por países para todos los archivos mediante "/" o carpetas seleccionadas especificando las rutas de acceso de directorio.

Filtro de ruta de acceso del directorio de ejemplo:
		
	/                                 
	/Photos/
	/Photos/Strasbourg

##Paso 2: Definir la acción: bloquear o permitir

**Bloquear**: as los usuarios de los países especificados se les denegará el acceso a los activos solicitados desde esa ruta recursiva. Si no se han configurado otras opciones de filtrado de país para esa ubicación, a continuación, se permitirá acceso a todos los demás usuarios.

**Permitir**: solo los usuarios de los países especificados podrán tener acceso a los recursos solicitados desde esa ruta recursiva.
	 
##Paso 3: Definir los países 

Seleccione los países que desee bloquear o permitir para la ruta de acceso.

Ejemplo:

La regla de bloqueo **/Fotos/Estrasburgo/** filtrará los archivos, incluidos:

	http://az123456.vo.msecnd.net/Photos/Strasbourg/1000.jpg. 
	http://az123456.vo.msecnd.net/Photos/Strasbourg/Cathedral/1000.jpg. 

##Códigos de país

La función **Filtrado por país** usa códigos de país para definir los países desde los que se permitirá o bloqueará una solicitud para un directorio protegido. Actualmente, son compatibles los dos códigos de país siguientes: "UE" (Europa) y "PA" (Asia-Pacífico). Sin embargo, estos códigos de país no se pueden usar para permitir o bloquear todas las solicitudes de esas regiones. En su lugar, estos códigos de país se aplican a las solicitudes que se originan en las direcciones IP que están repartidas en una región en lugar de un país. Para bloquear o permitir el acceso en países de la UE y de AP, seleccione los países y la región correspondiente de la UE y de AP.


##Consideraciones
 
- Es posible que los cambios en la configuración del filtro de país tarden hasta una hora en surtir efecto.
- Esta función no admite caracteres comodín (por ejemplo, *). - La configuración de filtrado de país asociada a la ruta de acceso relativa se aplicará de forma recursiva a esa ruta.
- Solo se puede aplicar una regla a la misma ruta de acceso relativa (no se pueden crear varios filtros de país que señalen a la misma ruta de acceso relativa). Sin embargo, una carpeta puede tener varios filtros de país. Esto se debe a la naturaleza recursiva de los filtros de país. En otras palabras, se puede asignar un filtro de país diferente a una subcarpeta de una carpeta configurada previamente.

<!---HONumber=July15_HO3-->