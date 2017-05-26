---

title: "Migración de Azure RemoteApp a Citrix XenApp Essentials | Microsoft Docs"
description: "Migración de Azure RemoteApp a Citrix XenApp Essentials"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Migración de Azure RemoteApp a Citrix XenApp Essentials

Los clientes de Azure RemoteApp que deseen migrar a Citrix XenApp Essentials deben cumplir varios requisitos previos.  Le recomendamos que lea primero la [guía técnica paso a paso de implementación técnica Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf), así como su [biblioteca técnica en línea](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

Afortunadamente puede volver a usar la mayoría de las inversiones realizadas en Azure RemoteApp, pero la implementación de XenApp Essentials tiene varios requisitos previos de alto nivel.

## <a name="prerequisites"></a>Requisitos previos

1. Cree una red virtual nueva o determine en qué red virtual de Azure de Azure Resource Manager va a implementar Citrix XenApp Essentials. Azure RemoteApp utiliza Azure clásico; Citrix XenApp Essentials solo admite Azure Resource Manager.  
2. Asegúrese de que la red virtual que ha seleccionado tiene acceso de red a su controlador de dominio, ya que Citrix solo admite implementaciones híbridas. Si usa una implementación en la nube de Azure RemoteApp, tendrá que asegurarse de que su red virtual tiene acceso de red a un controlador de dominio de Active Directory y le recomendamos que use Azure Active Directly Domain Services (AAD-DS). 
3. Asegúrese de que el DNS esté configurado correctamente para la red virtual, ya que, así, la unión al dominio se realizará correctamente al primer intento. Para comprobar que el DNS y la unión al dominio funcionan según lo previsto, puede crear una máquina virtual en la red virtual que ha seleccionado y realizar una unión manual al dominio. De esta forma se asegura que Citrix XenApp Essentials se implementa correctamente a la primera. 
4. Si es necesario, cree un emparejamiento de VNET entre una red virtual de Azure clásico que use con Azure RemoteApp y la red virtual de Azure Resource Manager, siempre que residan en la misma región o use la VPN de S2S si no están correctamente para conectar las redes virtuales a la red. 
5. Si fuera necesario, lea el artículo [Procedimiento para migrar datos en Azure RemoteApp como origen y destino](remoteapp-migrate.md). 
6. Actualice la imagen de Azure RemoteApp existente para incluir el componente de VDA de Citrix. Para obtener instrucciones al respecto, consulte la documentación de Citrix. 
7. Vaya a Azure Marketplace y comience la implementación de Citrix XenApp Essentials.

Gracias por usar Azure RemoteApp. 

## <a name="other-considerations"></a>Otras consideraciones:

- Citrix XenApp Essentials solo admite implementaciones híbridas. En otras palabras, para realizar la unión a un dominio se necesita acceso de red a un controlador de dominio. Si utiliza una implementación en la nube de Azure RemoteApp, es preciso que use AAD-DS o que se asegure de que la red virtual tiene acceso a Active Directory para unirse a un dominio. 
- Para aprender a mover datos de usuario a CXE, lea el articulo [Procedimiento para migrar datos en Azure RemoteApp como origen y destino](remoteapp-migrate.md). 
- Citrix XenApp Essentials solo admite cuentas de Active Directory. No admite cuentas Microsoft (@outlook.com, @msn.com, @hotmail.com, etc.) 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Descripción de la facturación de Citrix XenApp Essentials 

Para obtener toda la información acerca de los precios, consulte las [preguntas frecuentes](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) y el [artículo con información general de Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Hay tres componentes de la facturación en Citrix XenApp Essentials, son:

1. El cargo de 12 USD por usuario y mes por el servicio de Citrix. Al igual que todas las compras que se realizan en Azure Marketplace, esto se factura con el método de pago asociado a su suscripción de Azure. Los clientes de EA no pueden usar los créditos monetarios de Azure. 
2. CAL de RDS. Debe tener su propio CAL de RDS (próximamente) o abonar los 6,25 USD de cuota de acceso remoto (RAF) que se incluye con el pago de Citrix XenApp Essentials. Los clientes de EA pueden usar los créditos monetarios de Azure para pagarlo. Si desea utilizar sus propias CAL de RDS, póngase en contacto con nosotros [arainfo@microsoft.com] (envíe un correo electrónico a mailto:arainfo@microsoft.com para que podamos aplicarlo a su factura). 
3. Proceso y almacenamiento de Azure. Es el costo de almacenamiento de Azure y el consumo de procesos de las máquinas virtuales consumidas. El precio es un factor que conviene tener en cuenta al seleccionar el tamaño de la máquina virtual y la densidad de usuarios. Los clientes de EA pueden usar los créditos monetarios de Azure para pagarlo

Si le queda alguna duda, póngase en contacto con nosotros
1. Envíenos un correo electrónico a [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. [Póngase en contacto con el equipo de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Para empezar, [abra un caso de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), lo que servirá si necesita ayuda en los pasos 1 - 5. Póngase en contacto con Citrix, para lo que debe abrir una incidencia de soporte técnico en el portal de administración de Citrix para, si necesita ayuda en los pasos 6 y 7. 

