
---
title: "Habilitación de Enterprise State Roaming en Azure Active Directory | Microsoft Docs"
description: "Preguntas más frecuentes sobre la configuración Enterprise State Roaming en dispositivos de Windows. Enterprise State Roaming proporciona a los usuarios una experiencia unificada a través de sus dispositivos de Windows y reduce el tiempo necesario para configurar un nuevo dispositivo."
services: active-directory
keywords: "enterprise state roaming, nube de windows, cómo habilitar enterprise state roaming"
documentationcenter: 
author: femila
manager: swadhwa
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Habilitación de Enterprise State Roaming en Azure Active Directory
Enterprise State Roaming está disponible para cualquier organización con una suscripción Premium de Azure Active Directory (Azure AD). Para obtener más información sobre cómo obtener una suscripción de Azure AD, consulte la [página de producto de Azure AD](https://azure.microsoft.com/services/active-directory).

Al habilitar Enterprise State Roaming, a su organización se le otorgará automáticamente licencias para una suscripción gratuita de uso limitado a Azure Rights Management. Esta suscripción gratuita se limita a cifrar y descifrar los datos de configuración de la empresa sincronizados mediante el servicio Enterprise State Roaming; debe tener una suscripción de pago para utilizar todas las funcionalidades de Azure Rights Management.

Después de obtener una suscripción Premium de Azure AD, siga estos pasos para habilitar Enterprise State Roaming:

1. Inicie sesión en el Portal de Azure clásico.
2. A la izquierda, seleccione **ACTIVE DIRECTORY**y después seleccione el directorio para el que desea habilitar Enterprise State Roaming.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Vaya a la pestaña **CONFIGURAR** de la parte superior.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. Desplácese hacia abajo en la página y seleccione **Los usuarios pueden sincronizar la configuración y los datos de aplicaciones empresariales** y, después, haga clic en **Guardar**.
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Para que un dispositivo Windows 10 movilice la configuración con el servicio Enterprise State Roaming, el dispositivo debe autenticarse mediante una identidad de Azure AD. Para los dispositivos que están unidos a Azure AD, el inicio de sesión del usuario principal es la identidad de Azure AD, por lo que no se requiere ninguna configuración adicional. Para los dispositivos que usan una instancia de Active Directory local tradicional, los administradores de TI deben [conectar los dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="sync-data-storage"></a>Almacenamiento de datos de sincronización
Los datos de itinerancia de estado de empresa se hospedan en las [regiones de Azure](https://azure.microsoft.com/regions/) que mejor se alineen con el valor de país o región establecido en la instancia de Azure Active Directory. Los datos de Enterprise State Roaming se particionan en función de las tres regiones geográficas principales: Norteamérica, EMEA y APAC. Los datos móviles de Enterprise State Roaming del inquilino se encuentran ubicados de manera local con la región geográfica y no se replican entre regiones.  Por ejemplo, los datos de los clientes que tienen el valor de país o región establecido en uno de los países de EMEA como Francia o Zambia se hospedarán en una de las regiones de Azure en Europa.  Los datos de aquellos que establezcan el valor de país o región de Azure AD en uno de los países de Norteamérica como Estados Unidos o Canadá se hospedarán en una o varias de las regiones de Azure en Estados Unidos.  Los datos de los clientes que establezcan el valor de país o región de Azure AD en uno de los países de APAC como Australia o Nueva Zelanda se hospedarán en una o varias de las regiones de Azure en Asia.  Los datos de los países de América del Sur y Antártida se hospedarán en una o varias regiones de Azure dentro de los Estados Unidos.  El valor de país o región se establece como parte del proceso de creación del directorio de AD de Azure y no se puede modificar posteriormente. 

Para más detalles sobre la ubicación de almacenamiento de datos, genere una incidencia con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Administración de Enterprise State Roaming
Los administradores globales de Azure AD pueden habilitar y deshabilitar Enterprise State Roaming en el Portal de Azure clásico.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Los administradores globales pueden limitar la sincronización de configuración a grupos de seguridad específicos.

Los administradores globales también pueden ver un informe de estado de sincronización de dispositivos por usuarios. Para ello, seleccione un usuario concreto en la lista **USUARIOS** de la instancia de Active Directory, haga clic en la pestaña **DISPOSITIVOS** y seleccione la vista **Dispositivos sincronizando ajustes y datos de aplicaciones empresariales**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>Retención de datos
Los datos sincronizados con Azure a través de Enterprise State Roaming se conservarán indefinidamente a menos que se realice una operación de eliminación manual o se determine que los datos en cuestión sean obsoletos. 

**Eliminación explícita:** los datos se eliminan cuando un administrador de Azure elimina un usuario o un directorio, o bien cuando un administrador solicita expresamente que se eliminen los datos.

* **Eliminación de usuarios**: cuando se suprime un usuario en Azure AD, los datos de itinerancia de la cuenta de usuario se marcarán para eliminarse y la operación se completa entre 90 y 180 días. 
* **Eliminación de inquilinos**: la eliminación de un directorio completo en Azure AD se realiza inmediatamente. Todos los datos de configuración asociados a ese directorio se marcarán para eliminarse y la operación se completa entre 90 y 180 días. 
* **Eliminación bajo demanda**: si el administrador de Azure AD desea eliminar manualmente los datos de configuración o la información de un usuario específico, el administrador puede generar una incidencia con el servicio de [soporte técnico de Azure](https://azure.microsoft.com/support/). 

**Eliminación de datos obsoletos**: los datos a los que no se ha accedido durante un año (el periodo de retención) se considerarán obsoletos y se podrán eliminar de Azure. El periodo de retención está sujeto a cambios, pero no será inferior a 90 días. Los datos obsoletos pueden ser un conjunto específico de valores de configuración de Windows o de la aplicación o toda la configuración de un usuario. Por ejemplo:

* Si ningún dispositivo accede a una colección de configuraciones concreta (por ejemplo, una aplicación se quita del dispositivo o un grupo de configuraciones, como "Tema", está deshabilitado para todos los dispositivos de un usuario), esa colección quedará obsoleta tras el período de retención y se puede eliminar. 
* Si un usuario ha desactivado la sincronización de la configuración en todos sus dispositivos, no se podrá acceder a ninguno de los datos de configuración y todos los datos de configuración para ese usuario quedarán obsoletos y pueden eliminarse tras el período de retención. 
* Si el administrador del directorio de Azure AD desactiva Enterprise State Roaming para todo el directorio, todos los usuarios de ese directorio dejarán de sincronizar la configuración y todos los datos de configuración de todos los usuarios quedarán obsoletos y podrán eliminarse tras el período de retención. 

**Recuperación de los datos eliminados**: la directiva de retención de datos no es configurable. Cuando los datos se hayan eliminado permanentemente, no se pueden recuperar. Sin embargo, es importante tener en cuenta que solo se eliminarán los datos de configuración de Azure, no el dispositivo del usuario final. Si cualquier dispositivo más adelante vuelve a conectarse al servicio Enterprise State Roaming, la configuración se sincronizará de nuevo y almacenará en Azure.

## <a name="related-topics"></a>Temas relacionados
* [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Solución de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)



<!--HONumber=Jan17_HO1-->


