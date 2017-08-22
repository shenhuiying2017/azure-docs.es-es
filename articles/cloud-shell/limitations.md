---
title: "Limitaciones de Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las limitaciones de Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3ac234b27a0675f484018c357a65ab65049ceee0
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Limitaciones de Azure Cloud Shell
Azure Cloud Shell tiene las limitaciones conocidas siguientes.

## <a name="system-state-and-persistence"></a>Persistencia y estado del sistema
La máquina que proporciona la sesión de Cloud Shell es temporal y se recicla después de que la sesión esté inactiva durante 20 minutos. Cloud Shell requiere montar un recurso compartido de archivos. Como resultado, la suscripción debe poder configurar los recursos de almacenamiento para tener acceso a Cloud Shell. Otras consideraciones:
* Con el almacenamiento montado, solo se conservan las modificaciones de los directorios `$Home` o `clouddrive`.
* Solo se pueden montar recursos compartidos de archivos desde la [región asignada](persisting-shell-storage.md#mount-a-new-clouddrive).
* Azure Files solo admite cuentas de almacenamiento con redundancia local o de almacenamiento con redundancia geográfica.

## <a name="user-permissions"></a>Permisos de usuario
Los permisos se establecen como usuarios normales sin acceso a sudo. No se conservará cualquier instalación fuera del directorio `$Home`.
Aunque algunos comandos del directorio `clouddrive`, como `git clone`, no tienen los permisos adecuados, el directorio `$Home` sí los tiene.

## <a name="browser-support"></a>Compatibilidad con exploradores
Cloud Shell es compatible con las versiones más recientes de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox y Safari de Apple. Safari en modo privado no es compatible.

## <a name="copy-and-paste"></a>Copiar y pegar
Como Ctrl+C y Ctrl+V no funcionan como métodos abreviados de teclado para copiar y pegar en equipos Windows, use Ctrl+Insert y Mayús+Insert para copiar y pegar.

También se puede hacer clic con el botón derecho para copiar y pegar, pero dependerá del acceso al Portapapeles que tenga cada explorador.

## <a name="editing-bashrc"></a>Editar .bashrc
Tenga cuidado al editar .bashrc, porque al hacerlo puede provocar errores inesperados en Cloud Shell.

## <a name="bashhistory"></a>.bash_history
El historial de comandos de Bash puede ser incoherente debido a una interrupción de la sesión de Cloud Shell o a sesiones simultáneas.

## <a name="usage-limits"></a>Límites de uso
Cloud Shell está pensado para casos de uso interactivos. Por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

## <a name="network-connectivity"></a>Conectividad de red
La latencia de Cloud Shell está sujeta a la conectividad de Internet local y Cloud Shell seguirá tratando de funcionar con las instrucciones enviadas.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md)

