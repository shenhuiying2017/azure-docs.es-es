---
title: "Solución de problemas de creación de colecciones híbridas de RemoteApp | Microsoft Docs"
description: "Obtenga información sobre cómo solucionar problemas con la creación de las colecciones híbridas de RemoteApp"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: b32033ee-8d52-4e74-bb78-86ca873c34e2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 537fbe37b0ee21cb5d2c9f1ba437ac178850fee2


---
# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Solución de problemas de creación de colecciones híbridas de Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Una colección híbrida se hospeda en y almacena los datos en la nube de Azure, pero también permite a los usuarios acceder a datos y recursos almacenados en la red local. Los usuarios pueden acceder a las aplicaciones mediante el inicio de sesión con sus credenciales corporativas sincronizadas o federadas con Azure Active Directory. Puede implementar una colección híbrida que use una red virtual de Azure existente o bien puede crear una nueva red virtual. Se recomienda crear o utilizar una subred de red virtual con un rango de CIDR lo suficientemente grande como para cubrir el crecimiento futuro esperado para Azure RemoteApp.

¿Todavía no ha creado la colección? Consulte los pasos en [Creación de una colección híbrida](remoteapp-create-hybrid-deployment.md) .

Si tiene problemas al crear la colección, o si la colección no funciona del modo esperado, consulte la siguiente información.

## <a name="your-image-is-invalid"></a>La imagen no es válida
Si ve un mensaje como "GoldImageInvalid" cuando esté esperando a que Azure aprovisione la colección, la imagen de plantilla no cumple [los requisitos definidos para la imagen](remoteapp-imagereqs.md). Por lo tanto, consulte los [requisitos](remoteapp-imagereqs.md), corrija la imagen y pruebe a crear la colección de nuevo.

## <a name="does-your-vnet-have-network-security-groups-defined"></a>¿La red virtual tiene grupos de seguridad de red definidos?
Si ha definido grupos de seguridad de red en la subred que utiliza para la colección, asegúrese de que se puede acceder a estas [direcciones URL y puertos](remoteapp-ports.md) desde la subred.

Puede agregar grupos de seguridad de red adicionales a las red virtuales que implemente en la subred para disponer de un control más estricto.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>¿Utiliza sus propios servidores DNS? ¿Son accesibles desde la subred de red virtual?
> [!NOTE]
> Tendrá que asegurarse de que los servidores DNS de la red virtual estén siempre activos y puedan resolver en todo momento las máquinas virtuales hospedadas en la red virtual. No utilice Google DNS para ello.
> 
> 

En las colecciones híbridas utiliza sus propios servidores DNS. Los especifica en el esquema de configuración de red o a través del Portal de administración al crear la red virtual. Los servidores DNS se utilizan en el orden en que se especifican en forma de conmutación por error (como oposición a round robin).  
Consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para asegurarse de que los servidores DNS se han configurado correctamente.

Asegúrese de que los servidores DNS de la colección son accesibles y están disponibles en la subred de red virtual especificada para esta colección.

Por ejemplo:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definir el DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>¿Utiliza un controlador de dominio de Active Directory en la colección?
Actualmente solo se puede asociar un dominio de Active Directory con Azure RemoteApp. La colección híbrida solo admite cuentas de Azure Active Directory que se hayan sincronizado con la herramienta DirSync desde una implementación de Windows Server Active Directory; en concreto, sincronizadas con la opción Sincronización de contraseña o bien con la opción de federación de Servicios de federación de Active Directory (AD FS) configurada. Deberá crear un dominio personalizado que coincida con el sufijo UPN del dominio del dominio local para, a continuación, configurar la integración de directorio.

Consulte [Configuración de Active Directory para Azure RemoteApp](remoteapp-ad.md) para obtener información.

Asegúrese de que los detalles de dominio proporcionados son válidos y de que se puede acceder al controlador de dominio desde la máquina virtual creada en la subred que se utiliza para Azure RemoteApp. Asegúrese también de que las credenciales de cuenta de servicio suministradas tienen permisos para agregar equipos al dominio proporcionado y de que el nombre de AD indicado se puede resolver a partir del DNS proporcionado en la red virtual.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>¿Qué nombre de dominio especificó al crear la colección?
El nombre de dominio que creó o agregó debe ser un nombre de dominio interno (no un nombre de dominio de Azure AD) y debe utilizar el formato DNS que se puede resolver (contoso.local). Por ejemplo, si tiene un nombre interno de Active Directory (contoso.local) y un UPN de Active Directory (contoso.com), debe utilizar el nombre interno al crear la colección.




<!--HONumber=Dec16_HO2-->


