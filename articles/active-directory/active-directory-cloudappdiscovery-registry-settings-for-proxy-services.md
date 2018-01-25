---
title: "Configuración del Registro de Cloud App Discovery para servicios de proxy | Microsoft Docs"
description: El objetivo de este tema es proporcionarle los pasos que debe llevar a cabo para establecer el puerto requerido en los equipos que ejecutan el agente de Cloud App Discovery.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0e227d6e15789b29b40197a9ff71b2116312da78
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Configuración del registro de Cloud App Discovery para los servicios de proxy
El objetivo de este tema es indicar los pasos para establecer el puerto necesario en los equipos que ejecutan el agente de Cloud App Discovery. De forma predeterminada, el agente de Cloud App Discovery está configurado para usar solo los puertos 80 o 443. Si piensa instalar Cloud App Discovery en un entorno con un servidor proxy que usa un puerto personalizado (que no sea el 80 ni el 443), deberá configurar los agentes para usar dicho puerto. La configuración se basa en una clave del registro.

> [!TIP] 
> Eche un vistazo a las mejoras de la característica Cloud App Discovery (ahora sin agente) en Azure Active Directory (Azure AD), mejorada con [integración con Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Modificar el puerto que usa el equipo que ejecuta el agente de Cloud App Discovery

1. Inicie el editor del Registro.
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Navegue a la siguiente clave del Registro o créela: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. Cree un nuevo valor de **cadenas múltiples** denominado **Puertos**. 
  ![Nuevo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Para abrir el cuadro de diálogo **Modificar cadenas múltiples**, haga doble clic en el valor **Puertos**.
5. En **Datos de valor**, escriba los siguientes valores y agregue todos los puertos personalizados que se usan en la organización: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Modificar cadenas múltiples](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Modificar cadenas múltiples**.

## <a name="next-steps"></a>pasos siguientes

* [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md) 

