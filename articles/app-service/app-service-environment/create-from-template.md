---
title: "Creación de un entorno de Azure App Service Environment mediante plantillas de Resource Manager"
description: "Explica cómo crear una instancia externa o con un ILB de Azure App Service Environment mediante una plantilla de Resource Manager"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 370ab7a32634f04b4776a2a9a84ecaacf11ea617
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="how-to-create-an-ase-using-azure-resource-manager-templates"></a>Creación de un ASE mediante las plantillas de Azure Resource Manager

## <a name="overview"></a>Información general
Las instancias de App Service Environment (ASE) pueden crearse con un punto de conexión accesible de Internet o un punto de conexión en una dirección interna en Azure Virtual Network (VNet).  Cuando se crea con un punto de conexión interno, ese punto de conexión siempre lo proporciona un componente de Azure denominado equilibrador de carga interno (ILB).  El ASE con un punto de conexión público se denomina ASE externo y el ASE en una dirección IP interna se denomina ASE con un ILB.  

Un ASE puede crearse con Azure Portal o con plantillas Azure Resource Manager.  Este artículo le guía por los pasos y la sintaxis necesarios para crear un ASE externo o uno con ILB con plantillas de Azure Resource Manager.  Para más información sobre cómo crear un ASE en el portal, puede empezar por [crear un ASE externo] [ MakeExternalASE] o [crear un ASE con un ILB][MakeILBASE].

Cuando se crea un ASE en el portal, puede elegir crear la red virtual al mismo tiempo o seleccionar una red virtual existente en donde implementarlo.  Cuando se crea desde una plantilla, debe comenzar por: 
* Una red virtual de Resource Manager
* Una subred de esa red virtual.  Se recomienda que la subred del ASE sea un /25 con 128 direcciones con el fin de adaptarse al crecimiento futuro.  Esto no se puede cambiar después de la creación del ASE
* El identificador de recurso de la red virtual.  Puede obtenerlo en Azure Portal, en las propiedades de Azure Virtual Network.
* La suscripción en la que desea implementarlo.
* La ubicación en la que desea implementarlo.

Para automatizar la creación del ASE:

1. Cree el ASE a partir de una plantilla.  Si crea una ASE externo, ha terminado con este paso.  Si crea un ASE con un ILB, hay que hacer algunas acciones adicionales.
2. Una vez creado el ASE con un ILB, se carga un certificado SSL que coincida con su dominio de ASE con un ILB.
3. El certificado SSL cargado se asigna al ASE con un ILB como su certificado SSL "predeterminado".  Este certificado SSL se usará para el tráfico SSL a las aplicaciones del ASE de ILB cuando las aplicaciones se direccionan mediante el dominio raíz común asignado al ASE (por ejemplo, https://someapp.mycustomrootcomain.com)


## <a name="creating-the-ase"></a>Creación del ASE
[Aquí][quickstartasev2create], en GitHub, encontrará un ejemplo de plantilla de Azure Resource Manager que crea un ASE y su archivo de parámetros asociado.

Si desea crear un ASE con un ILB, debe usar las plantillas de Resource Manager [aquí][quickstartilbasecreate].  Son específicas a dicho caso de uso.  La mayoría de los parámetros del archivo *azuredeploy.parameters.json* son comunes para la creación de los dos ASE con un ILB, así como de los ASE externos.  La lista siguiente llama a los parámetros especiales o únicos al crear un ASE de ILB:

* *interalLoadBalancingMode*: en la mayoría de los casos se establece en 3, lo que significa que tanto el tráfico HTTP/HTTPS de los puertos 80 y 443 como los puertos de los canales de control o de datos a los que escucha el servicio FTP en el ASE estarán enlazados a una dirección de red virtual interna asignada al ILB.  Si esta propiedad se establece en 2, solo los puertos relacionados con el servicio FTP (los canales de control y de datos) estarán enlazados a una dirección de ILB, mientras que el tráfico HTTP/HTTPS permanecerá en la VIP pública.
* *dnsSuffix*: este parámetro define el dominio raíz predeterminado que se asignará al ASE.  En la variación pública del Servicio de aplicaciones de Azure, el dominio raíz predeterminado de todas las aplicaciones web es *azurewebsites.net*.  Sin embargo, dado que un ASE de ILB está dentro de la red virtual de un cliente, no tiene sentido utilizar el dominio raíz predeterminado del servicio público.  En su lugar, un ASE de ILB debe tener un dominio raíz predeterminado que tenga sentido usar en la red virtual interna de una compañía.  Por ejemplo, una empresa hipotética, Contoso Corporation, puede usar el dominio raíz predeterminado *interno contoso.com* para aquellas aplicaciones que se pretende que solo se puedan resolver en la red virtual de Contoso, y a las que solo se pueda acceder desde ella. 
* *ipSslAddressCount*: automáticamente, el valor predeterminado de este parámetro es 0 en el archivo *azuredeploy.json*, ya que los ASE de ILB solo tienen una dirección de ILB individual.  No hay direcciones IP-SSL explícitas para un ASE de ILB y, por consiguiente, el grupo de direcciones IP SSL de un ASE de ILB debe establecerse en cero, ya que, de lo contrario, se producirá un error de aprovisionamiento. 

Una vez que se haya rellenado el archivo *azuredeploy.parameters.json*, el ASE puede crearse mediante el siguiente fragmento de código de Powershell.  Cambie las carpetas PATH del archivo para que coincidan con la ubicación de la máquina en la que se encuentran los archivos de plantilla de Azure Resource Manager.  Recuerde también especificar sus propios valores para el nombre de implementación y el nombre del grupo de recursos de Azure Resource Manager.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Después que enviar la plantilla de Azure Resource Manager, el ASE tardará aproximadamente una hora en crearse.  Una vez completada la creación, el ASE se mostrará en el portal, en la lista de entornos de App Service de la suscripción que desencadenó la implementación.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carga y configuración del certificado SSL "predeterminado"
Una vez que se crea el ASE de ILB, es preciso asociarle un certificado SSL "predeterminado" que se use para establecer conexiones SSL con las aplicaciones.  Continuando con el ejemplo de Contoso Corporation, si el sufijo DNS predeterminado del ASE es *internal-contoso.com*, una conexión a *https://some-random-app.internal-contoso.com* requiere un certificado SSL que sea válido para **.internal contoso.com*. 

Hay varias maneras de obtener un certificado SSL válido, entre las que se incluyen las CA internas, la adquisición de un certificado de un emisor externo y el uso de un certificado autofirmado.  Independientemente del origen del certificado SSL, es preciso configurar correctamente los siguientes atributos del certificado:

* *Subject*: el valor de este atributo debe ser **.your-root-domain-here.com*
* *Nombre alternativo del firmante*: este atributo debe incluir tanto **.your-root-domain-here.com* como **.scm.your-root-domain-here.com*.  El motivo de la segunda entrada es que las conexiones SSL con el sitio de SCM/Kudu asociadas a cada aplicación se realizarán mediante una dirección, cuyo formato será *your-app-name.scm.your-root-domain-here.com*.

Con un certificado SSL válido, se necesitan dos pasos preparatorios adicionales.  El certificado SSL se debe guardar en formato .pfx, o bien convertirse a dicho formato.  Recuerde que es preciso que el archivo .pfx incluya todos los certificados raíz e intermedios, y también debe protegerse con una contraseña.

A continuación, el archivo .pfx resultante debe convertirse en una cadena base64, ya que el certificado SSL se cargará mediante una plantilla de Azure Resource Manager.  Dado que las plantillas de Azure Resource Manager son archivos de texto, el archivo .pfx debe convertirse en una cadena base64 para que puede incluirse como parámetro de la plantilla.

El siguiente fragmento de código de Powershell muestra un ejemplo de cómo generar un certificado autofirmado, exportar el certificado como un archivo .pfx, convertir el archivo .pfx en una cadena base64 codificada y, a continuación, guardar dicha cadena en otro archivo.  El código de Powershell para la codificación Base64 se adaptó del [blog Powershell Scripts][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Una vez que el certificado SSL se ha generado y convertido correctamente en una cadena con codificación Base64, se puede usar la plantilla de Azure Resource Manager de ejemplo en GitHub para [configurar el certificado SSL predeterminado][quickstartconfiguressl].

Los parámetros del archivo *azuredeploy.parameters.json* se enumeran a continuación:

* *appServiceEnvironmentName*: el nombre del ASE de ILB que se configura.
* *existingAseLocation*: cadena de texto que contiene la región de Azure en que se implementó el ASE de ILB.  Por ejemplo, "centro-sur de EE. UU.".
* *pfxBlobString*: la representación de la cadena con codificación Base64 del archivo pfx.  Mediante el fragmento de código que se ha mostrado anteriormente, se copiaría la cadena de "exportedcert.pfx.b64" y se pegaría como el valor del atributo *pfxBlobString* .
* *password*: la contraseña que se usa para proteger el archivo pfx.
* *certificateThumbprint*: la huella digital del certificado.  Si este valor se recupera de Powershell (por ejemplo, *$certificate.Thumbprint* del fragmento de código anterior), se puede usar tal cual.  Sin embargo, si copia el valor del cuadro de diálogo del certificado de Windows, no olvide eliminar los espacios superfluos.  *certificateThumbprint* debería ser similar a: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: un identificador de cadena fácil de usar que elija y que se usa para identificar el certificado.  El nombre se utiliza como parte del identificador único de Azure Resource Manager para la entidad *Microsoft.Web/certificates* que representa el certificado SSL.  El nombre **debe** terminar con el sufijo siguiente: \__yourASENameHere_InternalLoadBalancingASE.  El portal utiliza este sufijo como un indicador de que el certificado se usa para asegurar un ASE habilitado para ILB.

A continuación se muestra un ejemplo abreviado de *azuredeploy.parameters.json* :

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Una vez rellenado el archivo *azuredeploy.parameters.json* , se puede configurar el certificado SSL predeterminado mediante el siguiente fragmento de código de Powershell.  Cambie las carpetas PATH del archivo para que coincidan con la ubicación de la máquina en la que se encuentran los archivos de plantilla de Azure Resource Manager.  Recuerde también especificar sus propios valores para el nombre de implementación y el nombre del grupo de recursos de Azure Resource Manager.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Una vez que se envía la plantilla de Azure Resource Manager, el front-end de ASE tarda aproximadamente 40 minutos en aplicar el cambio.  Por ejemplo, con un ASE de un tamaño predeterminado que usa dos front-ends, la plantilla tardará aproximadamente una hora y veinte minutos en completarse.  Mientras la plantilla ejecute el ASE no se podrá escalar.  

Una vez que se completa la plantilla, se puede acceder a las aplicaciones del ASE de ILB a través de HTTPS y las conexiones se protegerán mediante el certificado SSL predeterminado.  El certificado SSL predeterminado se usará cuando las direcciones de las aplicaciones del ASE de ILB sean una combinación del nombre de la aplicación y el nombre de host predeterminado.  Por ejemplo,*https://mycustomapp.internal-contoso.com* usaría el certificado SSL predeterminado para **.internal-contoso.com*.

Sin embargo, al igual que las aplicaciones que se ejecutan en el servicio multiinquilino público, los desarrolladores también pueden configurar nombres de host personalizados para las aplicaciones individuales y luego configurar enlaces de certificados SSL SNI únicos para aplicaciones individuales.  

## <a name="asev1"></a>ASEv1 ##
App Service Environment tiene dos versiones: ASEv1 y ASEv2. La información anterior se centra en ASEv2. En esta sección se muestran las diferencias entre ASEv1 y ASEv2.

En ASEv1, debe administrar todos los recursos de forma manual. Esto incluye los front-end, los trabajos y las direcciones IP utilizadas para SSL basada en IP. Antes de escalar horizontalmente el plan de App Service, primero debe escalar horizontalmente el grupo de trabajo en el que desea hospedarlo.

ASEv1 utiliza un modelo de precios diferente al de ASEv2. En ASEv1, debe pagar por cada núcleo asignado. Esto incluye los núcleos usados por servidores front-end y trabajos que no hospedan ninguna carga de trabajo. En ASEv1, el tamaño de escala máxima predeterminada de una instancia de App Service Environment es de 55 hosts en total. Esto incluye servidores front-end y trabajos. Una ventaja de ASEv1 es que se puede implementar en una red virtual clásica, así como en una red virtual de Resource Manager. Puede obtener más información sobre ASEv1 aquí: [Introducción a la versión 1 de App Service Environment][ASEv1Intro].

Si desea crear un ASEv1 mediante una plantilla de Resource Manager, puede comenzar por [la creación de un ASE v1 con un ILB con una plantilla de Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: ../../app-service-web/app-service-app-service-environment-create-ilb-ase-resourcemanager.md

