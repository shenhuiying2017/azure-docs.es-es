---
title: "Corrección de errores de Azure Backup en el portal clásico | Microsoft Docs"
description: "Solución de problemas de Azure Backup y restauración de máquinas virtuales de Azure en el portal clásico."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solución de problemas de copia de seguridad de máquinas virtuales de Azure
> [!div class="op_single_selector"]
> * [Almacén de Recovery Services](backup-azure-vms-troubleshoot.md)
> * [Almacén de Backup](backup-azure-vms-troubleshoot-classic.md)
>
>

Puede solucionar los errores detectados al usar Copia de seguridad de Azure con la información incluida en la tabla siguiente.

## <a name="discovery"></a>Detección
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Detección |Se ha producido un error al detectar elementos nuevos: Copia de seguridad de Microsoft Azure ha detectado un error interno. Espere unos minutos y vuelva a intentar la operación. |Después de 15 minutos, vuelva a intentar el proceso de detección. |
| Detección |Se ha producido un error al detectar elementos nuevos: ya hay otra operación de detección en curso. Espere hasta que se haya completado la operación de detección. |None |

## <a name="register"></a>Registro
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Registro |El número de discos de datos asociados a la máquina virtual sobrepasa el límite admitido. Desconecte algunos discos de datos de esta máquina virtual y vuelva a intentarlo. Copia de seguridad de Azure admite hasta 16 discos de datos conectados a una máquina virtual de Azure para realizar una copia de seguridad. |None |
| Registro |Copia de seguridad de Microsoft Azure encontró un error interno. Espere unos minutos y vuelva a intentarlo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. |Este error puede deberse a una de las siguientes configuraciones no admitidas de máquina virtual en Premium LRS. <br> Se puede realizar copia de seguridad de las máquinas virtuales de almacenamiento premium mediante el almacén de servicios de recuperación. [Más información](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| Registro |Se ha producido un error en el registro porque se ha agotado el tiempo de espera de la operación de instalación del agente. |Compruebe si se admite la versión del sistema operativo de la máquina virtual. |
| Registro |Error en la ejecución de comando: hay otra operación en curso en este elemento. Espere hasta que se complete la operación anterior. |None |
| Registro |No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad |None |
| Registro |El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. |[Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## <a name="backup"></a>Copia de seguridad
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Copia de seguridad |No se pudo comunicar con el agente de máquina virtual para ver el estado de la instantánea. Tiempo de espera de la subtarea de máquina virtual de instantánea. Consulte la Guía de solución de problemas sobre cómo resolver este problema. |Este error se produce si hay un problema con el agente de la máquina virtual o cuando el acceso de red a la infraestructura de Azure está bloqueado por algún motivo. Aprenda más sobre cómo [depurar los problemas de las instantáneas de la máquina virtual](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Si el agente de la máquina virtual no está causando problemas, reinicie la máquina virtual. A veces, un estado incorrecto de la máquina virtual puede causar problemas, y reiniciar la máquina virtual restablece este "mal estado" |
| Copia de seguridad |Error interno en la copia de seguridad. Vuelva a intentar la operación en unos minutos. Si el problema persiste, póngase en contacto con el Servicio técnico de Microsoft. |Compruebe si hay un problema transitorio en el acceso al almacenamiento de máquinas virtuales. Compruebe el [estado de Azure](https://azure.microsoft.com/en-us/status/) para ver si hay algún problema recurrente relacionado con los procesos, el almacenamiento o la red en la región. Vuelva a intentarlo para ver si el problema se ha solucionado. |
| Copia de seguridad |No se pudo realizar la operación debido a que no existe la máquina virtual. |No se puede realizar la copia de seguridad, ya que se ha eliminado la máquina virtual configurada para dicha copia de seguridad. Detenga las copias de seguridad adicionales; para ello vaya a la vista de elementos protegidos, seleccione el elemento protegido y haga clic en Detener la protección. Puede conservar los datos seleccionando la opción correspondiente. Más adelante, puede reanudar la protección de esta máquina virtual haciendo clic en Configurar la protección en la vista de elementos registrados. |
| Copia de seguridad |No se pudo instalar la extensión de los Servicios de recuperación de Azure en el elemento seleccionado. El agente de la máquina virtual es un requisito previo para la extensión de los servicios de recuperación de Azure. Instale el agente de la máquina virtual de Azure y reinicie el funcionamiento del registro. |<ol> <li>Compruebe si el agente de la máquina virtual se ha instalado correctamente. <li>Asegúrese de que la marca de configuración de la máquina virtual se haya establecido correctamente.</ol> [Obtenga más información](#validating-vm-agent-installation) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |
| Copia de seguridad |Error en la ejecución del comando: otra operación está actualmente en curso en este elemento. Espere hasta que se complete la operación anterior y vuelva a intentarlo. |Se está ejecutando una copia de seguridad existente o un trabajo de restauración para la máquina virtual. No se puede iniciar un nuevo trabajo mientras se está ejecutando otro. |
| Copia de seguridad |Error de instalación de la extensión "COM+ no pudo realizar la conexión con MS DTC (Microsoft Distributed Transaction Coordinator)" |Normalmente, esto significa que el servicio COM+ no se ejecuta. Para obtener ayuda acerca de cómo solucionar este problema, póngase en contacto con el servicio de soporte técnico de Microsoft. |
| Copia de seguridad |Error en la operación de instantánea con el error de operación de VSS "El Cifrado de unidad BitLocker está bloqueando esta unidad" Debe desbloquear esta unidad en el Panel de Control. |Desactive BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema VSS |
| Copia de seguridad |No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad |None |
| Copia de seguridad |Máquina virtual de Azure no encontrada. |Esto sucede cuando se elimina la máquina virtual principal, pero la directiva de copia de seguridad continúa buscando una máquina virtual para realizar la copia de seguridad. Para solucionar este error:  <ol><li>Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre del servicio en la nube], <br>O BIEN <li> Desactive la protección para esta máquina virtual para que no se desencadenen las copias de seguridad subsiguientes. </ol> |
| Copia de seguridad |El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. |[Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## <a name="jobs"></a>Trabajos
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Cancelar trabajo |No se admite la cancelación para este tipo de trabajo; espere hasta que finalice el trabajo. |None |
| Cancelar trabajo |El trabajo no está en un estado que se pueda cancelar; espere hasta que finalice el trabajo. <br>OR<br> El trabajo seleccionado no está en un estado en que se pueda cancelar; espere hasta que finalice el trabajo. |Con toda probabilidad, el trabajo ya está casi completado; espere hasta que finalice. |
| Cancelar trabajo |No se puede cancelar el trabajo porque no está en curso; solo se admite la cancelación de trabajos que están en curso. Intente cancelar un trabajo que esté en curso. |Esto sucede debido a un estado transitorio. Espere un momento y vuelva a intentar la operación de cancelación |
| Cancelar trabajo |No se pudo cancelar el trabajo; espere hasta que finalice. |None |

## <a name="restore"></a>Restauración
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Restauración |Error en la restauración con error interno de nube |<ol><li>El servicio de nube que está intentando restaurar está configurado con la configuración de DNS. Puede consultar  <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si hay una dirección configurada, significa que se ha configurado DNS.<br> <li>El servicio en la nube al que está tratando restaurar está configurado con la IP reservada y las máquinas virtuales del servicio en la nube tienen un estado de detenido.<br>Puede comprobar que un servicio en la nube tiene una IP reservada utilizando los siguientes cmdlets de PowerShell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Está intentando restaurar una máquina virtual con las siguientes configuraciones de red especiales en el mismo servicio en la nube. <br>- Máquinas virtuales con la configuración del equilibrador de carga (interna y externa)<br>- Máquinas virtuales con varias direcciones IP reservadas<br>- Máquinas virtuales con varias NIC<br>Seleccione un nuevo servicio en la nube en la interfaz de usuario o consulte las [consideraciones de restauración](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations) de las máquinas virtuales con las configuraciones de red especiales</ol> |
| Restauración |El nombre DNS seleccionado ya existe: especifique otro nombre DNS y vuelva a intentarlo. |El nombre DNS aquí hace referencia al nombre del servicio en la nube (normalmente terminados con .cloudapp.net). Debe ser único. Si se produce este error, deberá elegir otro nombre para la máquina virtual durante la restauración. <br><br> Este error solo se muestra a los usuarios del Portal de Azure. La operación de restauración a través de PowerShell se realizará correctamente porque solo restaura los discos y no crea la máquina virtual. El error aparecerá cuando el usuario crea explícitamente la máquina virtual después de la operación de restauración del disco. |
| Restauración |La configuración de red virtual especificada no es correcta: especifique otra configuración de red virtual y vuelva a intentarlo. |None |
| Restauración |El servicio en la nube especificado usa una dirección IP reservada, que no coincide con la configuración de la máquina virtual que se está restaurando: especifique otro servicio en la nube que no use la IP reservada o elija otro punto de recuperación desde el que restaurar. |None |
| Restauración |El Servicio en la nube alcanzó el límite del número de puntos de entrada finales, vuelva a intentar la operación mediante la especificación de otro servicio en la nube o mediante un extremo existente. |None |
| Restauración |La cuenta de almacenamiento de destino y el almacén de copia de seguridad están en dos regiones diferentes: compruebe que la cuenta de almacenamiento especificada en la operación de restauración se encuentra en la misma región de Azure que el almacén de copia de seguridad. |None |
| Restauración |La cuenta de almacenamiento especificada para la operación de restauración no se admite: solo se admiten las cuentas de almacenamiento básicas o estándar con una configuración de réplica con redundancia local o redundancia geográfica. Seleccione una cuenta de almacenamiento compatible |None |
| Restauración |El tipo de cuenta de almacenamiento especificado para la operación de restauración no está en línea: asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración está en línea |Esto puede suceder debido a un error transitorio en Azure Storage o debido a una interrupción. Elija otra cuenta de almacenamiento. |
| Restauración |Se alcanzó la cuota del grupo de recursos: elimine algunos grupos de recursos desde el portal de Azure o póngase en contacto con el soporte técnico de Azure para aumentar los límites. |None |
| Restauración |La subred seleccionada no existe: seleccione una subred que exista |None |

## <a name="policy"></a>Directiva
| Operación | Detalles del error | Solución alternativa |
| --- | --- | --- |
| Creación de directiva |No se pudo crear la directiva: reduzca las opciones de retención para continuar con la configuración de directiva. |None |

## <a name="vm-agent"></a>Agente de máquina virtual
### <a name="setting-up-the-vm-agent"></a>Configuración del agente de la máquina virtual
Normalmente, el agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tienen instalado el agente de la máquina virtual. Para dichas máquinas virtuales, el agente de la máquina virtual debe instalarse explícitamente. Lea más sobre cómo [instalar el agente de la máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para las máquinas virtuales de Windows:

* Descargue e instale el [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación.
* [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado.

Máquinas virtuales de Linux:

* Instale el [Agente de Linux](https://github.com/Azure/WALinuxAgent) desde Github.
* [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado.

### <a name="updating-the-vm-agent"></a>Actualización del agente de la máquina virtual
Para las máquinas virtuales de Windows:

* Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sin embargo, deberá asegurarse de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual.

Máquinas virtuales de Linux:

* Siga las instrucciones proporcionadas en [Actualización del agente de máquina virtual Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>Validación de la instalación del agente de la máquina virtual
Cómo comprobar la versión del agente de la máquina virtual en máquinas virtuales de Windows:

1. Inicie sesión en la máquina virtual de Azure y vaya a la carpeta *C:\WindowsAzure\Packages*. El archivo WaAppAgent.exe debe estar ahí.
2. Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior.
