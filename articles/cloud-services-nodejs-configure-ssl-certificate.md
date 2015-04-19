<properties 
	pageTitle="Configuración de SSL para un servicio en la nube (Node.js) - Azure" 
	description="Vea cómo especificar un extremo HTTPS para un rol web de Node.js y cómo cargar un certificado SSL para proteger su aplicación." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>




# Configuración de SSL para una aplicación Node.js en un rol web de Azure

Cifrado de Secure Socket Layer (SSL) es el método más utilizado
proteger los datos enviados a través de internet. Esta tarea común analiza cómo
especificar un extremo HTTPS para una aplicación Node.js hospedada como un servicio de nube de Azure en un rol web y cómo cargar un
certificado SSL para proteger su aplicación.

> [AZURE.NOTE] Los pasos descritos en este artículo solo se aplican a las aplicaciones de Node hospedadas como un servicio de nube de Azure en un rol web; para los sitios Web, consulte [Configurar un certificado SSL para un sitio Web de Azure](web-sites-configure-ssl-certificate.md).

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Cree un servicio Node.js y publíquelo en la nube]
-   [Paso 2: Obtenga un certificado SSL]
-   [Paso 3: Importar el certificado SSL]
-   [Paso 4: Modificar la definición del servicio y los archivos de configuración]
-   [Paso 5: Conéctese a la instancia de rol con HTTPS]

## <a name="step1"> </a>Paso 1: Crear un servicio de Node.js y publicar el servicio en la nube

Cuando se implementa una aplicación Node.js en un rol web de Azure, el
certificado de servidor y la conexión SSL se administran por Internet
Information Services (IIS), por lo que el servicio de Node.js se puede escribir
como si fuese un servicio http. Puede crear un sencillo servicio Node.js 'hello
world' con Azure PowerShell siguiendo estos pasos:

1. Desde el **menú Inicio** o la **pantalla Inicio**, busque **Azure PowerShell**. Finalmente, haga clic en el botón secundario en **Azure PowerShell** y seleccione **Ejecutar como administrador**.

	![Azure PowerShell icon][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Cree un nuevo proyecto de servicio mediante el uso del cmdlet **New-AzureServiceProject**. 

	![][1]

3.  Agregue un rol web al servicio mediante el uso del cmdlet **Add-AzureNodeWebRole**:

    ![][2]

4.  Publique su servicio en la nube mediante el uso del cmdlet **Publish-AzureServiceProject**:

    ![][3]

	> [AZURE.NOTE] Si no ha importado previamente la configuración de publicación para su suscripción de Azure, recibirá un error al intentar publicar. Para obtener información sobre la descarga e importación de la configuración de publicación para la suscripción, consulte [Uso de Azure PowerShell para Node.js](https://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings).

El valor **Dirección URL del sitio web creado** devuelto por el cmdlet **Publish-AzureServiceProject** contiene el nombre de dominio completo de la aplicación hospedada. Necesitará obtener un certificado SSL para este nombre de dominio completo específico e implementarlo en Azure.

## <a name="step2"> </a>Paso 2: Obtener un certificado SSL

Para configurar SSL para una aplicación, primero debe obtener un certificado SSL
firmado por una entidad de certificación (CA), un
tercero de confianza que emite certificados con esta finalidad. Si todavía
no tiene uno, deberá obtenerlo de una empresa que
venda certificados SSL.

El certificado debe cumplir los siguientes requisitos para
certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves (archivo .pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso
    al servicio en la nube. No es posible adquirir un certificado SSL para el
    dominio cloudapp.net, por lo que el nombre de sujeto del certificado debe coincidir con el
    nombre de dominio personalizado que se usa para tener acceso a la aplicación. Por ejemplo, __mysecuresite.cloudapp.net__.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

## <a name="step3"> </a>Paso 3: Importar el certificado SSL

Una vez que cuenta con un certificado, instálelo en el almacén de certificados en el equipo de desarrollo. Este certificado se recuperará y cargará en Azure como parte del paquete de implementación de la aplicación basado en los cambios de configuración que realice en un paso posterior.

> [AZURE.NOTE] Los pasos utilizados en esta sección se basan en la versión de Windows 8 del Asistente para importación de certificados. Si está usando una versión anterior de Windows, los pasos aquí descritos pueden no coincidir con el orden en el que aparecen en el asistente. Si este es el caso, lea completamente esta sección antes de usar al Asistente para importación de certificados, de modo que comprenda las acciones globales que se deben realizar.

Para importar el certificado SSL, realice los siguientes pasos:

1.   En el Explorador de Windows, vaya al directorio donde se encuentra el archivo **.pfx** que contiene el certificado y, a continuación, haga doble clic en el certificado. Se mostrará al Asistente para importación de certificados.
	
	![certificate wizard][cert-wizard]

2.   En la sección **Ubicación del almacén**, seleccione **Usuario actual** y, a continuación, haga clic en **Siguiente**. El certificado se instalará en el almacén de certificados para su cuenta de usuario.

3.   Continúe con el asistente y acepte los valores predeterminados hasta llegar a la pantalla **Protección de clave privada**. Aquí, debe escribir la contraseña (si existe) para el certificado. También debe seleccionar **Marcar esta clave como exportable**. Finalmente, haga clic en **Siguiente**.

	![private key protection][key-protection]

4. Continúe recorriendo el asistente, acepte los valores predeterminados hasta que el certificado se haya instalado correctamente.

Ahora debe modificar la definición de servicio para hacer referencia al certificado que
instaló.

## <a name="step4"> </a>Paso 4: Modificar la definición del servicio y los archivos de configuración

Su aplicación debe estar configurada para que haga referencia al certificado y se debe
agregar un extremo HTTPS. Como resultado, se debe actualizar la definición de servicio y
los archivos de configuración de servicio.

1.  En el servicio de directorio, abra el archivo de definición de servicio
    (ServiceDefinition.csdef), agregue una sección **Certificados** dentro de la sección **WebRole** e incluya la siguiente información acerca del
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
    su ubicación y el nombre del almacén donde está ubicado. Debido a que instalamos el certificado en el almacén de certificados del usuario, se utiliza un valor de "My". También pueden utilizarse otras ubicaciones de almacén de certificados. Consulte [Asociación de un certificado con un servicio] para obtener más información.

2.  En el archivo de definición de servicio, actualice el elemento http **InputEndpoint** dentro de la sección **Extremos** para habilitar HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Todos los cambios requeridos en el archivo de definición de servicio se
    completaron, pero todavía es necesario agregar la información del certificado al
    archivo de configuración de servicio.

3.  En los archivos de configuración de servicio
    (**ServiceConfiguration.Cloud.cscfg** y
    **ServiceConfiguration.Local.cscfg**), agregue el certificado a la
    sección **Certificados** vacía dentro de la sección **Rol**,
    reemplazando el valor de huella digital que aparece a continuación por el valor de su
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

4.  Actualice la configuración modificada en la nube mediante una nueva publicación
    del servicio. En el símbolo del sistema PowerShell de Azure,
    escriba **Publish-AzureServiceProject** desde el directorio de servicio.

	Como parte del proceso de publicación, el certificado al que se hace referencia se copiará desde el almacén de certificados local y se incluirá en el paquete de implementación.

## <a name="step5"> </a>Paso 5: Conectarse a la instancia de rol usando HTTPS

Ahora que su implementación está funcionando en Azure, puede
conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione el servicio en la nube y, a continuación, haga clic en **Panel**.

2. Desplácese hacia abajo y haga clic en el vínculo que aparece como la **dirección URL del sitio**:

    ![the site url][site-url]

	> [AZURE.IMPORTANTE] Si la dirección URL del sitio que aparece en el portal no especifica HTTPS, debe escribir manualmente la dirección URL en el explorador con HTTPS en lugar de HTTP.

3.  Se abrirá un nuevo explorador y aparecerá su sitio web.

    El explorador mostrará un icono de bloqueo para indicar que
    usa una conexión HTTPS. Esto también indica que su aplicación
    se configuró correctamente para SSL.

    ![][8]

## Recursos adicionales

[Asociación de un certificado con un servicio]

[Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure]

[Configuración de un certificado SSL en un extremo HTTPS]

  [Paso 1: Crear un servicio de Node.js y publicar el servicio en la nube]: #step1
  [Paso 1: Cree un servicio Node.js y publíquelo en la nube]: #step1
  [Paso 2: Obtener un certificado SSL]: #step2
  [Paso 2: Obtenga un certificado SSL]: #step2
  [Paso 3: Importar el certificado SSL]: #step3
  [Paso 4: Modificar la definición del servicio y los archivos de configuración]: #step4
  [Paso 5: Conectarse a la instancia de rol usando HTTPS]: #step5
  [Paso 5: Conéctese a la instancia de rol con HTTPS]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  
  
  [Cómo asociar un certificado a un servicio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [Asociación de un certificado con un servicio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Cómo configurar un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [Configuración de un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Configuración de SSL para una aplicación Node.js en un rol de trabajo de Azure]: /es-es/develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=45--> 
