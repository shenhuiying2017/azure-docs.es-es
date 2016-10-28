<properties
	pageTitle="Uso de la red CDN de Azure con CORS | Microsoft Azure"
	description="Descubra cómo usar la red de entrega de contenido (CDN) de Azure con el Uso compartido de recursos entre orígenes (CORS)."
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
	ms.date="07/28/2016"
	ms.author="casoper"/>
    
# Uso de la red CDN de Azure con CORS     

## ¿Qué es CORS?

CORS (Uso compartido de recursos entre orígenes) es una característica de HTTP que permite que una aplicación web que se ejecuta en un dominio tenga acceso a recursos de otro dominio. Para reducir la posibilidad de ataques de scripts de sitios, todos los exploradores web modernos implementan una restricción de seguridad que se conoce como [directiva del mismo origen](http://www.w3.org/Security/wiki/Same_Origin_Policy). Esto impide que una página web llame a las API de un dominio distinto. CORS aporta un modo seguro de permitir que un dominio (el dominio de origen) llame a las API de otro dominio.
 
## Cómo funciona
1.	El explorador envía la solicitud OPTIONS con un encabezado HTTP **Origin**. El valor de este encabezado es el dominio del que proviene la página primaria. Cuando una página de https://www.contoso.com intenta obtener acceso a datos de un usuario en el dominio fabrikam.com, se envía el siguiente encabezado de solicitud a fabrikam.com:
    
    `Origin: https://www.contoso.com`
 
2.	El servidor puede responder con lo siguiente:
    - Un encabezado **Access-Control-Allow-Origin** en la respuesta, que indica cuál de los sitios de origen se permite. Por ejemplo:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Una página de error si el servidor no admite la solicitud de origen cruzado.
    - Un encabezado **Access-Control-Allow-Origin** con un carácter comodín que permite todos los dominios:
        
        `Access-Control-Allow-Origin: *`
 
En el caso de las solicitudes HTTP complejas, se efectúa una solicitud "previa" primero para determinar si se cuenta con permisos antes de enviar la solicitud completa.
 
## Escenarios de origen único o carácter comodín

En la red CDN de Azure, CORS funcionará automáticamente sin ninguna configuración adicional, cuando el encabezado **Access-Control-Allow-Origin** esté establecido en un carácter comodín (*) o en un solo origen. La red CDN copiará en caché la primera respuesta, y las solicitudes siguientes usarán el mismo encabezado.
 
Si ya se han enviado solicitudes a la red CDN antes de establecer CORS en el origen, tendrá que purgar el contenido del punto de conexión para volver a cargarlo con el encabezado **Access-Control-Allow-Origin**.
 
## Escenarios de varios orígenes

Si necesita permitir una lista específica de orígenes admitidos para CORS, el proceso puede resultar un poco más complicado. Encontramos el primer problema cuando la red CDN copia en caché el encabezado **Access-Control-Allow-Origin** el primer origen de CORS. Cuando un origen de CORS distinto realiza una solicitud posteriormente, la red CDN enviará el encabezado **Access-Control-Allow-Origin** copiado en la caché, que no coincidirá. Existen diversas formas de corregir este problema.
 
### Red CDN premium de Azure de Verizon

La mejor forma de habilitarlo es usar la **red CDN premium de Azure de Verizon**, que expone una serie de funciones avanzadas.
 
Tendrá que [crear una regla](cdn-rules-engine.md) para comprobar encabezado **Origin** de la solicitud. Si se trata de un origen válido, la regla establecerá el encabezado **Access-Control-Allow-Origin** con el origen proporcionado en la solicitud. Si el origen especificado en el encabezado **Origin** no se permite, la regla deberá omitir el encabezado **Access-Control-Allow-Origin** que ocasionará que el encabezado rechace la solicitud.
 
Hay dos formas de hacerlo con el motor de reglas. En ambos casos, el encabezado **Access-Control-Allow-Origin** del servidor de origen del archivo se pasa por alto completamente; el motor de reglas de la red CDN administra totalmente los orígenes de CORS admitidos.

#### Una expresión regular con todos los orígenes válidos
 
En este caso, creará una expresión regular en la que incluirá todos los orígenes que desea permitir:

	https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] La **red CDN de Azure de Verizon** utiliza las [expresiones regulares compatibles con Perl](http://pcre.org/) como su motor de expresiones regulares. Puede usar una herramienta como [Regular Expressions 101](https://regex101.com/) para validar la expresión regular. Tenga en cuenta que el carácter "/" es válido en expresiones regulares y no hace falta darle formato de escape; sin embargo, darle dicho formato se considera el procedimiento recomendado y algunos validadores de expresiones regulares lo esperan.

Si la expresión regular coincide, la regla reemplazará el encabezado **Access-Control-Allow-Origin** (en caso de haber alguno) del origen por el origen que envió la solicitud. También puede añadir encabezados de CORS adicionales, como **Access-Control-Allow-Methods**.

![Ejemplo de reglas con expresiones regulares](./media/cdn-cors/cdn-cors-regex.png)
 
#### Regla de encabezado de solicitud para cada origen

En lugar de usar expresiones regulares, puede crear una regla aparte para cada origen que desee permitir con la [condición de coincidencia](cdn-rules-engine-details.md#match-conditions) del **carácter comodín del encabezado de solicitud**. Al igual que con el método de expresión regular, el motor de reglas es el único que establece los encabezados de CORS.
  
![Ejemplo de reglas sin expresiones regulares](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] En el ejemplo anterior, el uso del carácter comodín * indica al motor de reglas que se admiten tanto HTTP como HTTPS.
 
### Estándar de red CDN de Azure

En los perfiles estándar de la red CDN de Azure, el único mecanismo para permitir varios orígenes sin usar el carácter comodín consiste en utilizar el [almacenamiento en caché de la cadena de consulta](cdn-query-string.md). Debe habilitar la configuración de la cadena de consulta para el punto de conexión de la red CDN y, después, utilizar una cadena de consulta única para las solicitudes de cada dominio permitido. De este modo, la red CDN copiará en caché un objeto independiente para cada cadena de consulta única. Sin embargo, este enfoque no es ideal, ya que conllevará varias copias del mismo archivo en caché en la red CDN.

<!---HONumber=AcomDC_0803_2016-->