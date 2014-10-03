# Habilitación de HTTPS en un sitio web de Azure

Cuando alguien visita su sitio web mediante HTTPS, la comunicación entre el sitio web y el explorador se protege con un cifrado de Capa de sockets seguros (SSL). Este es el método más usado para proteger los datos que se envían por Internet y asegura a los visitantes la protección de las transacciones que realizan en su sitio. Este artículo analiza la habilitación de HTTPS en un sitio web de Azure.

> [WACOM.NOTE] Para habilitar HTTPS en nombres de dominio personalizados, debe configurar sus sitios web en modo estándar. Si actualmente usa un modo gratuito o compartido, es posible que el cambio de modo suponga costes adicionales. Para obtener más información acerca de los precios de los modos estándar y compartido, consulte la [Información sobre el precio][]. Para comenzar con Azure, puede usar una [evaluación gratuita de Microsoft Azure][].

[][]

## El dominio \*.azurewebsites.net

</p>
Si en lugar de usar un nombre de dominio personalizado tiene previsto usar el dominio \*.azurewebsites.net que Azure ha asignado a su sitio web (por ejemplo, contoso.azurewebsites.net), su sitio ya está protegido por el certificado proporcionado por Microsoft. Puede usar **<https://mywebsite.azurewebsites.net>** para obtener acceso a su sitio de forma protegida. Sin embargo, \*.azurewebsites.net es un dominio compartido y, al igual que todos los dominios compartidos, no es igual de seguro que utilizar un dominio personalizado con su propio certificado.

El resto de este documento proporciona información acerca de la habilitación de HTTPS en nombres de dominio personalizados, como **contoso.com**, **www.contoso.com** o **\*.contoso.com**.

[][1]

## Nombres de dominio personalizados

</p>
Para habilitar HTTPS en un nombre de dominio personalizado, como **contoso.com**, debe registrar un nombre de dominio personalizado en un registrador de nombres de dominio. Para obtener más información acerca de la configuración del nombre de dominio de un sitio web de Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure][]. Una vez que haya registrado un nombre de dominio personalizado y configurado su sitio web para que se corresponda con el nombre personalizado, debe solicitar un certificado SSL para el dominio.

El registro de un nombre de dominio, además, le permite crear subdominios, como **www.contoso.com** o **mail.contoso.com**. Antes de solicitar un certificado SSL, debe determinar los nombres de dominio que contarán con la protección del certificado. De este modo, se determinará el tipo de certificado que debe obtener. Si solo necesita proteger un nombre de dominio, como **contoso.com** o **www.contoso.com**, es probable que un certificado básico sea suficiente. Si necesita proteger varios nombres de dominio, como **contoso.com**, **www.contoso.com** y **mail.contoso.com**, deberá solicitar un certificado comodín o un certificado con nombre alternativo del sujeto (subjectAltName, SAN).

> [WACOM.NOTE] La mayoría de los exploradores mostrarán una advertencia si el nombre de dominio que se especifica en el certificado no coincide con el nombre de dominio que se ha escrito en el explorador. Por ejemplo, si el certificado solo incluye www.contoso.com, pero el nombre de dominio usado en Internet Explorer para tener acceso al sitio es login.contoso.com, recibirá una advertencia con el texto "El certificado de seguridad de este sitio web se ha emitido para una dirección de sitio web diferente".

Los **certificados básicos** son certificados en los que el nombre común (CN) del certificado se establece para el dominio o subdominio específico que los clientes usarán para visitar el sitio. Por ejemplo, **www.contoso.com**. Estos certificados solo protegen el nombre de dominio que especifica el nombre común.

Los **certificados comodín** son certificados en los que el nombre común del certificado contiene un comodín '\*' en el nivel del subdominio. Gracias a ello, el certificado coincide con un único nivel de subdominios de un dominio determinado. Por ejemplo, un certificado comodín de **\*.contoso.com** sería válido para **www.contoso.com**, **payment.contoso.com** y **login.contoso.com**. No sería válido para **test.login.contoso.com**, dado que agrega un nivel de subdominio adicional. Tampoco sería válido para **contoso.com**, puesto que es el nivel de dominio raíz y no un subdominio.

Un certificado comodín es lo que proporciona Microsoft para el nombre de dominio \*.azurewebsites.net, que se crea automáticamente para su sitio web.

**subjectAltName** es una extensión de certificado que permite asociar varios valores, o Nombres alternativos del sujeto, a un certificado. A efectos de los certificados SSL, es posible agregar nombres DNS adicionales que serán válidos ante el certificado. Por ejemplo, un certificado que use subjectAltName puede tener un nombre común, como **contoso.com**, pero también puede tener nombres alternativos, como **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com** e incluso **fabrikam.com**. Dicho certificado sería válido para todos los dominios especificados en el nombre común y en subjectAltName.

Un certificado también puede ser compatible con comodines y subjectAltName.

[][2]

## Obtención de un certificado

</p>
Los certificados SSL usados con Sitios web Azure deben tener la firma de una entidad de certificación (CA), una entidad de terceros de confianza que emite certificados con esta finalidad. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][] en Microsoft TechNet Wiki.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.

-   El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).

-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al sitio web. Si necesita ofrecer servicio a varios dominios con este certificado, deberá usar un valor comodín o especificar los valores subjectAltName tal y como se ha descrito previamente.

    -   Para obtener información acerca de la configuración de un nombre de dominio personalizado para un Sitio web Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure][].

    > [WACOM.NOTE] No intente obtener o generar un certificado para el dominio azurewebsites.net.

-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

> [WACOM.NOTE] Los certificados emitidos por servidores de entidades de certificación privados no son compatibles con Sitios web Azure.

Para obtener un certificado SSL de una entidad de certificación, debe generar una solicitud de firma de certificado (CSR), que se enviará a la entidad de certificación. La entidad de certificación devolverá un certificado que deberá usar para completar la solicitud de firma de certificado. Normalmente, para generar una CSR, se usa una de las dos aplicaciones siguientes: certmgr.exe u [OpenSSL][]. Certmgr.exe solo está disponible en Windows, mientras que OpenSSL está disponible para la mayoría de las plataformas. Los pasos que debe seguir para emplear estas utilidades se muestran a continuación.

> [WACOM.NOTE] Los certificados de criptografía de curva elíptica (ECC) son compatibles con Sitios web Azure; sin embargo, son relativamente nuevos y debería preguntar a su entidad de certificación a fin de conocer los pasos exactos para crear la solicitud de firma de certificado. Cuando haya obtenido un certificado ECC, podrá cargarlo a su sitio web como se describe en los pasos que se describen más adelante.

Puede que deba obtener **certificados intermedios** (también denominados certificados de cadena), si su entidad de certificación los utiliza. Se considera que el uso de certificados intermedios es más seguro que los "certificados que no pertenecen a una cadena", por lo que las entidades de certificación suelen utilizarlos. Normalmente, los certificados intermedios se pueden descargar de forma independiente desde el sitio web de la entidad de certificación. Los pasos que se muestran en este artículo describen la manera de asegurar que todos los certificados intermedios se combinen con los certificados cargados en su sitio web de Azure.

> [WACOM.NOTE] Al seguir cualquier serie de pasos, se le solicitará que escriba un **nombre común**. Si va a obtener un certificado comodín para su uso en varios dominios (www.contoso.com, sales.contoso.com), este valor debe ser \*.nombre\_de\_dominio (por ejemplo, \*.contoso.com). Si va a obtener un certificado para un único nombre de dominio, este valor debe ser el valor exacto que los usuarios escribirán en el explorador para visitar su sitio web. Por ejemplo, www.contoso.com.
>
> Si necesita tener compatibilidad con un nombre comodín, como \*.contoso.com, y un nombre de dominio raíz, como contoso.com, puede usar un certificado de Nombres alternativos del sujeto (SAN). Para obtener un ejemplo de creación de una solicitud de certificado que use las extensiones SubjectAltName, consulte [Certificado SubjectAltName][].
>
> Para obtener más información acerca de la configuración del nombre de dominio de un sitio web de Azure, consulte [Configuración de un nombre de dominio personalizado para un sitio web de Azure][].

### Obtención de un certificado con Certreq.exe (solo Windows)

Certreq.exe es una utilidad de Windows para crear solicitudes de certificado. Ha formado parte de la instalación base de Windows desde Windows XP/Windows Server 2000, por lo que debe estar disponible en los sistemas Windows más recientes. Realice los siguientes pasos para obtener un certificado SSL con certreq.exe.

Si desea crear un certificado autofirmado de evaluación, consulte la sección [Certificados autofirmados][] de este documento.

Si desea usar el módulo Administrador de IIS para crear una solicitud de certificado, consulte la sección [Obtención de un certificado con el Administrador de IIS][].

1.  Abra la aplicación **Bloc de notas** y cree un nuevo documento que contenga lo siguiente: En la línea Subject, sustituya **mysite.com** por el nombre de dominio personalizado de su sitio web. Por ejemplo, Subject = "CN=www.contoso.com".

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

    Para obtener más información acerca de las opciones especificadas anteriormente, así como otras opciones disponibles, consulte la [Documentación de referencia de Certreq (en inglés)][].

2.  Guarde el archivo de texto como **myrequest.txt**.

3.  Desde la **pantalla Inicio** o el **menú Inicio**, ejecute **cmd.exe**.

4.  Desde el símbolo del sistema, use el siguiente comando para crear un archivo de solicitud de certificado:

        certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

    Especifique la ruta del archivo **myrequest.txt** que ha creado en el paso 1 y la ruta que se debe usar al crear el archivo **myrequest.csr**.

5.  Envíe el archivo **myrequest.csr** a una entidad de certificación para obtener un certificado SSL. Puede que sea necesario cargar el archivo o abrirlo en la aplicación Bloc de notas y pegar los contenidos directamente en el formulario web.

    Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][] en Microsoft TechNet Wiki.

6.  Una vez que la entidad de certificación le haya proporcionado un archivo de certificado (.CER), guarde este archivo en el equipo usado para generar la solicitud y, a continuación, use el siguiente comando para aceptar la solicitud y completar el proceso de generación del certificado.

        certreq -accept -user mycert.cer

    En este caso, el certificado **mycert.cer** recibido de la entidad de certificación se usará para completar el proceso de firma del certificado. No se creará ningún archivo; en su lugar, el certificado se almacenará en el almacén de certificados de Windows.

7.  Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en los pasos siguientes. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

    Una vez que haya descargado el certificado, haga clic con el botón secundario en el explorador y seleccione **Install certificate**. Use los valores predeterminados en **Certificate Import Wizard** y, a continuación, seleccione **Next** hasta que la importación haya finalizado.

8.  Para exportar el certificado desde el almacén de certificados, ejecute **certmgr.msc** desde la **pantalla Inicio** o el **menú Inicio**. Cuando aparezca **Certificate Manager**, expanda la carpeta **Personal** y, a continuación, seleccione **Certificates**. En el campo **Issued To**, busque una entrada con el nombre de dominio personalizado para el que solicitó un certificado. En el campo **Issued By**, debería mostrarse la entidad de certificación que ha usado para este certificado.

    ![Insertar imagen del administrador de certificados aquí][]

9.  Haga clic con el botón secundario en el certificado, seleccione **All Tasks** y, a continuación, seleccione **Export**. En **Certificate Export Wizard**, haga clic en **Next** y, a continuación, seleccione **Yes, export the private key**. Haga clic en **Next**.

    ![Exportar la clave privada][]

10. Seleccione **Personal Information Exchange - PKCS \#12**, **Include all certificates in the certificate chain** y **Export all extended properties**. Haga clic en **Next**.

    ![incluir todos los certificados y propiedades extendidas][]

11. Seleccione **Password** y, a continuación, escriba y confirme la contraseña. Haga clic en **Next**.

    ![especificar una contraseña][]

12. Proporcione una ruta y el nombre del archivo que contendrá el certificado exportado. El nombre de archivo debe tener una extensión **.pfx**. Haga clic en **Next** para finalizar el proceso.

    ![proporcionar una ruta de archivo][]

Ahora puede cargar el archivo PFX exportado en su sitio web de Azure.

### Obtención de un certificado con OpenSSL

1.  Genere una clave privada y una solicitud de firma de certificado utilizando lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal.

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  Cuando se le solicite, escriba la información apropiada. Por ejemplo:

        Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []: 

    Una vez finalizado este proceso, debe tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma del certificado.

3.  Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][] en Microsoft TechNet Wiki.

4.  Una vez haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo con el nombre **myserver.crt**. Si la entidad de certificación le ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

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

5.  Desde la línea de comandos, una sesión Bash o una sesión de Terminal, use el siguiente comando para convertir los archivos **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato requerido por Sitios web Azure:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

    <div class="dev-callout"> 
<b>Nota:</b>
<p>Si su entidad de certificaci&oacute;n utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportaci&oacute;n del certificado en el paso siguiente. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificaci&oacute;n y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versi&oacute;n que se le ha proporcionado como archivo PEM (extensi&oacute;n de archivo .pem).</p>
<p>El siguiente comando muestra la creaci&oacute;n de un archivo .pfx, que incluye los certificados intermedios que contiene el archivo <b>intermediate-cets.pem</b>:</p>
<pre><code>
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
</code></pre>
</div>

    Después de ejecutar este comando, debería tener un archivo **myserver.pfx** apto para el uso con Sitios web Azure.

[][3]

## Configuración del modo estándar

</p>
La habilitación de HTTPS para un dominio personalizado solo está disponible para el modo estándar de Sitios web Azure. Realice los pasos siguientes para cambiar al modo estándar.

> [WACOM.NOTE] Antes de cambiar un sitio web del modo gratis al modo estándar, debe eliminar los límites de gasto configurados en su suscripción de sitio web, puesto que, de lo contrario, se arriesga a que su sitio no esté disponible si alcanza el límite de gasto antes de que finalice el período de facturación. Para obtener más información acerca de los precios de los modos estándar y compartido, consulte la [Información sobre el precio][].

1.  En el explorador, abra el [Portal de administración][].

2.  En la pestaña **Sitios web**, haga clic en el nombre del sitio.

    ![selección de un sitio web][]

3.  Haga clic en la pestaña **SCALE**.

    ![Pestaña SCALE][]

4.  En la sección **general**, establezca el modo de sitio web haciendo clic en **ESTÁNDAR**.

    ![modo estándar seleccionado][]

5.  Haga clic en **Save**. Cuando se le solicite, haga clic en **Yes**.

    > [WACOM.NOTE] Si recibe el mensaje "Error en la configuración de escala del sitio web \<nombre del sitio\>" puede utilizar el botón de detalles para obtener más información. Puede que reciba un error "Not enough available standard instance servers to satisfy this request". Si recibe este error, póngase en contacto con el [Soporte técnico de Azure][].

[][4]

## Configuración de SSL

</p>
Antes de realizar los pasos de esta sección, debe haber asociado un nombre de dominio personalizado a su sitio web de Azure. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para un Sitio web Azure][Configuración de un nombre de dominio personalizado para un sitio web de Azure].

1.  En el explorador, abra el [Portal de administración de Azure][Portal de administración].

2.  En la pestaña **Sitios web**, haga clic en el nombre de su sitio y, a continuación, seleccione la pestaña **CONFIGURAR**.

    ![Pestaña CONFIGURE][]

3.  En la sección **certificates**, haga clic en **Upload a certificate**

    ![cargar un certificado][]

4.  Mediante el cuadro de diálogo **Upload a certificate**, seleccione el archivo de certificado .pfx que ha creado previamente con Administrador de IIS u OpenSSL. Especifique la contraseña, si procede, que se utilizó para proteger el archivo .pfx. Finalmente, haga clic en la **marca de verificación** para cargar el certificado.

    ![Cuadro de diálogo para cargar un certificado][]

5.  En la sección **ssl bindings** de la pestaña **CONFIGURE**, use las listas desplegables para seleccionar el nombre de dominio que va a proteger con SSL y el certificado que va a usar. Es posible que también desee seleccionar el uso de SSL basada en IP o en la extensión [Indicación de nombre de servidor][] (SNI).

    ![enlaces ssl][]

    -   El modo SSL basado en IP asocia un certificado a un nombre de dominio mediante la asignación de una dirección IP pública dedicada del servidor al nombre de dominio. En este caso es necesario que cada nombre de dominio (contoso.com, fabrikam.com, etc.) asociado a un servicio tenga una dirección IP dedicada. Este es el método tradicional de asociación de certificados SSL a un servidor web.

    -   La encriptación SSL basada en SNI es una extensión de SSL y [Seguridad de la capa de transporte][] (TLS) que permite que varios dominios compartan la misma dirección IP con certificados de seguridad independientes para cada dominio. Los exploradores más modernos (entre los que se incluyen Internet Explorer, Chrome, Firefox y Opera) son compatibles con la extensión SNI; sin embargo, es posible que los exploradores más antiguos no sean compatibles con la extensión SNI. Para obtener más información acerca de la extensión SNI, consulte el artículo [Indicación de nombre de servidor][] en Wikipedia.

6.  Haga clic en **Save** para guardar los cambios y habilitar la encriptación SSL.

> [WACOM.NOTE] Si ha seleccionado la encriptación **IP based SSL** y su dominio personalizado se ha configurado con un registro D, debe realizar los siguientes pasos adicionales:
>
> 1.  Después de haber configurado un enlace SSL basado en IP, se asigna una dirección IP dedicada a su sitio web. Puede encontrar esta dirección IP en la página **Panel** de su sitio web, en la sección **vista rápida**. Se mostrará como **Virtual IP Address**:
>
>     ![Dirección IP virtual][]
>
>     Tenga en cuenta que esta dirección IP será distinta de la dirección IP virtual que ha usado previamente para configurar el registro D de su dominio. Si lo ha configurado para usar un cifrado SSL basado en SNI, o no lo ha configurado para usar SSL, esta entrada no contendrá ninguna dirección.
>
> 2.  Mediante las herramientas proporcionadas por su registrador de nombres de dominio, modifique el registro D de su nombre de dominio personalizado para que apunte a la dirección IP del paso anterior.
>
Llegados a este punto, debería ser capaz de visitar su sitio web con HTTPS para comprobar que el certificado se ha configurado correctamente.

[][5]

## Certificados SubjectAltName (opcional)

</p>
OpenSSL se puede usar para crear una solicitud de certificado que use la extensión SubjectAltName para ser compatible con varios nombres de servidor mediante un único certificado; sin embargo, es necesario contar con un archivo de configuración. Los siguientes pasos describen la creación de un archivo de configuración y su modo de empleo para solicitar un certificado.

1.  Cree un nuevo archivo con nombre **sancert.cnf** y use lo siguiente como contenido del archivo:

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

    No es necesario que cambie el campo commonName\_default, dado que se le solicitará que escriba su nombre común en uno de los siguientes pasos.

2.  Guarde el archivo **sancert.cnf**.

3.  Genere una clave privada y una solicitud de firma de certificado mediante el archivo de configuración sancert.cnf. Desde una sesión Bash o una sesión de Terminal, use el siguiente comando:

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  Cuando se le solicite, escriba la información apropiada. Por ejemplo:

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    Una vez finalizado este proceso, debe tener dos archivos: **myserver.key** y **server.csr**. El archivo **server.csr** contiene la solicitud de firma del certificado.

5.  Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][] en Microsoft TechNet Wiki.

6.  Una vez haya obtenido un certificado de una entidad de certificación, guárdelo en un archivo con el nombre **myserver.crt**. Si la entidad de certificación le ha proporcionado el certificado en formato de texto, solo tiene que pegar el texto del certificado en el archivo **myserver.crt**. El contenido del archivo debería parecerse a lo siguiente, al visualizarlo en un editor de texto:

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

7.  Desde la línea de comandos, una sesión Bash o una sesión de Terminal, use el siguiente comando para convertir los archivos **myserver.key** y **myserver.crt** en **myserver.pfx**, que es el formato requerido por Sitios web Azure:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

    <div class="dev-callout"> 
<b>Nota:</b>
<p>Si su entidad de certificaci&oacute;n utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportaci&oacute;n del certificado en el paso siguiente. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificaci&oacute;n y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versi&oacute;n que se le ha proporcionado como archivo PEM (extensi&oacute;n de archivo .pem).</p>
<p>El siguiente comando muestra la creaci&oacute;n de un archivo .pfx, que incluye los certificados intermedios que contiene el archivo <b>intermediate-cets.pem</b>:</p>
<pre><code>
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
</code></pre>
</div>

    Después de ejecutar este comando, debería tener un archivo **myserver.pfx** apto para el uso con Sitios web Azure.

## <a name="bkmk_iismgr"></a>Obtención de un certificado con el Administrador de IIS (opcional)

Si está familiarizado con el Administrador de IIS, puede usarlo para generar un certificado apto para el uso con Sitios web Azure.

1.  Genere una solicitud de firma de certificado (CSR) con el Administrador de IIS para enviarla a la entidad de certificación. Para obtener más información acerca de la generación de solicitudes CSR, consulte [Solicitar un certificado de servidor de Internet (IIS 7)][].

2.  Envíe la solicitud CSR a una entidad de certificación para obtener un certificado SSL. Para obtener una lista de entidades de certificación, consulte [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)][] en Microsoft TechNet Wiki.

3.  Complete la solicitud CSR con el certificado proporcionado por la entidad de certificación. Para obtener más información acerca de la realización de solicitudes CSR, consulte [Instalar un certificado de servidor de Internet (IIS 7)][].

4.  Si su entidad de certificación utiliza certificados intermedios, debe instalar estos certificados antes de realizar la exportación del certificado en el paso siguiente. Normalmente, estos certificados se pueden descargar de forma independiente desde la entidad de certificación y se proporcionan en varios formatos para los diferentes tipos de servidores web. Seleccione la versión proporcionada para Microsoft IIS.

    Una vez que haya descargado el certificado, haga clic con el botón secundario en el explorador y seleccione **Install certificate**. Use los valores predeterminados en **Certificate Import Wizard** y, a continuación, seleccione **Next** hasta que la importación haya finalizado.

5.  Exporte el certificado desde el Administrador de IIS. Para obtener más información acerca de la exportación del certificado, consulte [Exportar un certificado de servidor (IIS 7)][]. El archivo exportado se cargará en Azure en los pasos posteriores para su uso en su sitio web de Azure.

    <div class="dev-callout"> 
<b>Nota:</b>
<p>Durante el proceso de exportaci&oacute;n, aseg&uacute;rese de seleccionar la opci&oacute;n <strong>Yes, export the private key</strong>. Dicha exportaci&oacute;n incluye la clave privada en el certificado exportado.</p>
</div>

    <div class="dev-callout"> 
<b>Nota:</b>
<p>Durante el proceso de exportaci&oacute;n, aseg&uacute;rese de seleccionar las opciones <strong>Include all certs in the certification path</strong> y <strong>Export all extended properties</strong>. Dicha exportaci&oacute;n incluye todos los certificados intermedios, si procede, en el certificado exportado.</p>
</div>

[][6]

## Certificados autofirmados (opcional)

</p>
En algunos casos, es posible que desee obtener un certificado de evaluación y posponer la compra a través de una entidad de certificación de confianza hasta que pase a la fase de producción. Los certificados autofirmados pueden resultarle útiles en este caso. Un certificado autofirmado es un certificado que puede crear y firmar como si fuera una entidad de certificación. Si bien este certificado se puede usar para proteger un sitio web, la mayoría de los exploradores devolverán errores cuando se visite el sitio, dado que el certificado no tiene la firma de una entidad de certificación de confianza. Algunos exploradores pueden incluso denegarle el permiso de visualización del sitio.

Aunque existen numerosas formas de crear un certificado autofirmado, este artículo solo proporciona información cerca del uso de **makecert** y **OpenSSL**.

### Creación de un certificado autofirmado con makecert

Puede crear un certificado de evaluación desde un sistema Windows que tenga instalado Visual Studio mediante los siguientes pasos:

1.  Desde el **menú Inicio** o la **pantalla Inicio**, busque **Símbolo del sistema para desarrolladores**. Finalmente, haga clic en el botón secundario en **Símbolo del sistema para desarrolladores** y seleccione **Ejecutar como administrador**.

    Si se muestra un cuadro de diálogo de Control de cuenta de usuario, seleccione **Aceptar** para continuar.

2.  Desde el Símbolo del sistema para desarrolladores, use el siguiente comando para crear un nuevo certificado autofirmado. Debe sustituir **serverdnsname** por el DNS de su sitio web.

        makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

    Este comando creará un certificado que será válido entre las fechas 01/01/2013 y 01/01/2014 y almacenará su ubicación en el almacén de certificados CurrentUser.

3.  Desde el **menú Inicio** o la **pantalla Inicio**, busque **Windows PowerShell** e inicie esta aplicación.

4.  Desde el símbolo del sistema de Windows PowerShell, use los siguientes comandos para exportar el certificado que ha creado previamente:

        $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
        get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

    De este modo, se almacena la clave especificada como cadena segura en $mypwd, a continuación se encuentra el certificado mediante el nombre DNS especificado por el parámetro **dnsname** y, finalmente, se exporta el archivo especificado por el parámetro **filepath**. La cadena segura que contiene la clave se usa para proteger el archivo exportado.

### Creación de un certificado autofirmado con OpenSSL

1.  Cree un nuevo documento con nombre **serverauth.cnf** e inserte el siguiente contenido:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your website's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

    Así se especifica la configuración necesaria para producir un certificado SSL que pueda ser usado por Sitios web Azure.

2.  Genere un nuevo certificado autofirmado con lo siguiente desde una línea de comandos, una sesión Bash o una sesión de Terminal:

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    De este modo, se crea un nuevo certificado con la configuración especificada en el archivo **serverauth.cnf**.

3.  Para exportar el certificado a un archivo .PFX que se pueda subir a un sitio web de Azure, use el siguiente comando:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Cuando se le solicite, escriba una contraseña para proteger el archivo .pfx.

    El archivo **myserver.pfx** producido por este comando se puede usar para proteger su sitio web de Azure con fines de evaluación.

  [Información sobre el precio]: https://www.windowsazure.com/en-us/pricing/details/
  [evaluación gratuita de Microsoft Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  []: bkmk_azurewebsites
  [1]: bkmk_domainname
  [Configuración de un nombre de dominio personalizado para un sitio web de Azure]: /en-us/develop/net/common-tasks/custom-dns-web-site/
  [2]: bkmk_getcert
  [Windows and Windows Phone 8 SSL Root Certificate Program (Members CAs)]: http://go.microsoft.com/fwlink/?LinkID=269988
  [OpenSSL]: http://www.openssl.org/
  [Certificado SubjectAltName]: #bkmk_subjectaltname
  [Certificados autofirmados]: #bkmk_selfsigned
  [Obtención de un certificado con el Administrador de IIS]: #bkmk_iismgr
  [Documentación de referencia de Certreq (en inglés)]: http://technet.microsoft.com/library/cc725793.aspx
  [Insertar imagen del administrador de certificados aquí]: ./media/configure-ssl-web-site/waws-certmgr.png
  [Exportar la clave privada]: ./media/configure-ssl-web-site/waws-certwiz1.png
  [incluir todos los certificados y propiedades extendidas]: ./media/configure-ssl-web-site/waws-certwiz2.png
  [especificar una contraseña]: ./media/configure-ssl-web-site/waws-certwiz3.png
  [proporcionar una ruta de archivo]: ./media/configure-ssl-web-site/waws-certwiz4.png
  [3]: bkmk_standardmode
  [Portal de administración]: https://manage.windowsazure.com/
  [selección de un sitio web]: ./media/configure-ssl-web-site/sslwebsite.png
  [Pestaña SCALE]: ./media/configure-ssl-web-site/sslscale.png
  [modo estándar seleccionado]: ./media/configure-ssl-web-site/sslreserved.png
  [Soporte técnico de Azure]: http://www.windowsazure.com/en-us/support/options/
  [4]: bkmk_configuressl
  [Pestaña CONFIGURE]: ./media/configure-ssl-web-site/sslconfig.png
  [cargar un certificado]: ./media/configure-ssl-web-site/ssluploadcert.png
  [Cuadro de diálogo para cargar un certificado]: ./media/configure-ssl-web-site/ssluploaddlg.png
  [Indicación de nombre de servidor]: http://en.wikipedia.org/wiki/Server_Name_Indication
  [enlaces ssl]: ./media/configure-ssl-web-site/sslbindings.png
  [Seguridad de la capa de transporte]: http://en.wikipedia.org/wiki/Transport_Layer_Security
  [Dirección IP virtual]: ./media/configure-ssl-web-site/staticip.png
  [5]: bkmk_subjectaltname
  [Solicitar un certificado de servidor de Internet (IIS 7)]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
  [Instalar un certificado de servidor de Internet (IIS 7)]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
  [Exportar un certificado de servidor (IIS 7)]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx
  [6]: bkmk_selfsigned
