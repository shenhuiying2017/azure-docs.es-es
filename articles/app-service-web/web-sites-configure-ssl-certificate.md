<properties
	pageTitle="Protección del dominio personalizado de la aplicación con HTTPS | Microsoft Azure"
	description="Aprenda cómo proteger el nombre de dominio personalizado para la aplicación en el Servicio de aplicaciones de Azure mediante la configuración de un enlace de certificado SSL. También aprenderá cómo obtener un certificado SSL desde varias herramientas."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cephalin"/>

# Protección del dominio personalizado de la aplicación con HTTPS


> [AZURE.SELECTOR]
- [Compra de un certificado SSL en Azure](web-sites-purchase-ssl-web-site.md)
- [Uso del certificado SSL desde cualquier lugar](web-sites-configure-ssl-certificate.md)


En este artículo se muestra cómo habilitar HTTPS para una aplicación web, un back-end de aplicación móvil o una aplicación de API en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md), que usa un nombre de dominio personalizado. Se ocupa de la autenticación solo del servidor. Si necesita la autenticación mutua (incluida la autenticación del cliente), consulte [Configuración de la autenticación mutua de TLS para el Servicio de aplicaciones](app-service-web-configure-tls-mutual-auth.md).

Para proteger con HTTPS una aplicación que tiene un nombre de dominio personalizado, agregue un certificado para ese nombre de dominio. De forma predeterminada, Azure protege el dominio con comodín ***.azurewebsites.net** con un certificado SSL único para que los clientes puedan acceder a su aplicación en **https://*&lt;appname>*.azurewebsites.net**. Pero si desea utilizar un dominio personalizado, como **contoso.com**, **www.contoso.com** y ***.contoso.com**, el certificado predeterminado no puede protegerlo. Además, al igual que todos los [certificados con comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), el certificado predeterminado no es tan seguro como utilizar un dominio personalizado y un certificado para él.

>[AZURE.NOTE] Puede obtener ayuda de expertos de Azure en cualquier momento en los [foros de Azure](https://azure.microsoft.com/support/forums/). Para obtener un soporte técnico más personalizado, vaya a [Soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

<a name="bkmk_domainname"></a>
## Lo que necesita
Para proteger su nombre de dominio personalizado con HTTPS, enlace un certificado SSL personalizado para ese dominio personalizado en Azure. Antes de enlazar un certificado personalizado, debe hacer lo siguiente:

- **Configurar el dominio personalizado**: el Servicio de aplicaciones solo permite agregar un certificado para un nombre de dominio que ya está configurado en la aplicación. Para ver instrucciones, consulte [Asignación de un nombre de dominio personalizado a una aplicación de Azure](web-sites-custom-domain-name.md).
- **Escalar verticalmente al nivel básico o superior**: los planes del Servicio de aplicaciones en los niveles de precios más bajos no son compatibles con los certificados SSL personalizados. Para ver instrucciones, consulte [Escalado vertical de aplicaciones en Azure](web-sites-scale.md).
- **Obtener un certificado SSL**: si aún no tiene ninguno, necesitará obtener uno en una [entidad de certificación](http://en.wikipedia.org/wiki/Certificate_authority) (CA) de confianza. El certificado debe cumplir todos los requisitos siguientes:

	- Está firmado por una CA de confianza (sin servidores de CA privados).
	- Contiene una clave privada.
	- Se ha creado para el intercambio de claves y se ha exportado a un archivo PFX.
	- Usa como mínimo un cifrado de 2048 bits.
	- El nombre de su asunto coincide con el dominio personalizado que debe proteger. Para proteger varios dominios con un certificado, debe usar un nombre con comodín (por ejemplo, ***.contoso.com**) o especificar valores subjectAltName.
	- Se combina con todos los **[certificados intermedios](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** utilizados por la entidad de certificación. De lo contrario, puede encontrarse con problemas de interoperabilidad irreproducibles en algunos clientes.

		>[AZURE.NOTE] La forma más sencilla de obtener un certificado SSL que cumple todos los requisitos es [comprar uno en el Portal de Azure directamente](web-sites-purchase-ssl-web-site.md). En este artículo se muestra cómo hacerlo de forma manual y, a continuación, enlazarlo a su dominio personalizado en el Servicio de aplicaciones.
		>	
		> Los **certificados de criptografía de curva elíptica (ECC)** pueden trabajar con el Servicio de aplicaciones, pero están fuera del ámbito de este artículo. Trabaje con la entidad de certificación sobre los pasos exactos para crear los certificados ECC.

<a name="bkmk_getcert"></a>
## Paso 1. Obtener un certificado SSL

Puesto que las entidades de certificación proporcionan los distintos tipos de certificados SSL a diferentes precios, debe empezar por decidir qué tipo de certificado SSL para comprar. Para proteger un nombre de dominio (**www.contoso.com**), solo necesitará un certificado básico. Si necesita proteger varios nombres de dominio, (**contoso.com** *,* **www.contoso.com** *y* **mail.contoso.com**), puede obtener un [certificado con comodín](http://en.wikipedia.org/wiki/Wildcard_certificate) o un [certificado con un nombre de sujeto alternativo](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Una vez que sepa qué certificado SSL desea comprar, envíe una Solicitud de firma de certificado (CSR) a una entidad de certificación. Cuando recibe el certificado solicitado de la entidad de certificación, genere un archivo .pfx a partir del certificado. Puede realizar estos pasos con la herramienta de su elección. A continuación se proporcionan instrucciones para las herramientas comunes:

- [Pasos para Certreq.exe](#bkmk_certreq): utilidad de Windows para crear solicitudes de certificado. Ha formado parte de Windows desde Windows XP y Windows Server 2000.
- [Pasos para IIS Manager](#bkmk_iismgr): la herramienta preferida si ya está familiarizado con ella.
- [Pasos para OpenSSL](#bkmk_openssl): [herramienta de código abierto y multiplataforma](https://www.openssl.org). Úsela para ayudarle a obtener un certificado SSL desde cualquier plataforma.
- [Pasos para subjectAltName con OpenSSL](#bkmk_subjectaltname): pasos para obtener certificados `subjectAltName`.

Si desea probar la configuración en el Servicio de aplicaciones antes de comprar un certificado, puede generar un [certificado autofirmado](https://en.wikipedia.org/wiki/Self-signed_certificate). Este tutorial le ofrece dos formas de generarlo:

- [Pasos para un certificado autofirmado, Certreq.exe](#bkmk_sscertreq)
- [Pasos para un certificado autofirmado, OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### Obtención de un certificado con Certreq.exe

1. Cree un archivo (por ejemplo, **myrequest.txt**), copie en él el texto siguiente y guárdelo en un directorio de trabajo. Reemplace el marcador de posición `<your-domain>` por el nombre de dominio personalizado de la aplicación.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; Required minimum is 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	Para más información sobre las opciones en el archivo CSR, así como otras opciones disponibles, consulte la [documentación de referencia de Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. En un símbolo del sistema, vaya con `CD` al directorio de trabajo y ejecute el siguiente comando para crear el archivo CSR:

		certreq -new myrequest.txt myrequest.csr

	**myrequest.csr** se ha creado en el directorio de trabajo actual.

5. Envíe **myrequest.csr** a una entidad de certificación para obtener un certificado SSL. Cargue el archivo o copie su contenido desde un editor de texto en un formulario web.

	Para obtener una lista de las entidades de certificación de confianza de Microsoft, consulte el [programa de certificados raíz de confianza de Microsoft: participantes][cas].

6. Una vez que la entidad de certificación le ha respondido con un archivo de certificado (.CER), guárdelo en el directorio de trabajo. A continuación, ejecute el comando siguiente para completar el archivo CSR pendiente.

		certreq -accept -user <certificate-name>.cer

	Este comando almacena el certificado terminado en el almacén de certificados de Windows.

6. Si la entidad de certificación utiliza certificados intermedios, instálelos antes de continuar. Normalmente se proporciona como una descarga independiente de la CA y en varios formatos para los diferentes tipos de servidor web. Seleccione la versión de Microsoft IIS.

	Una vez descargados los certificados, haga clic con el botón derecho en cada uno de ellos en el Explorador de Windows y seleccione **Instalar certificado**. Use los valores predeterminados del **Asistente para importación de certificados** y seleccione repetidamente **Siguiente** hasta que la importación haya finalizado.

7. Para exportar el certificado SSL desde el almacén de certificados, presione `Win`+`R` y ejecute **certmgr.msc** para iniciar el Administrador de certificados. Seleccione **Personal** > **Certificados**. En la columna **Emitido para**, verá una entrada con el nombre de su dominio personalizado y la entidad de certificación que usó para generar el certificado en la columna **Emitido por**.

	![Insertar imagen del administrador de certificados aquí][certmgr]

9. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar**. En el **Asistente para exportar certificados**, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y vuelva a hacer clic en **Siguiente**.

	![Exportar la clave privada][certwiz1]

10. Seleccione **Intercambio de información personal: PKCS #12**, **Si es posible, incluir todos los certificados en la ruta de acceso de certificación** y **Exportar todas las propiedades extendidas**. A continuación, haga clic en **Siguiente**.

	![incluir todos los certificados y propiedades extendidas][certwiz2]

11. Seleccione **Contraseña** y, a continuación, escriba y confirme la contraseña. Haga clic en **Siguiente**.

	![especificar una contraseña][certwiz3]

12. Proporcione una ruta y un nombre de archivo para el certificado exportado, con la extensión **.pfx**. Haga clic en **Siguiente** para finalizar.

	![proporcionar una ruta de archivo][certwiz4]

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>
### Obtención de un certificado con el Administrador de IIS

1. Genere un archivo CSR con el Administrador de IIS para enviarlo a la entidad de certificación. Para obtener más información acerca de la generación de solicitudes CSR, consulte [Solicitar un certificado de servidor de Internet (IIS 7)][iiscsr].

3. Envíe el CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de las entidades de certificación de confianza de Microsoft, consulte el [programa de certificados raíz de confianza de Microsoft: participantes][cas].


3. Complete el CSR con el certificado que la CA le envía. Para obtener más información acerca de la realización de solicitudes CSR, consulte [Instalación de un certificado de servidor de Internet (IIS 7)][installcertiis].

4. Si la entidad de certificación utiliza certificados intermedios, instálelos antes de continuar. Normalmente se proporciona como una descarga independiente de la CA y en varios formatos para los diferentes tipos de servidor web. Seleccione la versión de Microsoft IIS.

	Una vez descargados los certificados, haga clic con el botón derecho en cada uno de ellos en el Explorador de Windows y seleccione **Instalar certificado**. Use los valores predeterminados del **Asistente para importación de certificados** y seleccione repetidamente **Siguiente** hasta que la importación haya finalizado.

4. Exporte el certificado SSL desde el Administrador de IIS. Para más información acerca de cómo exportar el certificado, consulte [Export a Server Certificate (IIS 7)][exportcertiis] (Exportación de un certificado de servidor [IIS 7]).

	>[AZURE.IMPORTANT] En el **Asistente para exportar certificados**, asegúrese de seleccionar **Exportar la clave privada**
	>
	>![Exportar la clave privada][certwiz1]
	>
	> y seleccione también **Intercambio de información personal: PKCS #12**, **Si es posible, incluir todos los certificados en la ruta de acceso de certificación** y **Exportar todas las propiedades extendidas**.
	>
	>![incluir todos los certificados y propiedades extendidas][certwiz2]

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_openssl"></a>
### Obtención de un certificado con OpenSSL

1. En un terminal de la línea de comandos, vaya con `CD` a un directorio de trabajo y genere una clave privada y un CSR mediante el comando siguiente:

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	Cuando haya terminado, debería tener dos archivos en el directorio de trabajo: **myserver.key** y **server.csr**. El archivo **server.csr** contiene el CSR, y necesitará **myserver.key** más adelante.

3. Envíe el CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de las entidades de certificación de confianza de Microsoft, consulte el [programa de certificados raíz de confianza de Microsoft: participantes][cas].


4. Una vez que la entidad de certificación le haya enviado el certificado solicitado, guárdelo en un archivo denominado **myserver.crt** en el directorio de trabajo. Si la CA lo proporciona en un formato de texto, simplemente copie el contenido en **myserver.crt** en un editor de texto y guárdelo. El archivo debería tener este aspecto:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. En el terminal de la línea de comandos, ejecute el siguiente comando para exportar **myserver.pfx** desde **myserver.key** y **myserver.crt**:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	> [AZURE.NOTE] Si la entidad de certificación utiliza certificados intermedios, debe incluirlos con el parámetro `-certfile`. Normalmente se proporciona como una descarga independiente de la CA y en varios formatos para los diferentes tipos de servidor web. Seleccione la versión con la extensión `.pem`.
	>
	> Su comando `openssl -export` debe ser similar al ejemplo siguiente, que crea un archivo .pfx que incluye los certificados intermedios del archivo **intermediate-cets.pem**:
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>
### Obtención de un certificado SubjectAltName con OpenSSL

1. Cree un archivo denominado **sancert.cnf**, copie el texto siguiente en él y guardarlo en un directorio de trabajo:

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	En la línea que comienza con `subjectAltName`, reemplace el valor por todos los nombres de dominio que desea proteger (además de `commonName`). Por ejemplo:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	No es necesario cambiar ningún otro campo, ni siquiera `commonName`. Se le pedirá que los especifique en los pasos siguientes.

1. En un terminal de la línea de comandos, vaya con `CD` al directorio de trabajo y ejecute el siguiente comando:

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

	Cuando haya terminado, debería tener dos archivos en el directorio de trabajo: **myserver.key** y **server.csr**. El archivo **server.csr** contiene el CSR, y necesitará **myserver.key** más adelante.

3. Envíe el CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de las entidades de certificación de confianza de Microsoft, consulte el [programa de certificados raíz de confianza de Microsoft: participantes][cas].


4. Una vez que la entidad de certificación le envía el certificado solicitado, guárdelo en un archivo denominado **myserver.crt**. Si la CA lo proporciona en un formato de texto, simplemente copie el contenido en **myserver.crt** en un editor de texto y guárdelo. El archivo debería tener este aspecto:

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. En el terminal de la línea de comandos, ejecute el siguiente comando para exportar **myserver.pfx** desde **myserver.key** y **myserver.crt**:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	> [AZURE.NOTE] Si la entidad de certificación utiliza certificados intermedios, debe incluirlos con el parámetro `-certfile`. Normalmente se proporciona como una descarga independiente de la CA y en varios formatos para los diferentes tipos de servidor web. Seleccione la versión con la extensión `.pem`.
	>
	> Su comando `openssl -export` debe ser similar al ejemplo siguiente, que crea un archivo .pfx que incluye los certificados intermedios del archivo **intermediate-cets.pem**:
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>
### Generación de un certificado autofirmado con Certreq.exe ###

>[AZURE.IMPORTANT] Los certificados autofirmados son solo para prueba. La mayoría de los exploradores devuelven errores cuando se visita un sitio web que está protegido por un certificado autofirmado. Algunos exploradores pueden incluso denegarle el acceso al sitio.

1. Cree un archivo de texto (por ejemplo, **mycert.txt**), copie en él el texto siguiente y guárdelo en un directorio de trabajo. Reemplace el marcador de posición `<your-domain>` por el nombre de dominio personalizado de la aplicación.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256
		RequestType = Cert            ; Self-signed certificate
		ValidityPeriod = Years
		ValidityPeriodUnits = 1

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	El parámetro importante es `RequestType = Cert`, que especifica un certificado autofirmado. Para más información sobre las opciones en el archivo CSR, así como otras opciones disponibles, consulte la [documentación de referencia de Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. En el símbolo del sistema, vaya con `CD` al directorio de trabajo y ejecute el siguiente comando:

		certreq -new mycert.txt mycert.crt
	
	Ahora el nuevo certificado autofirmado está instalado en el almacén de certificados.

7. Para exportar el certificado desde el almacén de certificados, presione `Win`+`R` y ejecute **certmgr.msc** para iniciar el Administrador de certificados. Seleccione **Personal** > **Certificados**. En la columna **Emitido para**, verá una entrada con el nombre de su dominio personalizado y la entidad de certificación que usó para generar el certificado en la columna **Emitido por**.

	![Insertar imagen del administrador de certificados aquí][certmgr]

9. Haga clic con el botón derecho en el certificado, seleccione **Todas las tareas** > **Exportar**. En el **Asistente para exportar certificados**, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y vuelva a hacer clic en **Siguiente**.

	![Exportar la clave privada][certwiz1]

10. Seleccione **Intercambio de información personal: PKCS #12**, **Si es posible, incluir todos los certificados en la ruta de acceso de certificación** y **Exportar todas las propiedades extendidas**. A continuación, haga clic en **Siguiente**.

	![incluir todos los certificados y propiedades extendidas][certwiz2]

11. Seleccione **Contraseña** y, a continuación, escriba y confirme la contraseña. Haga clic en **Siguiente**.

	![especificar una contraseña][certwiz3]

12. Proporcione una ruta y un nombre de archivo para el certificado exportado, con la extensión **.pfx**. Haga clic en **Siguiente** para finalizar.

	![proporcionar una ruta de archivo][certwiz4]

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>
###Generación de un certificado autofirmado con OpenSSL ###

>[AZURE.IMPORTANT] Los certificados autofirmados son solo para prueba. La mayoría de los exploradores devuelven errores cuando se visita un sitio web que está protegido por un certificado autofirmado. Algunos exploradores pueden incluso denegarle el acceso al sitio.

1. Cree un archivo de texto denominado **serverauth.cnf**, copie el siguiente contenido en él y guárdelo en un directorio de trabajo:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. En un terminal de la línea de comandos, vaya con `CD` al directorio de trabajo y ejecute el siguiente comando:

		openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Este comando crea dos archivos: **myserver.crt** (el certificado autofirmado) y **myserver.key** (la clave privada), en función de la configuración **serverauth.cnf**.

3. Ejecute el comando siguiente, exporte el certificado a un archivo .pfx:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

Ahora está listo para cargar el archivo PFX exportado en el Servicio de aplicaciones. Consulte [Paso 2. Cargar y enlazar el certificado SSL personalizado](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>
## Paso 2: Cargar y enlazar el certificado SSL personalizado

Antes de continuar, revise la sección [Lo que necesita](#bkmk_domainname) y compruebe que:

- Tiene un dominio personalizado que se asigna a la aplicación de Azure.
- La aplicación se ejecuta en un nivel **Básico** o superior.
- Tiene un certificado SSL para el dominio personalizado de una entidad de certificación.
 
1.	En el [portal de Azure](https://portal.azure.com), vaya a la hoja **Dominios personalizados y SSL** de la aplicación.

7.	Haga clic en **Más** > **Cargar certificados**.

	![](./media/web-sites-configure-ssl-certificate/sslupload.png)

8.	Seleccione el archivo .pfx que exportó en el [Paso 1](#bkmk_getcert) y especifique la contraseña que creó antes. A continuación, haga clic en **Guardar** para cargar el certificado. Ahora debería ver su certificado cargado en la hoja **Dominios personalizados y SSL**.

	![](./media/web-sites-configure-ssl-certificate/sslcertview.png)

9. En la sección **Enlaces SSL**, seleccione el nombre de dominio y el certificado SSL que se enlazarán. También puede seleccionar si desea utilizar SSL SNI o SSL basada en IP.

	![](./media/web-sites-configure-ssl-certificate/sslbindcert.png)

	* **SSL basada en IP** enlaza un certificado a un nombre de dominio mediante una dirección IP pública dedicada de la aplicación al nombre de dominio. Este es el método tradicional de realizar enlaces SSL y el Servicio de aplicaciones crea una dirección IP dedicada para el enlace.

	* [**SSL SNI**](https://en.wikipedia.org/wiki/Server_Name_Indication) permite realizar enlaces de varios certificados a varios dominios. Los exploradores más modernos (incluidos Internet Explorer, Chrome, Firefox y Safari) son compatibles con ella; pero es posible que los exploradores más antiguos no lo sean.
 
10. Haga clic en **Guardar** para finalizar.

## Paso 3: Cambiar la asignación de nombres de dominio (solo SSL basada en IP)

Si solo utiliza enlaces **SSL SNI**, omita esta sección. Varios enlaces **SSL SNI** pueden trabajar juntos en la dirección IP compartida existente asignada a la aplicación. Sin embargo, si crea un enlace **SSL basada en IP**, el Servicio de aplicaciones crea una dirección IP dedicada para el enlace porque la **SSL basada en IP** requiere una. Debido a esta dirección IP dedicada, debe configurar la aplicación más si:

- Ha [usado un registro A para asignar su dominio personalizado](web-sites-custom-domain-name.md#a) a la aplicación de Azure y acaba de agregar un enlace **SSL basada en IP**. En este escenario, debe reasignar el registro A existente para que apunte a la dirección IP dedicada siguiendo estos pasos:

	1. Después de haber configurado un enlace SSL basada en IP, busque la nueva dirección IP en la hoja **Configuración** > **Propiedades** de la aplicación (la dirección IP virtual que se muestra en la hoja **Traer dominios externos** puede no estar actualizada):
    
	    ![Dirección IP virtual](./media/web-sites-configure-ssl-certificate/staticip.png)

	2. [Reasigne el registro A de su nombre de dominio personalizado para esta nueva dirección IP](web-sites-custom-domain-name.md#a).

- Ya tiene uno o más enlaces **SSL SNI** en la aplicación y acaba de agregar un enlace **SSL basada en IP**. Una vez completado el enlace, el nombre de dominio *&lt;nombreDeAplicación>*.azurewebsites.net apunta a la nueva dirección IP. Por lo tanto, cualquier [asignación de CNAME desde el dominio personalizado](web-sites-custom-domain-name.md#cname) existente a *& lt;nombreDeAplicación>*.azurewebsites.net, incluidos a los que protege la **SSL SNI**, también recibe el tráfico en la nueva dirección, que se crea solo para la **SSL basada en IP**. En este escenario, necesita enviar el tráfico de **SSL SNI** de nuevo a la dirección IP original compartida siguiendo estos pasos:

	1. Identifique todas las [asignaciones de CNAME de dominios personalizados](web-sites-custom-domain-name.md#cname) a la aplicación que tiene un enlace **SSL SNI**.

	2. Vuelva a asignar cada registro de CNAME a **sni.**&lt;nombreDeAplicación>.azurewebsites.net en lugar de &lt;nombreDeAplicación>.azurewebsites.net.

## Paso 4 Probar HTTPS para el dominio personalizado

Ahora todo lo que queda por hacer es asegurarse de que HTTPS funciona para el dominio personalizado. En varios exploradores, vaya a `https://<your.custom.domain>` para ver que da servicio a la aplicación.

- Si la aplicación genera errores de validación del certificado, probablemente esté utilizando un certificado autofirmado.

- Si no es así, puede haber dejado certificados intermedios cuando exportó el certificado .pfx. Vuelva a [Lo que necesita](#bkmk_domainname) para comprobar que su CSR cumple todos los requisitos del Servicio de aplicaciones.

<a name="bkmk_enforce"></a>
## Implementar HTTPS en la aplicación

Si desea permitir el acceso HTTP a la aplicación, omita este paso. El Servicio de aplicaciones *no* fuerza HTTPS, por lo que los visitantes aún pueden acceder a la aplicación mediante HTTP. Si desea forzar HTTPS para su aplicación, puede definir una regla de reescritura en el archivo `web.config` para la aplicación. Todas las aplicaciones del Servicio de aplicaciones tienen este archivo, independientemente de la plataforma del lenguaje de la aplicación.

> [AZURE.NOTE] Hay una redirección específica del lenguaje de las solicitudes. ASP.NET MVC puede usar el filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) en lugar de la regla de reescritura en `web.config` (consulte [Implementar una aplicación ASP.NET MVC 5 segura en una aplicación web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).

Siga estos pasos:

1. Vaya a la consola de depuración Kudu para la aplicación. La dirección es `https://<appname>.scm.azurewebsites.net/DebugConsole`.

2. En la consola de depuración, use CD para ir a `D:\home\site\wwwroot`.

3. Abra `web.config` haciendo clic en el botón del lápiz.

	![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

	Si implementa la aplicación con Visual Studio o Git, el Servicio de aplicaciones genera automáticamente el elemento `web.config` adecuado para la aplicación. NET, PHP, Node.js o Python en la raíz de la aplicación. Si `web.config` no existe, ejecute `touch web.config` en el símbolo del sistema basado en web para crearlo. O bien, puede crearlo en su proyecto local y volver a implementar el código.

4. Si tuviera que crear un elemento `web.config`, copie el código siguiente en él y guárdelo. Si ha abierto un elemento web.config existente, basta con copiar todo la etiqueta `<rule>` en el elemento `configuration/system.webServer/rewrite/rules` de su `web.config`.

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <rewrite>
		      <rules>
			    <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
		        <rule name="Force HTTPS" enabled="true">
		          <match url="(.*)" ignoreCase="false" />
		          <conditions>
		            <add input="{HTTPS}" pattern="off" />
		          </conditions>
		          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
		        </rule>
				<!-- END rule TAG FOR HTTPS REDIRECT -->
		      </rules>
		    </rewrite>
          </system.webServer>
		</configuration>

	Esta regla devuelve un código HTTP 301 (redirección permanente) para el protocolo HTTPS siempre que el usuario solicita una página mediante HTTP. Redirige de http://contoso.com a https://contoso.com.

	>[AZURE.IMPORTANT] Si ya tiene otras etiquetas `<rule>` en su `web.config`, coloque la etiqueta `<rule>` copiada antes que las otras etiquetas `<rule>`.

4. Guarde el archivo en la consola de depuración Kudu. Debe empezar a redirigir todas las solicitudes HTTPS inmediatamente.

Para obtener más información sobre el módulo URL Rewrite de IIS, consulte la documentación de [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite).

## Más recursos ##
- [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en Sitios web Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activación del registro de diagnósticos](web-sites-enable-diagnostic-log.md)
- [Configuración de Aplicaciones web en Servicio de aplicaciones de Azure](web-sites-configure.md)
- [Portal de administración de Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png

<!---HONumber=AcomDC_0810_2016-->