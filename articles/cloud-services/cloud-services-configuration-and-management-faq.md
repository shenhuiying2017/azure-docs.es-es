---
title: "P+F sobre configuración y problemas de administración de Microsoft Azure Cloud Services | Microsoft Docs"
description: "En este artículo se enumeran las preguntas frecuentes sobre la configuración y administración de Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: genli
ms.openlocfilehash: 2a20ee1df23df683c49444e8fb3ffdb2085b174f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Configuración y problemas de administración de Microsoft Azure Cloud Services: preguntas más frecuentes (P+F)

En este artículo se incluyen las preguntas frecuentes sobre la configuración y los problemas de administración de [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). También puede consultar la [página Tamaños de Cloud Services](cloud-services-sizes-specs.md) para obtener información de tamaño.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>¿Cómo se agrega "nosniff" a mi sitio web?
Para evitar que los clientes curioseen en los tipos MIME, agregue un ajuste a su archivo *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

También puede agregarlo como un ajuste en IIS. Use el comando siguiente con el artículo [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) (tareas comunes de inicio).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>¿Cómo se personaliza IIS para un rol web?
Use el script de inicio de IIS del artículo [common startup tasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) (tareas comunes de inicio).

## <a name="i-cannot-scale-beyond-x-instances"></a>No puedo escalar más allá de X instancias
La suscripción de Azure tiene un límite en el número de núcleos que se pueden usar. El escalado no funcionará si ha usado todos los núcleos disponibles. Por ejemplo, si tiene un límite de 100 núcleos, esto significa que podría tener 100 instancias de máquina virtual de tamaño A1 para su servicio en la nube o 50 instancias de máquina virtual de tamaño A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>¿Cómo se puede implementar el acceso basado en roles para Cloud Services?
Cloud Services no es compatible con el modelo de control de acceso basado en roles (RBAC), ya que no es un servicio basado en Azure Resource Manager.

Consulte [RBAC de Azure frente a administradores de la suscripción clásica](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>¿Cómo se configura el tiempo de espera de inactividad para Azure Load Balancer?
Puede especificar el tiempo de espera en el archivo de definición se servicio (csdef) de la forma siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Para más información consulte ///[New: Configurable Idle Timeout for Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) (Nuevo: Tiempo de espera de inactividad configurable para Azure Load Balancer).

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>¿Pueden los ingenieros dentro de Microsoft ejecutar instancias de protocolo de escritorio remoto en el servicio en la nube sin permiso?
Microsoft sigue un proceso estricto que no permite a los ingenieros internos ejecutar instancias de RDP en su servicio en la nube sin autorización por escrito (correo electrónico u otra comunicación escrita) del propietario o la persona designada a tal fin.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>¿Por qué está incompleta la cadena de certificados SSL de mi servicio de nube?
Se recomienda que los clientes instalen la cadena de certificados completa (certificado de hoja, certificados intermedios y certificado raíz) en lugar de simplemente el certificado de hoja. Cuando se instala solamente el certificado de hoja, se confía en Windows para que genere la cadena de certificados recorriendo la CTL. Si se producen problemas intermitentes de la red o problemas de DNS en Azure o Windows Update cuando Windows está intentando validar el certificado, es posible que este se considere no válido. Al instalar la cadena de certificados completa se evita este problema. El blog en [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) (cómo instalar una cadena de certificado SSL) muestra cómo hacerlo.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>¿Cómo se puede asociar una dirección IP estática al servicio en la nube?
Para configurar una dirección IP estática, tiene que crear una dirección IP reservada. Esta dirección IP reservada puede asociarse a un nuevo servicio en la nube o a una implementación existente. Consulte los siguientes documentos para más información:
* [Cómo crear una dirección IP reservada](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reserva de la dirección IP de un servicio en la nube existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Asociación de una IP reservada a un nuevo servicio en la nube](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Asociación de una dirección IP reservada a una implementación en ejecución](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Asociación de una dirección IP reservada a un servicio en la nube mediante un archivo de configuración de servicio](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>¿Cuál es el límite de cuota para el servicio en la nube?
Consulte los [Límites específicos del servicio](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>¿Por qué la unidad en mi máquina virtual del servicio de nube muestra muy poco espacio libre en disco?
Este es el comportamiento esperado y que no debería causar ningún problema para la aplicación. El registro en diario está activado para la unidad % uproot % en máquinas virtuales de PaaS de Azure, lo que básicamente consume doble cantidad de espacio del que normalmente ocupan los archivos. De todas formas, hay varios aspectos a tener en cuenta que básicamente hacen que esto no sea un problema.

El tamaño de la unidad % approot % se calcula como < tamaño de .cspkg + tamaño máximo del diario + un margen de espacio disponible>, o 1,5 GB, de los dos valores el que sea mayor. El tamaño de la máquina virtual no influye en este cálculo. (El tamaño de la máquina virtual solo afecta al tamaño de la unidad C: temporal). 

No se admite para escribir en la unidad % approot %. Si va a escribir en la máquina virtual de Azure, tiene que hacerlo en un recurso de LocalStorage temporal (u otras opciones, como almacenamiento de blobs, Azure Files, etc.). Por lo que la cantidad de espacio libre en la carpeta % approot % no es algo significativo. Si no está seguro de si la aplicación está escribiendo en la unidad % approot %, siempre puede dejar que el servicio se ejecute durante unos días y, a continuación, comparar los tamaños del "antes" y el "después". 

Azure no escribirá nada en la unidad % approot %. Una vez que el disco duro virtual se crea desde su .cspkg y se monta en la máquina virtual de Azure, lo único que puede escribir en esta unidad es la aplicación. 

Los ajustes del diario no son configurables, por lo que no se pueden desactivar.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>¿Cuáles son las características y funcionalidades que proporcionan los IPS/IDS y DDoS básicos de Azure?
Azure tiene IPS/IDS básicos en los servidores físicos de los centros de datos para la defensa contra amenazas. Además, los clientes pueden implementar soluciones de seguridad de terceros, como firewalls de aplicación web, firewalls de red, antimalware, detección de intrusiones, sistemas de prevención (IDS/IPS) y mucho más. Para más información, consulte ///[Protect your data and assets and comply with global security standards](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity) (Protección de datos y recursos y cumplimiento de los estándares de seguridad global).

Microsoft supervisa continuamente servidores, redes y aplicaciones para detectar amenazas. La administración de amenazas de Azure se realiza en varios frentes utilizando detección de intrusiones, prevención de ataques con denegación de servicio distribuido (DDoS), pruebas de penetración, análisis de comportamiento, detección de anomalías y aprendizaje automático para reforzar su defensa constantemente y reducir los riesgos. Microsoft Antimalware para Azure protege los servicios en la nube y las máquinas virtuales de Azure. Además tiene la opción de implementar soluciones de seguridad de terceros, como firewalls de aplicación web, firewalls de red, antimalware, detección de intrusiones, además de sistemas de prevención (IDS/IPS) y mucho más.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>¿Por qué IIS deja de escribir en el directorio de registro?
Se ha agotado la cuota de almacenamiento local para escribir en el directorio de registro. Para corregir este problema, puede elegir entre tres cosas:
* Habilitar los diagnósticos para IIS y periódicamente mover los diagnósticos al almacenamiento de blobs.
* Quitar manualmente los archivos de registro del directorio de registro.
* Aumentar el límite de cuota para los recursos locales.

Para obtener más información, vea los documentos siguientes:
* [Almacenamiento y visualización de los datos de diagnóstico en Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [IIS Logs stops writing in cloud service](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/) (El registro IIS deja de escribir en el servicio en la nube)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>¿Cuál es el propósito del "Windows Azure Tools Encryption Certificate for Extensions" (certificado de cifrado para extensiones de Windows Azure Tools)?
Estos certificados se crean automáticamente siempre que se agrega una extensión al servicio en la nube. Normalmente, se trata de la extensión WAD o la extensión RDP, pero pueden ser otras, como la extensión del antimalware o la del recopilador de registros. Estos certificados solo se utilizan para cifrar y descifrar la configuración privada de la extensión. Nunca se comprueba la fecha de expiración, por lo que no importa si el certificado ha caducado. 

Puede omitir estos certificados. Si desea limpiar los certificados, puede intentar eliminaros todos. Azure generará un error si se intenta eliminar un certificado que está en uso.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>¿Cómo se puede generar una solicitud de firma de certificado (CSR) sin ejecutar RDP en la instancia?
Consulte la siguiente documentación de guía:

>///[Obtaining a certificate for use with Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/) (Obtención de un certificado para su uso con los sitios web de Windows Azure)

Tenga en cuenta que un CSR es simplemente un archivo de texto. NO tiene que crearse a partir de la máquina en la que en última instancia se utilizará el certificado. Aunque este documento está escrito para una instancia de App Service, la creación de CSR es genérica y se aplica también a Cloud Services.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>¿Cómo puedo agregar una extensión antimalware para Cloud Services de forma automática?

Puede habilitar la extensión antimalware mediante el script de PowerShell en la tarea de inicio. Siga los pasos descritos en los siguientes artículos para implementarla: 
 
- [Creación de una tarea de inicio de PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Para obtener más información sobre los escenarios de implementación de antimalware y cómo habilitarlo desde el portal, vea [Escenarios de implementación de Antimalware](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>¿Cómo habilitar la Indicación de nombre de servidor (SNI) para Cloud Services?

Puede habilitar SNI en Cloud Services con alguno de los siguientes métodos:

### <a name="method-1-use-powershell"></a>Método 1: Uso de PowerShell

El enlace de SNI puede configurarse mediante el cmdlet de PowerShell **New-WebBinding** en una tarea de inicio de una instancia de rol de servicio en la nube como sigue:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Como se describe [aquí](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags puede ser uno de los valores del tipo:

|Valor|Significado|
------|------
|0|Sin SNI|
|1|SNI habilitada |
|2 |Sin enlace de SNI que use el almacén de certificados central|
|3|Enlace de SNI que usa el almacén de certificados central |
 
### <a name="method-2-use-code"></a>Método 2: Uso de código

El enlace de SNI también puede configurarse a través de código en el inicio del rol, tal y como se describe en esta [entrada de blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Con cualquiera de los enfoques anteriores, los certificados correspondientes (*.pfx) para los nombres de host específicos deben instalarse primero en las instancias de rol mediante una tarea de inicio o a través de código para que el enlace de SNI sea efectivo.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>¿Cómo puedo agregar etiquetas a mi servicio en la nube de Azure? 

El servicio en la nube es un recurso clásico. Solo los recursos creados a través de Azure Resource Manager son compatibles con las etiquetas. No puede aplicar etiquetas a los recursos clásicos, como el servicio en la nube. 

## <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>¿Cuáles son las próximas funcionalidades del servicio en la nube en Azure Portal que pueden ayudar en la administración y supervisión de las aplicaciones?

* Capacidad de generar un certificado nuevo para el Protocolo de escritorio remoto (RDP) disponible próximamente. Como alternativa, puede ejecutar el script siguiente:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
* Posibilidad de elegir blob o local para la ubicación de carga de csdef y cscfg, disponible próximamente. Si usa [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), puede establecer el valor de cada ubicación.
* Capacidad para supervisar las métricas en el nivel de instancia. Hay funcionalidades de supervisión adicionales disponibles en [Supervisión de Cloud Services](cloud-services-how-to-monitor.md).


## <a name="how-to-enable-http2-on-cloud-services-vm"></a>¿Cómo habilitar HTTP/2 en máquinas virtuales de Cloud Services?

Windows 10 y Windows Server 2016 incluyen compatibilidad con HTTP/2 en el lado servidor y cliente. El cliente (explorador) se conecta al servidor IIS a través de TLS que negocia HTTP/2 a través de extensiones TLS, no es necesario realizar ningún cambio en el lado servidor. Esto se debe a que, a través de TLS, el encabezado h2-14 que especifica el uso de HTTP/2 se envía de forma predeterminada. Si, por otro lado, el cliente envía un encabezado Upgrade para actualizar a HTTP/2, necesita realizar el cambio siguiente en el lado servidor para garantizar que Upgrade funciona y que acabará con una conexión HTTP/2. 

1. Ejecute regedit.exe.
2. Examine la clave del Registro: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Cree un nuevo valor DWORD denominado **DuoEnabled**.
4. Establezca su valor en 1.
5. Reinicie el servidor.
6. Vaya a **Sitio web predeterminado** y, en **Enlaces**, cree un enlace de TLS con el certificado autofirmado que acaba de crear. 

Para más información, consulte:

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 en IIS)
- [Vídeo: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (HTTP/2 en Windows 10: explorador, aplicaciones y servidor web)
         

Tenga en cuenta que los pasos anteriores pueden automatizarse mediante una tarea de inicio para que, cada vez que se crea una instancia de PaaS, pueda realizar los cambios anteriores en el registro del sistema. Para más información, consulte [Configuración y ejecución de tareas de inicio para un servicio en la nube](cloud-services-startup-tasks.md).

 
Una vez hecho esto, puede verificar si se ha habilitado HTTP/2 o no mediante alguno de los métodos siguientes:

- Habilite la versión del protocolo en los registros de IIS y busque en dichos registros. Mostrará HTTP/2 en los registros. 
- Habilite la herramienta de desarrollo F12 en Internet Explorer/Edge y cambie a la pestaña Red para verificar el protocolo. 

Para más información, vea [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 en IIS).
