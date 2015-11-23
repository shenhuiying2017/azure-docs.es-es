<properties 
   pageTitle="Introducción a la creación de un equilibrador de carga orientado a Internet en un modelo de implementación clásica con el portal de Azure | Microsoft Azure"
   description="Obtenga información acerca de cómo crear un equilibrador de carga orientado a Internet en el modelo de implementación clásica mediante el portal de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/05/2015"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet (clásico) en el portal de Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo se aborda el modelo de implementación clásica. Si está buscando el modelo de implementación del Administrador de recursos de Azure, vaya a [Introducción a la creación de un equilibrador de carga orientado a Internet mediante el administrador de recursos](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


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



## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->