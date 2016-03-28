<properties 
   pageTitle="Administración de registros de DNS inversos para sus servicios usando PowerShell en el modelo de implementación clásico | Microsoft Azure"
   description="Cómo administrar registros de DNS inversos o registros PTR para servicios Azure usando PowerShell en el modelo de implementación clásico."
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Administración de registros de DNS inversos para los servicios (clásico) mediante PowerShell

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md).

## Validación de registros de DNS inversos
Para asegurarse de que un tercero no pueda crear registros de DNS inversos que se asignen a sus dominios DNS, Azure solo permite la creación de un registro de DNS inverso en el que se cumpla una de las siguientes condiciones:

- El FQDN DNS inverso es el nombre del servicio en la nube para el que se ha especificado o cualquier nombre de servicio en la nube dentro de la misma suscripción. Por ejemplo, el DNS inverso es “contosoapp1.cloudapp.net.”.
- El FQDN DNS inverso se resuelve directamente en el nombre o la IP del servicio en la nube para el que se ha especificado o en cualquier nombre o IP de servicio en la nube dentro de la misma suscripción. Por ejemplo, el DNS es “app1.contoso.com.”, que es un alias CNAME para contosoapp1.cloudapp.net.

Solo se realizan comprobaciones de validación cuando la propiedad DNS inversa de un servicio en la nube se define o modifica. No se llevan a cabo revalidaciones periódicas.

## Adición de DNS inverso a los servicios en la nube existentes
Puede agregar un registro DNS inverso a un servicio en la nube existente usando el cmdlet “Set-AzureService”.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Creación de un servicio en la nube con un DNS inverso  
Puede agregar un nuevo servicio en la nube con la propiedad DNS inversa especificada usando el cmdlet “Set-AzureService”.

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Visualización de DNS inverso para los servicios en la nube existentes
Puede ver el valor configurado para un servicio en la nube existente usando el cmdlet “Get-AzureService”:

	PS C:\> Get-AzureService "contosoapp1"

## Eliminación de DNS inverso de los servicios en la nube existentes
Puede quitar una propiedad DNS inversa desde un servicio en la nube existente usando el cmdlet “Set-AzureService”. Para ello, deje en blanco el valor de la propiedad DNS inversa:

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [P+F](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!----HONumber=AcomDC_0316_2016-->