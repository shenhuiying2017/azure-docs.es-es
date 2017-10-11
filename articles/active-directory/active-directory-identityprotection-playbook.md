---
title: "Guía de Azure Active Directory Identity Protection | Microsoft Docs"
description: "Aprenda cómo Azure AD Identity Protection permite limitar la capacidad de un atacante para aprovechar una identidad o un dispositivo en peligro y asegurar una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro."
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 2ecd07faed785fa6aa179ac1cca35a70d965e1dc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Guía de Azure Active Directory Identity Protection
Esta guía le ayudará a:

* Rellenar los datos en el entorno de Identity Protection mediante la simulación de vulnerabilidades y eventos de riesgo
* Configurar directivas de acceso condicional en función del riesgo y probar el impacto de estas directivas

## <a name="simulating-risk-events"></a>Simulación de eventos de riesgo
En esta sección se indican los pasos para simular los siguientes tipos de eventos de riesgo:

* Inicios de sesión desde direcciones IP anónimas (fácil)
* Inicios de sesión desde ubicaciones desconocidas (moderada)
* Viaje imposible a ubicaciones inusuales (difícil)

Otros eventos de riesgo no se pueden simular de forma segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inicios de sesión desde direcciones IP anónimas
Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónima. A menudo, estos servidores proxy los usan los usuarios que desean ocultar la dirección IP del dispositivo y es posible que se usen con fines malintencionados.

**Para simular un inicio de sesión desde una IP anónima, realice los siguientes pasos**:

1. Descargue el [explorador Tor](https://www.torproject.org/projects/torbrowser.html.en).
2. Mediante el explorador Tor, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Escriba las credenciales de la cuenta que desee que aparezcan en el informe **Inicios de sesión desde direcciones IP anónimas** .

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 5 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inicios de sesión desde ubicaciones desconocidas
El riesgo de ubicaciones desconocidas es un mecanismo de evaluación de inicios de sesión en tiempo real que tiene en cuenta las ubicaciones de inicio de sesión anteriores (IP, latitud/longitud y ASN) para determinar las ubicaciones nuevas o desconocidas. El sistema almacena las direcciones IP, la latitud/longitud y las ASN anteriores, y considera que son ubicaciones conocidas. Una ubicación de inicio de sesión se considera desconocida si la ubicación de inicio de sesión no coincide con ninguna de las ubicaciones conocidas existentes.

Azure Active Directory Identity Protection:  

* Tiene un período de aprendizaje inicial de 14 días, durante el cual no marca ninguna nueva ubicación como ubicación desconocida.
* Omite los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación familiar existente 

Para simular ubicaciones desconocidas, debe iniciar sesión desde una ubicación y un dispositivo desde los que no se haya iniciado sesión en esa cuenta antes. 

**Para simular un inicio de sesión desde una ubicación desconocida, realice los siguientes pasos**:

1. Elija una cuenta que tenga al menos un historial de inicios de sesión de 14 días. 
2. Realice uno de estos pasos:
   
   a. Mientras usa una VPN, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com) y escriba las credenciales de la cuenta para la que desea simular el evento de riesgo.
   
   b. Pida a un colaborador en otra ubicación que inicie sesión con las credenciales de la cuenta (no recomendado).

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 5 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Viaje imposible a una ubicación inusual
Simular la condición de viaje imposible es complicado porque el algoritmo utiliza el aprendizaje automático para descartar falsos positivos, como un viaje imposible desde dispositivos conocidos o inicios de sesión de VPN usadas por otros usuarios del directorio. Además, el algoritmo requiere un historial de inicios de sesión del usuario de 3 a 14 días antes de que comience a generar eventos de riesgo.

**Para simular un viaje imposible a una ubicación inusual, realice los pasos siguientes**:

1. Mediante el explorador estándar, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Escriba las credenciales de la cuenta para la que desea generar un evento de riesgo de viaje imposible.
3. Cambie el agente de usuario. Puede cambiar el agente de usuario en Internet Explorer desde Herramientas de desarrollo, o bien en Firefox o Chrome con un complemento modificador del agente de usuario.
4. Cambie la dirección IP. Puede cambiar la dirección IP mediante una VPN, un complemento de Tor, o iniciando una máquina nueva en Azure en otro centro de datos.
5. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com) con las mismas credenciales que antes y pocos minutos después del inicio de sesión anterior.

El inicio de sesión se mostrará en el panel de Identity Protection entre 2 y 4 horas después.<br>
Debido a los complejo modelos de aprendizaje automático implicados, es posible que no se detecte.<br> Puede replicar estos pasos para varias cuentas de Azure AD.

## <a name="simulating-vulnerabilities"></a>Simulación de puntos vulnerables
Los puntos vulnerables son puntos débiles de un entorno de Azure AD que puede ser aprovechados por un actor perjudicial. Actualmente se exponen 3 tipos de puntos vulnerables en Azure AD Identity Protection que aprovechan otras características de Azure AD. Estos puntos vulnerables se mostrará en el panel de Identity Protection automáticamente una vez configuradas estas características.

* Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)de Azure AD. 

## <a name="user-compromise-risk"></a>Riesgo de compromiso de usuario
**Para probar el riesgo de compromiso de usuario, realice los pasos siguientes**:

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con las credenciales de administrador global para el inquilino.
2. Vaya a **Identity Protection**. 
3. En la hoja principal de **Azure AD Identity Protection**, haga clic en **Configuración**. 
4. En la hoja **Configuración del portal**, en **Reglas de seguridad**, haga clic en **Riesgo de compromiso de usuario**. 
5. En la hoja **Riesgo de inicio de sesión**, desactive **Habilitar regla** y, a continuación, haga clic en **Guardar** la configuración.
6. Para una cuenta de usuario determinada, simule un evento de riesgo de ubicaciones desconocidas o IP anónima. Esto eleva el nivel de riesgo del usuario para ese usuario a **Medio**.
7. Espere unos minutos y después compruebe que el nivel de usuario para el usuario sea **Medio**.
8. Vaya a la hoja **Configuración del portal** .
9. En la hoja **Riesgo de compromiso de usuario**, en **Habilitar regla**, seleccione **Activado**. 
10. Seleccione una de las siguientes opciones:
    
    a. Para bloquearlo, seleccione **Medio** en **Bloquear inicio de sesión**.
    
    b. Para aplicar el cambio de contraseña segura, seleccione **Medio** en **Requerir autenticación multifactor**.
11. Haga clic en **Guardar**.
12. Ahora puede probar el acceso condicional basado en riesgos mediante un inicio de sesión con un usuario con un nivel de riesgo elevado. Si el riesgo del usuario es Medio, en función de la directiva que establezca, se bloqueará el inicio de sesión o se forzará el cambio de contraseña. 
    <br><br>
    ![Guía de](./media/active-directory-identityprotection-playbook/201.png "guía")
    <br>

## <a name="sign-in-risk"></a>Riesgo de inicio de sesión
**Para probar el riesgo de inicio de sesión, realice los pasos siguientes:**

1. Inicie sesión en [https://portal.azure.com ](https://portal.azure.com) con las credenciales de administrador global para el inquilino.
2. Vaya a **Identity Protection**.
3. En la hoja principal de **Azure AD Identity Protection**, haga clic en **Configuración**. 
4. En la hoja **Configuración del portal**, en **Reglas de seguridad**, haga clic en **Riesgo de inicio de sesión**.
5. En el ** iniciar sesión en riesgo ** hoja, seleccione **en** en **Habilitar regla**. 
6. Seleccione una de las siguientes opciones:
   
   a. Para bloquearlo, seleccione **Medio** en **Bloquear inicio de sesión**.
   
   b. Para aplicar el cambio de contraseña segura, seleccione **Medio** en **Requerir autenticación multifactor**.
7. Para bloquearlo, seleccione Medio en Bloquear inicio de sesión.
8. Para aplicar la autenticación multifactor, seleccione **Medio** en **Requerir autenticación multifactor**.
9. Haga clic en **Save**(Guardar).
10. Ahora puede probar el acceso condicional basado en riesgos mediante la simulación de los eventos de riesgo de IP anónima o ubicaciones desconocidas, porque son eventos de riesgo **Medio** .


![Guía de](./media/active-directory-identityprotection-playbook/200.png "Guía")


## <a name="see-also"></a>Otras referencias
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

