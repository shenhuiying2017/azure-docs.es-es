---
title: "Solución de problemas de Azure Site Recovery de VMware a Azure | Microsoft Docs"
description: "Solución de problemas y errores al replicar máquinas virtuales de Azure."
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: anoopkv
ms.openlocfilehash: a03766f8a22399f7d72a01f8d744bfd1cef90ac3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Solución de problemas de instalación de inserción de Mobility Service

En este artículo se describen problemas comunes a los que puede enfrentarse al intentar instalar Azure Site Recovery Mobility Service en el servidor de origen para habilitar la protección.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Error 78007: No se pudo completar la operación solicitada.
Este error lo puede generar el servicio por varios motivos. Elija el error del proveedor correspondiente para seguir solucionando el problema.

* [Error 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Error 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Error 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Error 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Error 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Error 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Error 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Error 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Error 95105: No se pudo habilitar la protección (EP0856)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95105 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0856**. <br> La opción **Compartir archivos e impresoras** no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen.| **Compartir archivos e impresoras** no está habilitado. | Permita **Compartir archivos e impresoras** en el equipo de origen en el firewall de Windows. En el equipo de origen, en **Firewall de Windows** > **Permitir una aplicación o una característica a través de Firewall**, seleccione **Compartir archivos e impresoras para todos los perfiles**. </br> Además, compruebe los siguientes requisitos previos para finalizar correctamente la instalación de inserción.<br> Más información sobre la [solución de problemas de WMI](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Error 95107: No se pudo habilitar la protección (EP0858)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95107 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0858**. <br> Las credenciales proporcionadas para instalar Mobility Service son incorrectas o la cuenta de usuario no tiene privilegios suficientes. | Las credenciales de usuario que se proporcionan para instalar Mobility Service en la máquina de origen no son correctas. | Asegúrese de que las credenciales de usuario proporcionadas para la máquina de origen en el servidor de configuración son correctas. <br> Para agregar o modificar las credenciales del usuario, vaya al servidor de configuración y seleccione **Cspsconfigtool** > **Administrar cuenta**. </br> Además, compruebe los siguientes [requisitos previos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para finalizar correctamente la instalación de inserción.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Error 95117: No se pudo habilitar la protección (EP0865)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95117 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0865**. <br> La máquina de origen no está en ejecución o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen. | Problemas de conectividad de red entre el servidor de procesos y el servidor de origen. | Compruebe la conectividad entre el servidor de procesos y el servidor de origen. </br> Además, compruebe los siguientes [requisitos previos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para finalizar correctamente la instalación de inserción.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Error 95103: No se pudo habilitar la protección (EP0854)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95103 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0854**. <br> La opción Instrumental de administración de Windows (WMI) no se permite en la máquina de origen o hay problemas de conectividad de red entre el servidor de procesos y la máquina de origen.| WMI está bloqueado en Firewall de Windows. | Permita WMI en Firewall de Windows. En **Firewall de Windows** > **Permitir una aplicación o una característica a través del Firewall**, seleccione **WMI para todos los perfiles**. </br> Además, compruebe los siguientes [requisitos previos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) para finalizar correctamente la instalación de inserción.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Error 95213: No se pudo habilitar la protección (EP0874)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95213 </br>**Mensaje:** Error en la instalación de Mobility Service en la máquina de origen %SourceIP con el código de error **EP0874**. <br> | No se admite la versión del sistema operativo en la máquina de origen. <br>| Asegúrese de que se admita la versión del sistema operativo en la máquina de origen. Lea la [matriz de compatibilidad](https://aka.ms/asr-os-support). </br> Además, compruebe los siguientes [requisitos previos](https://aka.ms/pushinstallerror) para finalizar correctamente la instalación de inserción.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Error 95108: No se pudo habilitar la protección (EP0859)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95108 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen con el código de error **EP0859**. <br>| Las credenciales proporcionadas para instalar Mobility Service son incorrectas o la cuenta de usuario no tiene privilegios suficientes. <br>| Asegúrese de que las credenciales proporcionadas son las credenciales **raíz** de la cuenta. Para agregar o modificar las credenciales del usuario, vaya al servidor de configuración y seleccione el icono de acceso directo **Cspsconfigtool** del escritorio. Haga clic en **Administrar cuenta** para agregar o modificar las credenciales.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Error 95265: No se pudo habilitar la protección (EP0902)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95265 </br>**Mensaje:** La instalación de inserción de Mobility Service en la máquina de origen se realizó correctamente, pero la máquina de origen debe reiniciarse para que algunos cambios entren en vigor. <br>| Hay una versión antigua de Mobility Service instalada en el servidor.| La replicación de la máquina virtual continúa sin problemas.<br> Reinicie el servidor durante el siguiente periodo de mantenimiento para obtener las ventajas de las nuevas mejoras de Mobility Service.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Error 95224: No se pudo habilitar la protección (EP0883)

**Código de error** | **Causas posibles:** | **Recomendaciones específicas para el error**
--- | --- | ---
95224 </br>**Mensaje:** Error en la instalación de inserción de Mobility Service en la máquina de origen %SourceIP con el código de error **EP0883**. Hay pendiente un reinicio del sistema de una instalación o actualización anterior.| El sistema no se reinicia cuando se desinstala una versión anterior o incompatible de Mobility Service.| Asegúrese de que no exista ninguna versión de Mobility Service en el servidor. <br> Reinicie el servidor y vuelva a ejecutar el trabajo de habilitación de la protección|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Recursos para solucionar problemas de instalación de la inserción

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Solución de problemas de uso compartido de impresoras y archivos
* [Enable or disable File sharing with Group Policy](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx) (Habilitar o deshabilitar el uso compartido de archivos con directiva de grupo)
* [Habilitación del uso compartido de archivos e impresoras a través del firewall de Windows](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Solución de problemas de WMI
* [Basic WMI Testing](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) (Pruebas básicas de WMI)
* [WMI Troubleshooting](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx) (Solución de problemas de WMI)
* [Troubleshooting problems with WMI scripts and WMI Services](https://technet.microsoft.com/library/ff406382.aspx#H22) (Solución de problemas con los scripts y servicios de WMI)

## <a name="next-steps"></a>Pasos siguientes

[Aprenda cómo](tutorial-vmware-to-azure.md) configurar la recuperación ante desastres para máquinas virtuales de VMware.