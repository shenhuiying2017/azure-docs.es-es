<properties 
	pageTitle="Configuración de SSL para un servicio en la nube - Azure" 
	description="Aprenda a especificar un extremo HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="adegeo"/>




# Configuración de SSL para una aplicación en Azure

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común analiza cómo especificar un extremo HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación.

> [AZURE.NOTE] Los procedimientos de esta tarea se aplican a los Servicios en la nube de Azure. Para los sitios web, consulte [Configuración de un certificado SSL para un sitio web de Azure](../articles/web-sites-configure-ssl-certificate/).

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Obtenga un certificado SSL][]
-   [Paso 2: Modificar la definición del servicio y los archivos de configuración][]
-   [Paso 3: Cargar el paquete de implementación y el certificado][]
-   [Paso 4: Conectarse a la instancia de rol con HTTPS][]

Esta tarea utiliza una implementación de producción; al final de este tema se entrega información sobre el uso de una implementación de ensayo.

<h2><a name="step1"> </a>Paso 1: Obtener un certificado SSL</h2>

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene una de estas firmas, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para usarlo cuando obtenga acceso a su servicio. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usó para tener acceso a su aplicación. Por ejemplo, si el nombre del dominio personalizado es **contoso.com** debe solicitar un certificado de la CA para ***. contoso.com** o **www.contoso.com**.
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

Para propósitos de prueba, puede crear y usar un certificado autofirmado. Un certificado autofirmado no está autenticado por una CA y puede usar el dominio cloudapp.net como la dirección URL del sitio web. Por ejemplo, la tarea siguiente usa un certificado autofirmado en el que el nombre común (CN) usado en el certificado es **sslexample.cloudapp.net**. Para obtener más información sobre cómo crear un certificado autofirmado con el Administrador de IIS, consulte [Creación de un certificado para un rol][].

A continuación, debe incluir información sobre el certificado en su definición de servicio y los archivos de configuración del servicio.

<h2><a name="step2"> </a>Paso 2: Modificar la definición del servicio y los archivos de configuración</h2>

Su aplicación debe estar configurada para usar el certificado y se debe agregar un extremo HTTPS. Como resultado, se deben actualizar la definición de servicio y los archivos de configuración del servicio.

1.  En el entorno de desarrollo, abra el archivo de definición de servicio
    (CSDEF), agregue una sección **Certificates** dentro de la sección **WebRole**
    e incluya la siguiente información sobre el
    certificado:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    La sección **Certificates** define el nombre de nuestro certificado, su ubicación y el nombre de la tienda donde se encuentra. Hemos decidido guardar el certificado en la CA (entidad de certificación), pero puede elegir otras opciones también. Consulte [Asociar un certificado con un servicio][] para obtener más información.

2.  En el archivo de definición de servicio, agregue un elemento **InputEndpoint**
    en la sección **Endpoints** para habilitar HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  En el archivo de definición de servicio, agregue un elemento **Binding** en
    la sección **Sites**. Esto agrega un enlace de HTTPS para asignar el
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

    Todos los cambios requeridos en el archivo de definición de servicio se
    completaron, pero todavía es necesario agregar la información del certificado al
    archivo de configuración de servicio.

4.  En el archivo de configuración de servicio (CSCFG), ServiceConfiguration.Cloud.cscfg, agregue una sección **Certificates**
    dentro de la sección **Role** y reemplace el valor de la huella digital
    de muestra que se indica a continuación por el de su certificado:

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

Ahora que los archivos de definición y configuración de servicio se han
actualizado, empaquete la implementación para cargarla en Azure. Si
va a usar **cspack**, asegúrese de no usar la marca
**/generateConfigurationFile**, ya que de esta forma se sobrescribirá la
información del certificado que acaba de insertar.

<h2><a name="step3"> </a>Paso 3: Cargar el paquete de implementación y el certificado</h2>

El paquete de implementación se ha actualizado para usar el certificado y
se ha agregado un extremo HTTPS. Ahora podrá cargar el paquete y
el certificado en Azure con el Portal de administración.

1. Inicie sesión en el [Portal de administración de Azure][]. 
2. Haga clic en **Nuevo**, **Servicio en la nube** y, después, en **Creación personalizada**.
3. En el cuadro de diálogo **Crear un servicio en la nube**, escriba los valores de la dirección URL, la región o grupo de afinidad y la suscripción. Asegúrese de que la opción **Implementar un paquete de servicios en la nube** esté marcada y haga clic en el botón **Siguiente**.
3. En el cuadro de diálogo **Publicar su servicio en la nube**, escriba la información necesaria para el servicio en la nube, seleccione **Producción** para el entorno y asegúrese de que **Agregar certificados ahora** se haya marcado. (Si cualquiera de los roles contiene una sola instancia, asegúrese de que **Implementar aunque uno o varios roles contengan una sola instancia** se haya marcado). 

    ![Publish your cloud service][0]

4.  Haga clic en el botón **Siguiente**.
5.  En el cuadro de diálogo **Agregar certificado**, especifique la ubicación del archivo .pfx
    del certificado SSL, la contraseña del certificado y haga clic en
    **Adjuntar certificado**.  

    ![Add certificate][1]

6.  Asegúrese de que el certificado aparezca en la sección **Certificados adjuntos**.

    ![Attached certificates][4]

7.  Haga clic en el botón **Completar** para crear el servicio en la nube. Cuando la implementación haya llegado al estado **Listo**, puede continuar con los pasos siguientes.

<h2><a name="step4"> </a>Paso 4: Conectarse a la instancia de rol con HTTPS</h2>

Ahora que su implementación está funcionando en Azure, puede
conectarse a ella con HTTPS.

1.  En el Portal de administración, seleccione su implementación y, a continuación, haga clic en vínculo en **Dirección URL del sitio**.

    ![Determine site URL][2]

2.  En el explorador web, modifique el vínculo para usar **https** en lugar de **http** y, a continuación, visite la página.

    **Nota:** si usa un certificado autofirmado, al
    desplazarse a un extremo HTTPS asociado a dicho
    certificado, aparecerá un error de certificado en el explorador. El uso de un
    certificado firmado por una entidad de certificación de confianza eliminará el problema; mientras tanto, puede ignorar el error. (Otra opción es agregar el certificado autofirmado a la tienda de certificados de la entidad de certificación de confianza para el usuario).

    ![SSL example web site][3]

Si desea usar SSL para una implementación de ensayo en vez de una implementación de producción, tendrá que determinar primero la dirección URL que se usó para la implementación de ensayo. Implemente su servicio en la nube para el entorno de ensayo sin incluir un certificado ni ninguna información del certificado. Una vez implementado, puede determinar la dirección URL basada en el GUID, que se incluye en el campo **Dirección URL del sitio** del Portal de administración. Cree un certificado con un nombre común (CN) igual a la dirección URL basada en el GUID (por ejemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), use el Portal de administración para agregar el certificado al servicio en la nube de ensayo, agregue la información del certificado a los archivos CSDEF y CSCFG, vuelva a empaquetar la aplicación y actualice la implementación de ensayo para usar el paquete y el archivo CSCFG nuevos.

<h2><a name="additional_resources"> </a>Recursos adicionales</h2>

* [Asociación de un certificado con un servicio][]

* [Configuración de un certificado SSL en un extremo HTTPS][]

  [Paso 1: Obtener un certificado SSL]: #step1
  [Paso 1: Obtenga un certificado SSL]: #step1
  [Paso 2: Modificar la definición del servicio y los archivos de configuración]: #step2
  [Paso 3: Cargar el paquete de implementación y el certificado]: #step3
  [Paso 4: Conectarse a la instancia de rol con HTTPS]: #step4
  [Creación de un certificado para un rol]: http://msdn.microsoft.com/library/windowsazure/gg432987.aspx
  [Asociar un certificado con un servicio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [Asociación de un certificado con un servicio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png  
  [Configuración de un certificado SSL en un extremo HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx

<!--HONumber=45--> 
