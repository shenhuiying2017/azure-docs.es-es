---
title: "Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory | Microsoft Docs"
description: "Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 715fca79e488ae3759926181c244a42026f4a554
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory 

Este tema solo es aplicable a los siguientes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 o Windows Server 2016, consulte [Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

En este tema se da por supuesto que ha configurado [dispositivos híbridos unidos a Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) para admitir los escenarios siguientes:

- Acceso condicional basado en dispositivos

- [Perfiles móviles de empresa](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](active-directory-azureadjoin-passport-deployment.md) 





En este tema se proporcionan instrucciones sobre cómo resolver problemas potenciales.  

**Qué debería saber:** 

- El número máximo de dispositivos por usuario está centrado en dispositivos. Por ejemplo, si *jdoe* y *jharnett* inician sesión en un dispositivo, se crea un registro independiente (identificador de dispositivo) para cada uno de estos usuarios en la pestaña de información de **USUARIO**.  

- El registro inicial o unión de dispositivos se ha configurado para realizar un intento de inicio de sesión o de bloqueo/desbloqueo. Podría haber un retraso de 5 minutos desencadenado por una tarea de programador de tareas. 

- La reinstalación del sistema operativo o la anulación del registro y el nuevo registro de forma manual pueden crear un nuevo registro en Azure AD y generar varias entradas en la pestaña de información de USUARIO de Azure Portal. 


## <a name="step-1-retrieve-the-registration-status"></a>Paso 1: Recuperar el estado del registro 

**Para verificar el estado del registro:**  

1. Abra el símbolo del sistema como administrador. 

2. Escriba `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Este comando muestra un cuadro de diálogo que proporciona más detalles sobre el estado de la unión.

![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Paso 2: Evaluación del estado de unión a Azure AD híbrido 

Si la unión a Azure AD híbrido no se realiza correctamente, el cuadro de diálogo proporciona detalles sobre el problema que se ha producido.

**Los problemas más comunes son:**

- Una configuración incorrecta de AD FS o Azure AD

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- No está registrado como un usuario de dominio

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- Se ha alcanzado una cuota

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- El servicio no responde 

    ![Workplace Join for Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

También puede encontrar la información de estado en el registro de eventos en **Registros de aplicaciones y servicios\Microsoft-Workplace Join**.
  
**Las causas más comunes para una unión a Azure AD híbrido con error son:** 

- El equipo no está en la red interna de la organización o en una VPN sin conexión a una implementación local del controlador de dominio de AD.

- Ha iniciado sesión en el equipo con una cuenta del equipo local. 

- Problemas de configuración de servicio: 

  - El servidor de federación se ha configurado para admitir **WIAORMULTIAUTHN**. 

  - No hay ningún objeto de punto de conexión de servicio que haga referencia a su nombre de dominio comprobado en Azure AD en el bosque de AD al que pertenece el equipo.

  - Un usuario ha alcanzado el límite de dispositivos. 

## <a name="next-steps"></a>Pasos siguientes

Si tiene preguntas, consulte las [preguntas más frecuentes sobre la administración de dispositivos](device-management-faq.md).  
