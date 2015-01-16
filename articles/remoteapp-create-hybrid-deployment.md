<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="Creación de una implementación híbrida de RemoteApp" description="Obtenga información acerca de cómo crear una implementación de RemoteApp que se conecte a su red interna." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#Creación de una implementación híbrida de RemoteApp

Hay dos tipos de implementación de RemoteApp: 

- Nube: reside completamente en Azure y se crea usando la opción **Creación rápida** del Portal de administración de Azure.  
- Híbrida: incluye una red virtual para acceso local y se crea usando la opción **Crear con VPN** del Portal de administración.

En este tutorial se realizará un recorrido por el proceso de creación de una implementación híbrida. Son siete pasos: 

1.	Crear una [imagen de plantilla personalizada de RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/).
2.	Crear un servicio RemoteApp.
2.	Vincular una red virtual.
3.	Vincular una imagen de plantilla.
4.	Configurar la sincronización de directorios. RemoteApp requiere que realice la integración con Azure Active Directory de dos maneras: 1) bien puede configurar DirSync con la opción de sincronización de contraseñas o (2) puede configurar DirSync sin la opción de sincronización de contraseñas pero usando un dominio que esté federado a AD FS.
5.	Publicar programas RemoteApp.
6.	Configurar el acceso del usuario.

**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear el servicio:

- Suscríbase a la vista previa de RemoteApp. Puede hacerlo en la dirección [http://azure.microsoft.com/es-es/services/remoteapp/](http://azure.microsoft.com/es-es/services/remoteapp/).
- Cree una cuenta de usuario en Active Directory para usar la cuenta de servicio RemoteApp. Restrinja los permisos para esta cuenta de forma que solamente pueda unir máquinas al dominio.
- Recopile información sobre la red local: información de direcciones IP y detalles de dispositivos VPN.
- Instale el módulo [Azure PowerShell](http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/).
- Recopile información sobre los usuarios y grupos a los que desee conceder acceso. Esta información puede ser información de cuenta de Microsoft o información de cuenta de trabajo de Active Directory de usuarios o grupos.
- Cree su imagen de plantilla. Una imagen de plantilla de RemoteApp contiene las aplicaciones y los programas que desea publicar para los usuarios. Consulte [Creación de una imagen de plantilla personalizada para RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/) para conocer los pasos detallados. 






## **Paso 1: Creación de un servicio RemoteApp** ##



1. En el [Portal de administración de Azure](http://manage.windowsazure.com), vaya a la página RemoteApp.
2. Haga clic en **Nuevo > Crear con VPN**.
3. Escriba un nombre para el servicio y haga clic en **Crear servicio de RemoteApp**.

Después de crear el servicio RemoteApp, vaya a la página **Inicio rápido** de RemoteApp para continuar con los pasos de configuración.

## **Paso 2: Vinculación a una red virtual** ##

Una red virtual permite a los usuarios acceder a los datos de la red local a través de recursos remotos de RemoteApp. Debe llevar a cabo cuatro pasos para configurar el vínculo de red virtual:

1. En la página Inicio rápido, haga clic en **Vincular una red virtual RemoteApp**. 
2. Elija si desea crear una nueva red virtual o usar una existente. Para este tutorial, crearemos una red nueva.
3. Proporcione la siguiente información para la nueva red:  
      - Nombre
      - Espacio de dirección de red virtual
      - Espacio de dirección local
      - Dirección IP del servidor DNS
      - Dirección IP VPN

	Consulte [Configurar una VPN de sitio a sitio en el Portal de administración](http://msdn.microsoft.com/library/azure/dn133795.aspx) para obtener más información.

4. A continuación, vuelva a la página Inicio rápido, haga clic en **get script** para descargar un script para configurar el dispositivo VPN para conectarse a la red virtual que acaba de crear. Necesitará la siguiente información acerca del dispositivo VPN: 
	- Proveedor
	- Plataforma
	- Sistema operativos

	Guarde el script y ejecútelo en el dispositivo VPN. 

	**Nota:** después de ejecutar este script, la red virtual pasará al estado de preparada, lo que puede tardar entre 5 y 7 minutos. Hasta que la red esté preparada, no podrá usarla.

5. Por último, de nuevo en la página Inicio rápido, haga clic en **unirse al dominio local**. Agregue la cuenta de servicio RemoteApp al dominio de Active Directory local. Necesitará el nombre de dominio, la unidad organizativa, el nombre del usuario de la cuenta de servicio y la contraseña.


## **Paso 3: Vinculación a una imagen de plantilla de RemoteApp** ##

Una imagen de plantilla de RemoteApp contiene los programas que desea compartir con los usuarios. Puede cargar la nueva imagen de plantilla que ha creado (mediante las instrucciones que se indican en [Creación de una imagen de plantilla personalizada para RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/)) o crear un vínculo a una imagen existente (una ya cargada en Azure).

Si está cargando la nueva imagen, puede que necesite escribir el nombre y elegir la ubicación para dicha imagen. En la página siguiente del asistente, verá un conjunto de cmdlets de PowerShell. Copie y ejecute estos cmdlets desde un símbolo de Windows PowerShell con privilegios elevados para cargar la imagen especificada.

Si vincula una imagen de plantilla existente, simplemente especifique el nombre, la ubicación y la suscripción de Azure asociada de la imagen.



## **Paso 4: Configuración de la sincronización de directorios de Active Directory** ##

RemoteApp requiere que realice la integración con Azure Active Directory de dos maneras: 1) bien puede configurar DirSync con la opción de sincronización de contraseñas o (2) puede configurar DirSync sin la opción de sincronización de contraseñas pero usando un dominio que esté federado a AD FS. Consulte [Guía de sincronización de directorios](http://msdn.microsoft.com//library/azure/hh967642.aspx) para obtener información sobre planeación y pasos detallados.

## **Paso 5: Publicación de programas RemoteApp** ##

Un programa RemoteApp es la aplicación o el programa que proporciona a los usuarios. Se encuentra en la imagen de plantilla que cargó para el servicio. Cuando un usuario accede a un programa RemoteApp, da la sensación de que este se ejecuta en el entorno local, pero realmente se está ejecutando en Azure. 

Para que los usuarios puedan obtener acceso a programas RemoteApp, debe publicarlos en la fuente de usuarios finales (lista de programas disponibles a los que los usuarios acceden desde el portal de Azure).
 
Puede publicar varios programas en su servicio RemoteApp. Desde la página de programas de RemoteApp, haga clic en **Publicar** para agregar un programa. Puede publicar desde el menú Inicio de la imagen de plantilla o especificando la ruta de acceso en la imagen de plantilla para el programa. Si opta por agregar desde el menú Inicio, elija el programa para publicar. Si opta por proporcionar la ruta de acceso al programa, proporcione un nombre para el programa y la ruta de acceso en la que se instaló el programa en la imagen de la plantilla.

## **Paso 6: Configuración del acceso de usuarios** ##

Ahora que ha creado el servicio RemoteApp, necesita agregar los usuarios y grupos que desea que puedan usar los recursos remotos. Los usuarios y grupos a los que proporciona acceso necesitan existir en el inquilino de Active Directory asociado a la suscripción usada para crear este servicio RemoteApp.

1.	Desde la página Inicio rápido, haga clic en **Configurar acceso de usuario**. 
2.	Escriba la cuenta de trabajo o nombre de grupo (desde Active Directory) o la cuenta de Microsoft para la que desea conceder acceso.

	Para los usuarios, asegúrese de que usa el formato "usuario@dominio.com". Para los grupos, escriba el nombre de grupo.

3.	Una vez que los usuarios o grupos se validen, haga clic en **Guardar**.


## Pasos siguientes ##
Esto es todo; ha creado he implementado correctamente su implementación híbrida de RemoteApp. El paso siguiente es que los usuarios descarguen e instalen el cliente Escritorio remoto. Puede encontrar la dirección URL del cliente en la página Inicio rápido de RemoteApp. Después, indique a los usuarios que inicien sesión en el cliente y accedan a los programas de RemoteApp publicados.


