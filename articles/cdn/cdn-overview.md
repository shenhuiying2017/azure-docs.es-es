<properties 
	pageTitle="Información general de la red CDN de Azure" 
	description="Obtenga información acerca de la Red de entrega de contenido (CDN) de Azure y de cómo usarla para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="mazha"/>

#Información general de la red de entrega de contenido (CDN) de Azure

La Red de entrega de contenido (CDN) de Azure almacena en caché blobs de Azure y contenido estático usado por servicios en la nube en ubicaciones colocadas estratégicamente para proporcionar el ancho de banda máximo para entregar contenido a los usuarios.

Si es un cliente de red CDN existente, ahora puede administrar sus extremos de red CDN a través del [Portal de administración de Microsoft Azure](https://manage.windowsazure.com).


>[AZURE.NOTE]CDN de Azure tiene un [plan de facturación](http://www.microsoft.com/windowsazure/pricing/) independiente de Almacenamiento de Azure o Servicios en la nube de Azure.
 

CDN ofrece a los desarrolladores una solución global para entregar contenido de alto ancho de banda almacenando en caché el contenido en nodos físicos en todo el mundo. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

- Mejor rendimiento y experiencia de usuario para aquellos usuarios finales que están lejos de un origen de contenido y utilicen aplicaciones donde son necesarias muchas "conexiones a Internet" para cargar el contenido.
- Gran distribución para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto. 


>[AZURE.IMPORTANT]Cuando crea o habilita un extremo de red CDN, su propagación por todo el mundo puede tardar hasta 60 minutos.
 
Cuando una solicitud para un objeto se realiza primero a la red CDN, el objeto se recupera directamente desde el servicio BLOB o desde el servicio en la nube. Cuando una solicitud se realiza usando la sintaxis de red CDN, dicha solicitud se redirecciona al extremo de red CDN más cercano a la ubicación desde la que la solicitud se realizó para proporcionar acceso al objeto. Si el objeto no se encuentra en ese extremo, se recupera del servicio y se almacena en caché en el extremo, donde se mantiene una configuración de tiempo de vida (TTL) para el objeto almacenado en caché.
 
##Almacenamiento en caché de contenido de almacenamiento de Azure

Una vez habilitada la red CDN en una cuenta de almacenamiento de Azure, cualquier blob que se encuentre en contenedores públicos y esté disponible para acceso anónimo se almacenará en caché a través de la red CDN . Solamente los blobs que estén públicamente disponibles se pueden almacenar en caché con CDN de Azure. Para que un blob esté públicamente disponible para acceso anónimo, debe denotar su contenedor como público. Una vez hecho eso, todos los blobs que se encuentren dentro de ese contenedor estarán disponibles para acceso de lectura anónimo. También tiene la opción de hacer públicos los datos de un contenedor o restringir el acceso solamente a los bloques que se encuentran dentro de él. Consulte [Restricción del acceso a contenedores y blobs](http://msdn.microsoft.com/library/azure/dd179354.aspx) para obtener información acerca de la administración del control de acceso para contenedores y blobs.

Para obtener el máximo rendimiento, utilice la memoria caché perimetral de red CDN para entregar blobs con un tamaño inferior a 10 GB.

Cuando habilite el acceso de red CDN para una cuenta de almacenamiento, el Portal de administración le proporcionará un nombre de dominio de red CDN con el siguiente formato: http://<identifier>.vo.msecnd.net/. Este nombre de dominio puede usarse para obtener acceso a los blobs de un contenedor público. Por ejemplo, en un contenedor público llamado Música de una cuenta de almacenamiento denominada MiCuenta, los usuarios pueden obtener acceso a los blobs de ese contenedor mediante el uso de cualquiera de las dos direcciones URL siguientes:

- **Dirección URL del servicio BLOB de Azure**: `http://myAccount.blob.core.windows.net/music/` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.vo.msecnd.net/music/` 

##Almacenamiento en caché de contenido de Sitios web Azure

Puede habilitar la red CDN desde sus sitios web para almacenar en caché su contenido web, como imágenes, scripts y hojas de estilo. Consulte[ Integración de un sitio web de Azure con la red CDN de Azure](../cdn-websites-with-cdn.md).

Cuando habilite el acceso de la red CDN para un sitio web, el Portal de administración le proporcionará un nombre de dominio de red CDN con el siguiente formato: http://<identifier>.vo.msecnd.net/. Este nombre de dominio se puede usar para recuperar objetos desde un sitio web. Por ejemplo, dado un contenedor público llamado cdn y un archivo de imagen llamado music.png, los usuarios pueden obtener acceso al objeto usando una de las dos direcciones URL siguientes:

- **Dirección URL del sitio web de azure**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.vo.msecnd.net/cdn/music.png`
 
##Almacenamiento en caché de contenido de servicios en la nube de Azure

Puede almacenar en caché objetos en la red CDN proporcionados por un servicio en la nube de Azure.

El almacenamiento en caché para servicios en la nube tiene las siguientes restricciones:


- La red CDN solamente se debe usar para almacenar en caché contenido estático.

	>[AZURE.WARNING]
- En el servicio la nube se debe implementar en una implementación de producción.
- El servicio en la nube debe proporcionar el objeto en el puerto 80 mediante HTTP.
- El servicio en la nube debe colocar el contenido que se va a almacenar en caché o que se va a proporcionar en la carpeta /cdn en dicho servicio.

Cuando habilite el acceso de la red CDN para un servicio en la nube, el Portal de administración le proporcionará un nombre de dominio de red CDN con el siguiente formato: http://<identifier>.vo.msecnd.net/. Este nombre de dominio se puede usar para recuperar objetos desde un servicio en la nube. Por ejemplo, dado un servicio en la nube llamado myHostedService y una página web ASP.NET llamada music.aspx que proporciona contenido, los usuarios pueden obtener acceso al objeto usando una de las dos direcciones URL siguientes:


- **Dirección URL del servicio en la nube de Azure**: `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **Dirección URL de red CDN de Azure**: `http://<identifier>.vo.msecnd.net/music.aspx` 


###Almacenamiento en caché de contenido específico con cadenas de consulta

Puede utilizar cadenas de consulta para diferenciar objetos recuperados desde un servicio en la nube. Por ejemplo, si el servicio en la nube muestra un gráfico que puede variar, puede pasar una cadena de consulta para recuperar el gráfico específico requerido. Por ejemplo:

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

Las cadenas de consulta se pasan como literales de cárdenas. Si tiene un servicio que toma dos parámetros, como `?area=2&item=1` y realiza una llamada posterior al servicio mediante `?item=1&area=2`, almacenará en caché dos copias del mismo objeto.
 

##Obtener acceso a contenido almacenado en caché a través de HTTPS


Azure le permite recuperar contenido desde la red CDN usando llamadas HTTPS. Esto le permite incorporar contenido almacenado en caché en la red CDN en páginas web seguras sin recibir advertencias acerca de los tipos de contenido de seguridad mixtos.

El acceso al contenido de la red CDN usando HTTPS tiene la siguiente restricciones:


- Debe utilizar el certificado proporcionado por la red CDN. No se admiten certificados de terceros.
- Debe usar el dominio de red CDN para obtener acceso el contenido. La compatibilidad con HTTPS no está disponible para nombres de dominio personalizados (CNAME) dado que la red CDN no admite certificados personalizados en este momento.



Incluso cuando HTTPS se habilite, el contenido de la red CDN se puede recuperar usando tanto HTTP como HTTPS.

Para obtener más información acerca de la habilitación de HTTPS para contenido de red CDN, consulte [Habilitar la Red de entrega de contenido (CDN) para Azure](http://msdn.microsoft.com/library/azure/gg680301.aspx).


##Obtención de acceso a contenido almacenado en caché de con dominios personalizados

Puede asignar el extremo HTTP de la red CDN a un nombre de dominio personalizado y usar ese nombre para solicitar objetos desde dicha red.

Para obtener más información acerca de la asignación de un dominio personalizado, consulte [Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](http://msdn.microsoft.com/library/azure/gg680307.aspx).

 

<!---HONumber=July15_HO3-->