<properties title="What is RemoteApp?" pageTitle="¿Qué es RemoteApp?" description="Obtenga información acerca de RemoteApp de Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#¿Qué es Azure RemoteApp?

RemoteApp de Azure lleva la funcionalidad del programa RemoteApp de Microsoft local, respaldado por los servicios de Escritorio remoto, a Azure. RemoteApp de Azure permite proporcionar acceso remoto seguro a las aplicaciones desde muchos dispositivos de usuario diferentes.

Al pasar RemoteApp a Azure, podrá aprovechar el almacenamiento, la escalabilidad y la búsqueda global de Azure sin tener que preocuparse por una configuración local compleja. Microsoft proporciona el mantenimiento de Azure, lo que garantiza su confiabilidad y le permite centrarse en asuntos más importantes, como la creación de las mejores aplicaciones que su empresa puede utilizar. Otra ventaja de RemoteApp de Azure es la accesibilidad, los usuarios pueden tener acceso a los programas de RemoteApp de dispositivos Windows, iOS, Mac OS X y Android. Así, pueden usar las aplicaciones en el entorno que prefieran y usar el Portal de administración de Azure para administrarlas. 

Siga leyendo para obtener más información sobre RemoteApp, o, si ya está convencido, [pruébela ahora](http://azure.microsoft.com/es-es/services/remoteapp/).

RemoteApp de Azure es parte de la [infraestructura de escritorio virtual de Microsoft](http://www.microsoft.com/es-es/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**¡Nuevo!**¿Desea obtener más información acerca de RemoteApp de Azure? O bien, ¿está listo para validar RemoteApp a escala? Únase a nuestro [seminario web Pregunte a los expertos](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Opciones de implementación de RemoteApp
Hay dos tipos de implementación de RemoteApp:


- Una **implementación en la nube** en la nube se hospeda y almacena todos los datos de programas en la nube de Azure. Los usuarios pueden acceder a las aplicaciones iniciando sesión con su cuenta de Microsoft o con sus credenciales corporativas sincronizadas o federadas con Azure Active Directory.
- Una **implementación híbrida** se hospeda en la nube de Azure y ahí almacena los datos pero también permite a los usuarios acceder a los datos y a los recursos almacenados en la red local. Los usuarios pueden acceder a las aplicaciones iniciando sesión con sus credenciales corporativas sincronizadas o federadas con Azure Active Directory.

###Implementación en la nube

La implementación de [RemoteApp en la nube](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-cloud-deployment/) ofrece una forma independiente de hospedar las aplicaciones en la nube. Una implementación en la nube solo existe en la nube de Azure, en lugar de conectarse a la red local.

Como parte de la vista previa de RemoteApp, le proporcionamos las aplicaciones de Office 2013 previamente instaladas y listas para compartir con los usuarios. Si decide aprovechar el software disponible, puede aprovisionar rápidamente su servicio.

Una ventaja adicional del uso de la implementación en la nube con las aplicaciones de Office 2013 es que las aplicaciones y el sistema operativo (en el que se crea el servicio) siempre se mantienen actualizados a través de actualizaciones regulares, y la protección integral antimalware de Microsoft proporciona una defensa continua. Los usuarios finales utilizan sus cuentas de Microsoft o las credenciales corporativas para tener acceso a las aplicaciones. El administrador solo debe preocuparse por comprender quién debe tener acceso a qué aplicaciones.

También puede crear una implementación en la nube para compartir una aplicación personalizada o un conjunto de aplicaciones para los usuarios. Para ello, debe [crear una imagen de plantilla personalizada](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/) (que es el modo en que publicamos las aplicaciones en RemoteApp) y elegir simplemente esa imagen (en lugar de la imagen de Office 2013) cuando cree su implementación. 

###Implementación híbrida
La [implementación híbrida de RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-hybrid-deployment/) le permite proporcionar un conjunto personalizado de aplicaciones a sus usuarios y el acceso a los datos y a los recursos de la red local. A diferencia de una imagen personalizada que se utiliza con la implementación en la nube, la imagen que se crea para una implementación híbrida ejecuta las aplicaciones en un entorno unido a un dominio, y otorga acceso completo a la red local y a los datos.

Mediante la integración de Active Directory con Azure Active Directory (mediante DirSync), los usuarios pueden usar sus credenciales corporativas para el acceso a las aplicaciones y los datos. Cuando se utiliza una cuenta de trabajo en Active Directory, puede llevar las directivas corporativas a la nube para controlar las aplicaciones que ofrece a través de RemoteApp.

Mientras crea su imagen de plantilla en Windows Server 2012 R2 con el servicio de función Host de sesión de Escritorio remoto, hay algunos límites en las aplicaciones que se pueden publicar para los usuarios. Si las aplicaciones funcionan correctamente en ese entorno de imagen de plantilla, los usuarios finales pueden acceder a ellas a través de RemoteApp. 

###Actualización de la implementación
Una de las diferencias clave entre la implementación híbrida y en la nube es la manera de gestionar las actualizaciones de software. Con una implementación en la nube que utiliza la imagen de Office 2013 preinstalada, no tiene que preocuparse por las actualizaciones. El servicio se mantiene solo e implementa las actualizaciones de manera continua, tanto las de las aplicaciones como las del sistema operativo.

En las implementaciones híbridas, así como en las implementaciones en la nube que usan una imagen de plantilla personalizada, usted es el responsable del mantenimiento de la imagen y de las aplicaciones. En las imágenes unidas a un dominio, puede controlar las actualizaciones mediante herramientas como Windows Update, la directiva de grupo o System Center.

Después de actualizar la imagen de plantilla personalizada, cargue la nueva imagen en la nube de Azure y, a continuación, actualice la implementación para que use la nueva imagen. (Puede hacerlo desde el Panel o la página de inicio rápido de RemoteApp.)

##Clientes compatibles con RemoteApp
Como parte de la versión de vista previa de RemoteApp de Azure, publicamos una nueva aplicación de cliente de RemoteApp de Microsoft para Windows y Windows RT, así como actualizaciones de las aplicaciones de Escritorio remoto de Microsoft para iOS y Android. Los usuarios pueden usar estas aplicaciones en sus dispositivos informáticos o móviles para tener acceso a los nuevos programas de RemoteApp.

##Pasos siguientes
¡Vamos! ¡Pruébelo! Estos artículos le ayudarán a comenzar con RemoteApp:

- [Creación de una imagen de plantilla personalizada para RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/)
- [Creación de una implementación de RemoteApp en la nube](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-cloud-deployment/)
- [Creación de una implementación híbrida de RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-hybrid-deployment/)
