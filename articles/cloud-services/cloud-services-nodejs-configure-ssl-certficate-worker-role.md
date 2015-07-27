<properties 
	pageTitle="Configuración de SSL para un rol de trabajo de un servicio en la nube (Node.js)" 
	description="Configuración de SSL para un rol de trabajo de un servicio en la nube Node.js en Azure" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mwasson"/>





# Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común trata cómo especificar un extremo HTTPS para una aplicación Node.js hospedada como servicio de nube de Azure en un rol de trabajo.

> [AZURE.NOTE]Los pasos descritos en este artículo solo se aplican a las aplicaciones hospedadas como un servicio de nube de Azure en un rol de trabajo.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: crear un servicio de Node.js y publicar el servicio en la nube]
-   [Paso 2: obtener un certificado SSL]
-   [Paso 3: modificar la aplicación para que use el certificado SSL]
-   [Paso 4: modificar el archivo de definición de servicio]
-   [Paso 5: conectarse a la instancia de rol con HTTPS]

## <a name="step1"> </a>Paso 1: crear un servicio de Node.js y publicar el servicio en la nube

Puede crear un sencillo servicio Node.js "hello world" que use Azure PowerShell siguiendo estos pasos:

1. Desde el **menú Inicio** o la **pantalla Inicio**, busque **PowerShell de Azure**. Finalmente, haga clic con el botón secundario en **PowerShell de Azure** y seleccione **Ejecutar como administrador**.

	![Icono de Azure PowerShell][powershell-menu]

	

2.  Cree un nuevo servicio; para ello, use el cmdlet **New-AzureServiceProject**.

	![][1]

3.  Agregue un rol de trabajo al servicio; para ello, use el cmdlet **Add-AzureNodeWorkerRole**:

    ![][2]

4.  Publique su servicio en la nube mediante el uso del cmdlet **Publish-AzureServiceProject**:

    ![][3]

	> [AZURE.NOTE]Si no ha importado previamente la configuración de publicación para su suscripción de Azure, recibirá un error al intentar publicar. Para obtener información sobre la descarga e importación de la configuración de publicación para la suscripción, consulte [Uso de Azure PowerShell para Node.js](https://www.windowsazure.com/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings).

El valor **Dirección URL del sitio web creado** devuelto por el cmdlet **Publish-AzureServiceProject** contiene el nombre de dominio completo de la aplicación hospedada. Necesitará obtener un certificado SSL para este nombre de dominio completo específico e implementarlo en Azure.

## <a name="step2"> </a>Paso 2: obtener un certificado SSL

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves (archivo **.pfx**).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No se puede adquirir un certificado SSL para el dominio cloudapp.net; por lo tanto, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, __mysecuresite.cloudapp.net__.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

El archivo **.pfx** que contiene el certificado se agregará al proyecto de servicio y se implementará en Azure en los pasos siguientes.

## <a name="step3"> </a>Paso 3: modificar la aplicación para que use el certificado SSL

Cuando se implementa una aplicación Node.js en un rol de trabajo, el certificado de servidor y la conexión SSL se administran por Node.exe. Para controlar el tráfico SSL, debe usar el módulo 'https' en lugar de 'http'. Realice los pasos siguientes para agregar el certificado SSL al proyecto y, a continuación, modifique la aplicación para que use el certificado.

1.   Guarde el archivo **.pfx** proporcionado por la entidad de certificación (CA) en el directorio que contiene la aplicación. Por ejemplo, **c:\\\\node\\\\securesite\\\\workerrole1** es el directorio que contiene la aplicación usada en este artículo.

2.   Abra el archivo **c:\\\\node\\\\securesite\\\\workerrole1\\server.js** usando Notepad.exe y reemplace el contenido del archivo por lo siguiente:

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

	> [AZURE.IMPORTANT]debe reemplazar "certificate.pfx" por el nombre del archivo de certificado y "password" por la contraseña (si la hubiera) del archivo de certificado.

3.   Guarde el archivo **server.js**.

Las modificaciones realizadas en el archivo **server.js** consiguen que la aplicación escuche la comunicación en el puerto 443 (el puerto estándar para las comunicaciones SSL) cuando se implementa en Azure. El archivo **.pfx** se usará para implementar las comunicaciones SSL por medio de este transporte.

## <a name="step4"> </a>Paso 4: modificar el archivo de definición de servicio

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

4.  Actualice la configuración modificada en la nube publicando el servicio de nuevo. En el símbolo del sistema de Azure PowerShell, escriba **Publish-AzureServiceProject** desde el directorio de servicio.

## <a name="step5"> </a>Paso 5: conectarse a la instancia de rol con HTTPS

Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione el servicio en la nube y, a continuación, haga clic en **Panel**.

2. Desplácese hacia abajo y haga clic en el vínculo que aparece como la dirección **URL del sitio**:

    ![La dirección URL del sitio][site-url]

	> [AZURE.IMPORTANT]Si la dirección URL del sitio que se muestra en el portal no especifica HTTPS, entonces debe escribir manualmente la dirección URL en el explorador usando HTTPS en lugar de HTTP.

3.  Se abrirá un nuevo explorador y aparecerá su sitio web.

    El explorador mostrará un icono de bloqueo para indicar que está utilizando una conexión HTTPS. Esto también indica que la aplicación se ha configurado correctamente para SSL.

    ![][8]

## Recursos adicionales

[Asociación de un certificado con un servicio]

[Configuración de SSL para una aplicación Node.js en un rol web de Azure]

[Configuración de un certificado SSL en un extremo HTTPS]

  [Paso 1: crear un servicio de Node.js y publicar el servicio en la nube]: #step1
  [Paso 2: obtener un certificado SSL]: #step2
  [Paso 3: modificar la aplicación para que use el certificado SSL]: #step3
  [Paso 4: modificar el archivo de definición de servicio]: #step4
  [Paso 5: conectarse a la instancia de rol con HTTPS]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  [Asociación de un certificado con un servicio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Configuración de un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  
  
  [Configuración de SSL para una aplicación Node.js en un rol web de Azure]: /develop/nodejs/common-tasks/enable-ssl/
  
 

<!---HONumber=July15_HO3-->