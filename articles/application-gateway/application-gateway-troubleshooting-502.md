<properties
   pageTitle="Solución de errores de puerta de enlace incorrecta (502) en el servicio Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar errores 502 en el servicio Puerta de enlace de aplicaciones."
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/15/2016"
   ms.author="amitsriva" />

#Solución de errores de puerta de enlace incorrecta en el servicio Puerta de enlace de aplicaciones

##Información general
Después de configurar una instancia de Puerta de enlace de aplicaciones de Azure, uno de los errores que se pueden encontrar los usuarios es "Error de servidor 502: el servidor web recibió una respuesta no válida mientras actuaba como puerta de enlace o servidor proxy". Esto puede ocurrir debido a los siguientes motivos principales:

- El grupo de back-end de la instancia de Puerta de enlace de aplicaciones de Azure no está configurado o está vacío.
- Ninguna de las máquinas virtuales o instancias del conjunto de escalas de máquina virtual está en buen estado.
- Las máquinas virtuales de back-end o instancias del conjunto de escalas de máquina virtual no responden a la sonda de estado predeterminada.
- La configuración de las sondas de estado personalizadas no es válida o adecuada.
- El tiempo de espera de solicitud se superó o hay problemas de conectividad con las solicitudes de usuario.


##Valor de BackendAddressPool vacío
###Causa
Si la instancia de Puerta de enlace de aplicaciones no tiene máquinas virtuales ni un conjunto de escalas de máquina virtual configurado en el grupo de direcciones de back-end, no se pueden enrutar solicitudes de clientes y se genera un error de puerta de enlace incorrecta.

###Solución
Asegúrese de que el grupo de direcciones de back-end no está vacío. Puede hacerlo mediante PowerShell, la CLI o el Portal.

	
	Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

El resultado del cmdlet anterior debe contener un grupo de direcciones de back-end que no esté vacío. A continuación, se muestra un ejemplo en el que se devuelven dos grupos que están configurados con direcciones IP o FQDN de máquinas virtuales de back-end. El estado de aprovisionamiento de BackendAddressPool debe ser "Correcto".
	
		BackendAddressPoolsText: 
				[{
					"BackendAddresses": [{
						"ipAddress": "10.0.0.10",
						"ipAddress": "10.0.0.11"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool01",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
				}, {
					"BackendAddresses": [{
						"Fqdn": "xyx.cloudapp.net",
						"Fqdn": "abc.cloudapp.net"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool02",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
				}]
	
	
## Instancias incorrectas en BackendAddressPool

###Causa
Si todas las instancias de BackendAddressPool son incorrectas, el servicio Puerta de enlace de aplicaciones no tendría ningún back-end al que enrutar la solicitud de usuario. También podría pasar cuando las instancias de back-end con correctas, pero no tienen implementada la aplicación necesaria.

###Solución
Asegúrese de que las instancias son correctas y de que la aplicación está configurada de la forma adecuada. Compruebe si las instancias de back-end pueden responder a un ping de otra máquina virtual de la misma red virtual. Si se configura con un punto de conexión público, asegúrese de que se pueda utilizar una solicitud de explorador a la aplicación web.


##Problemas con la sonda de estado predeterminada
###Causa
Los errores 502 también pueden ser indicativos frecuentes de que la sonda de estado predeterminada no puede conectarse con las máquinas virtuales de back-end. Cuando se aprovisiona una instancia de Puerta de enlace de aplicaciones, se configura automáticamente una sonda de estado predeterminada para cada BackendAddressPool que utiliza las propiedades de BackendHttpSetting. El usuario no tiene que escribir datos para establecer esta sonda. En concreto, cuando se configura una regla de equilibrio de carga, se crea una asociación entre un elemento BackendHttpSetting y otro BackendAddressPool. Se ha configurado una sonda predeterminada para cada una de estas asociaciones y Puerta de enlace de aplicaciones inicia una conexión de comprobación de estado periódica en cada instancia del elemento BackendAddressPool del puerto especificado en BackendHttpSetting. En la tabla siguiente se enumeran los valores asociados con la sonda de estado predeterminada.


|Propiedad de sondeo | Valor | Descripción|
|---|---|---|
| Dirección URL de sondeo| http://127.0.0.1/ | Ruta de acceso URL |
| Intervalo | 30 | Intervalo de sondeo en segundos |
| Tiempo de espera | 30 | Tiempo de espera del sondeo en segundos |
| Umbral incorrecto | 3 | Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

###Solución
- Asegúrese de que se ha configurado un sitio predeterminado y de que está escuchando en 127.0.0.1.
- Si BackendHttpSetting especifica un puerto distinto de 80, se debe configurar que el sitio predeterminado escuche en ese puerto.
- La llamada a http://127.0.0.1:port debe devolver un código de resultado HTTP 200 dentro del periodo de espera de 30 segundos.
- Asegúrese de que el puerto configurado está abierto y de que no hay ninguna regla de firewall ni grupos de seguridad de red de Azure que bloqueen el tráfico entrante o saliente en dicho puerto.
- Si usa el servicio en la nube o las máquinas virtuales clásicas de Azure con una IP pública o un FQDN, asegúrese de que esté abierto el [punto de conexión](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) correspondiente.
- Si la máquina virtual se configura mediante Azure Resource Manager y la instancia de Puerta de enlace de aplicaciones se implementó fuera de la red virtual, debe establecerse el [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) para permitir el acceso en el puerto deseado.


##Problemas con la sonda de estado personalizada
###Causa
Las sondas de estado personalizadas dotan al comportamiento de sondeo predeterminado de mayor flexibilidad. Cuando se usan sondas personalizadas, puede configurar el intervalo de sondeo, la dirección URL y la ruta de acceso a la comprobación, además del número de respuestas erróneas que se aceptarán antes de marcar la instancia del grupo de back-end como incorrecta. Se agregan las siguientes propiedades adicionales.


|Propiedad de sondeo| Descripción|
|---|---|
| Nombre | Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP de back-end. |
| Protocolo | Protocolo usado para enviar el sondeo. HTTP es el único protocolo válido. |
| Host | Nombre de host para enviar el sondeo. Solo es aplicable cuando se ha configurado un entorno multisitio en Puerta de enlace de aplicaciones. Es diferente al nombre de host de máquina virtual. |
| Ruta de acceso | Ruta de acceso relativa del sondeo. La ruta de acceso válida se inicia desde '/'. La sonda se envía a <protocolo> ://<host>:< puerto><ruta de acceso>. |
| Intervalo | Intervalo de sondeo en segundos. Es el intervalo de tiempo entre dos sondeos consecutivos.|
| Tiempo de espera | Tiempo de espera del sondeo en segundos. El sondeo se marca como error si no se recibe una respuesta válida dentro de este período de tiempo de espera. |
| Umbral incorrecto | Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |


###Solución
Valide que el sondeo de mantenimiento personalizado esté configurado correctamente según la tabla anterior. Además de realizar los pasos de solución de problemas anteriores, no se olvide tampoco de llevar a cabo estos pasos:

- Asegúrese de que el protocolo está establecido solo en HTTP. En estos momentos no se admite HTTPS.
- Asegúrese de que la sonda se ha especificado correctamente según la [guía](application-gateway-create-probe-ps.md).
- Si la instancia de Puerta de enlace de aplicaciones está configurada para un único sitio, de forma predeterminada, el nombre de host debe especificarse como 127.0.0.1, salvo que se configure de otra manera en la sonda personalizada.
- Asegúrese de que una llamada a http://\<host>:<puerto><ruta de acceso> devuelva un código de resultado HTTP 200.
- Compruebe que los valores del intervalo, el tiempo de espera y UnhealtyThreshold estén dentro de rangos aceptables.


##Tiempo de solicitud superado
###Causa
Cuando se recibe una solicitud de usuario, la instancia de Puerta de enlace de aplicaciones aplica las reglas configuradas a la solicitud y la enruta a una instancia del grupo de back-end. Espera, durante un intervalo de tiempo que puede configurarse, una respuesta de la instancia de back-end. De forma predeterminada, este intervalo es de **30 segundos**. Si la instancia de Puerta de enlace de aplicaciones no recibe una respuesta de una aplicación de back-end en este intervalo, la solicitud de usuario generaría un error 502.

###Solución
El servicio Puerta de enlace de aplicaciones permite a los usuarios configurar esta opción mediante BackendHttpSetting que, posteriormente, puede aplicarse a diferentes grupos. Estos grupos de back-end pueden tener distintos elementos BackendHttpSetting y, por tanto, diferentes valores configurados para el tiempo de solicitud superado.

	New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

##Pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).

<!---HONumber=AcomDC_0720_2016-->