---
title: "Publicación de aplicaciones mediante el proxy de aplicación de Azure AD | Microsoft Docs"
description: "Publique aplicaciones locales en la nube con el proxy de aplicación de Azure AD en Azure Portal."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5acfbfbe7327fc28fa909e6ef7e0f9b6ce8b0e54
ms.openlocfilehash: e0be8f4617c2fdbe1cb6fe2f904bf9ea7056c55e

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Publicación de aplicaciones mediante el proxy de aplicación de Azure AD - Versión preliminar pública

> [!div class="op_single_selector"]
> * [Azure Portal](application-proxy-publish-azure-portal.md)
> * [Portal de Azure clásico](active-directory-application-proxy-publish.md)

El proxy de aplicación de Azure Active Directory (AD) permite publicar aplicaciones locales para acceder a ellas a través de Internet y poder admitir trabajadores remotos. A través de Azure Portal, puede publicar las aplicaciones que se ejecutan en una red local y proporcionan acceso remoto seguro desde fuera de la red.

Este artículo le guiará por los pasos necesarios para publicar una aplicación local con el proxy de aplicación. Después de completar este artículo, estará listo para configurar la aplicación con inicio de sesión único, información personalizada o requisitos de seguridad.

Si no está familiarizado con el proxy de aplicación, obtenga más información sobre esta característica con el artículo [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicación de una aplicación local para el acceso remoto


> [!TIP]
> Si se trata de la primera vez que usa el proxy de aplicación, elija una aplicación que ya esté configurada para la autenticación basada en contraseña. El proxy de aplicación es compatible con otros tipos de autenticación, pero las aplicaciones basadas en contraseña son las que se ponen más fácilmente a pleno rendimiento de manera rápida. 

1. Inicie sesión como administrador en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales** > **Agregar**.

  ![Adición de una aplicación empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. En la página Categorías, seleccione **Aplicación local**.  

  ![Adición de su propia aplicación](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Especifique la siguiente información acerca de la aplicación:

   - **Nombre**: el nombre de la aplicación que va a aparecer en el panel de acceso. 

   - **Dirección URL interna**: la dirección que usa el conector del proxy de aplicación para acceder a la aplicación desde la red privada. Puede especificar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor no se publica. De esta forma, puede publicar sitios diferentes en el mismo servidor como aplicaciones diferentes y dar a cada uno un nombre y unas reglas de acceso propios.

     > [!TIP]
     > Si publica una ruta de acceso, asegúrese de que incluye todas las imágenes, los scripts y las hojas de estilos necesarias para la aplicación. Por ejemplo, si la aplicación está en https://yourapp/app y usa las imágenes que se encuentra en https://yourapp/media, debe publicar https://yourapp/ como la ruta de acceso.

   - **Dirección URL externa**: la dirección a la que irán los usuarios para tener acceso a la aplicación desde fuera de la red.
   - **Autenticación previa**: la forma en que el proxy de aplicación comprueba los usuarios antes de concederles acceso a la aplicación. 

     - Azure Active Directory: el proxy de la aplicación redirige a los usuarios para que inicien sesión en Azure AD, que autentica sus permisos para el directorio y la aplicación. Se recomienda mantener esta opción como valor predeterminado.
     - Acceso directo: los usuarios no tienen que autenticarse en Azure Active Directory para tener acceso a la aplicación. Esto no impide que pueda configurar los requisitos de autenticación en el back-end.
   - **¿Quiere traducir las URL de encabezados?**: elija si desea traducir la dirección URL en los encabezados o mantener el original. 
   - **Grupo de conectores**: los conectores procesan el acceso remoto a la aplicación, y los grupos de conectores le ayudan a organizar los conectores y las aplicaciones por región, red o finalidad. Si aún no ha creado ningún grupo de conectores, la aplicación se asigna a un grupo **predeterminado** y verá un mensaje de advertencia que le pide que [cree un grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).

   ![Configuración de la aplicación](./media/application-proxy-publish-azure-portal/configure-app.png)

8. Seleccione **Agregar**.


## <a name="add-a-test-user"></a>Adición de un usuario de prueba 

Para probar que la aplicación se publicó correctamente, agregue una cuenta de usuario a la que tenga acceso. 

1. En la hoja de inicio rápido, seleccione **Assign a user for testing** (Asignar un usuario de prueba).

  ![Asignar un usuario de prueba](./media/application-proxy-publish-azure-portal/assign-user.png)

2. En la hoja Usuarios y grupos, seleccione **Agregar**.

  ![Agregar un usuario o grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. En la hoja para agregar asignación, seleccione **Usuarios y grupos** y elija luego la cuenta que va a agregar. 
4. Seleccione **Asignar**.

## <a name="test-your-published-app"></a>Prueba de la aplicación publicada

En el explorador, navegue a la dirección URL externa que configuró durante el paso de publicación. Debe ver la pantalla de inicio y poder iniciar sesión con la cuenta de prueba que configuró.

![Prueba de la aplicación publicada](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Pasos siguientes
- [Descargue conectores](active-directory-application-proxy-enable.md) y [cree grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md) para publicar aplicaciones en ubicaciones y redes independientes.

- [Configure el inicio de sesión único](application-proxy-sso-azure-portal.md) para la aplicación publicada recientemente.



<!--HONumber=Feb17_HO2-->


