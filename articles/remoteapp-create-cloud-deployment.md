<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Creación de una implementación de RemoteApp en la nube

Hay dos tipos de implementación de RemoteApp:

-   Nube: reside completamente en Azure y se crea con la opción **Creación rápida** en el Portal de administración de Azure.
-   Híbrida: incluye una red virtual para acceso local y se crea usando la opción **Crear con VPN** del Portal de administración.

Este tutorial le guía en el proceso de creación de una implementación en la nube. Hay cuatro pasos:

1.  Crear un servicio RemoteApp.
2.  Opcionalmente, puede configurar sincronización de directorios. RemoteApp lo requiere para sincronizar los usuarios, grupos, contactos y contraseñas de su Active Directory local con su inquilino de Azure Active Directory.
3.  Publicar programas RemoteApp.
4.  Configurar el acceso del usuario.

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear el servicio:

-   Suscríbase a la vista previa de RemoteApp. Puede hacerlo en la dirección [][]<http://azure.microsoft.com/en-us/services/remoteapp/></a>.
-   Recopile información sobre los usuarios y grupos a los que desee conceder acceso. Esta información puede ser información de cuenta de Microsoft o información de cuenta de organización de Active Directory de usuarios o grupos.
-   En este procedimiento se supone que va a usar la imagen de plantilla que se proporciona con la suscripción o que ya ha cargado la imagen de plantilla que desea usar. Si tiene que cargar una imagen de plantilla diferente, puede hacerlo desde la página Imágenes de plantilla. Haga clic en **Cargar imagen de plantilla** y siga los pasos del asistente.

## **Paso 1: Creación de un servicio RemoteApp**

1.  En el [Portal de administración de Windows Azure][], vaya a la página RemoteApp.
2.  Haga clic en **Nuevo \> Creación rápida**.

3.  Escriba un nombre para el servicio y seleccione su región.
4.  Elija la suscripción que desea usar para crear este servicio.
5.  Elija la plantilla que va a usar para este servicio.

    **Sugerencia:** La suscripción a RemoteApp incluye una imagen de plantilla que contiene programas de Office 2013, algunos publicados (como Word) y otros listos para publicarlos.

6.  Haga clic en **Crear un servicio RemoteApp**.

    **Importante:** El aprovisionamiento del servicio puede tardar hasta media hora.

Después de crear el servicio RemoteApp, vaya a la página **Inicio rápido** de RemoteApp para continuar con los pasos de configuración.

## **Paso 2: Configuración de sincronización de directorios de Active Directory (opcional)**

Si desea utilizar Active Directory, RemoteApp requiere sincronización de directorios entre Azure Active Directory y su implementación local de Active Directory para sincronizar usuarios, grupos, contactos y contraseñas con su inquilino de Azure Active Directory. Consulte [Guía de sincronización de directorios][] para obtener información sobre planeación y pasos detallados.

## **Paso 3: Publicación de programas RemoteApp**

Un programa RemoteApp es la aplicación o el programa que proporciona a los usuarios. Se encuentra en la imagen de plantilla que cargó para el servicio. Cuando un usuario accede a un programa RemoteApp, da la sensación de que este se ejecuta en el entorno local, pero realmente se está ejecutando en Azure.

Para que los usuarios puedan obtener acceso a programas RemoteApp, debe publicarlos en la fuente de usuarios finales (lista de programas disponibles a los que los usuarios acceden desde el portal de Azure).

Puede publicar varios programas en su servicio RemoteApp. Desde la página de programas de RemoteApp, haga clic en **Publicar** para agregar un programa. Puede publicar desde el menú Inicio de la imagen de plantilla o especificando la ruta de acceso en la imagen de plantilla para el programa. Si opta por agregar desde el menú Inicio, elija el programa para publicar. Si opta por proporcionar la ruta de acceso al programa, proporcione un nombre para el programa y la ruta de acceso en la que se instaló el programa en la imagen de la plantilla.

## **Paso 4: Configuración del acceso de usuarios**

Ahora que ha creado el servicio RemoteApp, necesita agregar los usuarios y grupos que desea que puedan usar los recursos remotos. Si usa Active Directory, los usuarios o grupos a los que proporcione acceso deben existir en el inquilino de Active Directory asociado con la suscripción que usó para crear este servicio RemoteApp.

1.  Desde la página Inicio rápido, haga clic en **Configurar acceso de usuario**.
2.  Escriba la cuenta organizativa o nombre de grupo (desde Active Directory) o la cuenta de Microsoft para la que desea conceder acceso.

    Para los usuarios, asegúrese de que usa el formato “[usuario@dominio.com][]”. Para los grupos, escriba el nombre de grupo.

3.  Una vez que los usuarios o grupos se validen, haga clic en **Guardar**.

## Pasos siguientes

Eso es todo, ha creado e implementado correctamente su implementación en la nube de RemoteApp. El paso siguiente es que los usuarios descarguen e instalen el cliente Escritorio remoto. Puede encontrar la dirección URL del cliente en la página Inicio rápido de RemoteApp. Después, indique a los usuarios que inicien sesión en Azure y accedan a los programas de RemoteApp publicados.

  []: http://azure.microsoft.com/en-us/services/remoteapp/
  [Portal de administración de Windows Azure]: http://manage.windowsazure.com
  [Guía de sincronización de directorios]: http://msdn.microsoft.com/en-us/library/azure/hh967642.aspx
  [usuario@dominio.com]: mailto:user@domain.com
