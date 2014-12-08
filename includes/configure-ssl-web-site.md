#Habilitación de HTTPS en un sitio web de Azure

> [WACOM.NOTE]
> Póngase en marcha más rápido: use el NUEVO [tutorial guiado](http://support.microsoft.com/kb/2990804) de Azure.  Con este tutorial, asociar un nombre de dominio personalizado y proteger las comunicaciones (SSL) con los Servicios en la nube de Azure o Sitios web Azure es facilísimo.

Puede proteger la comunicación entre el sitio web y el explorador con HTTPS, que utiliza el cifrado de capa de sockets seguros (SSL). Este es el método más usado para proteger los datos que se envían por Internet y asegura a los visitantes la protección de las transacciones que realizan en su sitio. Este artículo analiza la configuración de HTTPS en un sitio web de Azure. 

<a href="bkmk_azurewebsites"></a><h2>HTTPS para el dominio \*.azurewebsites.net</h2>

Si en lugar de usar un nombre de dominio personalizado tiene previsto usar el dominio \*.azurewebsites.net que Azure ha asignado a su sitio web (por ejemplo, contoso.azurewebsites.net), entonces HTTPS ya está habilitado en el sitio con un certificado de Microsoft. Puede usar **https://mywebsite.azurewebsites.net** para obtener acceso al sitio. Sin embargo, \*.azurewebsites.net es un dominio comodín. Al igual que [todos los dominios comodín](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/),no es tan seguro como utilizar un dominio personalizado con su propio certificado. 

El resto de este documento proporciona información acerca de la habilitación de HTTPS para dominios personalizados, como **contoso.com**, **www.contoso.com** o **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Habilitación de SSL para el dominio personalizado</h2>

Para habilitar HTTPS en un nombre de dominio personalizado, como **contoso.com**, primero debe registrar un nombre de dominio personalizado en un registrador de nombres de dominio. Para obtener más información acerca de la configuración del nombre de dominio de un sitio web de Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure](/es-es/develop/net/common-tasks/custom-dns-web-site/). Una vez que haya registrado un nombre de dominio personalizado y configurado su sitio web para que se corresponda con el nombre personalizado, debe solicitar un certificado SSL para el dominio. 

> [WACOM.NOTE] Para habilitar HTTPS para nombres de dominio personalizados, debe configurar su sitio web para el modo de plan de hospedaje web **estándar**. Si actualmente usa un modo gratuito o compartido, es posible que el cambio de modo suponga costes adicionales. Para obtener más información acerca de los precios para los modos **estándar** y compartido, consulte [Información sobre el precio][pricing]. 

Si ya dispone de un dominio personalizado válido, la habilitación de HTTPS para el sitio web consta de los pasos siguientes:

1. [Obtener un certificado SSL](#bkmk_getcert)
1. [Configuración del modo estándar](#bkmk_standardmode)
1. [Configuración de SSL](#bkmk_configuressl)
1. [Aplicación de HTTPS en el sitio web de Azure](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>Obtención de un certificado SSL</h2>

Antes de solicitar un certificado SSL, debe determinar los nombres de dominio que contarán con la protección del certificado. De este modo, se determinará el tipo de certificado que debe obtener. Si solo necesita proteger un nombre de dominio, como **contoso.com** o **www.contoso.com**, es suficiente con un certificado básico. Si necesita proteger varios nombres de dominio, como **contoso.com**, **www.contoso.com** y **mail.contoso.com**, puede obtener un [certificado comodín](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificado con [Nombre alternativo de sujeto](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

Los certificados SSL utilizados con Sitios web de Azure deben estar firmados por una [entidad de certificación](http://en.wikipedia.org/wiki/Certificate_authority) (CA). Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que expida certificados SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] en Microsoft TechNet Wiki.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al sitio web. Si necesita ofrecer servicio a varios dominios con este certificado, deberá usar un valor comodín o especificar los valores subjectAltName tal y como se ha descrito previamente.
* Este certificado debe usar un cifrado de 2048 bits como mínimo.
* Los certificados emitidos por servidores de entidades de certificación privados no son compatibles con Sitios web Azure.

Para obtener un certificado SSL para utilizarlo con Sitios web de Azure, envíe una solicitud de firma de certificado (CSR) a una entidad de certificación y, a continuación, genere un archivo .pfx del certificado que se recibe de vuelta. Puede hacerlo mediante la herramienta de su elección. A continuación se muestran algunas de las formas comunes para obtener un certificado:

- [Obtención de un certificado con Certreq.exe](#bkmk_certreq)
- [Obtención de un certificado con el Administrador de IIS](#bkmk_iismgr)
- [Obtención de un certificado con OpenSSL](#bkmk_openssl)
- [Obtención de un certificado SubjectAltName con OpenSSL](#bkmk_subjectaltname)
- [Expedición de certificados autofirmados (solo para pruebas)](#bkmk_selfsigned) 

> [WACOM.NOTE] Al seguir los pasos, se le pedirá que escriba un **nombre común**, como "www.contoso.com". Para los certificados comodín, este valor debe ser \*.domainname (por ejemplo, \*.contoso.com). Si necesita tener compatibilidad con un nombre comodín, como \*.contoso.com, y un nombre de dominio raíz, como contoso.com, puede usar un certificado comodín subjectAltName.

> [WACOM.NOTE] Los certificados de criptografía de curva elíptica (ECC) son compatibles con Sitios web Azure; sin embargo, son relativamente nuevos y debería preguntar a su entidad de certificación a fin de conocer los pasos exactos para crear la solicitud de firma de certificado.

Puede que deba obtener **[certificados intermedios](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (también denominados certificados de cadena), si su entidad de certificación los utiliza. Se considera que el uso de certificados intermedios es más seguro que los "certificados que no pertenecen a una cadena", por lo que las entidades de certificación suelen utilizarlos. Normalmente, los certificados intermedios se pueden descargar de forma independiente desde el sitio web de la entidad de certificación. Los pasos que se muestran en este artículo describen la manera de asegurar que todos los certificados intermedios se combinen con los certificados cargados en su sitio web de Azure. 

<a href="bkmk_certreq"></a>
###Obtención de un certificado con Certreq.exe (solo Windows)

Certreq.exe es una utilidad de Windows para crear solicitudes de certificado. Ha formado parte de la instalación base de Windows desde Windows XP/Windows Server 2000, por lo que debe estar disponible en los sistemas Windows más recientes. Realice los siguientes pasos para obtener un certificado SSL con certreq.exe.

1. Abra el **Bloc de notas** y cree un nuevo documento que contenga lo siguiente: En la línea Subject, sustituya **mysite.com** por el nombre de dominio personalizado de su sitio web. Por ejemplo, Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Para obtener más información acerca de las opciones especificadas anteriormente, así como otras opciones disponibles, consulte la [Documentación de referencia de Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Guarde el archivo de texto como **myrequest.txt**.

3. En la **pantalla de Inicio** o en el **menú Inicio**, ejecute **cmd.exe**.

4. Desde el símbolo del sistema, use el siguiente comando para crear un archivo de solicitud de certificado:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Especifique la ruta de acceso al archivo **myrequest.txt** creado en el paso 1 y la ruta de acceso que se debe usar al crear el archivo **myrequest.csr**.

5. Envíe el archivo **myrequest.csr** a una entidad de certificación para obtener un certificado SSL. Puede que sea necesario cargar el archivo o abrirlo en la aplicación Bloc de notas y pegar los contenidos directamente en el formulario web.

	Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] en Microsoft TechNet Wiki.

6. Una vez que la entidad de certificación le haya proporcionado un archivo de certificado (.CER), guarde este archivo en el equipo usado para generar la solicitud y, a continuación, use el siguiente comando para aceptar la solicitud y completar el proceso de generación del certificado.

		certreq -accept -user mycert.cer

	En este caso, el certificado **mycert.cer** recibido de la entidad de certificación se usará para completar el proceso de firma del certificado. No se creará ningún archivo; en su lugar, el certificado se almacenará en el almacén de certificados de Windows.

6. Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

	Una vez que haya descargado el certificado, haga clic con el botón secundario en el explorador y seleccione **Instalar certificado**. Use los valores predeterminados en el **Asistente para importación de certificados** y, a continuación, seleccione **Siguiente** hasta que la importación haya finalizado.

7. Para exportar el certificado desde el almacén de certificados, ejecute **certmgr.msc** desde la **pantalla de inicio** o desde el **menú Inicio**. Cuando aparezca **Certificate Manager**, expanda la carpeta **Personal** y, a continuación, seleccione **Certificates**. En el campo **Issued To**, busque una entrada con el nombre de dominio personalizado para el que solicitó un certificado. En el campo **Emitido por**, debería mostrarse la entidad de certificación que ha usado para este certificado.

	![insert image of cert manager here][certmgr]

9. Haga clic con el botón secundario en el certificado, seleccione **Todas las tareas** y, a continuación, seleccione **Exportar**. En **Certificate Export Wizard**, haga clic en **Next** y, a continuación, seleccione **Yes, export the private key**. Haga clic en **Next**.

	![Export the private key][certwiz1]

10. Seleccione **Intercambio de información personal - PKCS #12**, **Incluir todos los certificados en la cadena de certificados** y **Exportar todas las propiedades extendidas**. Haga clic en **Next**.

	![include all certs and extended properties][certwiz2]

11. Seleccione **Contraseña** y, a continuación, escriba y confirme la contraseña. Haga clic en **Next**.

	![specify a password][certwiz3]

12. Proporcione una ruta y el nombre del archivo que contendrá el certificado exportado. El nombre de archivo debe tener una extensión **.pfx**. Haga clic en **Siguiente** para completar el proceso.

	![provide a file path][certwiz4]

Ahora puede cargar el archivo PFX exportado en su sitio web de Azure.

<a href="bkmk_openssl"></a>
###Obtención de un certificado con OpenSSL

1. Genere una clave privada y una solicitud de firma de certificado utilizando lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal.

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Escriba los siguientes atributos "adicionales" que se enviarán con su solicitud de certificado

       	Una contraseña compleja []: 

	Una vez finalizado este proceso, debe tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma del certificado.

3. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] en Microsoft TechNet Wiki.

4. Una vez haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo con el nombre **myserver.crt**. Si la entidad de certificación le ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

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

	Guarde el archivo.

5. Desde la línea de comandos, una sesión Bash o una sesión de Terminal, use el siguiente comando para convertir los archivos **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato requerido por Sitios web Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	<div class="dev-callout"> 
	<b>Nota:</b>
	<p>Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión que se le ha proporcionado como archivo PEM (extensión de archivo .pem).</p>
	<p>El siguiente comando muestra la creación de un archivo .pfx, que incluye los certificados intermedios que contiene el archivo <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Después de ejecutar este comando, debería tener un archivo **myserver.pfx** apto para el uso con Sitios web Azure.

###<a name="bkmk_iismgr"></a>Obtención de un certificado con el Administrador de IIS

Si está familiarizado con el Administrador de IIS, puede usarlo para generar un certificado apto para el uso con Sitios web Azure.

1. Genere un CSR con el Administrador de IIS para enviarlo a la entidad de certificación. Para obtener más información acerca de la generación de solicitudes CSR, consulte [Solicitar un certificado de servidor de Internet (IIS 7)][iiscsr].

2. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] en Microsoft TechNet Wiki.

3. Complete la solicitud CSR con el certificado proporcionado por la entidad de certificación. Para obtener más información acerca de la realización de solicitudes CSR, consulte [Instalar un certificado de servidor de Internet (IIS 7)][installcertiis].

4. Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

	Una vez que haya descargado el certificado, haga clic con el botón secundario en el explorador y seleccione **Instalar certificado**. Use los valores predeterminados en el **Asistente para importación de certificados** y, a continuación, seleccione **Siguiente** hasta que la importación haya finalizado.

4. Exporte el certificado desde el Administrador de IIS. Para obtener más información acerca de la exportación del certificado, consulte [Exportar un certificado de servidor (IIS 7)][exportcertiis]. El archivo exportado se cargará en Azure en los pasos posteriores para su uso en su sitio web de Azure.

	<div class="dev-callout"> 
	<b>Nota:</b>
	<p>Durante el proceso de exportación, asegúrese de seleccionar la opción <strong>Exportar la clave privada</strong>. Dicha exportación incluye la clave privada en el certificado exportado.</p>
	</div>

	<div class="dev-callout"> 
	<b>Nota:</b>
	<p>Durante el proceso de exportación, asegúrese de seleccionar las opciones <strong>incluir todos los certificados en la ruta de certificación</strong> y <strong>Exportar todas las propiedades extendidas</strong>. Dicha exportación incluye todos los certificados intermedios, si procede, en el certificado exportado.</p>
	</div>


###<a href="bkmk_subjectaltname"></a>Obtención de un certificado SubjectAltName con OpenSSL

OpenSSL se puede usar para crear una solicitud de certificado que use la extensión SubjectAltName para ser compatible con varios nombres de servidor mediante un único certificado; sin embargo, es necesario contar con un archivo de configuración. Los siguientes pasos describen la creación de un archivo de configuración y su modo de empleo para solicitar un certificado.

1. Cree un nuevo archivo con nombre __sancert.cnf__ y use lo siguiente como contenido del archivo:
 
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

	Observe la línea que empieza por "subjectAltName". Sustituya los nombres de dominio actuales por nombres de dominio que desee que sean admitidos, además del nombre común. Por ejemplo:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	No es necesario que cambie el campo commonName_default, dado que se le solicitará que escriba su nombre común en uno de los siguientes pasos.

2. Guarde el archivo __sancert.cnf__.

1. Genere una clave privada y una solicitud de firma de certificado mediante el archivo de configuración sancert.cnf. Desde una sesión Bash o una sesión de Terminal, use el siguiente comando:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Cuando se le solicite, escriba la información apropiada. Por ejemplo:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	Una vez finalizado este proceso, debe tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma del certificado.

3. Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][cas] en Microsoft TechNet Wiki.

4. Una vez haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo con el nombre **myserver.crt**. Si la entidad de certificación le ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

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

	Guarde el archivo.

5. Desde la línea de comandos, una sesión Bash o una sesión de Terminal, use el siguiente comando para convertir los archivos **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato requerido por Sitios web Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	<div class="dev-callout"> 
	<b>Nota:</b>
	<p>Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión que se le ha proporcionado como archivo PEM (extensión de archivo .pem).</p>
	<p>El siguiente comando muestra la creación de un archivo .pfx, que incluye los certificados intermedios que contiene el archivo <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Después de ejecutar este comando, debería tener un archivo **myserver.pfx** apto para el uso con Sitios web Azure.


###<a href="bkmk_selfsigned"></a>Expedición de certificados autofirmados (solo para pruebas)

En algunos casos, es posible que desee obtener un certificado de evaluación y posponer la compra a través de una entidad de certificación de confianza hasta que pase a la fase de producción. Los certificados autofirmados pueden resultarle útiles en este caso. Un certificado autofirmado es un certificado que puede crear y firmar como si fuera una entidad de certificación. Si bien este certificado se puede usar para proteger un sitio web, la mayoría de los exploradores devolverán errores cuando se visite el sitio, dado que el certificado no tiene la firma de una entidad de certificación de confianza. Algunos exploradores pueden incluso denegarle el permiso de visualización del sitio.

- [Generación de un certificado autofirmado con makecert](#bkmk_ssmakecert)
- [Generación de un certificado autofirmado con OpenSSL](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### Generación de un certificado autofirmado con makecert ####

Puede crear un certificado de evaluación desde un sistema Windows que tenga instalado Visual Studio mediante los siguientes pasos:

1. Desde el **menú Inicio** o la **pantalla de inicio**, busque **Símbolo del sistema para desarrolladores**. Por último, haga clic en el botón secundario en **Símbolo del sistema para desarrolladores** y seleccione **Ejecutar como administrador**.

	Si se muestra un cuadro de diálogo de Control de cuenta de usuario, seleccione**Sí** para continuar.

2. Desde el Símbolo del sistema para desarrolladores, use el siguiente comando para crear un nuevo certificado autofirmado. Debe sustituir **serverdnsname** por el DNS de su sitio web.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Este comando creará un certificado que será válido entre las fechas 01/01/2013 y 01/01/2014 y almacenará su ubicación en el almacén de certificados CurrentUser.

3. Desde el **menú Inicio** o la **pantalla de inicio**, busque **Windows PowerShell** e inicie esta aplicación.

4. Desde el símbolo del sistema de Windows PowerShell, use los siguientes comandos para exportar el certificado que ha creado previamente:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	De este modo, se almacena la clave especificada como cadena segura en $mypwd, a continuación se encuentra el certificado mediante el nombre DNS especificado por el parámetro **dnsname** y, por último, se exporta el archivo especificado por el parámetro **filepath**. La cadena segura que contiene la clave se usa para proteger el archivo exportado.

<a href="bkmk_ssopenssl"></a>
####Generación de un certificado autofirmado con OpenSSL ####

1. Cree un nuevo documento con nombre **serverauth.cnf** e inserte el siguiente contenido:

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
        commonName			= Common Name (eg, your website's domain name)
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

	Así se especifica la configuración necesaria para producir un certificado SSL que pueda ser usado por Sitios web Azure.

2. Genere un nuevo certificado autofirmado con lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	De este modo, se crea un nuevo certificado con la configuración especificada en el archivo **serverauth.cnf**.

3. Para exportar el certificado a un archivo .PFX que se pueda subir a un sitio web de Azure, use el siguiente comando:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

	El archivo **myserver.pfx** producido por este comando se puede usar para proteger su sitio web de Azure con fines de evaluación.

<a href="bkmk_standardmode"></a><h2>Configuración del modo estándar</h2>

La habilitación de HTTPS para un dominio personalizado solo está disponible para el modo del plan de hospedaje web **estándar** de Sitios web Azure. Realice los pasos siguientes para cambiar al modo **estándar**.

> [WACOM.NOTE] Antes de cambiar un sitio web del modo gratis al modo **estándar**, debe eliminar los límites de gasto configurados en su suscripción de sitio web, puesto que, de lo contrario, se arriesga a que su sitio no esté disponible si alcanza el límite de gasto antes de que finalice el período de facturación. Para obtener más información acerca de los precios para los modos **estándar** o compartido, consulte [Información sobre el precio][pricing].

1. En el explorador, abra el [Portal de administración][portal].

2. En la pestaña **Sitios web**, haga clic en el nombre del sitio web.

	![selecting a web site][sitios web]

3. Haga clic en la pestaña **ESCALAR**.

	![The scale tab][escalar]

4. En la sección **general**, defina el modo del plan de hospedaje web; para ello, haga clic en **ESTÁNDAR**.

	![standard mode selected][estándar]

5. Haga clic en **Guardar**. Cuando se le solicite, haga clic en **Sí**.

	> [WACOM.NOTE] Si recibe un error tipo "error al configurar escala del sitio web '&lt;nombre del sitio web&gt;'", puede usar el botón de información para obtener más datos. Puede que reciba un error "Not enough available standard instance servers to satisfy this request". Si recibe este error, póngase en contacto con el [Soporte técnico de Azure](http://www.windowsazure.com/es-es/support/options/).


##<a href="bkmk_configuressl"></a>Configuración de SSL

Antes de realizar los pasos de esta sección, debe haber asociado un nombre de dominio personalizado a su sitio web de Azure. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para un Sitio web Azure][customdomain].

1. En el explorador, abra el [Portal de administración de Azure][portal].

2. En la pestaña **Sitios web**, haga clic en el nombre de su sitio y, a continuación, seleccione la pestaña **CONFIGURAR**.

	![the configure tab][configure]

3. En la sección **Certificados**, haga clic en **Cargar un certificado**

	![upload a certificate][uploadcert]

4. Mediante el cuadro de diálogo **Cargar un certificado**, seleccione el archivo de certificado .pfx que ha creado previamente con Administrador de IIS u OpenSSL. Especifique la contraseña, si procede, que se utilizó para proteger el archivo .pfx. Por último, haga clic en la **marca de verificación** para cargar el certificado.

	![upload certificate dialog][uploadcertdlg]

5. En la sección **Enlaces SSL** de la pestaña **CONFIGURAR**, use las listas desplegables para seleccionar el nombre de dominio que va a proteger con SSL y el certificado que va a usar. Es posible que también desee seleccionar el uso de [Indicación de nombre de servidor][sni] (SNI) O SSL basada en IP.

	![ssl bindings][sslbindings]
	
	* El modo SSL basado en IP asocia un certificado a un nombre de dominio mediante la asignación de una dirección IP pública dedicada del servidor al nombre de dominio. En este caso es necesario que cada nombre de dominio (contoso.com, fabrikam.com, etc.) asociado a un servicio tenga una dirección IP dedicada. Este es el método tradicional de asociación de certificados SSL a un servidor web.

	* La encriptación SSL basada en SNI es una extensión de SSL y [Seguridad de la capa de transporte][tls] (TLS) que permite que varios dominios compartan la misma dirección IP con certificados de seguridad independientes para cada dominio. Los exploradores más modernos (entre los que se incluyen Internet Explorer, Chrome, Firefox y Opera) son compatibles con la extensión SNI; sin embargo, es posible que los exploradores más antiguos no sean compatibles con la extensión SNI. Para obtener más información acerca de la extensión SNI, consulte el artículo [Indicación de nombre de servidor][sni] en Wikipedia.

6. Haga clic en **Guardar** para guardar los cambios y habilitar la encriptación SSL.

> [WACOM.NOTE] Si ha seleccionado la encriptación **SSL basada en IP** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales:
>
> 1. Después de haber configurado un enlace SSL basado en IP, se asigna una dirección IP dedicada a su sitio web. Puede encontrar esta dirección IP en la página **Panel** de su sitio web, en la sección **vista rápida**. Se mostrará como **Dirección IP virtual**:
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>     Tenga en cuenta que esta dirección IP será distinta de la dirección IP virtual que ha usado previamente para configurar el registro D de su dominio. Si lo ha configurado para usar un cifrado SSL basado en SNI, o no lo ha configurado para usar SSL, esta entrada no contendrá ninguna dirección.
>
> 2. Mediante las herramientas proporcionadas por su registrador de nombres de dominio, modifique el registro D de su nombre de dominio personalizado para que apunte a la dirección IP del paso anterior.


Llegados a este punto, debería ser capaz de visitar su sitio web con "HTTPS://" en lugar de "HTTP://" para comprobar que el certificado se ha configurado correctamente.

##<a href="bkmk_enforce"></a>Aplicación de HTTPS en el sitio web de Azure

Sitios web Azure *no* aplica HTTPS. Los visitantes aún pueden tener acceso a su sitio mediante HTTP, lo que puede comprometer la seguridad de su sitio web. Si desea aplicar HTTPS para su sitio web, puede usar el módulo **URL Rewrite**. El módulo URL Rewrite se incluye con Sitios web Azure, y le permite definir reglas que se aplican a las solicitudes entrantes antes de que las solicitudes lleguen a su aplicación. **Se puede utilizar para aplicaciones escritas en cualquier lenguaje de programación admitido por Sitios web Azure.** 

> [WACOM.NOTE] Las aplicaciones .NET MVC deben utilizar el filtro [RequireHttps](http://msdn.microsoft.com/es-es/library/system.web.mvc.requirehttpsattribute.aspx) en lugar de URL Rewrite. Para obtener más información acerca del uso de RequireHttps, consulte [Implementación de una aplicación ASP.NET MVC 5 segura en un Sitio web Azure](/es-es/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
> 
> Para obtener información sobre la redirección programática de las solicitudes utilizando otros lenguajes y marcos de programación, consulte la documentación de esas tecnologías.

Las reglas de URL Rewrite se definen en un archivo **web.config** almacenado en la raíz de su aplicación. El ejemplo siguiente contiene una regla básica de URL Rewrite que impone el uso de HTTPS a todo el tráfico entrante.

<a name="example"></a>**Web.Config de ejemplo para URL Rewrite**

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

Esta regla funciona devolviendo un código de estado HTTP de 301 (redirección permanente) cuando el usuario solicita una página mediante HTTP. El 301 redirige la solicitud a la misma URL que el visitante solicitó, pero sustituye la parte de HTTP de la solicitud por HTTPS. Por ejemplo, HTTP://contoso.com se redirigiría a HTTPS://contoso.com.

> [WACOM.NOTE] Si la aplicación está escrita en **Node.js**, **PHP**, **Python Django** o **Java**, probablemente no incluye un archivo web.config. Sin embargo, **Node.js**, **Python Django** y **Java** sí utilizan los tres un archivo web.config cuando se hospedan en Sitios web Azure. Azure crea automáticamente el archivo durante la implementación, así que no se ve nunca. Si incluye uno como parte de su aplicación, sobrescribirá el que Azure genera automáticamente.

###.NET

Para aplicaciones. NET, modifique el archivo web.config de la aplicación y agregue la sección **&lt;reescribir>** del [ejemplo](#example) a la sección **&lt;system.WebServer>**.

Si el archivo web.config ya incluye una sección **&lt;reescribir>**, agregue la **&lt;regla>** del [ejemplo](#example) como la primera entrada de la sección **&lt;reglas>**.

###PHP

En el caso de las aplicaciones PHP, guarde el [ejemplo](#example) como archivo web.config en la raíz de su aplicación y, a continuación, implemente de nuevo la aplicación en su Sitio web Azure.

###Node.js, Python Django y Java

Se crea automáticamente un archivo web.config para aplicaciones Node.js, Python Django y Java si estas no proporcionan uno, pero dicho archivo solo existe en el servidor porque se crea durante la implementación. El archivo generado automáticamente contiene valores de configuración que le indican a Azure cómo hospedar su aplicación.

Para recuperar y modificar el archivo generado automáticamente desde el Sitio web, siga los pasos que se enumeran a continuación.

1. Descargue el archivo mediante FTP (consulte [Carga/descarga de archivos a través de FTP y recopilación de registros de diagnóstico](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Agréguelo a la raíz de su aplicación.

3. Agregue las reglas de reescritura utilizando la siguiente información.

	* **Node.js y Python Django**

		El archivo web.config generado por las aplicaciones Node.js y Python Django ya tendrá una sección **&lt;reescribir>**, que contiene entradas de tipo **&lt;regla>** que son necesarias para el correcto funcionamiento del sitio. Para imponer el uso de HTTPS en el sitio, agregue el valor de **&lt;regla>** del ejemplo como primera entrada en la sección **&lt;reglas>**. Esto forzará el uso de HTTPS y dejará el resto de reglas intactas.

	* **Java**
	
		El archivo web.config para aplicaciones Java que utilizan Apache Tomcat no contiene una sección **&lt;reescribir>**; así pues, debe agregar la sección **&lt;reescribir>** del ejemplo en la sección **&lt;system.webServer>**.

4. Implemente el proyecto de nuevo (incluyendo en archivo web.config actualizado) en Azure.

Una vez que implemente un archivo web.config con una regla de reescritura para imponer el uso de HTTPS, esta debería surtir efecto de inmediato y redirigir todas las solicitudes a HTTPS.

Para obtener más información sobre el módulo URL Rewrite para IIS, consulte la documentación de [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite). 

## Más recursos ##
- [Centro de confianza de Microsoft Azure](/es-es/support/trust-center/security/)
- [Opciones de configuración desbloqueadas en Sitios web Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activación del registro de diagnósticos](/es-es/documentation/articles/web-sites-enable-diagnostic-log/)
- [Configuración de Sitios web](/es-es/documentation/articles/web-sites-configure/)
- [Portal de administración de Azure](https://manage.windowsazure.com)

[customdomain]: /es-es/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/es-es/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/es-es/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/es-es/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/es-es/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png
