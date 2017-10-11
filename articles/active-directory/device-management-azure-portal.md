---
title: "Administración de dispositivos con Azure Portal (versión preliminar) | Microsoft Docs"
description: Aprenda a usar Azure Portal para administrar dispositivos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4b46e1627a229b0649d9ccd2550cd28fda9849f8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="managing-devices-using-the-azure-portal---preview"></a>Administración de dispositivos con Azure Portal (versión preliminar)

>[!NOTE]
>Esta funcionalidad se encuentra actualmente en versión preliminar pública. Debe estar preparado para deshacer o eliminar los cambios. La característica está disponible en cualquier suscripción de Azure Active Directory (Azure AD) durante el período de versión preliminar pública. Pero cuando ya esté disponible con carácter general, algunos aspectos de ella podrían requerir una suscripción Premium de Azure Active Directory.


Con la administración de dispositivos en Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen acceso a los recursos desde dispositivos que cumplen los estándares de seguridad y cumplimiento. 

En este tema:

- Se da por hecho que está familiarizado con la [introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)

- Se proporciona información sobre la administración de dispositivos mediante Azure Portal


Para administrar dispositivos en Azure Portal, debe hacer clic en **Dispositivos** en la sección **Administrar** de la hoja **Azure Active Directory**.

![Administración de un dispositivo de Intune](./media/device-management-azure-portal/11.png)




## <a name="configure-device-settings"></a>Configuración de dispositivo

Para administrar los dispositivos mediante Azure Portal, estos deben estar registrados o unidos a Azure AD. Como administrador, puede ajustar el proceso de registro y unión de dispositivos al configurar los valores del dispositivo.

![Administración de un dispositivo de Intune](./media/device-management-azure-portal/22.png)


La hoja de configuración de dispositivo permite configurar:

- **Los usuarios pueden inscribir dispositivos en Azure AD**: esta opción permite seleccionar los usuarios que pueden unir dispositivos a Azure AD. El valor predeterminado es **Todos**.

- **Administradores locales adicionales en dispositivos unidos a Azure AD**: puede seleccionar a qué usuarios se conceden derechos de administrador local en un dispositivo. Los usuarios agregados aquí se agregan al rol *Administradores de dispositivos* de Azure AD. De forma predeterminada, a los administradores globales de Azure AD y a los propietarios de dispositivos se les conceden derechos de administrador local. Esta opción es una capacidad de la edición Premium disponible en productos como Azure AD Premium o Enterprise Mobility Suite (EMS). 

- **Los usuarios pueden registrar sus dispositivos con Azure AD**: debe configurar esta opción para permitir que los dispositivos se registren en Azure AD. Si selecciona **Ninguno**, no se permite a los dispositivos registrarse si no están unidos a Azure AD o a Azure AD híbrido. La inscripción en Microsoft Intune o Administración de dispositivos móviles (MDM) para Office 365 exige registrarse. Si ha configurado alguno de estos servicios, se selecciona **TODOS** y **NINGUNO** no está disponible.

- **Requerir Multi-factor Auth para conectar dispositivos**: puede decidir si se exige a los usuarios proporcionar un segundo factor de autenticación para unir su dispositivo a Azure AD. El valor predeterminado es **No**. Se recomienda exigir Multi-Factor Authentication al registrar un dispositivo. Antes de habilitar Multi-Factor Authentication para este servicio, debe asegurarse de que está configurado para los usuarios que registran sus dispositivos. Para más información sobre los distintos servicios de Azure Multi-Factor Authentication, vea [Introducción a Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Número máximo de dispositivos**: esta opción permite seleccionar el número máximo de dispositivos que puede tener un usuario en Azure AD. Si un usuario alcanza esta cuota, no puede agregar dispositivos adicionales hasta que se quitan uno o varios de los dispositivos existentes. La cuota de dispositivos cuenta todos los dispositivos unidos a Azure AD o registrados en Azure AD hoy. El valor predeterminado es **20**.

- **Los usuarios pueden sincronizar la configuración y los datos de aplicaciones en distintos dispositivos**: de forma predeterminada, esta opción se establece en **NINGUNO**. La selección de usuarios o grupos concretos o de TODOS permite sincronizar la configuración y los datos de aplicación del usuario en sus dispositivos Windows 10. Más información sobre cómo funciona la sincronización en Windows 10.
Esta opción es una capacidad Premium disponible en productos como Azure AD Premium o Enterprise Mobility Suite (EMS).
 
    ![Administración de un dispositivo de Intune](./media/device-management-azure-portal/21.png)




## <a name="locate-devices"></a>Búsqueda de dispositivos

Como administrador, en Azure Portal tiene dos opciones para buscar dispositivos registrados y unidos:

- **Todos los dispositivos** en la sección **Administrar** de la hoja **Dispositivos**  

    ![Todos los dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** en la sección **Administrar** de la hoja **Usuarios**
 
    ![Todos los dispositivos](./media/device-management-azure-portal/43.png)



Con ambas opciones, puede ir a una vista que:


- Permite buscar dispositivos si se usa el nombre para mostrar como filtro.

- Proporciona información general detallada de los dispositivos registrados y unidos.

- Permite realizar tareas comunes de administración de dispositivos.
   

![Todos los dispositivos](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Tareas de administración de dispositivos

Como administrador, puede administrar los dispositivos registrados o unidos. En esta sección se proporciona información sobre las tareas comunes de administración de dispositivos.


**Administrar un dispositivo de Intune**: si es administrador de Intune, puede administrar los dispositivos marcados como **Microsoft Intune**. Un administrador puede ver dispositivos adicionales 

![Administración de un dispositivo de Intune](./media/device-management-azure-portal/31.png)


**Habilitar o deshabilitar un dispositivo de Azure AD**

Para habilitar o deshabilitar un dispositivo, debe ser administrador global en Azure AD. Al deshabilitar un dispositivo se evita que acceda a los recursos de Azure AD.  Para deshabilitar el dispositivo, puede hacer clic en *...* o hacer clic en el dispositivo para obtener más detalles.

 
![Administración de un dispositivo de Intune](./media/device-management-azure-portal/33.png)

Al deshabilitar un dispositivo, el estado de la columna **HABILITADO** cambia a **No**.

![Deshabilitación de un dispositivo](./media/device-management-azure-portal/32.png)


**Eliminar un dispositivo de Azure AD**: para eliminar un dispositivo, debe ser administrador global en Azure AD.  
La eliminación de un dispositivo:
 
- Evita que un dispositivo acceda a los recursos de Azure AD 

- Quita todos los detalles asociados al dispositivo, por ejemplo, las claves de BitLocker de dispositivos Windows  

- Representa una actividad no recuperable y no se recomienda a menos que sea necesaria

Si un dispositivo está administrado por otra entidad de administración (por ejemplo, Microsoft Intune), asegúrese de que el dispositivo se haya borrado o retirado antes de eliminarlo de Azure AD.

Puede seleccionar "..." para eliminar el dispositivo o hacer clic en él para obtener más detalles
 
![Eliminar un dispositivo](./media/device-management-azure-portal/34.png)


**Ver o copiar el identificador de dispositivo**: puede usar un identificador de dispositivo para comprobar los detalles del identificador de dispositivo en el dispositivo o usar PowerShell durante la solución de problemas. Para acceder a la opción de copia, haga clic en el dispositivo.

![Visualización de identificador de dispositivo](./media/device-management-azure-portal/35.png)
  

**Ver o copiar las claves de BitLocker**: si es administrador, puede ver y copiar las claves de BitLocker para ayudar a los usuarios a recuperar su unidad cifrada. Estas claves solo están disponibles para dispositivos Windows cifrados y con las claves almacenadas en Azure AD. Puede copiar estas claves al acceder a los detalles del dispositivo.
 
![Visualización de claves de BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Registros de auditoría


Las actividades de un dispositivo están disponibles a través de los registros de actividad. Esto incluye actividades desencadenadas por el servicio de registro de dispositivos o por el usuario:

- Creación de dispositivos e incorporación de propietarios o usuarios al dispositivo

- Cambios en la configuración de un dispositivo

- Operaciones de dispositivo como eliminar o actualizar un dispositivo
 
El punto de entrada a los datos de auditoría es **Registros de auditoría**, en la sección **Actividad** de la hoja **Dispositivos*.

![Registros de auditoría](./media/device-management-azure-portal/61.png)


Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- la fecha y hora de la repetición

- los destinos

- el iniciador o actor (quién) de una actividad

- la actividad (qué)

![Registros de auditoría](./media/device-management-azure-portal/63.png)

Puede personalizar la vista de lista, haga clic en **Columnas** en la barra de herramientas.
 
![Registros de auditoría](./media/device-management-azure-portal/64.png)


Para restringir los datos del informe a un nivel que se adapte a sus necesidades, puede filtrar los datos de auditoría con los siguientes campos:

- Categoría
- Tipo de recurso de actividad
- Actividad
- Intervalo de fechas
- Destino
- Iniciado por (actor)

Además de los filtros, puede buscar entradas concretas.

![Registros de auditoría](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)



