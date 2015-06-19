<properties 
	pageTitle="Publicación de los servicios web de Aprendizaje automático de Azure en Azure Marketplace | Azure" 
	description="Publicación de un servicio web de Aprendizaje automático de Azure en Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/03/2014" 
	ms.author="luisca"/>

# Publicación de los servicios web de Aprendizaje automático de Azure en Azure Marketplace 

Este documento incluye los siguientes temas:

- [Introducción]
- [Introducción al proceso de publicación]
- [Directrices para publicar en Azure Marketplace]
- [Opciones específicas de aprendizaje automático] 

<!--Anchors-->
[Introducción]: #introduction
[Introducción al proceso de publicación]: #overview-of-the-publishing-process
[Directrices para publicar en Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
[Opciones específicas de aprendizaje automático]: #machine-learning-specific-options 

## Introducción

Azure Marketplace ofrece la posibilidad de publicar servicios web de Aprendizaje automático de Azure como servicios gratuitos o de pago para su consumo por clientes externos. Este documento proporciona una introducción a ese proceso junto con vínculos a directrices para comenzar. Mediante este proceso, puede comenzar a poner a disposición de otros desarrolladores los servicios web para que los consuman en sus aplicaciones.

## Introducción al proceso de publicación 

A continuación se muestran los pasos para publicar un servicio web de Aprendizaje automático de Azure en Azure Marketplace:

1.	Cree y publique un servicio web RRS (servicio de solicitud-respuesta) de Aprendizaje automático de Azure.
2.	Desde el Portal de administración de Azure, implemente el servicio en producción.
3.	Use la URL del servicio web publicado para publicar en Azure Marketplace.
4.	Introducción a la publicación: http://msdn.microsoft.com/library/azure/hh580725.aspx 
5.	Una vez enviada la oferta, se revisa y es necesario aprobarla antes de que los clientes puedan comenzar a comprarla. El proceso de publicación puede tardar varios días laborables. Estamos trabajando en acortar este tiempo lo máximo posible, y proporcionaremos una actualización en próximas comunicaciones.

## Directrices para publicar en Azure Marketplace

1.	Deberá registrarse como anunciante. Para obtener información, consulte: <http://msdn.microsoft.com/library/azure/hh563872.aspx>
2.	Deberá proporcionar información sobre su oferta, junto con un plan de precios. Decida si ofrecerá un servicio gratuito o de pago. Para obtener información, consulte: <http://msdn.microsoft.com/library/azure/hh563873.aspx> 
3.	Para que le paguen, deberá proporcionar información de pago como su cuenta bancaria y datos fiscales. Para obtener información, consulte: <http://msdn.microsoft.com/library/azure/hh563873.aspx>

## Opciones específicas de aprendizaje automático


1.	Cuando cree una nueva oferta, seleccione **Servicios de datos** y, a continuación, haga clic en **Crear un nuevo servicio de datos**. 
 
	![Azure Marketplace][image1]

	<br />

2. En la pestaña **Servicio de datos**, haga clic en **Servicio web** para el origen de datos.

	![Azure Marketplace][image2]

3.	Obtenga la clave de API y la dirección URL del servicio web en el Portal de administración de Azure:
	1.	Inicie sesión en el Portal de administración de Azure en una pestaña o ventana independiente del explorador ([https://manage.windowsazure.com](https://manage.windowsazure.com)). 
	2.	Seleccione **Aprendizaje automático** en el menú izquierdo.
	3.	Haga clic en **Servicios web** y, a continuación, haga clic en el servicio web que va a publicar.
	4.	Copie la **clave de API** en una ubicación temporal (por ejemplo, el Bloc de notas).
	5.	Haga clic en la **página de Ayuda de API** para el tipo de servicio de solicitud y respuesta.
	6.	Copie la **dirección de extremo de OData** en la ubicación temporal.

	<br />

3.	En el cuadro de diálogo de configuración Servicio de datos de Marketplace, pegue la dirección de extremo de OData en **Dirección URL del servicio**.

	<br />

4. En Autenticación, elija **Encabezado** como **Esquema de autenticación**.

	- Escriba "Autorización" en **Nombre de encabezado**
	- En **Valor de encabezado**, escriba "Bearer" (sin comillas), seguido de espacio y, a continuación, pegue la clave de API.
	- Marque la casilla **Este servicio es OData**.
	- Haga clic en **Probar conexión** para probar la conexión.

	<br />

5.	En Categorías:
	- Asegúrese de que la opción **Aprendizaje automático** está marcada.



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 