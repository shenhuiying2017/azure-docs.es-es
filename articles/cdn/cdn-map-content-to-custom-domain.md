<properties 
	 pageTitle="Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado" 
	 description="Este tema muestra cómo asignar contenido de la red CDN a un dominio personalizado" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>

#Cómo asignar un dominio personalizado al extremo de la Red de entrega de contenido (CDN)

Puede asignar un dominio personalizado a un extremo de red CDN para utilizar su propio nombre de dominio en direcciones URL a contenido almacenado en caché, en lugar de utilizar el extremo de red CDN que se le ha proporcionado al usuario. Para ello, creará un registro CNAME con su registrador de dominio y asignar su dominio y subdominio personalizados al punto de conexión de red CDN. Un registro CNAME es una característica DNS que asigna un dominio de origen a un dominio de destino. En este caso, el dominio de origen es su dominio y subdominio personalizados (el subdominio es siempre necesario). El dominio de destino es el extremo de red CDN.

> [AZURE.NOTE]- Debe crear un registro CNAME con su registrador de dominio y asignar su dominio al punto de conexión de red CDN. Los registros CNAME asignan subdominios específicos como www.mydomain.com o myblog.mydomain.com. No es posible asignar un registro CNAME a un dominio raíz, como mydomain.com. -Un subdominio solamente se puede asociar a un punto de conexión de red CDN. El registro CNAME que crea enrutará todo el tráfico dirigido al subdominio al extremo especificado. Por ejemplo, si asocia www.mydomain.com con su punto de conexión de red CDN, entonces no puede asociarlo con otros puntos de conexión de Azure, como un punto de conexión de cuenta de almacenamiento o un punto de conexión de servicio en la nube. Sin embargo, puede utilizar sus dominios diferentes desde el mismo dominio para distintos extremos de servicio. También puede asignar diferentes subdominios al mismo extremo de red CDN.

Los procedimientos de este tema le mostrarán cómo:

-	[Registrar un dominio personalizado para un extremo de red CDN de Azure](#subheading1)
-	[Comprobar que el subdominio personalizado hace referencia a su extremo de red CDN](#subheading3) 

##<a name="subheading1"></a>Registrar un dominio personalizado para un extremo de red CDN de Azure

1.	Inicie sesión en el [Portal de Azure](http://portal.azure.com/).
2.	Haga clic en **Examinar**, en **Perfiles de CDN** y, luego, en el perfil de red CDN con el punto de conexión que quieras asignar a un dominio personalizado.  
3.	En la hoja **Perfil de CDN**, haga clic en el punto de conexión de CDN con el que quiera asociar el subdominio.
4.	En la parte superior de la hoja del punto de conexión, haga clic en el botón **Agregar dominio personalizado**. En la hoja **Agregar un dominio personalizado**, verá el nombre de host del punto de conexión, derivado del punto de conexión de red CDN, para usar en la creación de un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como **&lt;EndpointName>.azureedge.net**. Puede copiar este nombre de host para usar en la creación del registro CNAME.  

5.	Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
6.	Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras CNAME, Alias o Subdomains.
7.	Cree un nuevo registro CNAME que asigne su subdominio elegido (por ejemplo, **www** o **cdn**) al nombre de host proporcionado en la hoja **Agregar un dominio personalizado**.
8.	Vuelva a la hoja **Agregar un dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro personalizado. Por ejemplo, escriba el nombre de dominio en el formato www.mydomain.com o cdn.mydomain.com.   

	Azure comprobará que el registro CNAME existe para el nombre de dominio que ha escrito. Si el CNAME es correcto, el dominio personalizado se valida y pasa a estar preparado para utilizarse con su contenido de red CDN.

	Tenga en cuenta que en algunos casos la propagación del registro CNAME puede tardar cierto tiempo en propagarse a los servidores de nombres. Si el dominio no se valida inmediatamente y cree que el registro CNAME es correcto, espere unos minutos e inténtelo de nuevo.

##<a name="subheading3"></a>Comprobar que el subdominio personalizado hace referencia a su extremo de red CDN

-	Después de haber completado el registro del dominio personalizado, puede tener acceso al contenido almacenado en caché en su extremo de red CDN mediante el dominio personalizado. En primer lugar, asegúrese de que tiene contenido público almacenado en caché en el extremo. Por ejemplo, si su extremo de red CDN está asociado a una cuenta de almacenamiento, la red CDN almacena en caché el contenido en contenedores de blob públicos. Para probar el dominio personalizado, asegúrese de que su contenedor está establecido para permitir acceso público y que contiene al menos un blob.
-	En el explorador, navegue a la dirección del blob usando el dominio personalizado. Por ejemplo, si el nombre de dominio es **www.mydomain.com**, la dirección de URL a un blob almacenado en caché será similar a la siguiente dirección URL:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Si el extremo de red CDN está asociado a un servicio en la nube, la dirección del contenido almacenado en caché será similar a la siguiente dirección URL:

		http://www.mydomain.com/mycloudservice

##Otras referencias


[Habilitar la Red de entrega de contenido (CDN) para Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->