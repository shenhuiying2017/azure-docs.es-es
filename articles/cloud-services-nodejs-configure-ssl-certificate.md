<properties linkid="dev-nodejs-enablessl" urlDisplayName="Enable SSL" pageTitle="Configure SSL for a cloud service (Node.js) - Azure" metaKeywords="Node.js Azure SSL, Node.js Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a Node.js web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Configuring SSL for a Node.js Application in an Azure Web Role" authors="" solutions="" manager="" editor="" />

Configuración de SSL para una aplicación Node.js en un rol web de Azure
=======================================================================

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común analiza cómo especificar un extremo HTTPS para una aplicación Node.js hospedada como un servicio en la nube de Azure en un rol web y cómo cargar un certificado SSL para proteger la aplicación.

Nota:

Los pasos descritos en este artículo solo se aplican a las aplicaciones de Node hospedadas como un servicio en la nube de Azure en un rol web; para los sitios web, consulte [Configuración de un certificado SSL para un sitio web de Azure](../web-sites-configure-ssl-certificate/).

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Crear un servicio de Node.js y publicar el servicio en la nube](#step1)
-   [Paso 2: Obtener un certificado SSL](#step2)
-   [Paso 3: Importar el certificado SSL](#step3)
-   [Paso 4: Modificar la definición del servicio y los archivos de configuración](#step4)
-   [Paso 5: Conectarse a la instancia de rol usando HTTPS](#step5)

Paso 1: Crear un servicio de Node.js y publicar el servicio en la nube
----------------------------------------------------------------------

Cuando se implementa una aplicación Node.js en un rol web de Azure, el certificado de servidor y la conexión SSL se administran mediante Internet Information Services (IIS), para que el servicio de Node.js pueda escribirse como si fuese un servicio http. Puede crear un sencillo servicio Node.js "hello world" que use Azure PowerShell siguiendo estos pasos:

1.  Desde el **menú Inicio** o la **pantalla Inicio**, busque **Azure PowerShell**. Finalmente, haga clic en el botón secundario en **Azure PowerShell** y seleccione **Ejecutar como administrador**.

    ![Icono de Azure PowerShell](./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

1.  Cree un proyecto de servicio nuevo usando el cmdlet **New-AzureServiceProject**.

    ![](./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png)

2.  Agregue un rol web al servicio usando el cmdlet **Add-AzureNodeWebRole**:

    ![](./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png)

3.  Publique el servicio en la nube utilizando el cmdlet **Publish-AzureServiceProject**:

    ![](./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png)

    **Nota:**

    Si no ha importado previamente la configuración de publicación para su suscripción de Azure, recibirá un error al intentar publicar. Para obtener información sobre la descarga e importación de la configuración de publicación para la suscripción, consulte [Uso de Azure PowerShell para Node.js](https://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

El valor **Created Web Site URL** devuelto por el cmdlet **Publish-AzureServiceProject** contiene el nombre de dominio completo de la aplicación hospedada. Necesitará obtener un certificado SSL para este nombre de dominio completo específico e implementarlo en Azure.

Paso 2: Obtener un certificado SSL
----------------------------------

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves (archivo .pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No se puede adquirir un certificado SSL para el dominio cloudapp.net; por lo tanto, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, **mysecuresite.cloudapp.net**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

Paso 3: Importar el certificado SSL
-----------------------------------

Una vez que cuenta con un certificado, instálelo en el almacén de certificados en el equipo de desarrollo. Este certificado se recuperará y cargará en Azure como parte del paquete de implementación de la aplicación basado en los cambios de configuración que realice en un paso posterior.

**Nota:**

Los pasos utilizados en esta sección se basan en la versión de Windows 8 del Asistente para importación de certificados. Si está usando una versión anterior de Windows, los pasos aquí descritos pueden no coincidir con el orden en el que aparecen en el asistente. Si este es el caso, lea completamente esta sección antes de usar al Asistente para importación de certificados, de modo que comprenda las acciones globales que se deben realizar.

Para importar el certificado SSL, realice los siguientes pasos:

1.  Utilizando el Explorador de Windows, desplácese hasta el directorio donde se encuentra el archivo **.pfx** que contiene el certificado y, a continuación, haga doble clic en él. Se mostrará al Asistente para importación de certificados.



    ![certificate wizard][cert-wizard]

1.  En la sección **Store Location**, seleccione **Current User** y, a continuación, haga clic en **Next**. El certificado se instalará en el almacén de certificados para su cuenta de usuario.

2.  Continúe recorriendo el asistente, acepte los valores predeterminados, hasta llegar a la pantalla **Private key protection**. Aquí, debe escribir la contraseña (si existe) para el certificado. También debe seleccionar **Mark this key as exportable**. Por último, haga clic en **Next**.



    ![private key protection][key-protection]

1.  Continúe recorriendo el asistente, acepte los valores predeterminados hasta que el certificado se haya instalado correctamente.

Ahora debe modificar la definición de servicio para hacer referencia al certificado que instaló.

Paso 4: Modificar la definición del servicio y los archivos de configuración
----------------------------------------------------------------------------

La aplicación debe estar configurada para hacer referencia al certificado y se debe agregar un extremo HTTPS. Como resultado, se deben actualizar la definición de servicio y los archivos de configuración del servicio.

1.  En el directorio de servicio, abra el archivo de definición de servicio (ServiceDefinition.csdef), agregue una sección **Certificates** dentro de la sección **WebRole** e incluya la siguiente información acerca del certificado:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    La sección **Certificates** define el nombre del certificado, su ubicación y el nombre del almacén donde se encuentra. Debido a que instalamos el certificado en el almacén de certificados del usuario, se utiliza un valor de "My". También pueden utilizarse otras ubicaciones de almacén de certificados. Consulte [Asociar un certificado con un servicio](http://msdn.microsoft.com/es-es/library/windowsazure/gg465718.aspx) para obtener más información.

2.  En su archivo de definición de servicio, actualice el elemento **InputEndpoint** de http en la sección **Endpoints** para habilitar HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Se han efectuado todos los cambios necesarios en el archivo de definición de servicio; sin embargo, todavía necesita agregar la información del certificado en el archivo de configuración del servicio.

3.  En sus archivos de configuración del servicio (**ServiceConfiguration.Cloud.cscfg** y **ServiceConfiguration.Local.cscfg**), agregue el certificado a la sección vacía **Certificates** dentro de la sección **Role** y reemplace el valor de huella digital de muestra por el de su certificado:

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Actualice la configuración del servicio en la nube publicando otra vez su servicio. En el símbolo del sistema de Azure PowerShell, escriba **Publish-AzureServiceProject** desde el directorio de servicio.

    Como parte del proceso de publicación, el certificado al que se hace referencia se copiará desde el almacén de certificados local y se incluirá en el paquete de implementación.

Paso 5: Conectarse a la instancia de rol usando HTTPS
-----------------------------------------------------

Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione el servicio en la nube y, a continuación, haga clic en **Panel**.

2.  Desplácese hacia abajo y haga clic en el vínculo mostrado como la **dirección URL del sitio**:

    ![La dirección URL del sitio](./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png)

    **Nota:**

    Si la dirección URL del sitio que se muestra en el portal no especifica HTTPS, entonces debe escribir manualmente la dirección URL en el explorador usando HTTPS en lugar de HTTP.

3.  Se abrirá un explorador nuevo que mostrará su sitio web.

    El explorador mostrará un icono de bloqueo para indicar que está utilizando una conexión HTTPS. Esto también indica que la aplicación se ha configurado correctamente para SSL.

    ![](./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png)

Recursos adicionales
--------------------

[Asociación de un certificado con un servicio](http://msdn.microsoft.com/es-es/library/windowsazure/gg465718.aspx)

[Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure](/es-es/develop/nodejs/common-tasks/enable-ssl-worker-role/)

[Configurar un certificado SSL en un extremo HTTPS](http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx)

  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  [How to Associate a Certificate with a Service]: http://msdn.microsoft.com/es-es/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [How to Configure an SSL Certificate on an HTTPS Endpoint]: http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Configuring SSL for a Node.js Application in an Azure Worker Role]: /es-es/develop/nodejs/common-tasks/enable-ssl-worker-role/