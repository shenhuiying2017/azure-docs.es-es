<properties
   pageTitle="Información general de caché local del Servicio de aplicaciones de Azure"
   description="En este artículo se describe cómo habilitar, cambiar el tamaño y consultar el estado de la característica de caché local del Servicio de aplicaciones de Azure"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="Solo para uso de campeones de optimización del motor de búsqueda (SEO). Separe los términos con comas. Consulte a su campeón de optimización del motor de búsqueda (SEO) antes de cambiar el contenido de este artículo que contiene estos términos."/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Información general de caché local del Servicio de aplicaciones de Azure

El contenido de las aplicaciones web de Azure se almacena en Almacenamiento de Azure y surge de manera duradera como un recurso compartido de contenido. Este diseño está pensado para trabajar con una amplia gama de aplicaciones y tiene los siguientes atributos:

* El contenido se comparte entre varias instancias de VM de la aplicación web. 
* El contenido es duradero y se puede modificar mediante la ejecución de aplicaciones web. 
* Los archivos de registro y los archivos de datos de diagnóstico están disponibles bajo la misma carpeta de contenido compartida. 
* La publicación de nuevo contenido directamente actualiza la carpeta de contenido y se puede ver lo mismo a través del sitio web de SCM y la aplic. web en ejecución inmediatamente (normalmente, algunas tecnologías como ASP.NET, llevan a cabo un reinicio de la aplicación web en algunos cambios de archivo para hacerse con el contenido más reciente). 

Si bien muchas aplicaciones web utilizan una o todas estas características, algunas aplicaciones web solo quieren un almacén de contenido de solo lectura de alto rendimiento desde el que se puedan ejecutar con alta disponibilidad. Estas aplicaciones pueden beneficiarse de una copia específica de instancia de VM del contenido que denominaremos "caché local". La memoria _caché local_ proporciona una vista de rol web de su contenido y este contenido es una caché temporal del contenido de almacenamiento que se crea de forma asincrónica durante el inicio del sitio. Cuando la memoria caché está preparada, el sitio cambia para ejecutarse con el contenido almacenado en caché. Las aplicaciones web que se ejecutan en la memoria caché local disfrutan de las siguientes ventajas:

* Son inmunes a latencias experimentadas al acceder a contenido de Almacenamiento de Azure 
* Son inmunes a los servidores que proporcionan el recurso compartido de contenido que sufren actualizaciones programas o tiempos de inactividad no planeados y cualquier otra interrupción con Almacenamiento de Azure. 
* Menos reinicios de aplicación debido a cambios en el recurso compartido de almacenamiento. 

## ¿Cómo cambia la memoria caché local el comportamiento del Servicio de aplicaciones?

* La memoria caché local es una copia de las carpetas /site y /siteextensions de la aplicación web y se crea en la instancia de VM local al iniciar dicha aplicación. El tamaño de la memoria caché local por aplicación web se limita a 300 MB de forma predeterminada, pero se puede aumentar hasta 1 GB. 
* La memoria caché local es de lectura y escritura pero se descartará cualquier modificación cuando la aplicación web mueve máquinas virtuales o se reinicia. La memoria caché local no debe usarse para aplicaciones que conservan datos críticos en el almacén de contenido. 
* Las aplicaciones web pueden continuar escribiendo archivos de registro y datos de diagnóstico como lo hacen actualmente. Sin embargo, los archivos de registro y datos se almacenan localmente en VM y después se copian periódicamente en el almacén de contenido compartido. La copia a través del almacén de contenido compartido se realiza lo mejor posible y las reescrituras podrían perderse debido a un bloqueo repentino de una instancia de VM. 
* Hay un cambio en la estructura de carpetas de las carpetas LogFiles y Data para las aplicaciones web que usan la memoria caché local. Ahora hay subcarpetas en las carpetas "LogFiles" y "Data" de almacenamiento que siguen el patrón de nomenclatura de "identificador único" + marca de tiempo. Cada una de las subcarpetas corresponde a una instancia de VM en donde la aplicación web se está ejecutando o se ha ejecutado.  
* La publicación de cambios en la aplicación web a través de cualquiera de los mecanismos de publicación se realizará en el almacén de contenido compartido. Esto es así por diseño, ya que queremos que el contenido publicado sea duradero. Para actualizar la memoria caché local de la aplicación web, debe reiniciarse. ¿Parece un paso excesivo? Consulte la siguiente información para realizar el ciclo de vida sin problemas.
* D:\\Home apuntará a la memoria caché local. D:\\Local continuará apuntando al almacenamiento específico de VM temporal. 
* La vista de contenido predeterminado del sitio de SCM seguirá siendo la del almacén de contenido compartido. Para ver el aspecto de la carpeta de caché local puede navegar a https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache.

## Cómo habilitar la memoria caché local en el Servicio de aplicaciones de Azure

La memoria caché local se configura con una combinación de configuraciones de aplicación reservadas. Estas configuraciones de aplicación se pueden definir mediante los métodos siguientes:

* [Portal de Azure](#Configure-Local-Cache-Portal)
* [Administrador de recursos de Azure](#Configure-Local-Cache-ARM)

### Configuración de la memoria caché local mediante el Portal de Azure
<a name="Configure-Local-Cache-Portal"></a>

La memoria caché local se habilitada por aplicación web utilizando una AppSetting. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![Configuración de aplicación del Portal de Azure: Caché Local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Configuración de la memoria caché local mediante Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300" 
    }
}

...
```

## ¿Cómo cambiar el tamaño de la memoria caché local del Servicio de aplicaciones?

El tamaño predeterminado de la memoria caché local es **300 MB**. Esto incluye las carpetas Site y SiteExtensions que se copian desde el almacén de contenido así como en cualquier registro y carpeta de datos creados localmente. Para aumentar este límite utilice la AppSetting `WEBSITE_LOCAL_CACHE_SIZEINMB`. El tamaño se puede aumentar hasta **1 GB** (1000 MB) por aplicación web.

## Prácticas recomendadas para utilizar la memoria caché local del Servicio de aplicaciones

Se recomienda que la memoria caché local se use conjuntamente con la característica [Entornos de ensayo](../app-service-web/web-sites-staged-publishing.md).

* Agregue una Appsetting _temporal_ `WEBSITE_LOCAL_CACHE_OPTION` con el valor `Always` a la **ranura de producción**. Si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, agréguela también como una configuración temporal a su ranura de producción. 
* Cree una ranura de ensayo y haga la publicación en su ranura de ensayo. La ranura de ensayo normalmente no utiliza memoria caché local para permitir un ciclo de vida de compilación, implementación y prueba sin problemas para ensayo pero obtiene las ventajas de la memoria caché local para la ranura de producción. 
*	Pruebe el sitio en la ranura de ensayo.  
*	Una vez que esté preparado, emita una [operación de intercambio](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre las ranuras de **ensayo** y **producción**.  
*	La configuración temporal es por nombre y se adhiere a una ranura. Por tanto, cuando la ranura de ensayo se intercambia por producción, hereda la configuración de aplicación de la memoria caché Local. La ranura de producción recién intercambiada se ejecutará en la memoria caché local después de unos minutos y se calentará como parte del calentamiento de la ranura después del intercambio. Por tanto, cuando se complete el intercambio de ranura, el espacio de producción del usuario se ejecutará en la memoria caché local.

## Preguntas frecuentes

### ¿Cómo puedo saber si en la memoria caché local se aplica a mi aplicación web? 

Si la aplicación web necesita un almacén de contenido confiable y de alto rendimiento y no usa el almacén de contenido para escribir datos críticos en tiempo de ejecución y tiene un tamaño total < 1 GB, ¡la respuesta es Sí! Para obtener el tamaño total de las carpetas del «sitio» y de las "extensiones del sitio" puede utilizar la extensión del sitio "Uso de disco de Aplicaciones web de Azure".
 
### ¿Cómo habilito la memoria caché local? 

Vea la sección anterior sobre las prácticas recomendadas cuando use caché local.
 
### ¿Cómo puedo saber si mi sitio ha cambiado para usar caché local? 

Si usa la característica de caché local con entornos de ensayo, la operación de intercambio no se completará hasta que la memoria caché local se haya calentado. Para comprobar si el sitio se ejecuta en caché local, puede comprobar la variable de entorno del proceso de trabajo `WEBSITE_LOCALCACHE_READY`. Use estas instrucciones para acceder a la variable de entorno del proceso de trabajo en varias instancias.
 
### Acabo de publicar nuevos cambios, pero mi aplicación web no parece tenerlos. ¿Por qué? 
Si la aplicación web usa caché local, debe reiniciar su sitio para obtener los cambios más recientes. ¿No desea eso en un sitio de producción? Consulte las opciones de ranura anteriores.
 
### ¿Dónde están mis registros? 

Con caché local, los registros y las carpetas de datos tienen un aspecto diferente. Sin embargo, la estructura de las subcarpetas permanece igual excepto en que están anidadas en una subcarpeta con el formato "identificador de VM único" + marca de tiempo.
 
### Tengo la memoria caché local habilitada pero mi aplicación web todavía se reinicia. ¿Por qué ocurre esto? Pensé que la memoria caché local que iba a ayudar con los reinicios de aplicación frecuentes. 

La memoria caché local ayuda a evitar que la aplicación web relacionada con el almacenamiento se reinicie. Sin embargo, la aplicación web todavía puede sufrir reinicios durante actualizaciones de infraestructura de la VM planeadas. Los reinicios totales de aplicación experimentados con la memoria caché local habilitada deben ser menos.

<!---HONumber=AcomDC_0309_2016-->