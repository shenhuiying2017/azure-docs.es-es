<properties
 pageTitle="Autenticación saliente de Programador"
 description="Autenticación saliente de Programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Autenticación saliente de Programador

Puede que los trabajos de Programador tengan que llamar a servicios que requieren autenticación. De este modo, un servicio llamado puede determinar si el trabajo de Programador puede tener acceso a sus recursos. Algunos de estos servicios incluyen otros servicios de Azure, Salesforce.com, Facebook y sitios web personalizados que sean seguros.

## Incorporación y eliminación de autenticación

La incorporación de autenticación a un trabajo de Programador es sencilla: agregue un elemento secundario JSON `authentication` al elemento `request` al crear o actualizar un trabajo. Los secretos que se pasan al servicio Programador en una solicitud PUT, PATCH o POST, como parte del objeto `authentication`, nunca se devuelven en respuestas. En las respuestas, la información secreta se establece en null o puede que tenga un token público que represente la entidad autenticada.

Para quitar la autenticación, incluya explícitamente una solicitud PUT o PATCH en el trabajo, lo que establece el objeto `authentication` en null. No verá ninguna propiedad de autenticación en la respuesta.

Actualmente, los únicos tipos de autenticación compatibles son el modelo `ClientCertificate` (para usar los certificados de cliente SSL/TLS), el modelo `Basic` (para la autenticación básica) y el modelo `ActiveDirectoryOAuth` (para autenticación ActiveDirectoryOAuth).

## Cuerpo de la solicitud en autenticación ClientCertificate

Al agregar autenticación mediante el modelo `ClientCertificate`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento|Descripción|
|:---|:---|
|_authentication (parent element)_|Objeto de autenticación para usar un certificado de cliente SSL.|
|_type_|Obligatorio. Tipo de autenticación. En certificados de cliente SSL, el valor debe ser `ClientCertificate`.|
|_pfx_|Obligatorio. Contenido codificado en base 64 del archivo PFX.|
|_password_|Obligatorio. Contraseña de acceso al archivo PFX.|


## Cuerpo de la respuesta en autenticación ClientCertificate

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar un certificado de cliente SSL.|
|_type_ |Tipo de autenticación. Para los certificados de cliente SSL, el valor es `ClientCertificate`.|
|_certificateThumbprint_ |Huella digital del certificado.|
|_certificateSubjectName_ |Nombre distintivo del sujeto del certificado.|
|_certificateExpiration_ |Fecha de expiración del certificado|

## Cuerpo de la solicitud en autenticación básica

Al agregar autenticación mediante el modelo `Basic`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento|Descripción|
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación básica.|
|_type_ |Obligatorio. Tipo de autenticación. En autenticación básica, el valor debe ser `Basic`.|
|_username_ |Obligatorio. Nombre de usuario para autenticar.|
|_password_ |Obligatorio. Contraseña para autenticar.|

## Cuerpo de la respuesta en autenticación básica

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento|Descripción|
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación básica.|
|_type_ |Tipo de autenticación. En autenticación básica, el valor es `Basic`.|
|_username_ |Nombre del usuario autenticado.|

## Cuerpo de la solicitud en autenticación ActiveDirectoryOAuth

Al agregar autenticación mediante el modelo `ActiveDirectoryOAuth`, especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth.|
|_type_ |Obligatorio. Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor debe ser `ActiveDirectoryOAuth`.|
|_tenant_ |Obligatorio. Identificador del inquilino de Azure AD.|
|_audience_ |Obligatorio. Se establece en https://management.core.windows.net/.|
|_clientId_ |Obligatorio. Proporcione el identificador de cliente para la aplicación de Azure AD.|
|_secret_ |Obligatorio. Secreto del cliente que solicita el token.|

### Determinación del identificador del inquilino

Encontrará el identificador del inquilino de Azure AD ejecutando `Get-AzureAccount` en Azure PowerShell.

## Cuerpo de la respuesta en autenticación ActiveDirectoryOAuth

Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

|Elemento |Descripción |
|:--|:--|
|_authentication (parent element)_ |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth.|
|_type_ |Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`.|
|_tenant_ |Identificador del inquilino de Azure AD. |
|_audience_ |Se establece en https://management.core.windows.net/.|.
|_clientId_ |Identificador de cliente para la aplicación de Azure AD.|

## Otras referencias


 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0420_2016-->