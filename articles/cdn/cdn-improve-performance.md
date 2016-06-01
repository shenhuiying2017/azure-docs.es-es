<properties
	pageTitle="Red CDN - Mejora del rendimiento mediante la compresión de archivos"
	description="Puede mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de archivos."
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

# Mejora del rendimiento mediante la compresión de archivos

La compresión es un método sencillo y eficaz para mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página al reducir el tamaño del archivo antes de que se envíe desde el servidor. Este método reduce los costos de ancho de banda y proporciona una mayor capacidad de respuesta para los usuarios.

La compresión se puede habilitar de dos maneras:

- Puede habilitar la compresión en el servidor de origen, en cuyo caso la red CDN transferirá los archivos comprimidos y los entregará a los clientes que los soliciten.
- Puede habilitar la compresión directamente en los servidores perimetrales de la red CDN, en cuyo caso esta red comprimirá los archivos y los servirá a los usuarios finales, incluso si el servidor de origen no los ha comprimido.

> [AZURE.IMPORTANT] Los cambios en la configuración de la red CDN demoran un tiempo en propagarse por la red. Para los perfiles de la <b>red CDN de Azure de Akamai</b>, la propagación normalmente se completa en menos de un minuto. Para los perfiles de la <b>red CDN de Azure de Verizon</b>, normalmente verá que los cambios se aplican en un período de 90 minutos. Si esta es la primera vez que configura la compresión para su punto de conexión de la red CDN, debe considerar la posibilidad de esperar entre 1 y 2 horas para asegurarse de que la configuración de la compresión se haya propagado a los POP antes de realizar la solución de problemas.

## Habilitar la compresión

> [AZURE.NOTE] Los niveles estándar y premium de CDN proporcionan la misma funcionalidad de compresión, pero la interfaz de usuario es distinta. Para obtener más información sobre las diferencias entre los niveles estándar y premium de CDN, consulte [Información general sobre la red CDN de Azure](cdn-overview.md).

### Nivel Standard

> [AZURE.NOTE] Esta sección es aplicable a perfiles de la **red CDN estándar de Azure de Verizon** y de la **red CDN estándar de Azure de Akamai**.

1. En la hoja de perfil de red CDN, haga clic en el punto de conexión de CDN que desea administrar.

	![Puntos de conexión de hoja del perfil de red CDN](./media/cdn-file-compression/cdn-endpoints.png)

	Se abre la hoja del punto de conexión de CDN.

2. Elija el botón **Configurar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-file-compression/cdn-config-btn.png)

	Se abre la hoja de configuración de CDN.

3. Active la **Compresión**.

	![Opciones de compresión de red CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Use los tipos predeterminados o modifique la lista quitando o agregando los tipos de archivo.
	
	> [AZURE.TIP] Si bien es posible, no se recomienda aplicar compresión a formatos comprimidos, como ZIP, MP3, MP4, JPG, etc.
	
5. Tras efectuar los cambios, haga clic en el botón **Guardar**.

### Nivel Premium

> [AZURE.NOTE] Esta sección es aplicable a perfiles de la **red CDN premium de Azure de Verizon**.

1. En la hoja de perfil de CDN, haga clic en el botón **Administrar**.

	![Botón de administración de hoja de perfil de red CDN](./media/cdn-file-compression/cdn-manage-btn.png)

	Se abre el portal de administración de CDN.

2. Desplace el mouse sobre la pestaña **HTTP grande** y, a continuación, mantenga el mouse sobre el control flotante **Configuración de caché**. Haga clic en **Compresión**.

	Se muestran las opciones de compresión.

	![Compresión de archivos](./media/cdn-file-compression/cdn-compress-files.png)

3. Habilite la compresión con un clic en el botón de selección **Compresión habilitada**. Escriba los tipos de MIME que desea comprimir como una lista separada por comas (sin espacios) en el cuadro de texto **Tipos de archivo**.
		
	> [AZURE.TIP] Si bien es posible, no se recomienda aplicar compresión a formatos comprimidos, como ZIP, MP3, MP4, JPG, etc.

4. Después de realizar los cambios, haga clic en el botón **Actualizar**.


## Reglas de compresión

Estas tablas describen el comportamiento de la compresión CDN de Azure para cada escenario.

> [AZURE.IMPORTANT] En el caso de la **red CDN de Azure de Verizon** (estándar y premium), solo se comprimen determinados archivos válidos. Para ser elegible para la compresión, un archivo debe cumplir con los siguientes requisitos:
>
> - Debe tener más de 128 bytes.
> - Debe tener menos de 1 MB.
> 
> En el caso de la **red CDN de Azure de Akamai**, todos los archivos son válidos para la compresión.
>
> Para todos los productos de la red CDN de Azure, un archivo debe tener un tipo MIME [configurado para compresión](#enabling-compression).
>
> Los perfiles de la **red CDN de Azure de Verizon** (estándar y premium) admiten codificación **gzip**, **deflate** o **bzip2**. Los perfiles de la **red CDN de Azure de Akamai** solo admiten la codificación **gzip**.
>
> Los puntos de conexión de la **red CDN de Azure de Akamai** siempre solicitan archivos codificados **gzip** al origen, independientemente de la solicitud del cliente.

### La compresión está deshabilitada o el archivo no es elegible para la compresión

|Formato solicitado por el cliente (mediante el encabezado Accept-Encoding)|Formato de archivo almacenado en caché|Respuesta de la red CDN al cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimidos|Comprimidos|Comprimidos| |
|Comprimidos|Sin comprimir|Sin comprimir| |	
|Comprimidos|No almacenado en caché|Comprimidos o sin comprimir|Depende de la respuesta de origen|
|Sin comprimir|Comprimidos|Sin comprimir| |
|Sin comprimir|Sin comprimir|Sin comprimir| |	
|Sin comprimir|No almacenado en caché|Sin comprimir| |

### La compresión está habilitada y el archivo es elegible para la compresión

|Formato solicitado por el cliente (mediante el encabezado Accept-Encoding)|Formato de archivo almacenado en caché|Respuesta de la red CDN al cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimidos|Comprimidos|Comprimidos|La red CDN transcodifica entre los formatos admitidos|
|Comprimidos|Sin comprimir|Comprimidos|La red CDN realiza la compresión|
|Comprimidos|No almacenado en caché|Comprimidos|La red CDN realiza la compresión si el origen devuelve sin comprimir|
|Sin comprimir|Comprimidos|Sin comprimir|La red CDN realiza la descompresión|
|Sin comprimir|Sin comprimir|Sin comprimir| |	
|Sin comprimir|No almacenado en caché|Sin comprimir| |	

## Notas
1. Para los puntos de conexión de streaming habilitados para la red CDN de Servicios multimedia, la compresión está habilitada de forma predeterminada para los siguientes tipos de contenido: application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. No se puede habilitar o deshabilitar la compresión de los tipos mencionados mediante el Portal de Azure.  

## Consulte también
- [Solución de problemas de compresión de archivos de red CDN](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0518_2016-->