---
title: "Limitaciones de Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las limitaciones de Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 411301df9647f64f9d4a0405d35d08f000d792a8
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Limitaciones de Azure Cloud Shell
Azure Cloud Shell tiene las limitaciones conocidas siguientes.

## <a name="system-state-and-persistence"></a>Persistencia y estado del sistema
La máquina que proporciona la sesión de Cloud Shell es temporal y se recicla después de que la sesión esté inactiva durante 10 minutos. Cloud Shell requiere montar un recurso compartido de archivos. Como resultado, la suscripción debe poder aprovisionar los recursos de almacenamiento para tener acceso a Cloud Shell.
* Con el almacenamiento montado, solo se mantienen las modificaciones de los directorios `$Home` o `clouddrive`.
  * Solo se pueden montar recursos compartidos de archivos desde la [región asignada](persisting-shell-storage.md#pre-requisites-for-manual-mounting).
  * Azure Files solo admite las cuentas de almacenamiento LRS y GRS.

## <a name="user-permissions"></a>Permisos de usuario
Los permisos se establecen como usuarios normales sin acceso a sudo. No se conservará cualquier instalación fuera de $Home.
Algunos comandos como `git clone` en el directorio `clouddrive` no tienen los permisos adecuados, sin embargo, $Home no.

## <a name="browser-support"></a>Compatibilidad con exploradores
Cloud Shell es compatible con las versiones más recientes de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox y Safari de Apple. Safari en modo privado no es compatible.

## <a name="copy-and-paste"></a>Copiar y pegar
Ctrl+V y Ctrl+C no funcionan como copiar y pegar en equipos con Windows, así que le rogamos que use Ctrl+Insertar y Mayús+Insertar.
También se puede hacer con el clic con el botón derecho para copiar y pegar, pero dependerá del acceso al Portapapeles que tenga cada explorador.

## <a name="editing-bashrc"></a>Editar .bashrc
Tenga cuidado al editar .bashrc, ya que puede producir errores inesperados de Cloud Shell.

## <a name="usage-limits"></a>Límites de uso
Cloud Shell está pensado para casos de uso interactivo; por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

## <a name="network-connectivity"></a>Conectividad de red
La latencia de Cloud Shell está sujeta a la conectividad de Internet local, Cloud Shell seguirá tratando de funcionar con las instrucciones enviadas.

## <a name="next-steps"></a>Pasos siguientes
[Inicio rápido de Cloud Shell](quickstart.md)

