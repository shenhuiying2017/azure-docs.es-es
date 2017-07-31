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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: es-es
ms.lasthandoff: 06/01/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Migración de Azure RemoteApp a Citrix XenApp Essentials

Si usa Azure RemoteApp y desea migrar a Citrix XenApp Essentials, debe tener en cuenta algunos requisitos previos. En primer lugar, lea la [guía de implementación técnica paso a paso de Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) y su [biblioteca técnica en línea](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

## <a name="prerequisite-steps-for-migration"></a>Pasos de requisitos previos para la migración

1. Cree una red virtual nueva o determine en qué red virtual de Azure de Azure Resource Manager va a implementar Citrix XenApp Essentials. Azure RemoteApp usa el Portal de Azure clásico; Citrix XenApp Essentials solo admite Azure Resource Manager.  
2. Asegúrese de que la red virtual que seleccionó tiene acceso de red a su controlador de dominio, ya que Citrix solo admite implementaciones híbridas. Si usa una implementación de nube de Azure RemoteApp, asegúrese de que la red virtual tiene acceso de red a un controlador de dominio de Active Directory. También puede usar Azure Active Directory Domain Services (Azure AD DS). 
3. Asegúrese de que el DNS esté configurado correctamente para la red virtual, de manera que la unión a un dominio se realice correctamente en el primer intento. Puede crear una máquina virtual (VM) en la red virtual que seleccionó y realizar una unión a un dominio manual para comprobar que el DNS y la unión a un dominio funcionan según lo previsto. De esta forma se asegura que Citrix XenApp Essentials se implemente correctamente a la primera. 
4. En caso de que sea necesario, cree un emparejamiento de red virtual entre una red virtual del Portal de Azure clásico que usa con Azure RemoteApp y la red virtual de Azure Resource Manager. Este proceso de emparejamiento funciona si ambas redes residen en la misma región. Si no es así, use la VPN de sitio a sitio para conectar las redes virtuales para establecer la red. 
5. Si fuera necesario, lea el artículo [Procedimiento para migrar datos en Azure RemoteApp como origen y destino](remoteapp-migrate.md). 
6. Actualice la imagen de Azure RemoteApp existente para incluir el componente de VDA de Citrix (para instrucciones, consulte la documentación de Citrix). 
7. Vaya a Azure Marketplace y comience la implementación de Citrix XenApp Essentials.

## <a name="other-considerations"></a>Otras consideraciones

Tenga en cuenta las siguientes consideraciones adicionales cuando realice la migración:
- Citrix XenApp Essentials solo admite implementaciones híbridas. En otras palabras, para realizar la unión a un dominio se necesita acceso de red a un controlador de dominio. Si usa una implementación en la nube de Azure RemoteApp, use Azure AD DS o asegúrese de que la red virtual tiene acceso a Active Directory para la unión a un dominio. 
- Para información sobre cómo migrar datos de usuario a Citrix XenApp Essentials, consulte [Procedimiento para migrar datos en Azure RemoteApp como origen y destino](remoteapp-migrate.md). 
- Citrix XenApp Essentials solo admite cuentas de Active Directory. No se admiten cuentas de Microsoft (como outlook.com, msn.com o hotmail.com). 

## <a name="citrix-xenapp-essentials-billing"></a>Facturación de Citrix XenApp Essentials

Para todos los detalles sobre los precios, consulte las [preguntas más frecuentes](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) y el [artículo con información general de Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Hay tres componentes de facturación a Citrix XenApp Essentials:

- El cargo de servicio de Citrix, que es de 12 USD por usuario al mes. Al igual que todas las compras que se realizan en Azure Marketplace, esto se factura con el método de pago asociado a su suscripción de Azure. Los clientes de Contrato Enterprise (EA) no pueden usar los créditos monetarios de Azure. 
- Licencias de acceso de cliente (CAL) de Servicios de datos remotos (RDS). Actualmente, puede adquirir la tarifa de acceso remoto que se incluye con el pago de Citrix XenApp Essentials por 6,25 USD. Si es cliente de EA, puede usar créditos monetarios de Azure para pagarlo. Si desea usar las CAL de RDS existentes, póngase en contacto con nosotros en [arainfo@microsoft.com](mailto:arainfo@microsoft.com) para que podamos aplicarlo a su factura. 
- Proceso y almacenamiento de Azure. Es el costo de almacenamiento de Azure y el consumo de procesos de las máquinas virtuales consumidas. El precio es un factor que conviene tener en cuenta al seleccionar el tamaño de la máquina virtual y la densidad de usuarios. Si es cliente de EA, puede usar créditos monetarios de Azure para pagarlo.

Si tiene más preguntas:
- Envíenos un correo electrónico a [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
- [Póngase en contacto con el equipo de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Para empezar, [abra un caso de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), lo que servirá si necesita ayuda en los pasos del 1 al 5 de requisitos previos. Si necesita ayuda en los pasos 6 y 7, abra una incidencia de soporte técnico en el portal de administración de Citrix para ponerse en contacto con Citrix. 

