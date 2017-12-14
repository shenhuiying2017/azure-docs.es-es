---
title: "Administración de dispositivos con Azure Portal | Microsoft Docs"
description: Aprenda a usar Azure Portal para administrar dispositivos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1e0d40b996e181a606d16d26633f890b9169ecbb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="managing-devices-using-the-azure-portal"></a>Administración de dispositivos con Azure Portal


Con la administración de dispositivos en Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen acceso a los recursos desde dispositivos que cumplen los estándares de seguridad y cumplimiento. 

En este tema:

- Se da por hecho que está familiarizado con la [introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)

- Se proporciona información sobre la administración de dispositivos mediante Azure Portal

## <a name="manage-devices"></a>Administración de dispositivos 

Azure Portal le proporciona una ubicación central desde la que administrar los dispositivos. Para obtener esta ubicación, use un [vínculo directo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) o siga los pasos que se detallan en el manual:

1. Inicie sesión como administrador en [Azure Portal](https://portal.azure.com).

2. En la barra de navegación izquierda, haga clic en **Active Directory**.

    ![Configuración de dispositivo](./media/device-management-azure-portal/01.png)

3. En la sección **Administrar**, haga clic en **Dispositivos**.

    ![Configuración de dispositivo](./media/device-management-azure-portal/11.png)
 
La página **Dispositivos** le permitirá:

- Configurar las opciones de administración de los dispositivos.

- Búsqueda de dispositivos

- Realizar tareas de administración de dispositivos.

- Revisar la administración de los dispositivos relacionados con los registros de auditoría.  
  

## <a name="configure-device-settings"></a>Configuración de dispositivo

Para administrar dispositivos mediante Azure Portal, estos deben estar [registrados o unidos](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) a Azure AD. Como administrador, puede ajustar el proceso de registro y unión de dispositivos al configurar los valores del dispositivo. 

![Configuración de dispositivo](./media/device-management-azure-portal/22.png)

La página de configuración del dispositivo le permite configurar:

![Administración de un dispositivo de Intune](./media/device-management-azure-portal/21.png)


- **Los usuarios pueden unir sus dispositivos a Azure AD**: esta opción le permite seleccionar los usuarios que pueden [unir dispositivos](device-management-introduction.md#azure-ad-joined-devices) a Azure AD. El valor predeterminado es **Todos**.

- **Administradores locales adicionales en dispositivos unidos a Azure AD**: puede seleccionar a qué usuarios se conceden derechos de administrador local en un dispositivo. Los usuarios agregados aquí se agregan al rol *Administradores de dispositivos* de Azure AD. De forma predeterminada, a los administradores globales de Azure AD y a los propietarios de dispositivos se les conceden derechos de administrador local. Esta opción es una capacidad de la edición Premium disponible en productos como Azure AD Premium o Enterprise Mobility Suite (EMS). 

- **Los usuarios pueden registrar sus dispositivos con Azure AD**: debe configurar esta opción para permitir que los dispositivos se [registren](device-management-introduction.md#azure-ad-registered-devices) en Azure AD. Si selecciona **Ninguno**, no se permite a los dispositivos registrarse si no están unidos a Azure AD o a Azure AD híbrido. La inscripción en Microsoft Intune o Administración de dispositivos móviles (MDM) para Office 365 exige registrarse. Si ha configurado alguno de estos servicios, se selecciona **TODOS** y **NINGUNO** no está disponible.

- **Requerir Multi-Factor Authentication para unir dispositivos**: puede decidir si se exige a los usuarios proporcionar una segunda fase de autenticación para [unir](device-management-introduction.md#azure-ad-joined-devices) su dispositivo a Azure AD. El valor predeterminado es **No**. Se recomienda exigir Multi-Factor Authentication al registrar un dispositivo. Antes de habilitar Multi-Factor Authentication para este servicio, debe asegurarse de que está configurado para los usuarios que registran sus dispositivos. Para más información sobre los distintos servicios de Azure Multi-Factor Authentication, vea [Introducción a Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Número máximo de dispositivos**: esta opción permite seleccionar el número máximo de dispositivos que puede tener un usuario en Azure AD. Si un usuario alcanza esta cuota, no puede agregar dispositivos adicionales hasta que se quitan uno o varios de los dispositivos existentes. La cuota de dispositivos cuenta todos los dispositivos unidos a Azure AD o registrados en Azure AD hoy. El valor predeterminado es **20**.

- **Los usuarios pueden sincronizar la configuración y los datos de aplicaciones en distintos dispositivos**: de forma predeterminada, esta opción se establece en **NINGUNO**. La selección de usuarios o grupos concretos o de TODOS permite sincronizar la configuración y los datos de aplicación del usuario en sus dispositivos Windows 10. Más información sobre cómo funciona la sincronización en Windows 10.
Esta opción es una capacidad Premium disponible en productos como Azure AD Premium o Enterprise Mobility Suite (EMS).
 




## <a name="locate-devices"></a>Búsqueda de dispositivos

Tiene dos opciones para buscar dispositivos registrados y unidos:

- **Todos los dispositivos** en la sección **Administrar** de la página **Dispositivos**.  

    ![Todos los dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** en la sección **Administrar** de la página **Usuarios**.
 
    ![Todos los dispositivos](./media/device-management-azure-portal/43.png)



Con ambas opciones, puede ir a una vista que:


- Permite buscar dispositivos si se usa el nombre para mostrar como filtro.

- Proporciona información general detallada de los dispositivos registrados y unidos.

- Permite realizar tareas comunes de administración de dispositivos.
   

![Todos los dispositivos](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Tareas de administración de dispositivos

Como administrador, puede administrar los dispositivos registrados o unidos. En esta sección se proporciona información sobre las tareas comunes de administración de dispositivos.


### <a name="manage-an-intune-device"></a>Administración de un dispositivo de Intune

Si es administrador de Intune, puede administrar los dispositivos marcados como **Microsoft Intune**. Un administrador puede ver dispositivos adicionales 

![Administración de un dispositivo de Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Habilitar o deshabilitar un dispositivo de Azure AD

Para habilitar o deshabilitar un dispositivo, tiene dos opciones:

- En el menú de tareas ("...") en la página **Todos los dispositivos**.

    ![Administración de un dispositivo de Intune](./media/device-management-azure-portal/71.png)

- La barra de herramientas en la página **Dispositivos**.

    ![Administración de un dispositivo de Intune](./media/device-management-azure-portal/32.png)


**Comentarios:**

- Para habilitar o deshabilitar un dispositivo, debe ser administrador global en Azure AD. 
- Al deshabilitar un dispositivo se evita que acceda a los recursos de Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Eliminar un dispositivo de Azure AD

Para eliminar un dispositivo, tiene dos opciones:

- En el menú de tareas ("...") en la página **Todos los dispositivos**.

    ![Administración de un dispositivo de Intune](./media/device-management-azure-portal/72.png)

- La barra de herramientas en la página **Dispositivos**.

    ![Eliminar un dispositivo](./media/device-management-azure-portal/34.png)


**Comentarios:**

- Debe ser administrador global de Azure AD para eliminar un dispositivo.  

- La eliminación de un dispositivo:
 
    - Evita que un dispositivo acceda a los recursos de Azure AD. 

    - Quita todos los detalles asociados al dispositivo como, por ejemplo, las claves de BitLocker de los dispositivos Windows.  

    - Representa una actividad no recuperable y no se recomienda a menos que sea necesaria.

Si es otra entidad de administración la que administra un dispositivo (por ejemplo, Microsoft Intune), asegúrese de que el dispositivo se haya borrado o retirado antes de eliminarlo de Azure AD.

 


### <a name="view-or-copy-device-id"></a>Ver o copiar el id. del dispositivo

Puede usar un id. de dispositivo para verificar los detalles del id.de dispositivo en ese dispositivo o usar PowerShell durante la solución de problemas. Para acceder a la opción de copia, haga clic en el dispositivo.

![Visualización de identificador de dispositivo](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Ver o copiar las claves de BitLocker

Si es administrador, puede ver y copiar las claves de BitLocker para ayudar a otros usuarios a recuperar su unidad cifrada. Estas claves solo están disponibles para dispositivos Windows cifrados y con las claves almacenadas en Azure AD. Puede copiar estas claves al acceder a los detalles del dispositivo.
 
![Visualización de claves de BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Registros de auditoría


Las actividades de un dispositivo están disponibles a través de los registros de actividad. Esto incluye actividades que desencadena el servicio de registro de dispositivos y los usuarios:

- Creación de dispositivos y adición de propietarios o usuarios al dispositivo.

- Cambios en la configuración de un dispositivo

- Operaciones de dispositivo como eliminar o actualizar un dispositivo
 
El punto de entrada a los datos de auditoría está en **Registros de auditoría**, que se encuentra en la sección **Actividad** de la página **Dispositivos**.

![Registros de auditoría](./media/device-management-azure-portal/61.png)


Un registro de auditoría tiene una vista de lista predeterminada que muestra:

- La fecha y hora de la repetición

- Los destinos

- El iniciador o actor (quién) de una actividad

- La actividad (qué)

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



