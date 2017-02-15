---
title: "Opciones para realizar una migración desde Azure RemoteApp | Microsoft Docs"
description: "Obtenga más información sobre las opciones para realizar una migración desde Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: d12ccdc13d6964a6de8068a63f945c7eac40b682


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opciones para realizar una migración desde Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Si ha dejado de utilizar Azure RemoteApp debido al [anuncio de retirada](https://go.microsoft.com/fwlink/?linkid=821148) o porque ha terminado la evaluación, tiene que migrar de Azure RemoteApp a otro servicio de aplicaciones. Hay dos enfoques diferentes de migración: una implementación autoadministrada (también denominada "infraestructura como servicio" [IaaS]) o una oferta totalmente administrada (a menudo denominada "plataforma como servicio" o "software como servicio"·[PaaS y SaaS respectivamente]). 

El IaaS de autoservicio es una implementación donde el usuario es el responsable y quien se encarga de administrarla y realizarla en sistemas físicos o máquinas virtuales. Por otro lado, una oferta PaaS y SaaS totalmente administrada guarda una gran similitud con Azure RemoteApp: un asociado proporciona una capa de servicio basada en una solución de acceso remoto que controla las tareas operativas y de mantenimiento, mientras que el usuario, como cliente, se encarga de la administración de las imágenes y las aplicaciones.

Siga leyendo este artículo para obtener más información, incluidos ejemplos de las distintas opciones de hospedaje.    

## <a name="self-managed-iaas-solutions"></a>Soluciones autoadministradas (IaaS)
### <a name="rds-on-iaas"></a>**RDS en IaaS**
Puede implementar una implementación nativa basada en sesiones de Servicios de Escritorio remoto (en Windows Server) mediante RemoteApp o escritorios locales, o bien en un entorno hospedado (como en máquinas virtuales de Azure). La opción de RDS en implementaciones de IaaS es más adecuada para los clientes que ya están familiarizados con esta herramienta y que tienen experiencia técnica en implementaciones de RDS. 

> [!NOTE]
> Para utilizar esta opción de implementación, necesita el programa de Licencias por Volumen con Software Assurance (SA) correspondiente a las licencias de acceso al cliente de RDS.
> 
> 

Implementar RDS en máquinas virtuales de Azure es más fácil que nunca cuando se utilizan plantillas de implementación y aplicación de revisiones (lea una [Introducción](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) y, luego, [descárguelas](https://aka.ms/rdautomation)). Puede obtener las mismas funcionalidades de escala elástica con los recursos del modelo de implementación clásica de Azure (no los recursos del modelo de Azure Resource Manager) en Azure RemoteApp mediante el [script de escalado automático](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), aunque hay más personalizaciones y configuraciones. Al implementar RDS en máquinas virtuales de Azure, el soporte técnico se proporciona a través del equipo de [soporte técnico de Azure](https://azure.microsoft.com/support/plans/), los mismos profesionales que le prestaron asistencia con Azure RemoteApp. Puede obtener estimaciones de costos en función del patrón de uso actual poniéndose en contacto el equipo de [soporte técnico de Azure](https://azure.microsoft.com/support/plans/), o bien puede realizar los cálculos con una calculadora de costos que se publicará próximamente.  Además, con las máquinas virtuales de serie N (actualmente en fase de versión preliminar), puede agregar vGPU (puede obtener más información sobre cómo agregar vGPU y [aprovechar las mejoras de RDS en Windows Server 2016](https://myignite.microsoft.com/videos/2794) en nuestra sesión Ignite).   

Contamos con guías de implementación paso a paso de [Windows Server 2012 R2](http://aka.ms/rdsonazure) y [Windows Server 2016](http://aka.ms/rdsonazure2016) para ayudarlo con el proceso de implementación. Visite el [blog de Escritorio remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) para consultar las noticias más recientes.

### <a name="citrix-on-iaas"></a>**Citrix en IaaS**
Las implementaciones nativas de Citrix de las aplicaciones XenApp o XenDesktop basadas en sesiones pueden realizarse en entornos locales u hospedados (por ejemplo, en máquinas virtuales de Azure). 

Consulte la guía de implementación paso a paso, [Citrix XA 7.6 on Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx) (Citrix XA 7.6 en Azure), para obtener más información. Consulte más detalles sobre [Citrix en Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), donde podrá utilizar también una calculadora de precios. También puede encontrar un [contacto de Citrix](http://citrix.com/English/contact/index.asp) con quien podrá debatir sus alternativas.

## <a name="fully-managed-paassaas-offerings"></a>Ofertas completamente administradas (PaaS y SaaS)
### <a name="citrix-cloud"></a>**Citrix Cloud**
La arquitectura de la [solución en la nube actual de Citrix](https://www.citrix.com/products/citrix-cloud/) es idéntica a la de Citrix XenApp Express. Citrix está ofreciendo una [promoción de un&50; % de descuento](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) a clientes actuales de Azure RemoteApp. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (en fase de versión preliminar técnica)**
[Regístrese para obtener la versión preliminar técnica](http://now.citrix.com/remoteapp) y ver la [sesión de Ignite](https://myignite.microsoft.com/videos/2792) (empieza en el minuto 20:30). La arquitectura de XenApp Express es idéntica a la de Citrix Cloud, salvo que incluye una interfaz de usuario de administración simplificada, y otras características y funcionalidades que son similares a Azure RemoteApp. 

Obtenga más información sobre [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programa de proveedor de servicios de Citrix**
Gracias al programa de proveedor de servicios de Citrix, los proveedores de servicios pueden brindar a las pymes informática en la nube virtual de forma fácil, de modo que pueden ofrecer los servicios que estos quieran con un modelo de pago por uso sencillo. Los proveedores de servicios de Citrix aumentan su volumen de negocios de SPLA de Microsoft y amplían sus inversiones en plataformas RDS con acceso desde cualquier dispositivo y ubicación, la compatibilidad con aplicaciones más extensa del mercado, una experiencia de usuario fructífera, mayor seguridad y más escalabilidad. A su vez, proveedores de servicios de Citrix atraen a más suscriptores, aumentan la satisfacción del cliente y reducen los costos operativos. [Obtenga más información](http://www.citrix.com/products/service-providers.html) o [busque un asociado](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Proveedor de servicios hospedados de Microsoft**
Los asociados de hospedaje suelen ofrecen un servicio hospedado de aplicaciones y escritorio de Windows totalmente administrado, que puede incluir la administración de recursos de Azure, sistemas operativos, aplicaciones y el departamento de soporte técnico a través de los contratos de licencias de contratos del asociado con Microsoft y otros proveedores de software, además de constituir un contrato de licencia de proveedor de servicios para permitir la reventa de licencias de acceso de suscriptor (SAL). La siguiente información proporciona detalles e información de contacto de algunos de los proveedores de hospedaje que están especializados en ayudar a los clientes con su migración de Azure RemoteApp. Consulte la [lista actual de proveedores de servicios hospedados](http://aka.ms/rdsonazurecertified) que finalizaron la evaluación y la ruta de aprendizaje de RDS en IaaS.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) se especializa en los ISV que realizan el proceso de transición a la nube y buscan optimizar sus configuraciones en la nube actuales. ASPEX ofrece una amplia gama de servicios administrados, DevOps y servicios de consultoría.  

Ubicación principal: Amberes, Bélgica

Región operativa: Europa Occidental

Estado de asociado: [Silver](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Proveedor de servicios en la nube de Microsoft: sí

Oferta de soluciones de RemoteApp y escritorio basadas en sesiones: Sí, ambas

Soluciones de migración de Azure RemoteApp: sí [(más información)](https://www.aspex.be/en/azure-remote-apps)

**Contacto:**

* Teléfono: +3232202198
* Correo electrónico: [info@aspex.be](mailto:info@aspex.be)
* Sitio web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nombre de la plataforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) es una plataforma de automatización que permite a las empresas de TI simplificar, optimizar, y escalar la migración y la entrega de aplicaciones y escritorios remotos, además de la infraestructura en la nube de Microsoft Azure. 

La plataforma MyCloudIT reduce el tiempo de implementación al 95 %, los costos de Azure al 30 % y mueve toda la infraestructura de TI del cliente a la nube en muy poco tiempo. Ahora, los asociados pueden administrar, desde un panel global, clientes y usuarios finales de servicios de todo el mundo como nunca antes, además de aumentar los ingresos, sin tener que agregar más sobrecarga ni ampliar el aprendizaje de Azure.  

Ubicación principal: Dallas, Texas (Estados Unidos)

Región operativa: en todo el mundo

Estado de asociado: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Proveedor de servicios en la nube de Microsoft: sí

Oferta de soluciones de RemoteApp y escritorio basadas en sesiones: Sí, ambas

Soluciones de migración de Azure RemoteApp: sí [(más información)](https://mycloudit.com/remote-app-microsoft/)

**Contacto:**

* Brian Garoutte, vicepresidente de Desarrollo Empresarial
  
   Teléfono: 972-218-0741
  
   Correo electrónico: [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) se especializa en proporcionar soluciones de escritorio hospedadas, de modo que brinda completas experiencias de aplicaciones de ISV y escritorios basadas en la tecnología de Microsoft a una base de clientes internacional a partir de Azure y sus propios centros de datos.

Ubicación principal: Londres, Reino Unido; Singapur; Houston, Texas

Región operativa: en todo el mundo

Estado de asociado: Gold

Proveedor de servicios en la nube de Microsoft: sí

Oferta de soluciones de RemoteApp y escritorio basadas en sesiones: Sí, ambas

Soluciones de migración de Azure RemoteApp: sí [(más información)](http://www.acuutech.com/ara-migration/)

**Contacto:**

* Reino Unido:
  
  5/6 York House, Langston Road,
  
  Loughton, Essex IG10 3TQ
  
  Teléfono: +44 (0) 20 8502 2155
* Singapur:
  
  100 Cecil Street, #09-02, 
  
  The Globe, Singapur 069532
  
  Teléfono: +65 6709 4933
* Norteamérica: 
  
  3601 S. Sandman St.
  
  Suite 200, Houston, TX 77098
  
  Teléfono: +1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) ofrece las soluciones en la nube públicas y privadas (Azure) de la cartera completa de Microsoft Dynamics (NAV, AX, GP, SL y CRM).

Ubicación principal: Países bajos

Región operativa: en todo el mundo

Estado de asociado: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)

Proveedor de servicios en la nube de Microsoft: sí

Oferta de soluciones de RemoteApp y escritorio basadas en sesiones: Sí, ambas

**Contacto:**

- EMEA:

   Prins Mauritslaan 29-35

   71 LP Badhoevedorp

   Países Bajos

   Teléfono: + 31 20 547 8060 

- América:

   171 Saxony Road, Suite 105

   Encinitas, CA 92024

   San Diego

   Estados Unidos

   Teléfono: +1 858 385 8900 

- APAC:

   105 Cecil Street
   
   \#11-08, The Octagon

   Singapur 069534

   Singapur
   
   Teléfono (Singapur): + 65 6222 6591

   Teléfono (Australia): + 61 2 8310 5568 
   
   Teléfono (Nueva Zelanda): + 64 4 488 0321

## <a name="need-more-help"></a>¿Necesita más ayuda?
¿Todavía necesita ayuda para decidirse o tiene más preguntas? Use cualquiera de los métodos siguientes para obtener ayuda. 

1. Envíenos un correo electrónico a [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Póngase en contacto con el equipo de [soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comience abriendo un [caso de soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3. Llámenos. [Encuentre un número de ventas local](https://azure.microsoft.com/overview/sales-number/).




<!--HONumber=Dec16_HO2-->


