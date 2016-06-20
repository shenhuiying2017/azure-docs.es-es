<properties
	pageTitle="Prácticas recomendadas de OAuth 2.0 en Azure AD | Microsoft Azure"
	description="En este artículo se describen las prácticas recomendadas para desarrollar aplicaciones que usen OAuth 2.0 en Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>


# Prácticas recomendadas de OAuth 2.0 en Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

## Uso del parámetro State

El parámetro `state` es un valor no reutilizado y generado de manera aleatoria (normalmente, un GUID) que el cliente envía en la solicitud. Aunque se trata de un parámetro opcional, se recomienda encarecidamente utilizarlo en las solicitudes del código de autorización. La respuesta incluye también el mismo valor `state`. Además, la aplicación debe comprobar que los valores de estado sean idénticos antes de utilizar la respuesta.

El parámetro `state` ayuda a detectar los ataques de falsificación de solicitud entre sitios en (CSRF) que se producen en el cliente. Para obtener más información sobre este tipo de ataques, consulte la sección [Cross-Site Request Forgery](https://tools.ietf.org/html/rfc6749#section-10.12) (Falsificación de solicitud entre sitios) del marco de autorización de OAuth 2.0.

## Almacenamiento en caché de los tokens de acceso

Para minimizar las llamadas de red realizadas desde la aplicación cliente y la latencia asociada, la aplicación cliente debe almacenar en caché los tokens de acceso durante el tiempo que se especifica en la respuesta de OAuth 2.0. Para determinar la duración del token, utilice los valores de parámetro `expires_in` o `expires_on`.

Si un recurso de API web devuelve un código de error `invalid_token`, podría significar que el recurso ha determinado que el token ha expirado. Si las horas de reloj del cliente y los recursos son diferentes (fenómeno conocido como "sesgo horario"), el recurso podría considerar que el token expire antes de que se borre de la memoria caché del cliente. Si esto ocurre, borre el token de la caché, aunque se encuentre todavía dentro de la duración calculada.

## Control de tokens de actualización

Los tokens de actualización no tienen una duración especificada. Normalmente, las duraciones de este tipo de tokens son relativamente largas. Sin embargo, en algunos casos, los tokens de actualización expiran, se revocan o carecen de privilegios suficientes para realizar la acción deseada. La aplicación cliente debe esperar y controlar los errores que devuelve el punto de conexión de emisión de tokens correctamente.

Cuando reciba una respuesta con un error de actualización de token, descarte el token de actualización actual y solicite un nuevo código de autorización o token de acceso. En concreto, al utilizar un token de actualización en el flujo de concesión del código de autorización, si recibe una respuesta con los códigos de error `interaction_required` o `invalid_grant`, descarte el token de actualización y solicite un nuevo código de autorización.

## Comprobación del parámetro resource\_id

La aplicación cliente debe comprobar el valor del parámetro `resource_id` en la respuesta. De lo contrario, un servicio malintencionado podría realizar un ataque de elevación de privilegios.

 La estrategia recomendada para prevenir ataques consiste en comprobar que resource\_id coincide con la base de la dirección URL de la API web que tiene acceso. Por ejemplo, si https://service.contoso.com/data tiene acceso, `resource_id` puede ser https://service.contoso.com/. La aplicación cliente debe rechazar un parámetro `resource_id` que no empiece por la dirección URL base, salvo que exista una alternativa confiable para comprobar el id.

## Emisión de tokens y directrices de reintento

Azure AD admite varios puntos de conexión de emisión de tokens que los clientes pueden consultar. Utilice las siguientes directrices para implementar la lógica de reintento en estos puntos de conexión con el fin de administrar un error inesperado de la red o el servidor.

Los errores que responden a reintentos suelen devolver códigos de error de la serie HTTP 500 para solicitudes a un punto de conexión de Azure AD. En algunos escenarios, el cliente es una aplicación o servicio que realiza solicitudes automatizadas a Azure AD. En otros, como la federación basada en web que utiliza el protocolo WS-Federation, el cliente es un explorador web y el usuario final debe volver a tratar de realizar el proceso de forma manual.

### Implementación de la lógica de reintento en función de las respuestas de error de la serie HTTP 500

Se recomienda utilizar la lógica de reintento cuando el Servicio de control de acceso (ACS) Active Directory devuelve errores de la serie HTTP 500. En ella se incluye:

- Error HTTP 500: error interno del servidor
- Error HTTP 502: puerta de enlace incorrecta
- Error HTTP 503: servicio no disponible
- Error HTTP 504: tiempo de espera agotado para la puerta de enlace

Aunque los códigos HTTP pueden enumerarse expresamente en la lógica de reintento, basta con invocar la lógica de reintento si se devuelve algún error de la serie HTTP 500.

Normalmente, no se recomienda utilizar la lógica de reintento cuando se devuelven códigos de error de la serie HTTP 400. Un código de respuesta de error de la serie HTTP 400 de ACS significa que la solicitud no es válida y que debe revisarse.

La lógica de reintento debe activarse mediante los códigos de error HTTP, como HTTP 504 (tiempo de espera agotado para el servidor externo) o la serie de códigos de error HTTP 500, y no mediante los códigos de error de ACS; por ejemplo, ACS90005. Los códigos de error de ACS son informativos y están sujetos a cambios.

### Los reintentos deben usar un temporizador de retroceso para controlar el flujo de manera óptima

Cuando un cliente recibe un error de la serie HTTP 500, el cliente debe esperar un tiempo antes de volver a tratar de realizar la solicitud. Para obtener mejores resultados, se recomienda que este periodo sea mayor en cada reintento. Gracias a este enfoque, podemos solucionar rápidamente los errores transitorios y optimizar al mismo tiempo la frecuencia de solicitudes de los problemas de servidor o de red transitorios que tardan más tiempo en resolverse.

Por ejemplo, utilice un temporizador de retroceso exponencial donde el retraso antes del reintento aumente exponencialmente con cada instancia, como reintento 1: 1 segundo; reintento 2: 2 segundos; reintento 3: 4 segundos, y así sucesivamente.

Ajuste el número de reintentos y el tiempo entre cada uno de ellos según los requisitos de experiencia del usuario. Sin embargo, se recomienda hasta cinco reintentos durante un periodo de cinco minutos. Los errores derivados de un tiempo de espera agotado tardan más en solucionarse.

## Pasos siguientes

[Bibliotecas de autenticación de Azure Active Directory (ADAL)](active-directory-authentication-libraries.md)

<!---HONumber=AcomDC_0608_2016-->