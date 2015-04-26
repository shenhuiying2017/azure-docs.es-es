<properties title="Using Office365 with Azure RemoteApp" pageTitle="Uso de Office 365 con Azure RemoteApp" description="Obtenga información sobre cómo funcionan conjuntamente Office 365 y Azure RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Uso de Office 365 con Azure RemoteApp

Tiene dos opciones para hospedar aplicaciones de Office en RemoteApp: Office 365 ProPlus u Office 2013 Professional Plus Trial.

##Office 365 ProPlus 
Puede crear una colección de RemoteApp mediante la imagen de plantilla de Office 365 ProPlus. Esta opción le permite ampliar el servicio de Office 365 a RemoteApp. Debe disponer de un plan de suscripción y los usuarios deben tener una licencia para el servicio Office 365 ProPlus, independiente o a través de planes de servicio de Office 365. Las instancias de RemoteApp de las aplicaciones de Office se contarán como una de las cinco instancias de instalación permitidas para cada usuario y se activarán completamente. Puede administrar las licencias de Office 365 de los usuarios en el [Portal de administración de Office 365](https://portal.office365.com/). Lea más información sobre [Planes de servicios de Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

La opción de Office 365 ProPlus está disponible durante la prueba gratuita de 30 días y en modo de producción, pero es la única opción admitida después de que la versión de prueba expire.  

Tenga en cuenta que también puede crear una imagen de plantilla personalizada que contenga Office 365 ProPlus. Para construir una imagen de plantilla, siga los [pasos de implementación](http://technet.microsoft.com/es-es/library/dn782858(v=office.15).aspx) para Office 365 ProPlus en RDS.

##Office 2013 Professional Plus Trial 
Durante una prueba de 30 días de RemoteApp, puede usar la imagen de plantilla de Office 2013 Professional Plus (prueba) para crear una colección de RemoteApp. Puede asignar usuarios a esta colección de prueba mediante sus cuentas de trabajo de Azure Active Directory o cuentas de Microsoft. No se requieren suscripciones adicionales.

Esta es una opción excelente para probar y hacerse una buena idea de Office de RemoteApp. Sin embargo, esta opción está pensada solo para su evaluación y prueba. Las colecciones de RemoteApp creadas mediante la imagen de plantilla de Office 2013 Professional Plus (prueba) no se pasan al modo de producción y se deshabilitarán al final del período de prueba.

##Cambio de versión de prueba a producción
Cuando se inicia la versión de prueba gratuita de 30 días, una nota en la sección de RemoteApp del portal le indicará cuánto le queda en la versión de prueba antes de tener que realizar una transición a una cuenta pagada. Puede activar su cuenta y cambiar al modo de producción mediante el vínculo en esta nota. 

Al activar su cuenta, esto afectará a todas las colecciones de RemoteApp en su cuenta. 

- Las colecciones que se ejecutan con Windows Server 2012 R2 o las imágenes de plantilla de Office 365 ProPlus pasarán a producción sin problemas. Todos los datos de usuario y configuración, incluidas las sesiones en curso, permanecerán intactos.
- Si cargó imágenes de plantilla personalizadas, las colecciones que usen esas imágenes también pasarán sin problemas.
- La imagen de plantilla de Office 2013 Professional Plus (prueba) está pensada para evaluación únicamente. Las colecciones que se ejecutan con esta imagen de plantilla no se pueden pasar a producción. Se pondrán en estado "deshabilitado".


Si no pasa al modo de producción en el momento de expiración de la prueba, las colecciones de RemoteApp se deshabilitarán. No se preocupe, la configuración y datos de los usuarios se guardan durante otros 90 días, por lo que puede activar el servicio y cambiar al modo de producción sin perder datos.

<!--HONumber=35.2-->
