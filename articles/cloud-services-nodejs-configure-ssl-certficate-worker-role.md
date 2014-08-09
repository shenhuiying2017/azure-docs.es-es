<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="" solutions="" manager="" editor="" />

Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure
==============================================================================

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común trata cómo especificar un extremo HTTPS para una aplicación Node.js hospedada como servicio de nube de Azure en un rol de trabajo.

**Nota:**

Los pasos descritos en este artículo solo se aplican a las aplicaciones hospedadas como un servicio de nube de Azure en un rol de trabajo.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Cree un servicio Node.js y publíquelo en la nube](#step1)
-   [Paso 2: Obtenga un certificado SSL](#step2)
-   [Paso 3: Modifique la aplicación para que use el certificado SSL](#step3)
-   [Paso 4: Modifique el archivo de definición de servicio](#step4)
-   [Paso 5: Conéctese a la instancia de rol con HTTPS](#step5)

Paso 1: Cree un servicio Node.js y publíquelo en la nube
--------------------------------------------------------

Puede crear un sencillo servicio Node.js "hello world" que use Azure PowerShell siguiendo estos pasos:

1.  Desde el **menú Inicio** o la **pantalla Inicio**, busque **Azure PowerShell**. Finalmente, haga clic en el botón secundario en **Azure PowerShell** y seleccione **Ejecutar como administrador**.

    ![Icono de Azure PowerShell](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png)

2.  Cree un nuevo servicio; para ello, use el cmdlet **New-AzureServiceProject**.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png)

3.  Agregue un rol de trabajo al servicio; para ello, use el cmdlet **Add-AzureNodeWorkerRole**:

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png)

4.  Publique el servicio en la nube; para ello, use el cmdlet **Publish-AzureServiceProject**:

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png)

    **Nota:**

    Si no ha importado previamente la configuración de publicación para su suscripción de Azure, recibirá un error al tratar de publicar. Para obtener información acerca de la descarga y la importación de la configuración de publicación para su suscripción, consulte [Uso de Azure PowerShell para Node.js](https://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

El valor **Dirección URL del sitio web creado** devuelto por el cmdlet **Publish-AzureServiceProject** contiene el nombre de dominio completo de la aplicación hospedada. Tendrá que obtener un certificado SSL para este nombre de dominio completo e implementarlo en Azure.

Paso 2: Obtenga un certificado SSL
----------------------------------

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves (archivo **.pfx**).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No se puede adquirir un certificado SSL para el dominio cloudapp.net; por lo tanto, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, **mysecuresite.cloudapp.net**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

El archivo **.pfx** que contiene el certificado se agregará al proyecto de servicio y se implementará en Azure en los pasos siguientes.

Paso 3: Modifique la aplicación para que use el certificado SSL
---------------------------------------------------------------

Cuando se implementa una aplicación Node.js en un rol de trabajo, Node.exe administra el certificado del servidor y la conexión SSL. Para controlar el tráfico SSL, debe usar el módulo "https" en lugar de "http". Realice los pasos siguientes para agregar el certificado SSL al proyecto y, a continuación, modifique la aplicación para que use el certificado.

1.  Guarde el archivo **.pfx** proporcionado por la entidad de certificación (CA) en el directorio que contiene la aplicación. Por ejemplo, el directorio que contiene la aplicación usada en este artículo es **c:\\node\\securesite\\workerrole1**.

2.  Abra el archivo **c:\\node\\securesite\\workerrole1\\server.js** usando Notepad.exe y reemplace el contenido del archivo por lo siguiente:

		var https = require('https');
		var fs = require('fs');

		var options = { 
			pfx: fs.readFileSync('certificate.pfx'),
			passphrase: "password"
		};
		var port = process.env.PORT || 8000;
		https.createServer(options, function (req, res) {
			res.writeHead(200, { 'Content-Type': 'text/plain' });
			res.end('Hello World\n');
		}).listen(port);


	<div class="dev-callout">
	<strong>Nota:</strong>
	<p>debe reemplazar "certificate.pfx" por el nombre del archivo de certificado y "password" por la contraseña (si la hubiera) del archivo de certificado.</p>
	</div>

1.  Guarde el archivo **server.js**.

Las modificaciones realizadas en el archivo **server.js** consiguen que la aplicación escuche la comunicación del puerto 443 (el puerto estándar para las comunicaciones SSL) cuando se implementa en Azure. El archivo **.pfx** se usará para implementar las comunicaciones SSL por medio de este transporte.

Paso 4: Modifique el archivo de definición de servicio
------------------------------------------------------

Como la aplicación ahora escucha al puerto 443, también debe modificar la definición de servicio para permitir la comunicación por medio de este puerto.

1.  En el directorio de servicio, abra el archivo de definición de servicio (**ServiceDefinition.csdef**), actualice el elemento http **InputEndpoint** que se encuentra en la sección **Extremos** para permitir la comunicación por medio del puerto 443:

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    Después de realizar este cambio, guarde el archivo **ServiceDefinition.csdef**.

2.  Actualice la configuración modificada en la nube publicando el servicio de nuevo. En el símbolo del sistema de Azure PowerShell, escriba **Publish-AzureServiceProject** desde el directorio de servicio.

Paso 5: Conéctese a la instancia de rol con HTTPS
-------------------------------------------------

Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione el servicio en la nube y, a continuación, haga clic en **Panel**.

2.  Desplácese hacia abajo y haga clic en el vínculo mostrado como **Dirección URL del sitio**:

    ![dirección url del sitio](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png)

    **Nota:**

    Si la dirección URL del sitio que aparece en el portal no especifica HTTPS, entones debe escribir manualmente la dirección URL en el explorador usando HTTPS en lugar de HTTP.

3.  Se abrirá un nuevo explorador y aparecerá su sitio web.

    El explorador mostrará un icono de bloqueo que indica el uso de una conexión HTTPS. Esto también indica que la aplicación se ha configurado correctamente para SSL.

    ![](./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png)

Recursos adicionales
--------------------

[Asociación de un certificado con un servicio](http://msdn.microsoft.com/es-es/library/windowsazure/gg465718.aspx)

[Configuración de SSL para una aplicación Node.js en un rol web de Azure](/es-es/develop/nodejs/common-tasks/enable-ssl/)

[Configurar un certificado SSL en un extremo HTTPS](http://msdn.microsoft.com/es-es/library/windowsazure/ff795779.aspx)

