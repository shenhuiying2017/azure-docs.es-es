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
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e42841b126a9df9240bec3f489589d5ce4a6db80
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Limitaciones de Azure Cloud Shell
Azure Cloud Shell tiene las limitaciones conocidas siguientes:

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
Ctrl+C y Ctrl+V no funcionan como métodos abreviados de teclado para copiar y pegar en equipos Windows, use Ctrl+Insert y Mayús+Insert para copiar y pegar respectivamente.

También se puede hacer clic con el botón derecho para copiar y pegar, pero dependerá del acceso al Portapapeles que tenga cada explorador.

## <a name="editing-bashrc"></a>Editar .bashrc
Tenga cuidado al editar .bashrc, ya que puede producir errores inesperados en Cloud Shell.

## <a name="bashhistory"></a>.bash_history
El historial de comandos de Bash puede ser incoherente debido a una interrupción de la sesión de Cloud Shell o a sesiones simultáneas.

## <a name="usage-limits"></a>Límites de uso
Cloud Shell está pensado para casos de uso interactivos. Por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

## <a name="network-connectivity"></a>Conectividad de red
La latencia de Cloud Shell está sujeta a la conectividad de Internet local, Cloud Shell seguirá tratando de ejecutar las instrucciones enviadas.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md)

