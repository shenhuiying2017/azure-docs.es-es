<properties
   pageTitle="Uso de Caché en Redis de Azure con Node.js"
   description="Introducción a Caché en Redis de Azure usando Node.js y node_redis."
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Uso de Caché en Redis de Azure con Node.js

Caché en Redis de Azure le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure usando Node.js. Para obtener otro ejemplo de uso de Caché en Redis de Azure con Node.js, consulte [Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure][].


## Requisitos previos

Instale [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node_redis](https://github.com/mranney/node_redis), pero puede usar cualquier cliente de Node.js enumerado en [http://redis.io/clients](http://redis.io/clients).

## Crear una caché de Redis en Azure

En la [Vista previa del Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkId=398536), haga clic en **Nuevo**, **Datos y almacenamiento** y seleccione **Caché en Redis**.

  ![][1]

Escriba un nombre de host DNS. Tendrá el formato `<name>.redis.cache.windows.net`. Haga clic en **Crear**.

  ![][2]


Una vez creada la memoria caché, haga clic en ella en el portal para ver su configuración. Haga clic en el vínculo bajo **Claves** y copie la clave principal. Necesitará esto para autenticar solicitudes.

  ![][4]


## Habilitar el extremo no SSL


Haga clic en el vínculo bajo **Puertos** y, a continuación, haga clic en **No** para "Permitir acceso solo a través de SSL". Esto habilitará el puerto no SSL para la memoria caché. El cliente node_redis actualmente no admite SSL.

  ![][3]


## Agregar algo a la memoria caché y recuperarlo

	var redis = require("redis");

    // Put in your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Salida:

	OK
	bar


## Pasos siguientes

- [Habilite los diagnósticos de caché](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché.
- Lea la [documentación de Redis](http://redis.io/documentation) oficial.


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=July15_HO1-->