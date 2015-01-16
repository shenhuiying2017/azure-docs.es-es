<properties urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Introducción a las conexiones híbridas | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Obtenga información acerca de las conexiones híbridas, incluida la seguridad." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="mandia" />



# Introducción a las conexiones híbridas
En este tema se presentan las conexiones híbridas, se muestran las configuraciones admitidas y se indican los puertos TCP requeridos. Concretamente:

- [¿Qué es una conexión híbrida?](#HCOverview)
- [Configuraciones admitidas](#KnownIssues)
- [Seguridad y puertos](#HCSecurity)

##<a name="HCOverview"></a>¿Qué es una conexión híbrida?

Las conexiones híbridas ofrecen un modo sencillo y práctico de conectar sitios web y servicios móviles de Azure a recursos locales. Las conexiones híbridas son una característica de los servicios de BizTalk de Azure:

![Hybrid Connections][HCImage]

Las ventajas de las conexiones híbridas incluyen:

- Los sitios web y los servicios móviles pueden acceder a los datos y servicios locales existentes de forma segura.
- Varios sitios web o servicios móviles pueden compartir una conexión híbrida para acceder a un recurso local. 
- Se requieren puertos TCP mínimos para acceder a la red.
- Las aplicaciones que usan conexiones híbridas solo acceden al recurso local específico que se publica a través de la conexión híbrida.
- Pueden conectarse a cualquier recurso local que use un puerto TCP estático, como SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.

> [WACOM.NOTE] Los servicios basados en TCP que usan puertos dinámicos (como modo pasivo FTP o modo pasivo extendido) no se admiten actualmente.

- Se pueden usar con todos los marcos de trabajo compatibles con Sitios web Azure (.NET, PHP, Java, Python, Node.js) y Servicios móviles de Azure (Node.js, .NET).
- Los sitios web y los servicios móviles pueden acceder a recursos locales exactamente de la misma manera que si el sitio web o el servicio móvil estuviera situado en la red local. Por ejemplo, la misma cadena de conexión usada en el entorno local se puede utilizar en Azure.


Las conexiones híbridas también ofrecen a los administradores de empresa control y visibilidad sobre los recursos corporativos a los que acceden las aplicaciones híbridas, por ejemplo:

- Mediante el uso de la configuración de directiva de grupo, los administradores pueden permitir conexiones híbridas en la red y designar también recursos a los que pueden acceder las aplicaciones híbridas.
- Los registros de eventos y de auditoría de la red corporativa proporcionan visibilidad sobre los recursos a los que acceden las conexiones híbridas.


##<a name="KnownIssues"></a>Configuraciones admitidas

Las conexiones híbridas admiten las siguientes combinaciones de aplicaciones y marcos de trabajo:

- Acceso del marco de trabajo .NET a SQL Server
- Acceso del marco de trabajo .NET a los servicios HTTP/HTTPS con WebClient
- Acceso de PHP a SQL Server, MySQL
- Acceso de Java a SQL Server, MySQL y Oracle
- Acceso de Java a los servicios HTTP/HTTPS

A la hora de usar conexiones híbridas para acceder a SQL Server local, tenga en cuenta los siguientes aspectos:

- Las instancias con nombre de SQL Express se deben configurar para usar puertos estáticos. De forma predeterminada, las instancias con nombre de SQL Express usan puertos dinámicos.
- Las instancias predeterminadas de SQL Express usan un puerto estático, pero TCP debe estar habilitado. De forma predeterminada, TCP no está habilitado.
- Al usar la organización en clústeres o los grupos de disponibilidad, no se admite actualmente el modo MultiSubnetFailover=true.
- El modo ApplicationIntent=ReadOnly no se admite actualmente.
- Puede que se requiera la autenticación de SQL como método de autorización completo que se admite en la aplicación de Azure y en el servidor SQL local.


##<a name="HCSecurity"></a>Seguridad y puertos

Las conexiones híbridas emplean la autorización de firma de acceso compartido (SAS) para proteger las conexiones entre las aplicaciones de Azure y el Administrador de conexiones híbridas y la conexión híbrida. Se crean claves de conexión diferentes para la aplicación y el Administrador de conexiones híbridas local. Estas claves de conexión se pueden sustituir y revocar de manera independiente.

Las conexiones híbridas proporcionan una distribución adecuada y segura de las claves a las aplicaciones y el Administrador de conexiones híbridas local. 

Consulte [Creación y administración de conexiones híbridas](http://azure.microsoft.com/es-es/documentation/articles/integration-hybrid-connection-create-manage). 

**La autorización de la aplicación es independiente de la conexión híbrida**. Se puede usar cualquier método de autorización adecuado. El método de autorización depende de los métodos de autorización completos que se admitan en la nube de Azure y de los componentes locales. Por ejemplo, su aplicación de Azure accede a un servidor SQL local. En este escenario, la autorización de SQL puede ser el método de autorización que se admita completamente.

####Puertos TCP
Las conexiones híbridas requieren únicamente conectividad TCP o HTTP de su red privada. No es necesario abrir los puertos de firewall ni cambiar la configuración del perímetro de red para permitir la conectividad entrante a la red.

Los siguientes puertos TCP se usan en las conexiones híbridas:

<table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Porqué</th>
    </tr>
    <tr>
        <td>80</td>
        <td>Puerto HTTP; usado para la validación del certificado.</td>
    </tr>
    <tr>
        <td>443</td>
        <td>Puerto HTTPS</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>Usado para conectarse a Azure. Si el puerto TCP 5671 no está disponible, se usa el puerto TCP 443.</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>Se usa para insertar y extraer datos. Si el puerto TCP 9352 no está disponible, se usa el puerto TCP 443.</td>
	</tr>
</table>


## Pasos siguientes

- [Creación y administración de conexiones híbridas](http://azure.microsoft.com/es-es/documentation/articles/integration-hybrid-connection-create-manage)
- [Conexión de un sitio web de Azure a un recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Conexiones híbridas paso a paso: Conexión a SQL Server local desde un sitio web de Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Servicios móviles de Azure y conexiones híbridas](http://azure.microsoft.com/es-es/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)


## Otras referencias

- [API REST para administrar los servicios de BizTalk en Windows Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [Servicios de BizTalk: Gráfico de ediciones](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Creación un servicio de BizTalk mediante el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
