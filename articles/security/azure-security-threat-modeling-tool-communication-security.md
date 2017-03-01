---
title: 'Seguridad en las comunicaciones: Herramienta de modelado de amenazas de Microsoft (Azure) | Microsoft Docs'
description: mitigaciones para amenazas expuestas en Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 8251f44200c11d3efcec04b7ac99857232b2f9ed
ms.openlocfilehash: 4bb69046373971b43bc8c92651daf4e153d28555
ms.lasthandoff: 02/15/2017


---

# <a name="security-frame-communication-security--mitigations"></a>Marco de seguridad: Seguridad en las comunicaciones | Mitigaciones 
| Producto o servicio | Artículo |
| --------------- | ------- |
| Centro de eventos de Azure | <ul><li>[Protección de las comunicaciones con el centro de eventos mediante SSL/TLS](#comm-ssltls)</li></ul> |
| Dynamics CRM | <ul><li>[Comprobación de los privilegios de cuenta de servicio y de que las páginas de ASP.NET o los servicios personalizados respetan la seguridad de CRM](#priv-aspnet)</li></ul> |
| Factoría de datos de Azure | <ul><li>[Uso de Data Management Gateway durante la conexión del servidor SQL Server local a Azure Data Factory](#sqlserver-factory)</li></ul> |
| Identity Server | <ul><li>[Comprobación de que todo el tráfico a Identity Server se transmite a través de una conexión HTTPS](#identity-https)</li></ul> |
| Aplicación web | <ul><li>[Comprobación de certificados X.509 utilizados para autenticar las conexiones SSL, TLS y DTLS](#x509-ssltls)</li><li>[Configuración de un certificado SSL para un dominio personalizado en Azure App Service](#ssl-appservice)</li><li>[Direccionamiento forzoso de todo el tráfico a Azure App Service a través de una conexión HTTPS](#appservice-https)</li><li>[Habilitación de seguridad de transporte estricto HTTP (HSTS)](#http-hsts)</li></ul> |
| Base de datos | <ul><li>[Comprobación de cifrado de la conexión de SQL Server y validación de certificados](#sqlserver-validation)</li><li>[Aplicación forzosa de comunicación cifrada a SQL Server](#encrypted-sqlserver)</li></ul> |
| Almacenamiento de Azure | <ul><li>[Comprobación de que la comunicación a Azure Storage se realiza a través de HTTPS](#comm-storage)</li><li>[Validación de hash MD5 después de descargar blob si no se puede habilitar HTTPS](#md5-https)</li><li>[Uso de un cliente compatible con SMB 3.0 para garantizar el cifrado de datos en tránsito en recursos compartidos de archivos de Azure](#smb-shares)</li></ul> |
| Cliente móvil | <ul><li>[Implementación de asignación de certificados](#cert-pinning)</li></ul> |
| WCF | <ul><li>[Habilitación de HTTPS: canal de transporte seguro](#https-transport)</li><li>[WCF: establecimiento del nivel de protección de seguridad de mensajes en EncryptAndSign](#message-protection)</li><li>[WCF: uso de una cuenta con privilegios mínimos para ejecutar el servicio WCF](#least-account-wcf)</li></ul> |
| API Web | <ul><li>[Direccionamiento forzoso de todo el tráfico a API web a través de una conexión HTTPS](#webapi-https)</li></ul> |
| Caché en Redis de Azure | <ul><li>[Comprobación de que la comunicación a Azure Redis Cache se realiza a través de SSL](#redis-ssl)</li></ul> |
| Puerta de enlace de campo de IoT | <ul><li>[Protección de la comunicación entre el dispositivo y la puerta de enlace de campo](#device-field)</li></ul> |
| Puerta de enlace de la nube de IoT | <ul><li>[Protección de la comunicación entre el dispositivo y la puerta de enlace de nube mediante SSL/TLS](#device-cloud)</li></ul> |

## <a name="a-idcomm-ssltlsasecure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Protección de las comunicaciones con el centro de eventos mediante SSL/TLS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Centro de eventos de Azure | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [Introducción al modelo de autenticación y seguridad de Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| Pasos | Proteja conexiones AMQP o HTTP al centro de eventos mediante SSL/TLS. |

## <a name="a-idpriv-aspnetacheck-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Comprobación de los privilegios de cuenta de servicio y de que las páginas de ASP.NET o los servicios personalizados respetan la seguridad de CRM

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Dynamics CRM | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | N/D  |
| Pasos | Compruebe los privilegios de cuenta de servicio y que las páginas de ASP.NET o los servicios personalizados respetan la seguridad de CRM. |

## <a name="a-idsqlserver-factoryause-data-management-gateway-while-connecting-on-prem-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Uso de puerta de enlace de administración de datos durante la conexión del servidor SQL Server local a Azure Data Factory

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Factoría de datos de Azure | 
| Fase de SDL               | Implementación |  
| Tecnologías aplicables | Genérico |
| Attributes              | Tipos de servicios vinculados: Azure y local |
| Referencias              |[Movimiento de datos entre orígenes locales y Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [Data Management Gateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| Pasos | <p>La herramienta Data Management Gateway (DMG) es necesaria para conectarse a orígenes de datos que están protegidos en una red corporativa o mediante firewall.</p><ol><li>Al bloquearse la máquina, se aísla la herramienta DMG, lo que impide que los programas que funcionan incorrectamente dañen o accedan a la máquina del origen de datos (por ejemplo, deben instalarse las actualizaciones más recientes, habilitar los puertos mínimos necesarios, aprovisionar cuentas controladas, habilitar la auditoría, habilitar el cifrado de disco, etc.).</li><li>La clave de puerta de enlace de datos se debe alternar a intervalos frecuentes o siempre que se renueva la contraseña de cuenta del servicio DMG.</li><li>El tránsito de datos a través del servicio de vínculos debe cifrarse.</li></ol> |

## <a name="a-ididentity-httpsaensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Comprobación de que todo el tráfico a Identity Server se transmite a través de una conexión HTTPS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Identity Server | 
| Fase de SDL               | Implementación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [IdentityServer3: claves, firmas y criptografía](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3: implementación](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| Pasos | De forma predeterminada, IdentityServer requiere que todas las conexiones entrantes se realicen a través de HTTPS. Es absolutamente obligatorio que la comunicación con IdentityServer se realice exclusivamente a través de transportes seguros. En ciertos escenarios de implementación, como la descarga de SSL, este requisito puede ser más flexible. Consulte la página de implementación de Identity Server en las referencias para más información. |

## <a name="a-idx509-ssltlsaverify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Comprobación de certificados X.509 utilizados para autenticar las conexiones SSL, TLS y DTLS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Aplicación web | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | N/D  |
| Pasos | <p>Las aplicaciones que utilizan SSL, TLS y DTLS deben comprobar completamente los certificados X.509 de las entidades a las que se conectan. Esto incluye la comprobación de los certificados de:</p><ul><li>Nombre de dominio</li><li>Fechas de validez (fechas de inicio y caducidad)</li><li>Estado de revocación</li><li>Uso (por ejemplo, autenticación de servidor para servidores, autenticación de cliente para clientes)</li><li>Cadena de confianza: los certificados deben estar encadenados a una entidad de certificación raíz (CA) que sea de confianza para la plataforma o que haya configurado explícitamente el administrador</li><li>Longitud de la clave pública del certificado >&2048; bits</li><li>Algoritmo hash: SHA256 y versiones posteriores |

## <a name="a-idssl-appserviceaconfigure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configuración de un certificado SSL para un dominio personalizado en Azure App Service

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Aplicación web | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | EnvironmentType: Azure |
| Referencias              | [Habilitación de HTTPS para una aplicación en el servicio de aplicaciones de Azure](https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/) |
| Pasos | De forma predeterminada, Azure ya habilita HTTPS para cada aplicación con un certificado comodín para el dominio *.azurewebsites.net. Sin embargo, al igual que todos los dominios comodín, no es tan seguro como usar un dominio personalizado con su propio certificado. [Más información](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Se recomienda habilitar SSL para el dominio personalizado a través del que se accederá a la aplicación implementada.|

## <a name="a-idappservice-httpsaforce-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Direccionamiento forzoso de todo el tráfico a Azure App Service a través de una conexión HTTPS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Aplicación web | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | EnvironmentType: Azure |
| Referencias              | [Exigencia de HTTPS en Azure App Service]https://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/#4-enforce-https-on-your-app) |
| Pasos | <p>Aunque Azure ya habilita HTTPS para los servicios de aplicaciones de Azure con un certificado comodín para el dominio *.azurewebsites.net, no lo exige. Los visitantes pueden seguir accediendo a la aplicación mediante HTTP, lo que puede comprometer la seguridad de la aplicación, por lo que debe exigirse el uso de HTTPS explícitamente. Las aplicaciones de ASP.NET MVC deben utilizar el [filtro RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) que obliga a que una solicitud HTTP no segura se vuelva a enviar a través de HTTPS.</p><p>También es posible utilizar el módulo URL Rewrite, incluido con Azure App Service, para exigir el uso de HTTPS. El módulo URL Rewrite permite a los desarrolladores definir reglas que se aplican a las solicitudes entrantes antes de que las solicitudes lleguen a su aplicación. Las reglas de URL Rewrite se definen en el archivo web.config, que se almacena en la raíz de la aplicación.</p>|

### <a name="example"></a>Ejemplo
El ejemplo siguiente contiene una regla básica de URL Rewrite que impone el uso de HTTPS a todo el tráfico entrante.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Esta regla funciona devolviendo un código de estado HTTP de 301 (redirección permanente) cuando el usuario solicita una página mediante HTTP. El 301 redirige la solicitud a la misma URL que el visitante solicitó, pero sustituye la parte de HTTP de la solicitud por HTTPS. Por ejemplo, HTTP://contoso.com se redirigiría a HTTPS://contoso.com. 

## <a name="a-idhttp-hstsaenable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Habilitación de seguridad de transporte estricto HTTP (HSTS)

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Aplicación web | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [Hoja de referencia rápida de seguridad de transporte estricto HTTP del Proyecto de seguridad de aplicación web abierta](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| Pasos | <p>La seguridad de transporto estricta HTTP (HSTS) es una mejora de seguridad opcional que se especifica con una aplicación web mediante el uso de un encabezado de respuesta especial. Una vez que un explorador compatible recibe este encabezado, ese explorador impedirá que se envíen comunicaciones a través de HTTP al dominio especificado y, en su lugar, enviará todas las comunicaciones a través de HTTPS. También evita que aparezcan en los exploradores elementos click-through HTTPS.</p><p>Para implementar HSTS, se debe configurar el siguiente encabezado de respuesta para un sitio web a nivel global, ya sea en el código o en la configuración. Strict-Transport-Security: max-age=300; includeSubDomains. HSTS resuelve las amenazas siguientes:</p><ul><li>Un usuario guarda en sus marcadores la dirección http://example.com o la introduce manualmente y sufre un ataque de tipo "Man in the middle": HSTS redirige automáticamente las solicitudes HTTP a HTTPS para el dominio de destino.</li><li>Una aplicación web diseñada para ser exclusivamente HTTPS contiene accidentalmente vínculos HTTP o sirve contenido a través de HTTP: HSTS redirige automáticamente las solicitudes HTTP a HTTPS para el dominio de destino.</li><li>Un ataque de tipo "Man in the middle" intenta interceptar el tráfico de un usuario mediante un certificado no válido y espera que este usuario acepte dicho certificado: HSTS no permite que un usuario omita el mensaje de certificado no válido.</li></ul>|

## <a name="a-idsqlserver-validationaensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Comprobación de cifrado de la conexión de SQL Server y validación de certificados

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Base de datos | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | SQL Azure  |
| Attributes              | Versión de SQL: V12 |
| Referencias              | [Procedimientos recomendados sobre cómo escribir cadenas de conexión seguras para SQL Database](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| Pasos | <p>Todas las comunicaciones entre SQL Database y una aplicación cliente se cifran mediante capa de sockets seguros (SSL) en todo momento. SQL Database no admite conexiones no cifradas. Para validar certificados con código de aplicación o herramientas, solicite explícitamente una conexión cifrada y no confíe en los certificados de servidor. Si el código de su aplicación o las herramientas no solicitan una conexión cifrada, seguirán recibiendo conexiones cifradas.</p><p>Sin embargo, es posible que no validen los certificados de servidor, por lo que serán susceptibles de recibir ataques de tipo "man in the middle". Para validar certificados con código de aplicación ADO.NET, establezca `Encrypt=True` y `TrustServerCertificate=False` en la cadena de conexión de la base de datos. Para validar certificados mediante SQL Server Management Studio, abra el cuadro de diálogo Conectar con el servidor. Haga clic en Cifrar conexión en la pestaña Propiedades de conexión.</p>|

## <a name="a-idencrypted-sqlserveraforce-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Aplicación forzosa de comunicación cifrada a SQL Server

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Base de datos | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | OnPrem |
| Attributes              | Versión de SQL: MsSQL2016, Versión de SQL: MsSQL2012, Versión de SQL: MsSQL2014 |
| Referencias              | [Habilitar conexiones cifradas en el motor de base de datos](https://msdn.microsoft.com/library/ms191192)  |
| Pasos | Al habilitar el cifrado SSL, aumenta la seguridad de los datos transmitidos a través de redes entre instancias de SQL Server y las aplicaciones. |

## <a name="a-idcomm-storageaensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Comprobación de que la comunicación a Azure Storage se realiza a través de HTTPS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Almacenamiento de Azure | 
| Fase de SDL               | Implementación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [Azure Storage: Cifrado de nivel de transporte – Uso de HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| Pasos | Para garantizar la seguridad de los datos de Azure Storage en tránsito, utilice siempre el protocolo HTTPS al llamar a las API de REST o al acceder a objetos en el almacenamiento. Además, las Firmas de acceso compartido, que pueden utilizarse para delegar el acceso a objetos de Azure Storage, incluyen una opción para especificar que se utilice solo el protocolo HTTPS con las Firmas de acceso compartido, lo que garantiza que cualquiera que envíe vínculos con tokens de SAS utilice el protocolo adecuado.|

## <a name="a-idmd5-httpsavalidate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Validación de hash MD5 después de descargar blob si no se puede habilitar HTTPS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Almacenamiento de Azure | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | StorageType: blob |
| Referencias              | [Información general de MD5 de Windows Azure Blob](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| Pasos | <p>El servicio Windows Azure Blob proporciona mecanismos para garantizar la integridad de datos tanto en el nivel de la aplicación como en el de transporte. Si por algún motivo debe utilizar HTTP en lugar de HTTPS y está trabajando con blobs en bloques, puede usar la comprobación de MD5 para ayudar a comprobar la integridad de los blobs que se transfieren.</p><p>Esto le ayudará con la protección frente a errores de red o de la capa de transporte, pero no necesariamente con ataques de intermediarios. Si puede usar HTTPS, que proporciona seguridad de nivel de transporte, el uso de la comprobación de MD5 es redundante e innecesario.</p>|

## <a name="a-idsmb-sharesause-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Uso de un cliente compatible con SMB 3.0 para garantizar el cifrado de datos en tránsito en recursos compartidos de archivos de Azure

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Cliente móvil | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | StorageType: archivo |
| Referencias              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Compatibilidad con SMB de Azure File Storage para clientes de Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| Pasos | Almacenamiento de archivos de Azure admite HTTPS cuando se usa la API de REST, pero se usa con más frecuencia como un recurso compartido de archivos de SMB adjunto a una máquina virtual. SMB 2.1 no admite el cifrado, por lo que solo se permiten las conexiones dentro de la misma región de Azure. Sin embargo, SMB 3.0 admite el cifrado, y se puede utilizar con Windows Server 2012 R2, Windows 8, Windows 8.1 y Windows 10, lo que permite el acceso entre regiones e incluso el acceso en el escritorio. |

## <a name="a-idcert-pinningaimplement-certificate-pinning"></a><a id="cert-pinning"></a>Implementación de asignación de certificados

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Almacenamiento de Azure | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico, Windows Phone |
| Attributes              | N/D  |
| Referencias              | [Asignación de certificados y claves públicas](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| Pasos | <p>La asignación de certificados protege frente a ataques de tipo "man in the middle". La asignación es el proceso de asociar un host a su certificado X509 o clave pública esperados. Tras determinar el certificado o clave pública de un host, dicho certificado o clave pública se asocia o asigna al host. </p><p>De este modo, cuando un atacante intenta realizar un ataque de tipo "man in the middle" SSL, durante el protocolo de enlace SSL la clave del servidor del atacante será distinta a la clave del certificado asignado, por lo que la solicitud se descartará, lo que evita que pueda realizarse una asociación de certificados mediante ataque de tipo "man in the middle" al implementar el delegado `ServerCertificateValidationCallback` de ServicePointManager.</p>|

### <a name="example"></a>Ejemplo
```C#
private static String PUB_KEY = "30818902818100C4A06B7B52F8D17DC1CCB47362" +
    "C64AB799AAE19E245A7559E9CEEC7D8AA4DF07CB0B21FDFD763C63A313A668FE9D764E" +
    "D913C51A676788DB62AF624F422C2F112C1316922AA5D37823CD9F43D1FC54513D14B2" +
    "9E36991F08A042C42EAAEEE5FE8E2CB10167174A359CEBF6FACC2C9CA933AD403137EE" +
    "2C3F4CBED9460129C72B0203010001";

public static void Main(string[] args)
{
  ServicePointManager.ServerCertificateValidationCallback = PinPublicKey;
  WebRequest wr = WebRequest.Create("https://encrypted.google.com/");
  wr.GetResponse();
}

public static bool PinPublicKey(object sender, X509Certificate certificate, X509Chain chain,
                                SslPolicyErrors sslPolicyErrors)
{
  if (null == certificate)
    return false;

  String pk = certificate.GetPublicKeyString();
  if (pk.Equals(PUB_KEY))
    return true;

  // Bad dog
  return false;
}
```

## <a name="a-idhttps-transportaenable-https---secure-transport-channel"></a><a id="https-transport"></a>Habilitación de HTTPS: canal de transporte seguro

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | NET Framework 3 |
| Attributes              | N/D  |
| Referencias              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| Pasos | La configuración de la aplicación debe garantizar que se utilice HTTPS siempre que se acceda a información confidencial.<ul><li>**EXPLICACIÓN:** si una aplicación administra información confidencial y no usa el cifrado de nivel de mensaje, solo debe poder comunicarse a través de un canal de transporte cifrado.</li><li>**RECOMENDACIONES:** asegúrese de que el transporte HTTP está deshabilitado y habilite el transporte HTTPS en su lugar. Por ejemplo, reemplace `<httpTransport/>` por la etiqueta `<httpsTransport/>`. No utilice una configuración de red (firewall) para asegurarse de que solo se pueda acceder a la aplicación a través de un canal seguro. Desde un enfoque filosófico, la seguridad de la aplicación no debe depender de la red.</li></ul><p>Desde un punto de vista práctico, las personas responsables de proteger la red no siempre llevan al día los requisitos de seguridad de la aplicación a medida que estos evolucionan.</p>|

## <a name="a-idmessage-protectionawcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: establecimiento del nivel de protección de seguridad de mensajes en EncryptAndSign

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | .NET Framework 3 |
| Attributes              | N/D  |
| Referencias              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| Pasos | <ul><li>**EXPLICACIÓN:** cuando el nivel de protección está establecido en "none", se deshabilitará la protección de mensajes. La confidencialidad e integridad requieren definir un nivel adecuado.</li><li>**RECOMENDACIONES:**<ul><li>si `Mode=None`: deshabilita la protección del mensaje.</li><li>si `Mode=Sign`: firma el mensaje, pero no lo cifra. Debe utilizarse cuando importa la integridad de los datos.</li><li>si `Mode=EncryptAndSign`: firma y cifra el mensaje.</li></ul></li></ul><p>Considere la posibilidad de desactivar el cifrado y solo firmar el mensaje cuando únicamente necesite validar la integridad de la información sin necesidad de preocuparse por la confidencialidad. Esto puede resultar útil para operaciones o contratos de servicio en los que se debe validar el remitente original pero no se transmiten datos confidenciales. Al reducir el nivel de protección, asegúrese de que el mensaje no contenga ninguna información de identificación personal.</p>|

### <a name="example"></a>Ejemplo
En los siguientes ejemplos, se muestra cómo configurar el servicio y la operación para que solo se firme el mensaje. Ejemplo de contrato de servicio de `ProtectionLevel.Sign`: el siguiente es un ejemplo del uso de ProtectionLevel.Sign en el nivel de contrato de servicio: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Ejemplo
Ejemplo de contrato de operación de `ProtectionLevel.Sign` (para control detallado): el siguiente es un ejemplo del uso de `ProtectionLevel.Sign` en el nivel de contrato de operación:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="a-idleast-account-wcfawcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: uso de una cuenta con privilegios mínimos para ejecutar el servicio WCF

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | WCF | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | .NET Framework 3 |
| Attributes              | N/D  |
| Referencias              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| Pasos | <ul><li>**EXPLICACIÓN:** no ejecute servicios WCF en una cuenta de administrador o con privilegios elevados. Esto tendría una gran incidencia en caso de que los servicios se vieran comprometidos.</li><li>**RECOMENDACIONES:** utilice una cuenta con privilegios mínimos para hospedar el servicio WCF, ya que así se reducirá la superficie expuesta a ataques de su aplicación, así como los posibles daños en caso de ataque. Si la cuenta de servicio requiere derechos de acceso adicionales en los recursos de infraestructura, como MSMQ, el registro de eventos, los contadores de rendimiento y el sistema de archivos, deben otorgarse los permisos adecuados a estos recursos para que el servicio WCF se pueda ejecutar correctamente.</li></ul><p>Si el servicio necesita acceder a recursos específicos en nombre del llamador original, utilice la suplantación y la delegación para transmitir la identidad del llamador para una comprobación de autorización indirecta. En un escenario de desarrollo, use la cuenta de servicio de red local, que es una cuenta especial integrada que tiene privilegios reducidos. En un escenario de producción, cree una cuenta de servicio de dominio personalizado con privilegios mínimos.</p>|

## <a name="a-idwebapi-httpsaforce-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Direccionamiento forzoso de todo el tráfico a API web a través de una conexión HTTPS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | API Web | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | MVC5, MVC6 |
| Attributes              | N/D  |
| Referencias              | [Aplicación de SSL en un controlador de API web](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| Pasos | Si una aplicación tiene tanto un enlace HTTP como HTTPS, los clientes aún pueden usar HTTP para acceder al sitio. Para evitar esto, utilice un filtro de acción para asegurarse de que las solicitudes de API protegidas se realizan siempre a través de HTTPS.|

### <a name="example"></a>Ejemplo 
El código siguiente muestra un filtro de autenticación de API web que comprueba el estado de SSL: 
```C#
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Añada este filtro a todas las acciones de API web que requieran SSL: 
```C#
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="a-idredis-sslaensure-that-communication-to-azure-redis-cache-is-over-ssl"></a><a id="redis-ssl"></a>Comprobación de que la comunicación a Azure Redis Cache se realiza a través de SSL

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Caché en Redis de Azure | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [Soporte técnico de Azure Redis SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| Pasos | El servidor de Redis no admite SSL desde el principio, pero hace Caché en Redis de Azure. Si se conecta a Caché en Redis de Azure y el cliente admite SSL, como StackExchange.Redis, deberá utilizar SSL. De forma predeterminada, el puerto no SSL está deshabilitado para las instancias nuevas de Azure Redis Cache. Asegúrese de que no se modifiquen los valores predeterminados seguros a menos que exista una dependencia de compatibilidad con SSL para los clientes de Redis. |

Tenga en cuenta que Redis está diseñado para que puedan acceder clientes de confianza dentro de entornos de confianza. Por ello, normalmente no es recomendable exponer la instancia de Redis directamente a Internet o, en general, a un entorno desde el que clientes que no sean de confianza puedan acceder directamente el puerto TCP de Redis o a un socket de UNIX. 

## <a name="a-iddevice-fieldasecure-device-to-field-gateway-communication"></a><a id="device-field"></a>Protección de la comunicación entre el dispositivo y la puerta de enlace de campo

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Puerta de enlace de campo de IoT | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | N/D  |
| Pasos | En el caso de dispositivos basados en IP, normalmente es posible encapsular el protocolo de comunicación en un canal SSL/TLS para proteger los datos en tránsito. Para otros protocolos que no admitan SSL/TLS, investigue si existen versiones seguras de esos protocolos que proporcionen seguridad en el nivel de transporte o de mensaje. |

## <a name="a-iddevice-cloudasecure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Protección de la comunicación entre el dispositivo y la puerta de enlace de nube mediante SSL/TLS

| Título                   | Detalles      |
| ----------------------- | ------------ |
| Componente               | Puerta de enlace de la nube de IoT | 
| Fase de SDL               | Compilación |  
| Tecnologías aplicables | Genérico |
| Attributes              | N/D  |
| Referencias              | [Elección del protocolo de comunicación](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| Pasos | Proteja los protocolos HTTP/AMQP o MQTT mediante SSL/TLS. |

