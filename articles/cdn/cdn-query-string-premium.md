<properties
	pageTitle="CDN - Control del comportamiento del almacenamiento en caché de las solicitudes con cadenas de consulta - Premium"
	description="El almacenamiento en caché de las cadenas de consultas de CDN controla la manera en que se almacenarán en caché los archivos cuando contienen cadenas de consulta."
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

#Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Red CDN premium de Azure de Verizon](cdn-query-string-premium.md)

##Información general

El almacenamiento en caché de las cadenas de consultas controla la manera en que se almacenarán en caché los archivos cuando contienen cadenas de consulta.

> [AZURE.IMPORTANT] Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consultas, pero la interfaz de usuario es distinta. En este documento se describe la interfaz de la **red CDN premium de Azure de Verizon**. Para más información sobre el almacenamiento en caché de cadenas de consulta con la **red CDN estándar de Azure de Akamai** y la **red CDN estándar de Azure de Verizon**, consulte [Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas](cdn-query-string.md).

Hay tres modos disponibles:

- **standard-cache**: este es el modo predeterminado. El nodo perimetral de CDN pasará la cadena de consulta del solicitante al origen en la primera solicitud y almacenará en la memoria caché el activo. Todas las solicitudes posteriores de ese activo que se ofrecen desde el nodo perimetral ignorarán la cadena de consulta hasta que expira el activo en caché.
- **no-cache**: en este modo, las solicitudes con cadenas de consultas no están almacenadas en caché en el nodo perimetral de CDN. El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
- **unique-cache**: este modo trata cada solicitud con una cadena de consulta como un activo único con su propia memoria caché. Por ejemplo, la respuesta desde el origen para una solicitud de *foo.ashx?q=bar* se almacenaría en la memoria caché en el nodo perimetral y se devolvería para cachés posteriores con esa misma cadena de consulta. Se almacenaría en caché una solicitud de *foo.ashx?q=somethingelse* como un activo independiente con su propio período de vida.

##Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN premium

1. En la hoja de perfil de CDN, haga clic en el botón **Administrar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

	Se abre el portal de administración de CDN.

2. Desplace el mouse sobre la pestaña **HTTP grande** y luego mantenga el mouse sobre el control flotante **Configuración de caché**. Haga clic en **Almacenamiento en caché de cadenas de consultas**.

	Aparecen las opciones del almacenamiento en caché de cadenas de consultas.

	![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string-premium/cdn-query-string.png)

3. Tras efectuar su selección, haga clic en el botón **Actualizar**.


> [AZURE.IMPORTANT] Es posible que los cambios en la configuración no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de la red CDN. Para los perfiles de <b>red CDN de Azure de Verizon</b>, la propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más tiempo.

<!---HONumber=AcomDC_0518_2016-->