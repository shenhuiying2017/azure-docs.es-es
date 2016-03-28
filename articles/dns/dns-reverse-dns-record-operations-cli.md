<properties 
   pageTitle="Administración de registros de DNS inversos para los servicios usando la CLI de Azure en Resource Manager | Microsoft Azure"
   description="Cómo administrar registros de DNS inversos o registros PTR para servicios Azure usando la CLI de Azure en Resource Manager"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Administración de registros de DNS inversos para los servicios mediante la CLI de Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## Validación de registros de DNS inversos 
Para asegurarse de que un tercero no pueda crear registros de DNS inversos que se asignen a sus dominios DNS, Azure solo permite la creación de un registro de DNS inverso en el que se cumpla una de las siguientes condiciones:

- El valor de “ReverseFqdn” es el mismo que el de “Fqdn” para el recurso de dirección IP pública para el que se ha especificado o el de “Fqdn” para cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, “ReverseFqdn” es “contosoapp1.northus.cloudapp.azure.com.”.

- El valor de “ReverseFqdn” se resuelve directamente en el nombre o la IP de la dirección IP pública para la que se ha especificado, o en el IP o “Fqdn” de cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, “ReverseFqdn” es “app1.contoso.com.”, que es un alias CNAME para “contosoapp1.northus.cloudapp.azure.com.”

Solo se realizan comprobaciones de validación cuando la propiedad DNS inversa de una dirección IP pública se define o modifica. No se llevan a cabo revalidaciones periódicas.

## Adición de DNS inversos a las direcciones IP públicas existentes
Puede agregar DNS inversos a una dirección IP pública existente usando azure network public-ip set:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si desea agregar un DNS inverso a una dirección IP pública existente que no tenga aún un nombre DNS, debe especificar también un nombre DNS. Puede realizar la adición para conseguir esto con azure network public-ip set:

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## Creación de una dirección IP pública con un DNS inverso
Puede agregar una nueva dirección IP pública con la propiedad DNS inversa especificada usando azure network public-ip create:

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
 
## Visualización de DNS inversos para las direcciones IP públicas existentes
Puede ver el valor configurado para una dirección IP pública existente usando azure network public-ip show:

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Eliminación de DNS inversos de direcciones IP públicas existentes
Puede quitar una propiedad DNS inversa de una dirección IP pública existente usando azure network public-ip set. Para ello, se deja en blanco el valor de la propiedad ReverseFqdn:

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[AZURE.INCLUDE [P+F](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0316_2016-->