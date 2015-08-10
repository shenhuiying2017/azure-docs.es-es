<properties
   pageTitle="Creación de una nueva entidad de servicio de Azure mediante el portal de Azure"
   description="Describe cómo crear una nueva entidad de servicio de Azure que puede utilizarse con el control de acceso basado en rol en el Administrador de recursos de Azure para administrar el acceso a los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Creación de una nueva entidad de servicio de Azure mediante el portal de Azure

## Información general
Una entidad de servicio es un proceso, aplicación o servicio automatizado que necesita tener acceso a otros recursos. Mediante el Administrador de recursos de Azure, puede conceder acceso a una entidad de servicio y autenticarla para que pueda realizar las acciones de administración permitidas sobre los recursos que existen en la suscripción o como inquilino.

En este tema se muestra cómo crear a una nueva entidad de servicio mediante el portal de Azure. Actualmente, debe usar el portal de Microsoft Azure para crear una nueva entidad de servicio. Esta capacidad se agregará al portal de vista previa de Azure en una versión posterior.

## Conceptos
1. Azure Active Directory (AAD): un servicio de administración de identidades y acceso creado para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](./active-directory-whatis/)
2. Entidad de servicio: una instancia de una aplicación en un directorio.
3. Aplicación de AD: un registro de directorio en AAD que identifica una aplicación en AAD. Para obtener más información, consulte [Conceptos básicos sobre autenticación en Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).


## Creación de aplicación de Active Directory
1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

   ![seleccionar Active Directory][1]

3. Seleccione el directorio que desea utilizar para crear la nueva aplicación.

   ![elegir directorio][2]

3. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.

   ![ver aplicaciones][11]

4. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.

   ![agregar aplicación][6]

   O bien, haga clic en **Agregar** en el panel inferior.

   ![agregar][12]

5. Seleccione el tipo de aplicación que desea crear. Para este tutorial, no usaremos una aplicación desde la galería.

   ![nueva aplicación][10]

6. Rellene el nombre de la aplicación y seleccione el tipo de aplicación que desea utilizar. Puesto que se va a utilizar la entidad de servicio de esta aplicación para autenticar con el Administrador de recursos de Azure, se elegirá crear una **APLICACIÓN WEB Y/O API WEB** y haremos clic en el botón siguiente.

   ![aplicación de nombre][9]

7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación. No se valida la singularidad o la existencia del extremo. Haga clic en **Completo** para crear la aplicación de AAD.

   ![propiedades de la aplicación][4]

## Creación de la contraseña de la entidad de servicio
El portal ahora debería tener la aplicación seleccionada.

1. Haga clic en la pestaña **Configurar** para configurar la contraseña de su aplicación.

   ![configurar aplicación][3]

2. Desplácese hacia abajo hasta la sección **Claves** y seleccione cuánto tiempo desea que la contraseña sea válida.

   ![claves][7]

3. Seleccione **Guardar** para crear la clave.

   ![guardar][13]

   Se muestra la clave guardada, y tiene la posibilidad de copiarla.

   ![clave guardada][8]

4. Ahora puede usar su clave para autenticarse como una entidad de servicio. Necesitará su **ID. DE CLIENTE** además su **CLAVE** para iniciar sesión. Vaya al **ID. DE CLIENTE** y cópielo.
  
   ![id. de cliente][5]


La aplicación está ahora lista y la entidad de servicio creada en el inquilino. Al iniciar sesión como una entidad de servicio asegúrese de usar lo siguiente:

* **ID. DE CLIENTE**: como nombre de usuario.
* **CLAVE**: como contraseña.

## Pasos siguientes

- Para obtener información sobre cómo especificar directivas de seguridad, consulte [Administración y auditoría del acceso a los recursos](azure-portal/resource-group-rbac.md).  
- Para ver los pasos que permiten que una entidad de servicio tenga acceso a los recursos, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](./resource-group-authenticate-service-principal.md).  
- Para obtener información general sobre el control de acceso basado en rol, consulte [Control de acceso basado en roles en el Portal de Microsoft Azure](role-based-access-control-configure.md).
- Para obtener instrucciones sobre cómo implementar la seguridad con el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=July15_HO5-->