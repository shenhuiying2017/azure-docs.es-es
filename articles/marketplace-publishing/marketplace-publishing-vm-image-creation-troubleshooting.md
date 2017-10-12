---
title: "Solución de problemas comunes durante la creación del VHD | Microsoft Docs"
description: "Responde a preguntas sobre solución de problemas comunes y errores durante la creación del VHD."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Solución de problemas comunes detectados durante la creación del VHD
En este artículo se proporciona ayuda a un publicador o coadministrador de Azure Marketplace que puede experimentar un problema o que tiene preguntas comunes mientras publica o administra sus soluciones de máquina virtual.

1. ¿Cómo se cambia el nombre del host?
   
    Una vez creada la máquina virtual, los usuarios no pueden actualizar el nombre del host.
2. ¿Cómo se restablece el servicio Escritorio remoto o su contraseña de inicio de sesión?
   
   * [Referencia para máquinas virtuales con Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referencia para máquinas virtuales con Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. ¿Cómo generar certificados SSH nuevos?
   
   Consulte el siguiente vínculo: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. ¿Cómo configurar un certificado VPN abierto?
   
   Vea la información facilitada en el siguiente vínculo: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. ¿Cuál es la directiva de soporte técnico para ejecutar software de servidor de Microsoft en el entorno de máquina virtual de Microsoft Azure (infraestructura como servicio)?
   
   Acceda al artículo del siguiente vínculo: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. ¿Las máquinas virtuales tienen un identificador único?
   
   Azure codifica un identificador exclusivo de máquina virtual de Azure en cada máquina virtual. Vea los detalles en este blog y en la documentación.
7. ¿Cómo se puede administrar la extensión de script personalizada en la tarea de inicio en una máquina virtual?
   
   Consulte este vínculo: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. ¿Cómo se crea una máquina virtual desde Azure Portal usando el VHD que se carga en Premium Storage?
   
   Aún no se admite esta característica.
9. ¿Se admite una aplicación de 32 bits en Azure Marketplace?
   
   Consulte el vínculo para obtener información detallada sobre la directiva de soporte técnico: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Cada vez que trato de crear una imagen desde el VHD, se produce el error ". VHD ya está registrado con el repositorio de imágenes como un recurso" en PowerShell. No he creado ninguna imagen antes ni he encontrado ninguna imagen con este nombre en Azure. ¿Cómo se resuelve este problema?
    
    Esto suele suceder si el usuario ha aprovisionado una máquina virtual desde este VHD y existe un bloqueo en ese VHD. Compruebe que no haya ninguna máquina virtual asignada desde este VHD. Si el error persiste, cree una incidencia de soporte técnico mediante este vínculo o desde el Portal de publicación sobre este asunto (los detalles se proporcionan en la respuesta a la pregunta 11).