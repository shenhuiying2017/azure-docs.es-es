<properties 
   pageTitle="Información general de DNS de Azure | Microsoft Azure" 
   description="Información general sobre los servicios de hospedaje DNS de Azure en Microsoft Azure y sobre cómo empezar a hospedar dominios en Microsoft Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Introducción a DNS de Azure

Detrás de cualquier sitio web o servicio en Internet, hay una dirección IP. Por ejemplo, www.microsoft.com utiliza la dirección IP 134.170.185.46. El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP.

DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure. Al hospedar dominios en Azure, puede administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure.

Los dominios DNS de DNS de Azure se hospedan en la red global de servidores de nombres DNS de Azure. Utilizamos redes de difusión por proximidad, por lo que cada consulta DNS la responde el servidor DNS disponible más cercano. Esto ofrece un rendimiento rápido y alta disponibilidad para el dominio.

El servicio se basa en el Administrador de recursos de Azure (ARM). Como tal, se beneficia de características ARM como control de acceso basado en roles, registros de auditoría y bloqueo de recursos.

Los dominios y registros pueden administrarse mediante el Portal de Azure, cmdlets de Azure PowerShell y la CLI de Azure multiplataforma. Las aplicaciones que requieren la administración automática de DNS pueden integrarse con el servicio a través de los SDK y API de REST.

DNS de Azure actualmente no admite la adquisición de nombres de dominio. Para adquirir dominios, debe recurrir a un registrador de nombres de dominio, que suele cobrar por ello una tarifa anual reducida. Estos dominios pueden hospedarse en DNS de Azure para la administración de registros DNS; consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener información.


## Pasos siguientes

[Introducción sobre la creación de zonas DNS](dns-getstarted-create-dnszone.md)

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)

[Referencia de la API de REST a DNS de Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=AcomDC_0309_2016-->