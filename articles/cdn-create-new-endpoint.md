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
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Habilitar la Red de entrega de contenido (CDN) para Azure  

Una vez que haya habilitado un extremo de red CDN para su origen como cuenta de almacenamiento o servicio en la nube, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN.  


##Para crear un nuevo extremo de red CDN  

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/).
2.	En el panel de navegación, haga clic en **CDN**.
3.	En la cinta de opciones, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Servicios de aplicaciones**, luego **CDN** y, a continuación, **Creación rápida**.
4.	En el cuadro desplegable **Dominio de origen**, seleccione el destino que desee en la lista de tipos de origen disponibles.
5.	Haga clic en el botón **Crear** para crear el nuevo extremo.




> Nota: La configuración que ha establecido para el extremo no estará disponible de forma inmediata; el registro puede tardar hasta 60 minutos en propagarse a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 400 (solicitud incorrecta) hasta que el contenido esté disponible a través de la red CDN.

#Otras referencias
[Asignación del contenido de la Red de entrega de contenido (CDN) a un dominio personalizado](./cdn-map-content-to-custom-domain.md)

<!--HONumber=49-->