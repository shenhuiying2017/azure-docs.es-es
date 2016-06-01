<properties
	pageTitle="Purgar un punto de conexión de red CDN de Azure"
	description="Obtenga información sobre cómo purgar todo el contenido almacenado en caché desde un punto de conexión de red CDN."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Purgar un punto de conexión de red CDN de Azure

## Información general

Los nodos perimetrales de la red CDN almacenarán recursos en caché hasta que el período de vida de dichos recursos (TTL) expire. Tras la expiración del TTL del activo, cuando un cliente solicite el activo desde el nodo perimetral, este nodo recuperará una nueva copia actualizada del activo para atender la solicitud de cliente y almacenar actualizada la memoria caché.

A veces puede que quiera purgar contenido almacenado en caché de todos los nodos perimetrales y forzarlos todos para recuperar nuevos activos actualizados. Esto puede deberse a actualizaciones de la aplicación web o a actualizaciones rápidas de los activos de actualización que contienen información incorrecta.

> [AZURE.TIP] Tenga en cuenta que el purgado solo borra el contenido almacenado en caché de los servidores perimetrales de la red CDN. Es posible que las cachés de nivel inferior, como las cachés de servidores proxy y de explorador local, aún contengan una copia en caché del archivo. Es importante que recuerde esto cuando defina el período de vida de un archivo. Para hacer que un cliente de nivel inferior solicite la versión más reciente del archivo, asígnele un nombre único cada vez que lo actualice o use el [almacenamiento en caché de cadenas de consulta](cdn-query-string.md).

Este tutorial le guiará a través de purga de los recursos de todos los nodos perimetrales de un punto de conexión.

## Tutorial

1. En el [Portal de Azure](https://portal.azure.com), examine el perfil de red CDN que contiene el punto de conexión que quiera purgar.

2. En la hoja Perfil de CDN, haga clic en el botón para purgar.

	![Hoja del perfil de red CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	Abre la hoja para purgar.

	![Hoja de purga de red CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. En la hoja para purgar, seleccione la dirección del servicio que quiera purgar en la lista desplegable de la dirección URL.

	![Formulario para purgar](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] También puede obtener acceso a la hoja para purgar haciendo clic en el botón **Purgar** de la hoja del punto de conexión de red de CDN. En ese caso, el campo **URL** se rellenará previamente con la dirección del servicio de ese punto de conexión concreto.

4. Seleccione los activos que quiera purgar de los nodos perimetrales. Si quiere borrar todos los recursos, haga clic en la casilla **Purgar todo**. De lo contrario, escriba la ruta de acceso completa de cada recurso que quiera purgar (por ejemplo, `/pictures/kitten.png`) en el cuadro de texto **Ruta de acceso**.

	> [AZURE.TIP] Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos. Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
	>
	> Las rutas de acceso deben ser una dirección URL relativa que se ajuste a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)**$";`. Para la **red CDN de Azure de Verizon** (estándar y premium), el asterisco (*) se puede usar como un carácter comodín (por ejemplo, `/music/*`). Los caracteres comodín y **Purgar todo** no se admiten con la **red CDN de Azure de Akamai**.
	
5. Haga clic en el botón **Purgar**.

	![Botón Purgar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Las solicitudes de purga tardan aproximadamente de 2 a 3 minutos en procesarse con la red **CDN de Azure de Verizon** (estándar y premium) y unos 7 minutos con la **red CDN de Azure de Akamai**. La red CDN de Azure tiene un límite de 50 solicitudes de purga simultáneas en un momento dado.

## Consulte también
- [Carga previa de activos en un punto de conexión de CDN de Azure](cdn-preload-endpoint.md)
- [Referencia de la API de REST de red de CDN de Azure - purgar o cargar previamente un punto de conexión](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0518_2016-->