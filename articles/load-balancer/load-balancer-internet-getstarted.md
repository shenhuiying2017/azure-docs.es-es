
<properties
   pageTitle="Introducción a la configuración del equilibrador de carga accesible desde Internet | Microsoft Azure"
   description="Configure su primer conjunto de equilibrio de carga accesible desde Internet para sus máquinas virtuales o servicios en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/12/2015"
   ms.author="joaoma" />

# Introducción a la configuración del equilibrador de carga accesible desde Internet

> [AZURE.SELECTOR]
- [Azure classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)

Los servicios de equilibrio de carga de Microsoft Azure funcionan con todos los tipos de inquilinos (IaaS o PaaS) y todos los sistemas operativos compatibles (Windows o cualquier sistema operativo basado en Linux compatible).


## Configuración de un equilibrador de carga accesible desde Internet para máquinas virtuales

Para equilibrar la carga del tráfico de red de Internet entre las máquinas virtuales de un servicio en la nube, debe crear un conjunto con equilibrio de carga. En este procedimiento se supone que ya ha creado las máquinas virtuales y que están toda dentro del mismo servicio en la nube.

**Para configurar un equilibrio de carga establecido para máquinas virtuales**

1. En el Portal de Azure, haga clic en **Máquinas virtuales** y luego en el nombre de una máquina virtual del conjunto con equilibrio de carga.
2.	Haga clic en **Endpoints** y, a continuación, haga clic en **Add**.

4.	En la página **Agregar un extremo a una máquina virtual**, haga clic en la flecha derecha.

4.	En la página **Specify the details of the endpoint**:
	- En **Name**, escriba un nombre para el extremo o seleccione uno de la lista de extremos predefinidos para protocolos comunes.
	-  En **Protocol**, seleccione el protocolo que requiere el tipo de extremo, TCP o UDP, según sea necesario.
 	-  En **Public Port y Private Port**, escriba los números de puerto que desee que utilice la máquina virtual, según sea necesario. Puede utilizar el puerto privado y las reglas de firewall en la máquina virtual para redirigir el tráfico de la manera más adecuada para su aplicación. El puerto privado puede ser el mismo que el puerto público. Por ejemplo, para un extremo para tráfico web (HTTP), puede asignar el puerto 80 al puerto público y el privado.

5.	Haga clic en **Create a load-balanced set** y, a continuación, en la flecha derecha.

6.	En la página **Configure the load-balanced set**, especifique un nombre para el conjunto con equilibrio de carga y, a continuación, asigne los valores para probar el comportamiento del equilibrador de carga de Azure. El equilibrador de carga utiliza pruebas para determinar si las máquinas virtuales del conjunto con equilibrio de carga están disponibles para recibir tráfico entrante.

7.	Haga clic en la marca de verificación para crear el extremo con equilibrio de carga. Verá **Yes** en la columna **Load-balanced set name** de la página **Endpoints** de la máquina virtual.

8.	En el portal, haga clic en **Máquinas virtuales**, en el nombre de otra máquina virtual del conjunto con equilibrio de carga, en **Extremos** y, finalmente, en **Agregar**.

9.	En la página **Add an endpoint to a virtual machine**, haga clic en **Add endpoint to an existing load-balanced set**, seleccione el nombre del conjunto con equilibrio de carga y, a continuación, haga clic en la flecha derecha.

10.	En la página **Specify the details of the endpoint**, escriba un nombre para el extremo y, a continuación, haga clic en la marca de verificación. Para las máquinas virtuales adicionales del conjunto con equilibrio de carga, repita los pasos del 8 al 10.

También puede configurar extremos con los siguientes cmdlets de Windows PowerShell:

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## Configuración de un equilibrador de carga accesible desde Internet para servicios en la nube


Los servicios en la nube se configuran automáticamente con un equilibrador de carga y se pueden personalizar mediante el modelo de servicio.

Puede aprovechar el SDK de Azure para .NET 2.5 para actualizar el servicio en la nube. La configuración de extremos para los servicios en la nube se realiza en el archivo [definición de servicio](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

En el ejemplo siguiente se muestra cómo se configura un archivo servicedefinition.csdef para una implementación en la nube:

Mediante la comprobación del fragmento de código para el archivo .csdef generado por una implementación en la nube, puede ver el extremo externo configurado para usar puertos HTTP en los puertos 10000, 10001 y 10002.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Comprobación del estado de mantenimiento del equilibrador de carga para servicios en la nube


A continuación se muestra un sondeo de estado:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

El equilibrador de carga combina la información del extremo y la información del sondeo para crear una dirección URL en forma de http://{DIP de VM}:80/Probe.aspx que se puede usar para consultar el estado del servicio.

El servicio detecta sondeos periódicos desde la misma dirección IP. Se trata de la solicitud de sondeo de estado procedente del host del nodo donde se ejecuta la máquina virtual. El servicio debe responder con un código de estado HTTP 200 para que el equilibrador de carga asuma que el estado del servicio es correcto. Cualquier otro código de estado HTTP (por ejemplo, el 503) extrae directamente la máquina virtual de la rotación.

La definición de sondeo también controla la frecuencia del sondeo. En nuestro caso anterior, el equilibrador de carga está sondeando el extremo de cada 5 segundos. Si no se recibe una respuesta positiva durante 10 segundos (dos intervalos de sondeo), se supone que el sondeo es negativo y la máquina virtual se excluye de la rotación. De forma similar, si el servicio está fuera de la rotación y se recibe una respuesta positiva, el servicio se pone de nuevo en rotación de forma inmediata. Si el servicio fluctúa entre correcto e incorrecto, el equilibrador de carga puede decidir retrasar la reintroducción del servicio a rotación hasta que ha sido correcto durante un número de sondeos.

Compruebe el esquema de definición del [sondeo de estado](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obtener más información.

## Configurar el equilibrador de carga con PowerShell

Después de crear una máquina virtual, puede usar cmdlets de PowerShell para agregar un equilibrador de carga a una máquina virtual dentro del mismo servicio en la nube.

En el ejemplo siguiente, agregará un equilibrador de carga denominado "webfarm" al extremo de servicio en la nube "mycloudservice" (o mycloudservice.cloudapp.net) y una máquina virtual llamada myVM. El equilibrador de carga recibe tráfico en el puerto 80 y equilibra la carga del tráfico de red entre las máquinas virtuales en el puerto 8080 mediante HTTP.

	Get-AzureVM -ServiceName "mycloudservice" -Name "MyVM" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM


## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Oct15_HO3-->