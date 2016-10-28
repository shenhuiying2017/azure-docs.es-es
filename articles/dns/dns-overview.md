<properties 
   pageTitle="Información general de DNS de Azure | Microsoft Azure" 
   description="Información general de servicios de hospedaje de DNS de Azure en Microsoft Azure. Hospede el dominio en Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Introducción a DNS de Azure


El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP. DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure. Al hospedar dominios en Azure, puede administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure.


Los dominios DNS de DNS de Azure se hospedan en la red global de servidores de nombres DNS de Azure. Utilizamos redes de difusión por proximidad, por lo que cada consulta de DNS la responde el servidor DNS disponible más cercano. Esto ofrece un rendimiento rápido y alta disponibilidad para el dominio.

El servicio DNS de Azure se basa en Azure Resource Manager (ARM). Como tal, se beneficia de características ARM como control de acceso basado en roles, registros de auditoría y bloqueo de recursos. Los dominios y registros pueden administrarse mediante el Portal de Azure, cmdlets de Azure PowerShell y la CLI de Azure multiplataforma. Las aplicaciones que requieren la administración automática de DNS pueden integrarse con el servicio a través de los SDK y API de REST.

DNS de Azure actualmente no admite la adquisición de nombres de dominio. Si desea adquirir dominios, debe usar un registrador de nombres de dominio de un tercero. Normalmente, el registrador cobrará una tarifa anual reducida. Los dominios se pueden hospedar en DNS de Azure para la administración de registros de DNS. Consulte [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md) para más información.


## Pasos siguientes

[Creación de una zona DNS](dns-getstarted-create-dnszone-portal.md)




 

<!---HONumber=AcomDC_0817_2016-->