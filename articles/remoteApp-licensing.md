<properties title="Azure RemoteApp licensing" pageTitle="Concesión de licencias de RemoteApp de Azure" description="Obtenga información sobre la obtención de licencias de Azure RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#¿Cómo funciona la concesión de licencias de RemoteApp de Azure?


Ha configurado el servicio de RemoteApp de Azure, creado las plantillas y está listo para publicar aplicaciones para los usuarios. Pero hay una última cosa que debe averiguar: cómo funciona la concesión de licencias. ¿Cómo funciona la concesión de licencias para las aplicaciones que se comparten a través de RemoteApp?

Puede ofrecer cualquier programa o aplicación para el que disponga de una licencia. 

Como parte de su suscripción de RemoteApp de Azure, no necesitará comprar licencias de Windows o CAL de escritorio remoto, ya que estas se incluyen en el coste de la suscripción.

Si quiere usar la imagen de plantilla de Office 365 que viene con RemoteApp de Azure, debe tener un plan de Office 365 ProPlus *existente*. Lo mismo ocurre con cualquier aplicación de Office 365 que se publique mediante una plantilla personalizada. Debe activar las aplicaciones con su propia suscripción. Lo mismo ocurre con las suscripciones de prueba y pagadas. Si quiere usar la imagen de plantilla de Office 365 durante el período de prueba, *y ya no tiene una suscripción*, vaya a la página de Office 365 para [suscribirse](https://go.microsoft.com/fwlink/p/?LinkID=403802) y obtener una suscripción de prueba. Consulte [¿Cómo funcionan conjuntamente RemoteApp y Office?](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-o365/) para obtener más información.

Si, durante el período de prueba, no quiere obtener una suscripción de prueba de Office 365, use la imagen de plantilla de Office 2013 Professional Plus que viene con RemoteApp. Esta imagen de plantilla solo puede usarse durante 30 días y no se puede convertir en una colección de pago. Después de que finalice el período de prueba, deberá eliminar la colección y crear una nueva si quiere usar Office 2013 más adelante.  

Esto tiene sentido, ¿no? Puede publicar cualquier aplicación que pueda compartir legalmente. Además, deberá asegurarse de que realmente tiene derecho a compartir sus programas.

Tenga en cuenta que no puede usar un contrato de licencia por volumen ni una licencia CAL en una colección en la nube. *Puede* usar un contrato de licencia por volumen para activar aplicaciones en su colección híbrida (excepto para Office). Necesitará instalarlas en la imagen de plantilla desde los soportes de licencias por volumen. Siga la información del proveedor de la aplicación para instalar licencias en un entorno de Escritorio remoto.

<!--HONumber=35.2-->
