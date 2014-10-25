<properties linkid="dev-nodejs-enablessl" urlDisplayName="Enable SSL" pageTitle="Configure SSL for a cloud service (Node.js) - Azure" metaKeywords="Node.js Azure SSL, Node.js Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a Node.js web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configuring SSL for a Node.js Application in an Azure Web Role" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Configuración de SSL para una aplicación Node.js en un rol web de Azure

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger
los datos que se envían por Internet. Esta tarea común analiza cómo
especificar un extremo HTTPS para una aplicación Node.js hospedada como un servicio en la nube de Azure en un rol web y
cómo cargar un certificado SSL para proteger la aplicación.

<div class="dev-callout">Nota:
<p>Los pasos descritos en este art&iacute;culo solo se aplican a las aplicaciones de Node hospedadas como un servicio en la nube de Azure en un rol web; para los sitios web, consulte <a href="../web-sites-configure-ssl-certificate/">Configuraci&oacute;n de un certificado SSL para un sitio web de Azure</a>.</p>
</div>

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Cree un servicio Node.js y publíquelo en la nube][]
-   [Paso 2: Obtenga un certificado SSL][]
-   [Paso 3: Importar el certificado SSL][]
-   [Paso 4: Modificar la definición del servicio y los archivos de configuración][]
-   [Paso 5: Conéctese a la instancia de rol con HTTPS][]

## <a name="step1"> </a>Paso 1: Crear un servicio de Node.js y publicar el servicio en la nube

Cuando se implementa una aplicación Node.js en un rol web de Azure, el
certificado de servidor y la conexión SSL se administran mediante
Internet Information Services (IIS), para que el servicio de Node.js pueda escribirse
como si fuese un servicio http. Puede crear un sencillo servicio Node.js "hello
world" que use Azure PowerShell siguiendo estos pasos:

1.  Desde el **menú Inicio** o la **pantalla Inicio**, busque **Azure PowerShell**. Finalmente, haga clic en el botón secundario en **Azure PowerShell** y seleccione **Ejecutar como administrador**.

    ![Icono de Azure PowerShell][]

[WACOM.INCLUDE [install-dev-tools][]]

1.  Cree un proyecto de servicio nuevo usando el cmdlet **New-AzureServiceProject**.

    ![][1]

2.  Agregue un rol web al servicio usando el cmdlet **Add-AzureNodeWebRole**:

    ![][1]

3.  Publique el servicio en la nube utilizando el cmdlet **Publish-AzureServiceProject**:

    ![][2]

    <div class="dev-callout">
<strong>Nota:</strong>
<p>Si no ha importado previamente la configuraci&oacute;n de publicaci&oacute;n para su suscripci&oacute;n de Azure, recibir&aacute; un error al intentar publicar. Para obtener informaci&oacute;n acerca de la descarga y la importaci&oacute;n de la configuraci&oacute;n de publicaci&oacute;n para su suscripci&oacute;n, consulte <a href="https://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Uso de Azure PowerShell para Node.js</a></p>
</div>

El valor **Dirección URL del sitio web creado** devuelto por el cmdlet **Publish-AzureServiceProject** contiene el nombre de dominio completo de la aplicación hospedada. Necesitará obtener un certificado SSL para este nombre de dominio completo específico e implementarlo en Azure.

## <a name="step2"> </a>Paso 2: Obtener un certificado SSL

Para configurar SSL para una aplicación, necesita primero obtener un
certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de
confianza que emite certificados para este propósito. Si todavía no
tiene una de estas firmas, deberá obtenerla mediante una compañía
que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de
certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves (archivo .pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso
    al servicio en la nube. No se puede adquirir un certificado SSL para el
    dominio cloudapp.net; por lo tanto, el nombre de sujeto del certificado debe
    coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, **mysecuresite.cloudapp.net**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

## <a name="step3"> </a>Paso 3: Importar el certificado SSL

Una vez que cuenta con un certificado, instálelo en el almacén de certificados en el equipo de desarrollo. Este certificado se recuperará y cargará en Azure como parte del paquete de implementación de la aplicación basado en los cambios de configuración que realice en un paso posterior.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Los pasos utilizados en esta secci&oacute;n se basan en la versi&oacute;n de Windows 8 del Asistente para importaci&oacute;n de certificados. Si est&aacute; usando una versi&oacute;n anterior de Windows, los pasos aqu&iacute; descritos pueden no coincidir con el orden en el que aparecen en el asistente. Si este es el caso, lea completamente esta secci&oacute;n antes de usar al Asistente para importaci&oacute;n de certificados, de modo que comprenda las acciones globales que se deben realizar.</p>
</div>

Para importar el certificado SSL, realice los siguientes pasos:

1.  Utilizando el Explorador de Windows, desplácese hasta el directorio donde se encuentra el archivo **.pfx** que contiene el certificado y, a continuación, haga doble clic en él. Se mostrará al Asistente para importación de certificados.

    ![asistente para certificados][]

2.  En la sección **Store Location**, seleccione **Current User** y, a continuación, haga clic en **Next**. El certificado se instalará en el almacén de certificados para su cuenta de usuario.

3.  Continúe recorriendo el asistente, acepte los valores predeterminados, hasta llegar a la pantalla **Private key protection**. Aquí, debe escribir la contraseña (si existe) para el certificado. También debe seleccionar **Mark this key as exportable**. Por último, haga clic en **Next**.

    ![protección de clave privada][]

4.  Continúe recorriendo el asistente, acepte los valores predeterminados hasta que el certificado se haya instalado correctamente.

Ahora debe modificar la definición de servicio para hacer referencia al certificado
que instaló.

## <a name="step4"> </a>Paso 4: Modificar la definición del servicio y los archivos de configuración

La aplicación debe estar configurada para hacer referencia al certificado y se debe
agregar un extremo HTTPS. Como resultado, se deben actualizar la definición de servicio
y los archivos de configuración del servicio.

1.  En el directorio de servicio, abra el archivo de definición de servicio
    (ServiceDefinition.csdef), agregue una sección **Certificates** dentro de la sección **WebRole** e incluya la siguiente información acerca del
    certificado:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    La sección **Certificados** define el nombre del certificado,
    su ubicación y el nombre del almacén donde se encuentra. Debido a que instalamos el certificado en el almacén de certificados del usuario, se utiliza un valor de "My". También pueden utilizarse otras ubicaciones de almacén de certificados. Consulte [Asociar un
    certificado con un servicio][] para obtener más información.

2.  En su archivo de definición de servicio, actualice el elemento **InputEndpoint** de http en la sección **Endpoints** para habilitar HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Se han efectuado todos los cambios necesarios en el archivo de definición de
    servicio; sin embargo, todavía necesita agregar la información del certificado en el archivo
    de configuración del servicio.

3.  En sus archivos de configuración del servicio
    (**ServiceConfiguration.Cloud.cscfg** y
    **ServiceConfiguration.Local.cscfg**), agregue el certificado a la sección
    vacía **Certificados** dentro de la sección **Rol** y reemplace el valor
    de huella digital de muestra por el de su
    certificado:

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Actualice la configuración del servicio en la nube publicando otra
    vez su servicio. En el símbolo del sistema de
    Azure PowerShell, escriba **Publish-AzureServiceProject** desde el directorio de servicio.

    Como parte del proceso de publicación, el certificado al que se hace referencia se copiará desde el almacén de certificados local y se incluirá en el paquete de implementación.

## <a name="step5"> </a>Paso 5: Conectarse a la instancia de rol usando HTTPS

Ahora que su implementación está funcionando en Azure, puede
conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione el servicio en la nube y, a continuación, haga clic en **Panel**.

2.  Desplácese hacia abajo y haga clic en el vínculo mostrado como la **dirección URL del sitio**:

    ![La dirección URL del sitio][]

    <div class="dev-callout">
<strong>Nota:</strong>
<p>Si la direcci&oacute;n URL del sitio que se muestra en el portal no especifica HTTPS, entonces debe escribir manualmente la direcci&oacute;n URL en el explorador usando HTTPS en lugar de HTTP.</p>
</div>

3.  Se abrirá un nuevo explorador y aparecerá su sitio web.

    El explorador mostrará un icono de bloqueo para indicar que está
    utilizando una conexión HTTPS. Esto también indica que la aplicación se ha
    configurado correctamente para SSL.

    ![][3]

## Recursos adicionales

[Asociación de un certificado con un servicio][Asociar un
certificado con un servicio]

[Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure][]

[Configurar un certificado SSL en un extremo HTTPS][]

  [Configuración de un certificado SSL para un sitio web de Azure]: ../web-sites-configure-ssl-certificate/
  [Paso 1: Cree un servicio Node.js y publíquelo en la nube]: #step1
  [Paso 2: Obtenga un certificado SSL]: #step2
  [Paso 3: Importar el certificado SSL]: #step3
  [Paso 4: Modificar la definición del servicio y los archivos de configuración]: #step4
  [Paso 5: Conéctese a la instancia de rol con HTTPS]: #step5
  [Icono de Azure PowerShell]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md

  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Uso de Azure PowerShell para Node.js]: https://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [asistente para certificados]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [protección de clave privada]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Asociar un
  certificado con un servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/gg465718.aspx
  [La dirección URL del sitio]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure]: /es-es/develop/nodejs/common-tasks/enable-ssl-worker-role/
  [Configurar un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx
