---
title: "Autenticación con las API de REST de Mobile Engagement: configuración manual"
description: "Describe cómo configurar manualmente la autenticación para las API de REST de Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticación con las API de REST de Mobile Engagement: configuración manual
Se trata de una documentación de apéndice de [Autenticación con las API de REST de Mobile Engagement](mobile-engagement-api-authentication.md). Asegúrese de leerla primero para obtener el contexto. Describe una forma alternativa de realizar una única configuración para establecer la autenticación para las API de REST de Mobile Engagement mediante el Portal de Azure. 

> [!NOTE]
> Las instrucciones siguientes se basan en esta [guía de Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) y se personalizan según lo necesario para la autenticación para las API de Mobile Engagement. Así que consúltela si desea conocer los pasos siguientes en detalle. 
> 
> 

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).
2. Seleccione **Active Directory** en el panel izquierdo.
   
     ![seleccionar Active Directory][1]
3. Elija el **Active Directory predeterminado** en el Portal de Azure. 
   
     ![elegir directorio][2]
   
   > [!IMPORTANT]
   > Este enfoque solo funciona si está trabajando en el Active Directory predeterminado de una cuenta y no funcionará si está realizando la operación en un Active Directory que ha creado en su cuenta. 
   > 
   > 
4. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.
   
     ![ver aplicaciones][3]
5. Haga clic en **AGREGAR**. 
   
     ![agregar aplicación][4]
6. Haga clic en **Agregar una aplicación que mi organización está desarrollando**
   
     ![nueva aplicación][5]
7. Rellene el nombre de la aplicación y seleccione el tipo de aplicación como **APLICACIÓN WEB O API WEB** y haga clic en el botón Siguiente.
   
     ![aplicación de nombre][6]
8. Puede especificar direcciones URL ficticias en **URL DE INICIO DE SESIÓN** y **URI DE ID. DE APLICACIÓN**. No se utilizan para nuestro escenario y no se validan las propias direcciones URL.  
   
     ![propiedades de la aplicación][7]
9. Al final, tendrá una aplicación AAD con el nombre proporcionado anteriormente como el siguiente. Este es el **AD\_APP\_NAME** (anótelo).  
   
     ![nombre de la aplicación][8]
10. Haga clic en el nombre de la aplicación y haga clic en **Configurar**.
    
      ![configurar aplicación ][9]
11. Anote el ID. DEL CLIENTE que se usará como **CLIENT\_ID** para las llamadas API. 
    
     ![configurar aplicación ][10]
12. Desplácese hacia abajo hasta la sección **Claves** y agregue una clave, preferiblemente con una duración de 2 años (expiración), y haga clic en **Guardar**. 
    
     ![configurar aplicación ][11]
13. Copie inmediatamente el valor que se muestra para la clave, ya que solo se muestra ahora y no se almacena, por lo que no se mostrarán nunca más. Si se pierde, tendrá que generar una clave nueva. Este será el **CLIENT_SECRET** para las llamadas API. 
    
     ![configurar aplicación ][12]
    
    > [!IMPORTANT]
    > Esta clave expirará al final de la duración especificada. Por lo tanto, asegúrese de renovarla cuando llegue el momento o, de lo contrario, la autenticación de la API dejará de funcionar. También puede eliminar y volver a crear esta clave si cree que se ha visto comprometida.
    > 
    > 
14. Haga clic en el botón **Ver puntos de conexión**. Se abrirá el cuadro de diálogo **Puntos de conexión de la aplicación**. 
    
    ![][13]
15. En el cuadro de diálogo Extremos de la aplicación, copie el **PUNTO DE CONEXIÓN DE TOKEN DE OAUTH 2.0**. 
    
    ![][14]
16. Este punto de conexión tendrá el formato siguiente, donde el GUID de la dirección URL es el **TENANT_ID** (anótelo): 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Ahora procederemos a configurar los permisos para esta aplicación. Para ello, tendrá que abrir el [Portal de Azure](https://portal.azure.com). 
18. Haga clic en **Grupos de recursos** y busque el grupo de recursos de **Mobile Engagement**.  
    
    ![][15]
19. Haga clic en el grupo de recursos de **Mobile Engagement** y desplácese hasta la hoja **Configuración** aquí. 
    
    ![][16]
20. Haga clic en **Usuarios** en la hoja Configuración y, después, haga clic en **Agregar** para agregar un usuario. 
    
    ![][17]
21. Haga clic en **Seleccionar un rol**
    
    ![][18]
22. Haga clic en **Propietario**
    
    ![][19]
23. Busque el nombre de la aplicación (**AD\_APP\_NAME**) en el cuadro de búsqueda. No verá esto de forma predeterminada aquí. Una vez que lo encuentre, selecciónelo y haga clic en **Seleccionar** en la parte inferior de la hoja. 
    
    ![][20]
24. En la hoja **Agregar acceso**, se mostrará como **1 usuario, 0 grupos**. Haga clic en **Aceptar** en esta hoja para confirmar el cambio. 
    
    ![][21]

Ahora ha completado la configuración de AAD necesaria y ya está listo para llamar a las API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



