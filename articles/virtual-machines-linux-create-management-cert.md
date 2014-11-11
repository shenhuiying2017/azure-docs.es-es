<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Creación de certificados de administración para Linux en Azure

Es necesario contar con un certificado de administración cuando desea utilizar la API de administración de servicios para interactuar con la plataforma de imágenes de Azure.

Documentación sobre cómo crear y administrar estos certificados ya se encuentra disponible en [][]<http://msdn.microsoft.com/es-es/library/azure/gg981929.aspx></a>. También pueden usar OpenSSL para crear el certificado de administración. Para obtener más información, consulte [OpenSSL][OpenSSL]. Sin embargo, esta documentación está orientada principalmente al uso del portal de Silverlight, al que probablemente no todos los usuarios de Linux puedan tener acceso. Describe cómo podrá tener acceso a estos certificados e integrarlos con nuestras distintas herramientas y socios, y usarlos por su propia cuenta hasta que esta funcionalidad se agregue al Portal de administración de Azure.

## Tabla de contenido

-   [Obtención de un certificado de administración desde el archivo publishsettings][Obtención de un certificado de administración desde el archivo publishsettings]
-   [Instalación de un certificado de administración mediante el Portal de administración de Azure][Instalación de un certificado de administración mediante el Portal de administración de Azure]

## <span id="publishsettings"></span></a>Direccionamiento del y carga de un certificado de administración

Hemos elaborado una manera sencilla de crear un certificado de administración de Azure; puede verla en [][1]<https://windows.azure.com/download/publishprofile.aspx></a>

Este sitio web le pedirá iniciar sesión con sus credenciales del portal para, a continuación, generar un certificado de administración que se empaquetan junto con su identificador de suscripción en un archivo publishsettings que deberá descargar.

![Credenciales de Linux][Credenciales de Linux]

Asegúrese de guardar este archivo en un lugar seguro, puesto que no podrá recuperarlo y deberá generar un certificado de administración nuevo. (Existe un límite en cuanto al número total de certificados que puede utilizar en el sistema. Consulte la sección apropiada de este sitio web para confirmarlo). Luego, puede usar este certificado de muchas maneras:

### En Visual Studio

![Publicación de VS][Publicación de VS]

### En la línea de comandos de Azure para Linux

Puede importar el certificado y utilizarlo para ejecutar el comando de importación de la cuenta de Azure:

![Publicación mediante la línea de comandos][Publicación mediante la línea de comandos]

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

Necesitará copiar esa información en un archivo (p. ej. encodedCert.txt) y después descodificarla usando un descodificador base64:

    base64 -d [encodedCert.txt] > [decodedCert.pfx]

Con esto tendrá un archivo PFX que podrá convertir a otros formatos con openssl para extraer la clave privada en caso de ser necesario:

    openssl pkcs12 -in [decodedCert.pfx] -out [cert.pem] -nodes

En Windows puede descodificar y extraer el archivo PFX con un descodificador a base64 de Windows gratuito o PowerShell, como <http://www.fourmilab.ch/webtools/base64/base64.zip></a> si ejecuta el comando

    base64 -d key.txt ->key.pfx

#### Límite para la generación de certificados

Existe un límite de hasta 10 certificados que puede crear en la plataforma con esta herramienta.
Desafortunadamente, no hay manera de recuperar las claves una vez que se han generado, dado que dichas claves privadas no se guardan en ninguna parte del sistema.
Si llega al límite de certificados en el sistema, deberá ponerse en contacto con el soporte técnico a través de los foros de Azure para que se borren los certificados o utilizar un explorador que pueda mostrarle el antiguo portal de Silverlight con el fin de ejecutar estas tareas.

#### Si su clave privada se encuentra en peligro

Si su clave privada se encuentra en peligro en algún punto, deberá utilizar un explorador que admita Silverlight para tener acceso al portal antiguo y eliminar los correspondientes certificados de administración en el archivo, o bien, ponerse en contacto con el soporte técnico a través de los foros.
Generar un certificado nuevo no es suficiente, porque los 10 certificados son válidos y su clave anterior en riesgo seguirá teniendo la capacidad de tener acceso al sitio web.

## <span id="management"></span></a>Instalación de un certificado de administración mediante el Portal de administración de Azure

Puede crear un certificado de administración de diversas maneras. Para obtener más información acerca de la creación de certificados, consulte [Crear y cargar un certificado de administración para Azure][Crear y cargar un certificado de administración para Azure]. Una vez creado el certificado, agréguelo a su suscripción en Azure.

1.  Inicie sesión en el Portal de administración de Azure.

2.  En el panel de navegación, haga clic en **Configuración**.

3.  En **Certificados de administración**, haga clic en **Cargar un certificado de administración**.

4.  En **Cargar un certificado de administración**, diríjase al archivo de certificado y, a continuación, haga clic en **OK**.

### Obtención de la huella digital del certificado y el identificador de suscripción

Necesita la huella digital del certificado de administración que agregó y también necesita el identificador de suscripción para poder cargar el archivo .vhd en Azure.

1.  En el Portal de administración, haga clic en **Configuración**.

2.  En **Certificados de administración**, haga clic en su certificado y luego, para registrar la huella digital desde el panel **Propiedades**, cópiela y péguela en una ubicación desde donde pueda recuperarla más adelante.

También necesita el identificador de su suscripción para cargar el archivo .vhd.

1.  En el Portal de administración, haga clic en **Todos los elementos**.

2.  En el panel central, en **Suscripción**, copie la suscripción y péguela en una ubicación desde donde pueda recuperarla más adelante.

### Proporcione esta información a las herramientas si generó su propia clave

#### Para CSUPLOAD

1.  Abra una ventana de símbolo del sistema de SDK de Azure como administrador.
2.  Defina la cadena de conexión con el siguiente comando y reemplace **Subscriptionid** y **CertThumbprint** por los valores que obtuvo anteriormente:

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Para las herramientas de la línea de comandos de Azure para Linux

Necesitará codificar a base64 el archivo PFX que creó usando openssl con el comando:

        Base64 [file] > [econded file]

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
        

Donde xxxxx es el contenido del [archivo codificado] que utilizará para brindar los detalles a las herramientas de la línea de comandos de Azure para Linux con los siguientes comandos:
Azure account import (archivo)

  []: http://msdn.microsoft.com/es-es/library/azure/gg981929.aspx
  [OpenSSL]: http://openssl.org/
  [Obtención de un certificado de administración desde el archivo publishsettings]: #createcert
  [Instalación de un certificado de administración mediante el Portal de administración de Azure]: #management
  [1]: https://windows.azure.com/download/publishprofile.aspx
  [Credenciales de Linux]: ./media/virtual-machines-linux-create-management-cert/linuxcredentials.png
  [Publicación de VS]: ./media/virtual-machines-linux-create-management-cert/VSpublish.png
  [Publicación mediante la línea de comandos]: ./media/virtual-machines-linux-create-management-cert/cmdlinepublish.png

  [Crear y cargar un certificado de administración para Azure]: http://msdn.microsoft.com/library/azure/gg551722.aspx
