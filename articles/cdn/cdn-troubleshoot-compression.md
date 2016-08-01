<properties
	pageTitle="Red CDN: solución de problemas de compresión de archivos"
	description="Solucione los problemas con la compresión de archivos de red CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="casoper"/>
    
# Solución de problemas de compresión de archivos de red CDN

Este artículo le ayudará a solucionar los problemas con la [compresión de archivos de red CDN](cdn-improve-performance.md).

Si necesita más ayuda en cualquier punto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

## Síntoma

Está habilitada la compresión para el punto de conexión, pero se devuelven archivos sin comprimir.

## Causa

Hay varias causas posibles, por nombrar algunas:

- El contenido solicitado no es apto para la compresión.
- La compresión no está habilitada para el tipo de archivo solicitado.
- La solicitud HTTP no incluía un encabezado que solicitara un tipo de compresión válido.

## Pasos para solucionar problemas

> [AZURE.TIP] Al igual que lo que ocurre cuando se implementan puntos de conexión nuevos, los cambios en la configuración de la red CDN demoran un tiempo en propagarse por la red. En la mayoría de los casos, verá que los cambios se aplican dentro de 90 minutos. Si esta es la primera vez que configura la compresión para su punto de conexión de la red CDN, debe considerar una espera de 1 o 2 horas para asegurarse de que la configuración de la compresión se propagó a los POP.

### Comprobar la solicitud

En primer lugar, se debe hacer una comprobación rápida en la solicitud. Puede usar las [herramientas para desarrolladores](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del explorador para ver las solicitudes que se realizan.

- Compruebe que la solicitud se envía a la dirección URL del punto de conexión, `<endpointname>.azureedge.net`, y no a su origen.
- Compruebe que la solicitud contenga un encabezado **Accept-Encoding** y que el valor para el encabezado contenga **gzip**, **defalte** o **bzip2**.

> [AZURE.NOTE] Los perfiles de la **red CDN de Azure de Akamai** solo admiten la codificación **gzip**.

![Encabezados de solicitud de red CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### Comprobar la configuración de compresión (perfil de red CDN estándar)

> [AZURE.NOTE] Este paso solo se aplica si el perfil de red CDN es un perfil de **red CDN estándar de Azure de Verizon** o de **red CDN estándar de Azure de Akamai**.

Desplácese hasta el punto de conexión en el [Portal de Azure](https://portal.azure.com) y haga clic en el botón **Configurar**.

- Compruebe que la compresión está habilitada.
- Compruebe que el tipo MIME del contenido que se va a comprimir se incluya en la lista de formatos comprimidos.

![Configuración de compresión de red CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### Comprobar la configuración de compresión (perfil de red CDN Premium)

> [AZURE.NOTE] Este paso solo se aplica si el perfil de red CDN es un perfil de **red CDN Premium de Azure de Verizon**.

Desplácese hasta el punto de conexión en el [Portal de Azure](https://portal.azure.com) y haga clic en el botón **Administrar**. Se abrirá el portal complementario. Desplace el mouse sobre la pestaña **HTTP grande** y luego mantenga el mouse sobre el control flotante **Configuración de caché**. Haga clic en **Compresión**.

- Compruebe que la compresión está habilitada.
- Compruebe que la lista de **Tipos de archivo** contiene una lista de tipos MIME separados por coma (sin espacios).
- Compruebe que el tipo MIME del contenido que se va a comprimir se incluya en la lista de formatos comprimidos.

![Configuración de compresión de red CDN Premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### Comprobar que el contenido se almacena en caché

> [AZURE.NOTE] Este paso solo se aplica si el perfil de red CDN es un perfil de **red CDN de Verizon** (Estándar o Premium).

Con las herramientas para desarrolladores de su explorador, compruebe los encabezados de respuesta para asegurarse de que el archivo se almacena en caché en la región donde se solicita.

- Compruebe el encabezado de respuesta **Server**. El encabezado debe tener el formato **Plataforma (POP/id. de servidor)**, tal como se muestra en el ejemplo siguiente.
- Compruebe el encabezado de respuesta **X Cache**. Debe poner **HIT**.

![Encabezados de respuesta de red CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### Comprobar que el archivo cumple los requisitos de tamaño

> [AZURE.NOTE] Este paso solo se aplica si el perfil de red CDN es un perfil de **red CDN de Verizon** (Estándar o Premium).

Para que un archivo sea apto para la compresión, debe cumplir los siguientes requisitos de tamaño:

- Mayor que 128 bytes.
- Menor que 1 MB.

### Compruebe la solicitud en el servidor de origen para un encabezado **Mediante**

El encabezado **Mediante** HTTP indica al servidor web que un servidor proxy pasará la solicitud. De forma predeterminada, los servidores web de Microsoft IIS no comprimen las respuestas si la solicitud contiene un encabezado **Mediante**. Para anular este comportamiento, haga lo siguiente:

- **IIS 6**: [Establezca HcNoCompressionForProxies = "FALSE" en las propiedades de la metabase de IIS](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 y posteriores**: [Establezca **noCompressionForHttp10** y **noCompressionForProxies** en False en la configuración del servidor](http://www.iis.net/configreference/system.webserver/httpcompression)

<!---HONumber=AcomDC_0720_2016-->