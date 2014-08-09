<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Creación de certificados de administración para Linux en Azure
==============================================================

Es necesario contar con un certificado de administración cuando desea utilizar la API de administración de servicios para interactuar con la plataforma de imágenes de Azure.

Documentación sobre cómo crear y administrar estos certificados ya se encuentra disponible en <http://msdn.microsoft.com/es-es/library/windowsazure/gg551721.aspx>. También pueden usar OpenSSL para crear el certificado de administración. Para obtener más información, consulte [OpenSSL](http://openssl.org/). Sin embargo, esta documentación está orientada principalmente al uso del portal de Silverlight, al que probablemente no todos los usuarios de Linux puedan tener acceso. Describe cómo podrá tener acceso a estos certificados e integrarlos con nuestras distintas herramientas y socios, y usarlos por su propia cuenta hasta que esta funcionalidad se agregue al Portal de administración de Azure.

Tabla de contenido
------------------

-   [Obtención de un certificado de administración desde el archivo publishsettings](#createcert)
-   [Instalación de un certificado de administración mediante el Portal de administración de Azure](#management)

Creación y carga de un certificado de administración
----------------------------------------------------

Hemos elaborado una manera sencilla de crear un certificado de administración de Azure; puede verla en <https://windows.azure.com/download/publishprofile.aspx>

Este sitio web le pedirá iniciar sesión con sus credenciales del portal para, a continuación, generar un certificado de administración que se empaquetan junto con su Id. de suscripción en un archivo publishsettings que deberá descargar.

![Credenciales de Linux](./media/linux-create-management-cert/linuxcredentials.png)

Asegúrese de guardar este archivo en un lugar seguro, puesto que no podrá recuperarlo y deberá generar un certificado de administración nuevo. (Existe un límite en cuanto al número total de certificados que puede utilizar en el sistema. Consulte la sección apropiada de este sitio web para confirmarlo). Luego, puede usar este certificado de muchas maneras:

### En Visual Studio

![Publicación de VS](./media/linux-create-management-cert/VSpublish.png)

### En la línea de comandos de Azure para Linux

Puede importar el certificado y utilizarlo para ejecutar el comando de importación de la cuenta de Azure:

![Publicación mediante la línea de comandos](./media/linux-create-management-cert/cmdlinepublish.png)

Con cualquier otro socio o software en que necesite la herramienta, deberá extraer el certificado de administración del propio archivo y descodificarlo a base64. Algunos socios, como ScaleXtreme y SUSE Studio, utilizarán directamente el archivo en su formato actual.

A fin de extraer el certificado de administración, deberá seguir este procedimiento.

Deberá extraer de ese archivo el contenido codificado a base64 entre comillas después de ManagementCertificate.

    ?xml version="1.0" encoding="utf-8"?>
    <PublishData>
      <PublishProfile 
		PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription 
		  Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

Será necesario copiar ese contenido en un archivo y, a continuación, descodificarlo con un descodificador a base64 en Linux que pueda usar:

    Base64 -d [encodedfile] > [decodedfile].pfx

Con esto tendrá un archivo PFX que podrá convertir a otros formatos con openssl para extraer la clave privada en caso de ser necesario:

	openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem

En Windows puede descodificar y extraer el archivo PFX con un descodificador a base64 de Windows gratuito o PowerShell, como [http://www.fourmilab.ch/webtools/base64/base64.zip]() si ejecuta el comando

    base64 -d key.txt ->key.pfx

#### Límite para la generación de certificados

Existe un límite de hasta 10 certificados que puede crear en la plataforma con esta herramienta. Desafortunadamente, no hay manera de recuperar las claves una vez que se han generado, dado que dichas claves privadas no se guardan en ninguna parte del sistema. Si llega al límite de certificados en el sistema, deberá ponerse en contacto con el soporte técnico a través de los foros de Azure para que se borren los certificados o utilizar un explorador que pueda mostrarle el antiguo portal de Silverlight con el fin de ejecutar estas tareas.

#### Si su clave privada se encuentra en peligro

Si su clave privada se encuentra en peligro en algún punto, deberá utilizar un explorador que admita Silverlight para tener acceso al portal antiguo y eliminar los correspondientes certificados de administración en el archivo, o bien, ponerse en contacto con el soporte técnico a través de los foros.
 Generar un certificado nuevo no es suficiente, porque los 10 certificados son válidos y su clave anterior en riesgo seguirá teniendo la capacidad de tener acceso al sitio web.

Instalación de un certificado de administración mediante el Portal de administración de Azure
---------------------------------------------------------------------------------------------

Puede crear un certificado de administración de diversas maneras. Para obtener más información acerca de la creación de certificados, consulte [Crear y cargar un certificado de administración para Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg551722.aspx). Una vez creado el certificado, agréguelo a su suscripción en Azure.

1.  Inicie sesión en el Portal de administración de Azure.

2.  En el panel de navegación, haga clic en **Settings**.

3.  En **Certificados de administración**, haga clic en **Cargar un certificado de administración**.

4.  En **Cargar un certificado de administración**, diríjase al archivo de certificado y, a continuación, haga clic en **OK**.

### Obtención de la huella digital del certificado y el identificador de suscripción

Necesita la huella digital del certificado de administración que agregó y también necesita el identificador de suscripción para poder cargar el archivo .vhd en Azure.

1.  En el Portal de administración, haga clic en **Settings**.

2.  En **Certificados de administración**, haga clic en su certificado y luego, para registrar la huella digital desde el panel **Propiedades**, cópiela y péguela en una ubicación desde donde pueda recuperarla más adelante.

También necesita el identificador de su suscripción para cargar el archivo .vhd.

1.  En el Portal de administración, haga clic en **All Items**.

2.  En el panel central, en **Suscripción**, copie la suscripción y péguela en una ubicación desde donde pueda recuperarla más adelante.

### Proporcione esta información a las herramientas si generó su propia clave

#### Para CSUPLOAD

1.  Abra una ventana de símbolo del sistema de SDK de Azure como administrador.
2.  Defina la cadena de conexión con el siguiente comando y reemplace **Subscriptionid** y **CertThumbprint** por los valores que obtuvo anteriormente:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Para las herramientas de la línea de comandos de Azure para Linux

Necesitará codificar a base64 el archivo PFX que creó usando openssl con el comando:

	Base64 [archivo] > [archivo codificado]

Luego deberá combinar su identificador de suscripción y el archivo PFX codificado a base64 en un archivo único con la estructura siguiente:

     
       ?xml version="1.0" encoding="utf-8"?>
        <PublishData>
          <PublishProfile 
			PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription
			  Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>

Donde xxxxx es el contenido del [archivo codificado] que utilizará para brindar los detalles a las herramientas de la línea de comandos de Azure para Linux con los siguientes comandos: Azure account import (archivo)

