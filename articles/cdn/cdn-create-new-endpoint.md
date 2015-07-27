<properties 
	 pageTitle="Habilitar la Red de entrega de contenido (CDN) para Azure" 
	 description="En este tema se muestra cómo habilitar la Red de entrega de contenido (CDN) para Azure." 
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
	 ms.date="06/03/2015" 
	 ms.author="mazha"/>



#Habilitar la Red de entrega de contenido (CDN) para Azure  

CDN puede habilitarse para el origen mediante el Portal de administración de Azure. El tipo de origen disponible actual incluye: aplicaciones web, almacenamiento, servicios en la nube. También puede habilitar la red CDN para el extremo de streaming de Servicios multimedia de Azure. Una vez que haya habilitado un extremo de red CDN para su origen, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN.

Tenga en cuenta que ahora también puede crear un origen personalizado y no tiene que ser Azure.

##Para crear un nuevo extremo de red CDN  

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).
2.	En el panel de navegación, haga clic en **CDN**.
3.	En la cinta, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **SERVICIOS DE APLICACIONES**, a continuación **CDN** y, finalmente, **CREACIÓN RÁPIDA**.
4.	En la lista desplegable **TIPO DE ORIGEN**, seleccione un tipo de origen de la lista de tipos de origen disponibles.
	
	Se mostrará la lista de direcciones URL de origen disponibles en lista desplegable **URL DE ORIGEN**.
	

	![createnew][createnew]

	Si selecciona **Origen personalizado**, puede especificar una dirección URL de origen personalizado. Qué no tiene que ser un origen de Azure.

	![customorigin][customorigin]

	>[AZURE.NOTE]Actualmente solo se admite HTTP para el origen y debe usar la extensión de Servicios multimedia para habilitar la red CDN de Azure para un extremo de streaming de Servicios multimedia de Azure.
	
5.	Haga clic en el botón **Crear** para crear el nuevo extremo.


>[AZURE.NOTE]La configuración que ha establecido para el extremo no estará disponible de forma inmediata; el registro puede tardar hasta 60 minutos en propagarse a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 400 (solicitud incorrecta) hasta que el contenido esté disponible a través de la red CDN.

##Otras referencias
[Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=July15_HO3-->