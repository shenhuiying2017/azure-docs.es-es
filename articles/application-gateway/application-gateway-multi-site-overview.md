<properties
   pageTitle="Hospedaje de varios sitios en Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Esta página proporciona información general de la compatibilidad multisitio de Puerta de enlace de aplicaciones."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>

# Hospedaje de varios sitios de Puerta de enlace de aplicaciones

El hospedaje de varios sitios permite configurar más de una aplicación web en la misma instancia de Puerta de enlace de aplicaciones. Esto permite configurar topología más eficaz para las implementaciones empaquetando hasta 20 sitios web en una puerta de enlace de aplicaciones. Cada sitio web podría dirigirse a su propio grupo de back-end. En el ejemplo siguiente, la puerta de enlace de aplicaciones atiende el tráfico de contoso.com y fabrikam.com desde dos grupos de servidores back-end denominados ContosoServerPool y FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Las solicitudes de http://contoso.com se enrutarán a ContosoServerPool y las de http://fabrikam.com a FabrikamServerPool.

De forma similar, dos subdominios del mismo dominio primario pueden hospedarse en la misma implementación de puerta de enlace de aplicaciones. Entre los ejemplos de esto, podrían incluirse http://blog.contoso.com y http://app.contoso.com hospedados en una sola implementación de puerta de enlace de aplicaciones.


##Encabezados de host e Indicación de nombre de servidor (SNI)
Existen tres mecanismos comunes para habilitar el hospedaje de varios sitios en la misma infraestructura.

1. Hospede varias aplicaciones web, cada una en una dirección IP única.
2. Use el nombre de host para hospedar varias aplicaciones web en la misma dirección IP.
3. Use puertos distintos para hospedar varias aplicaciones web en la misma dirección IP.

Actualmente, una puerta de enlace de aplicaciones obtiene una dirección IP pública única en la que escucha el tráfico. Por lo tanto, la compatibilidad con varias aplicaciones, cada una con su propia dirección IP, no se admite actualmente. Puerta de enlace de aplicaciones admite el hospedaje de varias aplicaciones, cada una escuchando en puertos distintos, pero esto requeriría que las aplicaciones aceptaran el tráfico en puertos no estándar y no suele ser una configuración deseada. Puerta de enlace de aplicaciones se basa en los encabezados de host HTTP 1.1 para hospedar más de un sitio web en la misma dirección IP pública y en el mismo puerto. Los sitios que se hospedan en la puerta de enlace de aplicaciones también pueden admitir la descarga SSL con la extensión TLS de Indicación de nombre de servidor (SNI). Esto significa que el explorador web y la granja de servidores web de back-end deben admitir la extensión TLS y HTTP/1.1 como se define en RFC 6066.

  

## Elemento de configuración de agente de escucha

El elemento de configuración HTTPListener existente se mejora para admitir los elementos de indicación de nombre de servidor y nombre de host que usa la puerta de enlace de aplicaciones para enrutar el tráfico al grupo de back-end adecuado. Este es el fragmento de código del elemento HttpListeners del archivo de plantilla.

 		"httpListeners": [
                    {
                        "name": "appGatewayHttpsListener1",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                            },
                            "Protocol": "Https",
                            "SslCertificate": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                            },
                            "HostName": "contoso.com",
                            "RequireServerNameIndication": "true"
                        }
                    },
                    {
                        "name": "appGatewayHttpListener2",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                            },
                            "Protocol": "Http",
                            "HostName": "fabrikam.com",
                            "RequireServerNameIndication": "false"
                        }
                    }
                ],

	


Puede consultar la [plantilla ARM mediante el hospedaje de varios sitios](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para obtener una implementación basada en una plantilla de un extremo a otro.

## Regla de enrutamiento

No se requiere ningún cambio en la regla de enrutamiento. La regla de enrutamiento 'básica' debería seguir eligiéndose para vincular el agente de escucha del sitio adecuado al grupo de direcciones de back-end correspondiente.

	"requestRoutingRules": [
	{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	},
	{
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}
	]
	
## Pasos siguientes 

Ahora que conoce el hospedaje de varios sitios, vaya a la sección sobre cómo [crear una puerta de enlace de aplicaciones mediante el hospedaje de varios sitios](application-gateway-create-multisite-azureresourcemanager-powershell.md) para crear una puerta de enlace de aplicaciones con la capacidad de admitir más de una aplicación web.

<!---HONumber=AcomDC_0810_2016-->