<properties 
	 pageTitle="Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado" 
	 description="Este tema muestra cómo asignar contenido de la red CDN a un dominio personalizado" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="07/07/2015" 
	 ms.author="mazha"/>

#Cómo asignar un dominio personalizado al extremo de la Red de entrega de contenido (CDN)

Puede asignar un dominio personalizado a un extremo de red CDN para utilizar su propio nombre de dominio en direcciones URL a contenido almacenado en caché, en lugar de utilizar el extremo de red CDN que se le ha proporcionado al usuario.

Hay dos formas de asignar su dominio personalizado a un extremo de red CDN.

1. **Crear un registro CNAME con su registrador de dominio y asignar su dominio y subdominio personalizados al extremo de red CDN** 
	
	Un registro CNAME es una característica DNS que asigna un dominio de origen a un dominio de destino. En este caso, el dominio de origen es su dominio y subdominio personalizados (el subdominio es siempre necesario). El dominio de destino es el extremo de red CDN.

	El proceso de asignación del dominio personalizado al extremo de red CDN puede provocar un período breve de inactividad para el dominio mientras registra el dominio en el Portal de administración de Azure. 
2. **Agregar un paso de registro intermedio con cdnverify de Azure**

	Si el dominio personalizado ya es compatible con una aplicación con un contrato de nivel de servicio (SLA) que requiere que no exista tiempo de inactividad, puede usar el subdominio **cdnverify** de Azure para proporcionar un paso de registro intermedio para que los usuarios puedan obtener acceso al dominio mientras se realiza la asignación de DNS.

> AZURE.NOTE
> 
-	Debe crear un registro CNAME con su registrador de dominio y asignar su dominio al extremo de red CDN. Los registros CNAME asignan subdominios específicos como www.mydomain.com o myblog.mydomain.com. No es posible asignar un registro CNAME a un dominio raíz, como mydomain.com.
-	Un subdominio solamente se puede asociar con un extremo de red CDN. El registro CNAME que crea enrutará todo el tráfico dirigido al subdominio al extremo especificado. Por ejemplo, si asocia www.mydomain.com con su extremo de red CDN, entonces no puede asociarlo con otros extremos de Azure, como un extremo de cuenta de almacenamiento o un extremo de servicio en la nube. Sin embargo, puede utilizar sus dominios diferentes desde el mismo dominio para distintos extremos de servicio. También puede asignar diferentes subdominios al mismo extremo de red CDN.

Los procedimientos de este tema le mostrarán cómo:

-	[Registrar un dominio personalizado para un extremo de red CDN de Azure](#subheading1)
-	[Registrar un dominio personalizado para un extremo de red CDN de Azure usando el subdominio cdnverify intermediario](#subheading2)
-	[Comprobar que el subdominio personalizado hace referencia a su extremo de red CDN](#subheading3) 

##<a name="subheading1"></a>Registrar un dominio personalizado para un extremo de red CDN de Azure

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).
2.	En el panel de navegación, haga clic en **CDN**.
3.	En la vista de lista, haga clic en el nombre del extremo de red CDN al que desea asociar el subdominio para navegar a la página de detalles para ese extremo.
4.	En la cinta, haga clic en **Administrar dominio** para mostrar el cuadro de diálogo **Administrar dominio personalizado**. En el texto del cuadro de diálogo, verá el nombre de host, derivado del extremo de red CDN, para utilizar en la creación de un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como **az#####.vo.msecnd.net** (donde **az#####** es el identificador para el extremo de red CDN). Puede copiar este nombre de host para usar en la creación del registro CNAME. Para este procedimiento, ignore el texto que hace referencia al subdominio **cdnverify**.
5.	Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
6.	Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras CNAME, Alias o Subdomains.
7.	Cree un nuevo registro CNAME que asigne su subdominio elegido (por ejemplo, **www** o **cdn**) al nombre de host proporcionado en el cuadro de diálogo **Administrar dominio personalizado**.
8.	Vuelva al cuadro de diálogo **Administrar dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro personalizado. Por ejemplo, escriba el nombre de dominio en el formato www.mydomain.com o cdn.mydomain.com.   

	Azure comprobará que el registro CNAME existe para el nombre de dominio que ha escrito. Si el CNAME es correcto, el dominio personalizado se valida y pasa a estar preparado para utilizarse con su contenido de red CDN.

	Tenga en cuenta que en algunos casos la propagación del registro CNAME puede tardar cierto tiempo en propagarse a los servidores de nombres. Si el dominio no se valida inmediatamente y cree que el registro CNAME es correcto, espere unos minutos e inténtelo de nuevo.

##<a name="subheading2"></a>Registrar un dominio personalizado para un extremo de red CDN de Azure usando el subdominio cdnverify intermediario  


1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).
2.	En el panel de navegación, haga clic en **CDN**.
3.	En la vista de lista, haga clic en el nombre del extremo de red CDN al que desea asociar el subdominio para navegar a la página de detalles para ese extremo.
4.	En la cinta, haga clic en **Administrar dominio** para mostrar el cuadro de diálogo **Administrar dominio personalizado**. En el texto del cuadro de diálogo, verá el nombre de host, derivado del extremo de red CDN, para utilizar en la creación de un nuevo registro CNAME usando el subdominio intermediario **cdnverify**. El formato de la dirección del nombre de host aparecerá como **cdnverify.az#####.vo.msecnd.net**. Puede copiar este nombre de host para usar en la creación del registro CNAME.
5.	Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
6.	Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.
7.	Cree un nuevo registro CNAME y proporcione un alias de subdominio que incluya el subdominio **cdnverify**. Por ejemplo, el subdominio que especifique estará en formato **cdnverify.www** o **cdnverify.cdn**. A continuación, proporcione un nombre de host, que es el extremo de red CDN, en el formato **cdnverify.az#####.vo.msecnd.net** (donde **az#####** es el nombre del extremo de red CDN). El formato del nombre de host se proporciona en el cuadro de diálogo **Administrar dominio personalizado**.
8.	Vuelva al cuadro de diálogo **Administrar dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro personalizado. Por ejemplo, escriba el nombre de dominio en el formato **www.mydomain.com** o **cdn.mydomain.com**. Tenga en cuenta que en este paso, no es necesario que el subdominio vaya precedido de **cdnverify**.  

	Azure comprobará que el registro CNAME existe para el nombre de dominio cdnverify que ha escrito.
9.	En este momento, Azure ha comprobado el dominio personalizado, pero no se ha realizado el enrutamiento del tráfico al extremo de red CDN. Para completar el proceso, vuelva al sitio web del registrador DNS y cree otro registro CNAME que asigne su subdominio al extremo de red CDN. Por ejemplo, especifique el subdominio como **www** o **cdn** y el nombre de host como **az#####.vo.msecnd.net**. Con este paso se completa el registro del dominio personalizado. 
10.	Finalmente, puede eliminar el registro CNAME que creó con **cdnverify**, ya que solo era necesario como paso intermedio.  


##<a name="subheading3"></a>Comprobar que el subdominio personalizado hace referencia a su extremo de red CDN

-	Después de haber completado el registro del dominio personalizado, puede tener acceso al contenido almacenado en caché en su extremo de red CDN mediante el dominio personalizado. En primer lugar, asegúrese de que tiene contenido público almacenado en caché en el extremo. Por ejemplo, si su extremo de red CDN está asociado a una cuenta de almacenamiento, la red CDN almacena en caché el contenido en contenedores de blob públicos. Para probar el dominio personalizado, asegúrese de que su contenedor está establecido para permitir acceso público y que contiene al menos un blob.
-	En el explorador, navegue a la dirección del blob usando el dominio personalizado. Por ejemplo, si el nombre de dominio es **www.mydomain.com**, la dirección de URL a un blob almacenado en caché será similar a la siguiente dirección URL:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Si el extremo de red CDN está asociado a un servicio en la nube, la dirección del contenido almacenado en caché será similar a la siguiente dirección URL:

		http://www.mydomain.com/cdn/mycloudservice

##Otras referencias


[Habilitar la Red de entrega de contenido (CDN) para Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=July15_HO4-->