<properties
   pageTitle="Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure en Resource Manager | Microsoft Azure"
   description="Aprenda a crear una puerta de enlace de aplicaciones mediante la CLI de Azure en Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="gwallace" />

# Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies, descarga SSL (capa de sockets seguros), sondeos personalizados sobre el estado y compatibilidad con sitios múltiples.

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

<BR>

## Requisito previo: instalar la CLI de Azure

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](../xplat-cli-install.md) e [iniciar sesión en Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Si no tiene una cuenta de Azure, necesitará una. Regístrese para [obtener una prueba gratuita aquí](../active-directory/sign-up-organization.md).

## Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante el Portal de Azure.

En este escenario:

- Creará una puerta de enlace de aplicaciones media con dos instancias.
- Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
- Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
- Configurará un certificado para la descarga SSL.

![Escenario de ejemplo][scenario]

>[AZURE.NOTE] La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## Antes de empezar

Puerta de enlace de aplicaciones de Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

## Creación del grupo de recursos

Antes de crear la puerta de enlace de aplicaciones, se creará un grupo de recursos para que pueda contenerla. A continuación se muestra el comando y la salida prevista.

    azure group create -n AdatumAppGatewayRG -l eastus

    info:    Executing command group create
    + Getting resource group AdatumAppGatewayRG
    + Creating resource group AdatumAppGatewayRG
    info:    Created resource group AdatumAppGatewayRG
    data:    Id:                  /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG
    data:    Name:                AdatumAppGatewayRG
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

## Crear una red virtual

Una vez creado el grupo de recursos, se crea una red virtual para la puerta de enlace de aplicaciones. En el ejemplo siguiente, el espacio de direcciones era 10.0.0.0/16 tal como se definió en las notas del escenario anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

    info:    Executing command network vnet create
    + Looking up the virtual network "AdatumAppGatewayVNET"
    + Creating virtual network "AdatumAppGatewayVNET"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET
    data:    Name                            : AdatumAppGatewayVNET
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Address prefixes:
    data:      10.0.0.0/16
    info:    network vnet create command OK

## Creación de una subred

Después de crear la red virtual, se agrega una subred para la puerta de enlace de aplicaciones. Si piensa utilizar la puerta de enlace de aplicaciones con una aplicación web hospedada en la misma red virtual que la puerta de enlace de aplicaciones asegúrese de dejar suficiente espacio para otra subred.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

    info:    Executing command network vnet subnet create
    verbose: Looking up the virtual network "AdatumAppGatewayVNET"
    verbose: Looking up the subnet "Appgatewaysubnet"
    verbose: Creating subnet "Appgatewaysubnet"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet
    data:    Name                            : Appgatewaysubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.0.0.0/28
    info:    network vnet subnet create command OK

## Creación de la puerta de enlace de aplicaciones

Una vez que se crean la red virtual y la subred, los requisitos previos de la puerta de enlace de aplicaciones están completos. Además, se necesita un certificado .pfx exportado previamente y la contraseña de este para el paso siguiente. Las direcciones IP usadas para el back-end son las direcciones IP del servidor back-end. Puede tratarse de direcciones IP privadas de la red virtual, direcciones IP públicas o nombres de dominio completos de los servidores back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

    info:    Executing command network application-gateway create
    + Looking up an application gateway "AdatumAppGateway"
    + Looking up the subnet "Appgatewaysubnet"
    warn:    Using default http listener protocol: https
    warn:    Using default gateway ip name: ipConfig01
    warn:    Using default sku name: Standard_Medium
    warn:    Using default sku tier: Standard
    warn:    Using default sku capacity: 2
    warn:    Using default frontend ip name: frontendIp01
    warn:    Using default frontend port name: frontendPort01
    warn:    Using default frontend port: 443
    warn:    Using default address pool name: pool01
    warn:    Using default http settings name: httpSettings01
    warn:    Using default http settings protocol: http
    warn:    Using default http settings port: 80
    warn:    Using default http settings cookie based affinity: Disabled
    warn:    Using default http listener name: listener01
    warn:    Using default request routing rule name: rule01
    warn:    Using default request routing rule type: Basic
    + Looking up the subnet "Appgatewaysubnet"
    + Creating configuration for an application gateway "AdatumAppGateway"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway
    data:    Name                            : AdatumAppGateway
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Sku                             : Standard_Medium
    data:    Resource Group                  : AdatumAppGatewayRG
    data:    Gateway IP configations         : [ipConfig01]
    data:    SSL cerificates                 : [cert01]
    data:    Frontend ip configurations      : [frontendIp01]
    data:    Frontend ports                  : [frontendPort01]
    data:    Backend address pools           : [pool01]
    data:    Backend http settings           : [httpSettings01]
    data:    Http listeners                  : [listener01]
    data:    Request routing rules           : [rule01]
    data:    Probes                          : []
    data:    Url Path Maps                   : []
    data:    GatewayIpConfigurationText      : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/gatewayIPConfigurations/ipConfig01",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "ipConfig01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SslCertificateText              : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01",
            "publicCertData": "MIIDHjCCAgagAwIBAgIQE8ZZuidyb59IscYNJPYTvDANBgkqhkiG9w0BAQsFADA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wHhcNMTYwNzI4MTUyNzIwWhcNMTcwNzI4MDAwMDAwWjA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDC+pkArA1y9C10AOPWISHEThbNqvuRA+MaVDFUOu15NB9M7+0PUx5pyGlbW+1MuXb9acia/KXF9WxpYUMbQt8t8p1S5HsV4oKGdEOpdR0d7dozyPrkNgBgYvqRzDQ6R5VuK/uLq9oWpjPkqNoQeYR1wr7f/SNsIA4YsaDqqAi62ET6cvg1wN/VRXbWyi9wLeon7g6fZiCrFZspTUiSyqrRQx7sO0e/bqV7nKgSWmaqo4jLoUAqJBBCUJryDaTNfkFO4VEdnsQLN+PSGO8HwSZPJOzG1V6+MynmaGCKaTJE2UCxtLIJQHBhmES+X/BoinrIsjNVxsKqWFMv/mV7M2GBAgMBAAGjJDAiMAsGA1UdDwQEAwIEMDATBgNVHSUEDDAKBggrBgEFBQcDATANBgkqhkiG9w0BAQsFAAOCAQEAa2XLrwQJwX2ZmVN0MR+/+jWTED134wgoIKw6Ni30ukF9U936FsuvFcEjPr4vBp82cjnz76BjLNhyw/MxAHP7tTaguxzHgUHP9X9fmtcsLEUD74/D5BPmnpl+4cJ/BZMdyzIsuyyPSsDxkVN/W70ykOVTJeAb1ycwfJCllgLgkZcLVgTcMMAJYSttfWn9e1dhTUIlTIYKzD669emFvdBHi+sdTT1HGrZenpkT5oK+H6/5wIV7/DW+C+pqvXCsK0XSeYWW7KuBk5MpD8829HeCvV0rBSf538nYLwUUVUUMNHuTp5QXzouAtHOWyvo00/xRi+aDeq0NfUvTv2iS2BS/Ow==",
            "provisioningState": "Succeeded",
            "name": "cert01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendIpConfigurationText     : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01",
            "privateIPAddress": "10.0.0.6",
            "privateIPAllocationMethod": "Dynamic",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "frontendIp01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendPortText                : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01",
            "port": 443,
            "provisioningState": "Succeeded",
            "name": "frontendPort01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendAddressPoolText          : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01",
            "backendAddresses": [
                {
                    "ipAddress": "134.170.185.46"
                },
                {
                    "ipAddress": "134.170.188.221"
                },
                {
                    "ipAddress": "134.170.185.50"
                }
            ],
            "provisioningState": "Succeeded",
            "name": "pool01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendHttpSettingsText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01",
            "port": 80,
            "protocol": "Http",
            "cookieBasedAffinity": "Disabled",
            "requestTimeout": 30,
            "provisioningState": "Succeeded",
            "name": "httpSettings01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    HttpListenersText               : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01",
            "frontendIPConfiguration": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01"
            },
            "frontendPort": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01"
            },
            "protocol": "Https",
            "sslCertificate": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01"
            },
            "requireServerNameIndication": false,
            "provisioningState": "Succeeded",
            "name": "listener01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    RequestRoutingRulesText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/requestRoutingRules/rule01",
            "ruleType": "Basic",
            "backendAddressPool": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01"
            },
            "httpListener": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01"
            },
            "provisioningState": "Succeeded",
            "name": "rule01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SkuText                         : {
        "name": "Standard_Medium",
        "tier": "Standard",
        "capacity": 2
    }
    data:    ProbesText                      : []
    data:    UrlPathMapsText                 : []
    info:    network application-gateway create command OK

Así se crea una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. También configura la descarga SSL. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto. Si ya tiene la aplicación web definida con las direcciones IP del grupo de back-end definido en los pasos anteriores, una vez aprovisionada e iniciada la puerta de enlace de aplicaciones empezará el equilibrio de carga.

## Pasos siguientes

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md) (Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0831_2016-->