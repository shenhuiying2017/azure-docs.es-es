<properties
    pageTitle="¿Cuáles son las imágenes de plantilla de RemoteApp?"
    description="Obtenga información sobre las imágenes de plantilla incluidas con RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2015" 
    ms.author="elizapo" />

# ¿Cuáles son las imágenes de plantilla de RemoteApp?

La suscripción de Azure RemoteApp incluye tres imágenes de plantilla:


- Windows Server 2012
- Microsoft Office 365 ProPlus (se requiere suscripción a Office 365)
- Microsoft Office Professional Plus 2013 (solo versión de prueba)

> [AZURE.IMPORTANT]Independientemente de la imagen que use, hay consideraciones sobre la concesión de licencias en cualquier momento que comparta una aplicación con los usuarios. Consulte los [Detalles de concesión de licencias de RemoteApp](remoteapp-licensing.md) para obtener más información.

Siga leyendo para obtener más información sobre lo que contiene cada imagen.

## Windows Server 2012 R2 ("vanilla image")
Esta imagen se basa en el sistema operativo Microsoft Windows Server 2012 R2 Datacenter y tiene los siguientes roles y características instalados para cumplir los requisitos de las imágenes de plantilla de Azure RemoteApp:


- .NET Framework 4.5, 3.5.1, 3.5
- Experiencia de escritorio
- Servicios de Escritura con lápiz y Escritura a mano
- Media Foundation
- Host de sesión de Escritorio remoto
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Compatibilidad con WoW64

Esta imagen también tiene instaladas las siguientes aplicaciones:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (se requiere suscripción)
Office 365 es la aplicación más solicitada, por lo que hemos creado una imagen "personalizada" para que pueda trabajar con ella.

Esta imagen es una extensión de la vanilla image y tiene los siguientes componentes de Microsoft Office 365 ProPlus instalados, además de los componentes descritos en la imagen de Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para la Empresa
- Outlook
- PowerPoint
- proyecto
- Visio
- Word
- Herramientas de corrección de Microsoft Office

Además de las aplicaciones siguientes:

- SQL Native Client
- Controlador ODBC
- Cliente de minería de datos para SQL Server
- Cliente MasterDataServices
- Microsoft Publisher
- PowerQuery
- PowerMap


La funcionalidad completa de las aplicaciones de Office 365 ProPlus está disponible solo para los usuarios que tienen un plan de Office 365 ProPlus. Para obtener más detalles sobre los planes de suscripción a Office 365, consulte [Planes de servicio de Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Para obtener más detalles acerca de la obtención de licencias de RemoteApp, consulte [¿Cómo funciona la concesión de licencias de RemoteApp de Azure?](remoteapp-licensing.md)

## Microsoft Office Professional Plus 2013 (solo versión de prueba)
Durante el período de prueba gratuito, puede probar el servicio con la imagen de Office 2013.

Esta imagen es una extensión de la vanilla image y tiene los siguientes componentes de Microsoft Office 2013 Professional Plus instalados, además de los componentes descritos en la imagen de Windows Server 2012 R2:


- Access
- Excel
- Lync
- OneNote
- OneDrive para la Empresa
- Outlook
- PowerPoint
- proyecto
- Visio
- Word
- Herramientas de corrección de Microsoft Office

> [AZURE.IMPORTANT]**Información legal:** esta imagen no incluye una licencia de Microsoft Office y *no se puede usar para producción*. La imagen de Office 2013 Professional Plus solo tiene fines de prueba. Si quiere usar aplicaciones de Office en RemoteApp de Azure para producción, deberá usar la imagen de Office 365 ProPlus. Para obtener más detalles acerca de la obtención de licencias de RemoteApp, consulte [¿Cómo funciona la concesión de licencias de RemoteApp de Azure?](remoteapp-licensing.md)
 

<!---HONumber=July15_HO4-->