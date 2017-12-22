---
title: "Configuración de la descarga SSL para Azure Application Gateway mediante el PowerShell clásico | Microsoft Docs"
description: "En este artículo se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el modelo de implementación clásica de Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 9540522a945e1ea2a09456b42d64b7b94753791f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-ssl-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
> * [PowerShell clásico de Azure](application-gateway-ssl.md)
> * [CLI de Azure 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/).
2. Compruebe que tiene una red virtual de trabajo con una subred válida. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configure para que usen la puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener asignada una dirección IP pública o una dirección IP virtual (VIP).

Para configurar la descarga SSL en una puerta de enlace de aplicaciones, siga los pasos que se indican a continuación en el orden mostrado:

1. [Creación de una puerta de enlace de aplicaciones](#create-an-application-gateway)
2. [Carga de certificados SSL](#upload-ssl-certificates)
3. [Configuración de la puerta de enlace](#configure-the-gateway)
4. [Establecimiento de la configuración de la puerta de enlace](#set-the-gateway-configuration)
5. [Inicio de la puerta de enlace](#start-the-gateway)
6. [Comprobación del estado de la puerta de enlace](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Para crear la puerta de enlace, utilice el cmdlet `New-AzureApplicationGateway` y reemplace los valores por los suyos. La facturación de la puerta de enlace no se inicia en este momento. La facturación comienza en un paso posterior, cuando la puerta de enlace se ha iniciado correctamente.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar la creación de la puerta de enlace, puede usar el cmdlet `Get-AzureApplicationGateway`.

En el ejemplo, **Description**, **InstanceCount** y **GatewaySize** son parámetros opcionales. El valor predeterminado de **InstanceCount** es **2**, con un valor máximo de **10**. El valor predeterminado de **GatewaySize** es **Medium**. Small y Large son otros valores disponibles. **VirtualIPs** y **DnsName** están en blanco porque todavía no se ha iniciado la puerta de enlace. Estos valores se crean una vez que la puerta de enlace está en estado de ejecución.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Carga de certificados SSL

Escriba `Add-AzureApplicationGatewaySslCertificate` para cargar el certificado del servidor en formato PFX en la puerta de enlace de aplicaciones. El nombre del certificado es un nombre elegido por el usuario y debe ser único dentro de la puerta de enlace de aplicaciones. Este certificado se conoce con este nombre en todas las operaciones de administración de certificados en la puerta de enlace de aplicaciones.

En el ejemplo siguiente se muestra el cmdlet. Sustituya los valores por los suyos propios.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

A continuación, valide la carga del certificado. Escriba el cmdlet `Get-AzureApplicationGatewayCertificate`.

En la primera línea del siguiente ejemplo, se muestra el cmdlet, seguido de la salida:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> La contraseña del certificado debe tener entre 4 y 12 caracteres alfanuméricos. No se aceptan caracteres especiales.

## <a name="configure-the-gateway"></a>Configuración de la puerta de enlace

Una configuración de puerta de enlace de aplicaciones consta de varios valores. Los valores pueden ir juntos para construir la configuración.

Los valores son:

* **Back-end server pool** (Grupo de servidores back-end): lista de direcciones IP de los servidores back-end. Las direcciones IP de la lista deben pertenecer a la subred de la red virtual o ser una dirección VIP/IP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Front-end port** (Puerto front-end): este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha**: el agente de escucha tiene un puerto front-end, un protocolo (Http o Https; estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla:** la regla enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se va a dirigir el tráfico cuando llegue a un determinado agente de escucha. Actualmente, solo se admite la regla *básica* . La regla *básica* es la distribución de carga round robin.

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a **Https** (con distinción entre mayúsculas y minúsculas). Agregue el elemento **SslCert** a **HttpListener** con el valor establecido en el mismo nombre que utilizó en la sección [Carga de certificados SSL](#upload-ssl-certificates). El puerto front-end debe actualizarse a **443**.

**Para habilitar la afinidad basada en cookies:** puede configurar una puerta de enlace de aplicaciones para asegurarse de que las solicitudes de una sesión de cliente siempre se dirigen a la misma máquina virtual de la granja de servidores web. Para conseguirlo, inserte una cookie de sesión que permita a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en **Habilitado** en el elemento **BackendHttpSettings**.

Puede llevar a cabo la configuración mediante la creación de un objeto de configuración o usando un archivo XML de configuración.
Para establecer la configuración con un archivo XML, use el siguiente ejemplo:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Establecimiento de la configuración de la puerta de enlace

Después, establezca la Puerta de enlace de aplicaciones. Puede escribir el cmdlet `Set-AzureApplicationGatewayConfig` con un objeto de configuración o con un archivo XML de configuración.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Inicio de la puerta de enlace

Una vez configurada la puerta de enlace, escriba el cmdlet `Start-AzureApplicationGateway` para iniciarla. La facturación de una puerta de enlace de aplicaciones comienza después de que se haya iniciado correctamente.

> [!NOTE]
> El cmdlet `Start-AzureApplicationGateway` puede tardar entre 15 y 20 en finalizar.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Comprobación del estado de la puerta de enlace

Escriba el cmdlet `Get-AzureApplicationGateway` para comprobar el estado de la puerta de enlace. Si `Start-AzureApplicationGateway` se realizó correctamente en el paso anterior, el valor de **State** debería ser **Running** y tanto **VirtualIps** como **DnsName** deberían tener entradas válidas.

En este ejemplo, se muestra una puerta de enlace de aplicaciones que está operativa, en ejecución y lista para asumir el tráfico:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las de opciones de equilibrio de carga en general, consulte:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
