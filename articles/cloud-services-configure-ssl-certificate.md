<properties urlDisplayName="Enable SSL" pageTitle="Configuración de SSL para un servicio en la nube - Azure" metaKeywords="Azure SSL, Azure HTTPS, Azure SSL, Azure HTTPS, .NET Azure SSL, .NET Azure HTTPS, C# Azure SSL, C# Azure HTTPS, VB Azure SSL, VB Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Configuring SSL for an application in Azure" authors="adegeo" solutions="" manager="timlt" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="adegeo" />




# Configuring SSL for an application in Azure

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común analiza cómo especificar un extremo HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación.

<div class="dev-callout">Nota:
<p>Los procedimientos de esta tarea se aplican a los Servicios en la nube de Azure. <a href="../web-sites-configure-ssl-certificate/">Configuración de un certificado SSL para un Sitio web de Azure</a>.</p>
</div>

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Obtener un certificado SSL][]
-   [Paso 2: Modificar la definición del servicio y los archivos de configuración][]
-   [Paso 3: Cargar el paquete de implementación y el certificado][]
-   [Paso 4: Conectarse a la instancia de rol con HTTPS][]

Esta tarea utiliza una implementación de producción; al final de este tema se entrega información sobre el uso de una implementación de ensayo.

<h2><a name="step1"> </a>Paso 1: Obtener un certificado SSL</h2>

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para usarlo cuando obtenga acceso a su servicio. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usó para tener acceso a su aplicación. Por ejemplo, si su nombre de dominio personalizado es **contoso.com** debe solicitar un certificado de su CA para ***.contoso.com** o **www.contoso.com**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

Para propósitos de prueba, puede crear y usar un certificado autofirmado. Un certificado autofirmado no está autenticado por una CA y puede usar el dominio cloudapp.net como la dirección URL del sitio web. Por ejemplo, la tarea a continuación usa un certificado autofirmado en el que el nombre común (CN) usado en el certificado es **sslexample.cloudapp.net**. Para obtener detalles sobre la creación de un certificado autofirmado con Administrador de IIS, consulte [Creación de un certificado para un rol][].

A continuación, debe incluir información sobre el certificado en su definición de servicio y los archivos de configuración del servicio.

<h2><a name="step2"> </a>Paso 2: Modificar la definición del servicio y los archivos de configuración</h2>

Su aplicación debe estar configurada para usar el certificado y se debe agregar un extremo HTTPS. Como resultado, se deben actualizar la definición de servicio y los archivos de configuración del servicio.

1.  En su entorno de desarrollo, abra el archivo de definición de servicio (CSDEF), agregue una sección de **Certificado** dentro de la sección **WebRole** e incluya la siguiente información sobre el certificado:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    La sección **Certificado** define el nombre de nuestro certificado, su ubicación y el nombre de la tienda donde se encuentra. Hemos decidido guardar el certificado en la CA (entidad de certificación), pero puede elegir otras opciones también. Consulte [Asociación de un certificado con un servicio][] para obtener más información.

2.  En su archivo de definición de servicio, agregue un elemento **InputEndpoint **en la sección Extremos para habilitar HTTPS: 
        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  En su archivo de definición de servicio, agregue un elemento **Enlace
    en la sección **Sitios**. Esto agrega un enlace de HTTPS para asignar el
    extremo a su sitio:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Se han efectuado todos los cambios necesarios en el archivo de definición de servicio; sin embargo, todavía necesita agregar la información del certificado en el archivo de configuración del servicio.

4.  En su archivo de configuración de servicio (CSCFG), ServiceConfiguration.Cloud.cscfg, agregue una sección **Certificados** en la sección **Rol** y reemplace el valor de la huella digital de muestra que se indica a continuación por el de su certificado:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(El ejemplo anterior usa **sha1** para el algoritmo de huella digital. Especifique el valor adecuado para el algoritmo de huella digital de su certificado).

Ahora que se actualizaron los archivos de definición del servicio y configuración del servicio, prepare su implementación para cargarla en Azure. Si va a usar **cspack**, asegúrese de no usar la marca **/generateConfigurationFile**, puesto que así se sobrescribe la información del certificado que acaba de insertar.

<h2><a name="step3"> </a>Paso 3: Cargar el paquete de implementación y el certificado</h2>

Su paquete de implementación se actualizó para usar el certificado y se agregó un extremo HTTPS. Ahora podrá cargar el paquete y el certificado en Azure con el Portal de administración.

1. Inicie sesión en el [Portal de administración de Azure][]. 
2. Haga clic en **Nuevo**, haga clic en **Servicio en la nube** y, a continuación, haga clic en **Creación personalizada**.
3. En el cuadro de diálogo **Crear un servicio en la nube**, escriba los valores de la dirección URL, la región/grupo de afinidad y la suscripción. Asegúrese de que **Implementar un paquete de servicios en la nube** esté marcado y haga clic en el botón **Siguiente**.
3. En el cuadro de diálogo **Publicar su servicio en la nube**, escriba la información requerida para su servicio en la nube, seleccione **Producción** para el entorno y asegúrese de que **Agregar certificados ahora** esté seleccionado. (Si cualquiera de sus roles contiene una sola instancia, asegúrese de que **Implementar aunque uno o varios roles contengan una sola instancia** esté seleccionado). 

    ![Publish your cloud service][0]

4.  Haga clic en el botón **Siguiente**.
5.  En el cuadro de diálogo **Agregar certificado**, especifique la ubicación del archivo .pfx del certificado SSL, la contraseña del certificado y haga clic en **Adjuntar certificado**.  

    ![Add certificate][1]

6.  Asegúrese de que el certificado aparezca en la sección **Certificados adjuntos**.

    ![Attached certificates][4]

7.  Haga clic en el botón **Completar** para crear su servicio en la nube. Cuando la implementación haya llegado al estado **Listo**, puede continuar con los pasos siguientes.

<h2><a name="step4"> </a>Paso 4: Conectarse a la instancia de rol con HTTPS</h2>

Ahora que su implementación está funcionando en Azure, puede
conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione su implementación y, a continuación, haga clic en vínculo en **Dirección URL del sitio**.

    ![Determine site URL][2]

2.  En su explorador web, modifique el vínculo para usar **https** en vez de **http** y, a continuación, visite la página.

    **Nota:** Si va a usar un certificado autofirmado, cuando vaya a un extremo HTTPS que está asociado al certificado autofirmado, aparecerá un error de certificado en el explorador. El uso de un certificado firmado por una entidad de certificación de confianza elimina el problema; mientras tanto, puede ignorar el error. (Otra opción es agregar el certificado autofirmado a la tienda de certificados de la entidad de certificación de confianza para el usuario).

    ![SSL example web site][3]

Si desea usar SSL para una implementación de ensayo en vez de una implementación de producción, tendrá que determinar primero la dirección URL que se usó para la implementación de ensayo. Implemente su servicio en la nube para el entorno de ensayo sin incluir un certificado ni ninguna información del certificado. Después de implementado, puede determinar la dirección URL basada en el GUID, que se menciona en el campo **Dirección URL del sitio** del Portal de administración. Cree un certificado con un nombre común (CN) igual a la URL basado en el GUID (por ejemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), use el Portal de administración para agregar el certificado a su servicio en la nube de ensayo, agregue la información del certificado a sus archivos CSDEF y CSCFG, vuelva a empaquetar la aplicación y actualice su implementación de ensayo a fin de usar el paquete y el archivo CSCFG nuevos.

<h2><a name="additional_resources"> </a>Recursos adicionales</h2>

* [Asociación de un certificado con un servicio][]

* [Configuración de un certificado SSL en un extremo HTTPS][]

  [Paso 1: Obtener un certificado SSL]: #step1
  [Paso 2: Modificar la definición del servicio y los archivos de configuración]: #step2
  [Paso 3: Cargar el paquete de implementación y el certificado]: #step3
  [Paso 4: Conectarse a la instancia de rol con HTTPS]: #step4
  [Creación de un certificado para un rol]: http://msdn.microsoft.com/en-us/library/windowsazure/gg432987.aspx
  [Asociación de un certificado con un servicio]: http://msdn.microsoft.com/en-us/library/windowsazure/gg465718.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png  
  [How to configure an SSL certificate on an HTTPS endpoint]: http://msdn.microsoft.com/en-us/library/windowsazure/ff795779.aspx

<!--HONumber=35_1-->
