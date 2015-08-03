<properties 
	pageTitle="¿Qué es RemoteApp?" 
	description="Obtenga información acerca de RemoteApp de Azure." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="elizapo"/>

# ¿Qué es Azure RemoteApp?

Azure RemoteApp ofrece la funcionalidad del programa RemoteApp Microsoft local, respaldado por Servicios de Escritorio remoto, en Azure. Azure RemoteApp ayuda a proporcionar acceso remoto y seguro a aplicaciones desde diferentes dispositivos de usuario.

Cuando mueve RemoteApp a Azure, tiene la posibilidad de aprovechar el almacenamiento, la escalabilidad y el alcance global de Azure sin tener que preocuparse por una configuración local compleja. Microsoft proporciona mantenimiento de Azure, que garantiza su fiabilidad y le permite centrarse en problemas más importantes, como la creación de las mejores aplicaciones para su negocio. Otra ventaja de Azure RemoteApp es la accesibilidad, los usuarios pueden acceder a los programas de RemoteApp desde dispositivos Android, iOS, Mac OS X y Windows. Pueden usar las aplicaciones en el entorno que prefieran, mientras que usted usa el portal de administración de Azure para administrar esas aplicaciones.

Siga leyendo para obtener más información sobre RemoteApp o, si ya está convencido, [probarla ahora](http://azure.microsoft.com/services/remoteapp/).

¿Tiene preguntas acerca de Azure RemoteApp? Consulte nuestras [preguntas más frecuentes](remoteapp-faq.md).

RemoteApp de Azure es parte de la [Infraestructura de Escritorio virtual de Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**¡Nuevo!** ¿Desea obtener más información acerca de RemoteApp de Azure? O bien, ¿está listo para validar RemoteApp a escala? Únase a nuestro [seminario web Pregunte a los expertos](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website) semanal.

## Colecciones RemoteApp
Hay dos tipos de colecciones de RemoteApp:


- Una **colección en la nube** se hospeda en la nube de Azure y almacena allí todos los datos de los programas. Los usuarios pueden acceder a las aplicaciones mediante el inicio de sesión con sus cuentas de Microsoft o credenciales corporativas sincronizadas o federadas con Azure Active Directory.
- Una **colección híbrida** se hospeda en la nube de Azure y almacena allí los datos, pero también permite a los usuarios acceder a datos y recursos almacenados en la red local. Los usuarios pueden acceder a las aplicaciones mediante el inicio de sesión con sus credenciales corporativas sincronizadas o federadas con Azure Active Directory.

### Colección en la nube

La [colección RemoteApp en la nube](remoteapp-create-cloud-deployment.md) ofrece una forma independiente de hospedar aplicaciones en la nube. Una colección en la nube solo existe en la nube de Azure, en lugar de conectarse a la red local.

Como parte de la prueba de RemoteApp, le proporcionamos las aplicaciones de Office 365 ProPlus u Office 2013 previamente instaladas y listas para compartir con los usuarios. Si decide aprovechar el software disponible, puede aprovisionar rápidamente su servicio.

Una ventaja adicional del uso de la colección en la nube con las aplicaciones de Office es que las aplicaciones y el sistema operativo (en el que se crea el servicio) siempre se mantienen actualizados a través de actualizaciones regulares y la protección de extremo de antimalware de Microsoft proporciona una defensa continua. Los usuarios finales usan sus cuentas de Microsoft o credenciales corporativas para acceder a las aplicaciones. Usted, el administrador, solo debe preocuparse en averiguar quién debería tener acceso a qué aplicaciones.

También puede crear una colección en la nube para compartir una aplicación personalizada o un conjunto de aplicaciones para los usuarios. Para ello, tendrá que [crear una imagen personalizada](remoteapp-imageoptions.md) (que es cómo publicamos las aplicaciones en RemoteApp) y simplemente elegir esa imagen (en lugar de la imagen de Office 2013) al crear la colección.

#### Cuándo se debe elegir la nube

Elija una colección en la nube cuando la aplicación que desea compartir no requiere una conexión a ningún recurso de la red privada de su empresa (por ejemplo, a través de un dispositivo VPN). Si la aplicación usa recursos en Internet, OneDrive o Azure, una colección en la nube funcionará en su caso. También es la más rápida crear.


### Colección híbrida
La [colección híbrida de RemoteApp](remoteapp-create-hybrid-deployment.md) permite proporcionar un conjunto personalizado de aplicaciones a los usuarios y acceso a los datos y recursos en la red local. A diferencia de una imagen personalizada que se usa con la colección en la nube, la imagen que se crea para una colección híbrida ejecuta aplicaciones en un entorno unido a dominios, lo que concede acceso completo a la red local y a los datos.

Mediante la integración de Active Directory con Azure Active Directory (mediante DirSync), los usuarios pueden usar sus credenciales corporativas para acceder a aplicaciones y datos. Cuando se usa una cuenta de trabajo en Active Directory, puede llevar las directivas corporativas a la nube para controlar las aplicaciones que se ofrecen a través de RemoteApp.

Cuando genera la imagen de plantilla en Windows Server 2012 R2 con el Servicio de rol Host de sesión de Escritorio remoto, hay algunos límites en las aplicaciones que se pueden publicar para los usuarios. Si las aplicaciones funcionan correctamente en ese entorno de imagen de plantilla, los usuarios finales pueden acceder a ellas a través de RemoteApp.

#### Cuándo se debe elegir la híbrida

Elija una colección híbrida si necesita una conexión a recursos de la red privada de su empresa. Por ejemplo, si la aplicación necesita acceso a uno de los siguientes elementos:

- Servidores de archivos ubicados en la intranet
- Quicken
- Bases de datos detrás de un firewall

Esto generalmente resulta más útil en el caso de grandes organizaciones con muchos recursos en sus redes privadas que no se pueden mover a la nube.

### Actualización de la colección
Una de las diferencias clave entre las colecciones híbridas y en la nube es cómo se administran las actualizaciones de software. Con una colección en la nube que usa la imagen de Office 365 ProPlus u Office 2013 preinstalada, no tiene que preocuparse por las actualizaciones. El servicio se mantiene a sí mismo e implementa las actualizaciones de manera continua para aplicaciones y el sistema operativo.

Para colecciones híbridas, así como para las colecciones en la nube que usan una imagen de plantilla personalizada, usted es responsable de mantener la imagen y las aplicaciones. Para imágenes unidas a dominio, puede controlar las actualizaciones mediante herramientas como Windows Update, directiva de grupo o System Center.

Después de actualizar la imagen de plantilla personalizada, cargue la nueva imagen en la nube de Azure y, luego, actualice la colección para usar la nueva imagen. (Puede hacerlo desde la página **Inicio rápido** de RemoteApp o el panel).

Vea [Actualización de la colección](remoteapp-update.md) para obtener más información.

## Clientes compatibles con RemoteApp
Azure RemoteApp es compatible con las aplicaciones de cliente de RemoteApp para Windows y Windows RT, así como con las aplicaciones de Escritorio remoto de Microsoft para Mac, iOS y Android. Los usuarios pueden usar estas aplicaciones en sus dispositivos portátiles o móviles para acceder a los nuevos programas de RemoteApp.

Vea [Acceso a las aplicaciones de Azure RemoteApp](remoteapp-clients.md) para obtener más información sobre los clientes.

## Pasos siguientes
Venga, pruébelo. Estos artículos le ayudarán a comenzar a usar RemoteApp:

- [Creación de una imagen de RemoteApp](remoteapp-imageoptions.md)
- [Creación de una colección en la nube de RemoteApp](remoteapp-create-cloud-deployment.md)
- [Creación de una colección híbrida de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [¿Cómo funciona la concesión de licencias en RemoteApp?](remoteapp-licensing.md)
- [Prácticas recomendadas para usar RemoteApp de Azure](remoteapp-bestpractices.md)
- [Preguntas más frecuentes sobre RemoteApp de Azure](remoteapp-faq.md)
 

<!---HONumber=July15_HO4-->