<properties 
	pageTitle="Cómo crear una colección en la nube de RemoteApp" 
	description="Aprenda a crear una implementación de RemoteApp que guarda datos en la nube de Azure." 
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
	ms.date="05/26/2015" 
	ms.author="elizapo"/>

# Creación de una colección en la nube de Azure RemoteApp

Hay dos tipos de colecciones de Azure RemoteApp:

- Nube: reside completamente en Azure y se crea mediante la opción **Creación rápida** del Portal de administración de Azure.  
- Híbrida: incluye una red virtual para acceso local y se crea con la opción **Crear con VPN** del Portal de administración.

Este tutorial le guía en el proceso de creación de una colección en la nube. Hay cuatro pasos:

1.	Crear una colección de RemoteApp.
2.	Opcionalmente, puede configurar sincronización de directorios. RemoteApp lo requiere para sincronizar usuarios, contactos y contraseñas de su Active Directory local a su inquilino de Azure Active Directory.
5.	Publicar aplicaciones de RemoteApp.
6.	Configurar el acceso del usuario.

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear la colección:

- [Suscríbase](http://azure.microsoft.com/services/remoteapp/) a RemoteApp. 
- Recopile información sobre los usuarios a los que quiera conceder acceso. Esta información puede ser información de cuentas de Microsoft o información de cuentas de trabajo de Active Directory de usuarios.
- En este procedimiento se supone que va a usar una de las imágenes de plantilla que se proporcionan con la suscripción o que ya ha cargado la imagen de plantilla que quiere usar. Si tiene que cargar una imagen de plantilla diferente, puede hacerlo desde la página Imágenes de plantilla. Haga clic en **cargar una imagen de plantilla** y siga los pasos del asistente. 
- ¿Desea proporcionar aplicaciones personalizadas o programas LOB? Cree una nueva [imagen](remoteapp-imageoptions.md) y úsela en su colección en la nube.

## Paso 1: Creación de una colección de RemoteApp ##



1. En el Portal de administración, vaya a la página RemoteApp.
2. Haga clic en **Nuevo > Creación rápida**.
3. Escriba un nombre para la colección y seleccione su región.
4. Seleccione el plan que quiere usar: Standard o Basic.
5. Elija la plantilla que se usará para esta colección. 

	**Sugerencia:** la suscripción a RemoteApp incluye [imágenes de plantilla](remoteapp-images.md) incluidas en programas de Office 365 o de Office 2013 (con fines de prueba), algunos publicados (como Word) y otros listos para su publicación. También puede crear una [imagen](remoteapp-imageoptions.md) y usarla en su colección en la nube.


1. Haga clic en **Crear colección de RemoteApp**.
	
	**Importante:** puede tardarse hasta 30 minutos en aprovisionar la colección.

Después de crear la colección de RemoteApp, vaya a la página **Inicio rápido** de RemoteApp para continuar con los pasos de configuración.


## Paso 2: Configuración de la sincronización de directorios de Active Directory (opcional) ##

Si quiere usar Active Directory, RemoteApp requiere sincronización de directorios entre Azure Active Directory y su implementación local de Active Directory para sincronizar usuarios, contactos y contraseñas con su inquilino de Azure Active Directory. Consulte [Configuración de Active Directory para RemoteApp de Azure](remoteapp-ad.md) para obtener información sobre planeación.

## Paso 3. Publicación de aplicaciones de RemoteApp ##

Una aplicación de RemoteApp es la aplicación o el programa que proporciona a los usuarios. Se encuentra en la imagen de plantilla que cargó para la colección. Cuando un usuario accede a una aplicación de RemoteApp, da la sensación de que esta se ejecuta en el entorno local, pero realmente se está ejecutando en Azure.

Antes de que los usuarios puedan acceder a aplicaciones, es necesario publicarlos en la fuente del usuario final: una lista de aplicaciones disponibles a los que los usuarios acceden a través del cliente de Escritorio remoto.
 
Puede publicar varias aplicaciones en su colección de RemoteApp. En la página de publicación de RemoteApp, haga clic en **Publicar** para agregar un programa. Puede publicar desde el menú Inicio de la imagen de plantilla o especificando la ruta de acceso en la imagen de plantilla para la aplicación. Si opta por agregar desde el menú Inicio, elija la aplicación para publicar. Si opta por proporcionar la ruta de acceso a la aplicación, proporcione un nombre para la aplicación y la ruta de acceso en la que se instaló en la imagen de la plantilla.

## Paso 4: Configuración del acceso de usuarios ##

Ahora que ha creado la colección de RemoteApp, necesita agregar los usuarios que quiere que puedan usar los recursos remotos. Si usa Active Directory, los usuarios a los que proporcione acceso deben existir en el inquilino de Active Directory asociado con la suscripción que usó para crear esta colección de RemoteApp.

1.	En la página Inicio rápido, haga clic en **Configurar acceso de usuario**. 
2.	Escriba la cuenta de trabajo (desde Active Directory) o la cuenta de Microsoft para la que quiere conceder acceso.

	**Notas:**

	Asegúrese de que usa el formato "usuario@dominio.com".

	Si usa Office 365 ProPlus en su colección, debe usar las identidades de Active Directory para los usuarios. Esto ayuda a validar las licencias.

3.	Cuando se validen los usuarios, haga clic en **Guardar**.


## Pasos siguientes ##

Eso es todo, ha creado e implementado correctamente su colección en la nube de RemoteApp. El paso siguiente es que los usuarios descarguen e instalen el cliente Escritorio remoto. Puede encontrar la dirección URL del cliente en la página Inicio rápido de RemoteApp. Después, indique a los usuarios que inicien sesión en el cliente y accedan a las aplicaciones publicadas.

 

<!---HONumber=July15_HO2-->