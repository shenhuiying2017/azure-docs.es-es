<properties
	pageTitle="Copia de seguridad de máquinas virtuales de Azure: solución de problemas | Microsoft Azure"
	description="Obtenga información para solucionar problemas de copia de seguridad y restaurar la máquina virtual de Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="aashishr"/>


# Solución de errores
Puede solucionar los errores detectados al usar Copia de seguridad de Azure con la información incluida en la tabla siguiente.

## Detección

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Detección | Se ha producido un error al detectar elementos nuevos: Copia de seguridad de Microsoft Azure ha detectado un error interno. Espere unos minutos y vuelva a intentar la operación. | Después de 15 minutos, vuelva a intentar el proceso de detección.
| Detección | Se ha producido un error al detectar elementos nuevos: ya hay otra operación de detección en curso. Espere hasta que se haya completado la operación de detección. | None |

## Registro
| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Registro | El rol de VM de Azure no está en un estado para instalar la extensión: compruebe si la máquina virtual está en estado de ejecución. La extensión de Servicios de recuperación de Azure requiere que la máquina virtual se esté ejecutando. | Inicie la máquina virtual y cuando esté en estado en ejecución, vuelva a intentar la operación de registro.|
| Registro | El número de discos de datos asociados a la máquina virtual sobrepasa el límite admitido. Desconecte algunos discos de datos de esta máquina virtual y vuelva a intentarlo. Copia de seguridad de Azure admite hasta 16 discos de datos conectados a una máquina virtual de Azure para realizar una copia de seguridad. | None |
| Registro | Copia de seguridad de Microsoft Azure encontró un error interno. Espere unos minutos y vuelva a intentarlo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. | Este error puede deberse a una de las siguientes configuraciones no admitidas: <ol><li>LRS Premium <li>Varias NIC <li>Equilibrador de carga </ol> |
| Registro | Se ha producido un error en el registro porque se ha agotado el tiempo de espera de la operación de instalación del agente. | Compruebe si se admite la versión del sistema operativo de la máquina virtual. |
| Registro | Error en la ejecución de comando: hay otra operación en curso en este elemento. Espere hasta que se complete la operación anterior. | None |
| Registro | No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad | None |
| Registro | El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. | [Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## Copia de seguridad

| Operación de copia de seguridad | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Copia de seguridad | Al copiar discos duros virtuales desde el almacén de copia de seguridad se agotó el tiempo de espera. Vuelva a intentar la operación en unos minutos. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. | Esto ocurre cuando hay demasiados datos que copiar. Compruebe si tiene menos de 16 discos de datos. |
| Copia de seguridad | No se pudo comunicar con el agente de VM para el estado de la instantánea. Tiempo de espera agotado para esta subtarea de instantánea de máquina virtual: consulte la Guía de solución de problemas acerca de cómo resolver este problema. | Este error se produce si hay un problema con el agente de la máquina virtual o cuando el acceso de red a la infraestructura de Azure está bloqueado por algún motivo. <ul><li>Obtenga información acerca de la [depuración de problemas de agente de máquina virtual](#vm-agent) <li>Obtenga información acerca de la [depuración de problemas de red](#networking) </ul><br>Si el agente de VM no está causando problemas, reinicie la máquina virtual. A veces, un estado incorrecto de la máquina virtual puede causar problemas, y reiniciar la máquina virtual restablece este "mal estado" |
| Copia de seguridad | Error interno en la copia de seguridad. Vuelva a intentar la operación en unos minutos. Si el problema persiste, póngase en contacto con el Servicio técnico de Microsoft. | Este error puede aparecer por 2 razones: <ol><li> Hay demasiados datos que copiar. <li>Se ha eliminado la máquina virtual original y, por tanto, no se puede realizar la copia de seguridad. Para mantener los datos de copia de seguridad de una máquina virtual eliminada y, a la vez, detener los errores de copia de seguridad, desproteja la máquina virtual y elija la opción para conservar los datos. Este comando detiene la programación de la copia de seguridad y también los mensajes de error recurrentes. |
| Copia de seguridad | No se pudo instalar la extensión de los Servicios de recuperación de Azure en el elemento seleccionado. El agente de la máquina virtual es un requisito previo para la extensión de los servicios de recuperación de Azure. Instale el agente de la máquina virtual de Azure y reinicie el funcionamiento del registro. | <ol> <li>Compruebe si el agente de la máquina virtual se ha instalado correctamente. <li>Asegúrese de que la marca de configuración de la máquina virtual se haya establecido correctamente.</ol> [Obtenga más información](#validating-vm-agent-installation) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |
| Copia de seguridad | Error en la ejecución del comando: otra operación está actualmente en curso en este elemento. Espere hasta que se complete la operación anterior y vuelva a intentarlo. | Se está ejecutando una copia de seguridad existente o un trabajo de restauración para la máquina virtual. No se puede iniciar un nuevo trabajo mientras se está ejecutando otro. |
| Copia de seguridad | Error de instalación de la extensión "COM+ no pudo realizar la conexión con MS DTC (Microsoft Distributed Transaction Coordinator)" | Normalmente, esto significa que el servicio COM+ no se ejecuta. Para obtener ayuda acerca de cómo solucionar este problema, póngase en contacto con el servicio de soporte técnico de Microsoft. |
| Copia de seguridad | Error en la operación de instantánea con el error de operación de VSS "El Cifrado de unidad BitLocker está bloqueando esta unidad" Debe desbloquear esta unidad en el Panel de Control. | Desactive BitLocker para todas las unidades de la máquina virtual y observe si se resuelve el problema VSS |
| Copia de seguridad | No se pueden usar máquinas virtuales con discos duros virtuales almacenados en Almacenamiento Premium para realizar copias de seguridad | None |
| Copia de seguridad | No se admite la copia de seguridad de una máquina virtual con una configuración de equilibrador de carga. | None |
| Copia de seguridad | No se admite la copia de seguridad de una máquina virtual con varias tarjetas de interfaz de red. | None |
| Copia de seguridad | Máquina virtual de Azure no encontrada. | Esto sucede cuando se elimina la máquina virtual principal, pero la directiva de copia de seguridad continúa buscando una máquina virtual para realizar la copia de seguridad. Para corregir este error: <ol><li>Vuelva a crear la máquina virtual con el mismo nombre y el mismo nombre de grupo de recursos [nombre del servicio en la nube,] <br>(O BIEN) <li> Desactive la protección para esta máquina virtual para que no se creen los trabajos de copia de seguridad </ol> |
| Copia de seguridad | El agente de máquina virtual no está presente en la máquina virtual: instale los requisitos previos necesarios, el agente de máquina virtual y reinicie la operación. | [Obtenga más información](#vm-agent) acerca del agente de la máquina virtual y sobre cómo validar su instalación. |

## Trabajos
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Cancelar trabajo | No se admite la cancelación para este tipo de trabajo; espere hasta que finalice el trabajo. | None |
| Cancelar trabajo | El trabajo no está en un estado que se pueda cancelar; espere hasta que finalice el trabajo. <br>O BIEN<br> El trabajo seleccionado no está en un estado que se pueda cancelar; espere hasta que finalice el trabajo.| Con toda probabilidad, el trabajo ya está casi completado; espere hasta que finalice el trabajo. |
| Cancelar trabajo | No se puede cancelar el trabajo porque no está en curso; solo se admite la cancelación de trabajos que están en curso. Intente cancelar un trabajo que esté en curso. | Esto sucede debido a un estado transitorio. Espere un momento y vuelva a intentar la operación de cancelación |
| Cancelar trabajo | No se pudo cancelar el trabajo; espere hasta que finalice el trabajo. | None |


## Restauración
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Restauración | Error en la restauración con error interno de nube | <ol><li>El servicio de nube que está intentando restaurar está configurado con la configuración de DNS. Puede consultar <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si hay una dirección configurada, significa que los ajustes de DNS están configurados.<br> <li>El servicio de nube que está intentando restaurar está configurado con ReservedIP, y las VM existentes en el servicio de nube están detenidas.<br>Puede comprobar que un servicio de nube tiene IP reservada usando los siguientes cmdlets de Powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |
| Restauración | El nombre DNS seleccionado ya existe: especifique otro nombre DNS y vuelva a intentarlo. | El nombre DNS aquí hace referencia al nombre del servicio en la nube (normalmente terminados con .cloudapp.net). Debe ser único. Si se produce este error, deberá elegir otro nombre para la máquina virtual durante la restauración. <br><br> Tenga en cuenta que este error solo se muestra a los usuarios del portal de Azure. La operación de restauración a través de PowerShell se realizará correctamente porque solo restaura los discos y no crea la máquina virtual. El error aparecerá cuando el usuario crea explícitamente la máquina virtual después de la operación de restauración del disco. |
| Restauración | La configuración de red virtual especificada no es correcta: especifique otra configuración de red virtual y vuelva a intentarlo. | None |
| Restauración | El servicio en la nube especificado usa una dirección IP reservada, que no coincide con la configuración de la máquina virtual que se está restaurando: especifique otro servicio en la nube que no use la IP reservada o elija otro punto de recuperación desde el que restaurar. | None |
| Restauración | El Servicio en la nube alcanzó el límite del número de puntos de entrada finales, vuelva a intentar la operación mediante la especificación de otro servicio en la nube o mediante un extremo existente. | None |
| Restauración | La cuenta de almacenamiento de destino y el almacén de copia de seguridad están en dos regiones diferentes: compruebe que la cuenta de almacenamiento especificada en la operación de restauración se encuentra en la misma región de Azure que el almacén de copia de seguridad. | None |
| Restauración | La cuenta de almacenamiento especificada para la operación de restauración no se admite: solo se admiten las cuentas de almacenamiento básicas o estándar con una configuración de réplica con redundancia local o redundancia geográfica. Seleccione una cuenta de almacenamiento compatible | None |
| Restauración | El tipo de cuenta de almacenamiento especificado para la operación de restauración no está en línea: asegúrese de que la cuenta de almacenamiento especificada en la operación de restauración está en línea | Esto puede suceder debido a un error transitorio en el almacenamiento de Azure o debido a una interrupción. Elija otra cuenta de almacenamiento. |
| Restauración | Se alcanzó la cuota del grupo de recursos: elimine algunos grupos de recursos desde el portal de vista previa o póngase en contacto con el servicio de soporte técnico de Azure para aumentar los límites. | None |
| Restauración | La subred seleccionada no existe: seleccione una subred que exista | None |


## Directiva
| Operación | Detalles del error | Solución alternativa |
| -------- | -------- | -------|
| Creación de directiva | No se pudo crear la directiva: reduzca las opciones de retención para continuar con la configuración de directiva. | None |


## Agente de máquina virtual de Azure

### Configuración del agente de la máquina virtual
Normalmente, el agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tienen instalado el agente de la máquina virtual. Para dichas máquinas virtuales, el agente de la máquina virtual debe instalarse explícitamente. Lea más sobre cómo [instalar el agente de la máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para las máquinas virtuales de Windows:

- Descargue e instale el [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación.
- [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado.


### Actualización del agente de la máquina virtual
Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sin embargo, deberá asegurarse de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual.


### Validación de la instalación del agente de la máquina virtual
Cómo comprobar la versión del agente de la máquina virtual en máquinas virtuales de Windows:

1. Inicie sesión en la máquina virtual de Azure y vaya a la carpeta *C:\\WindowsAzure\\Packages*. El archivo WaAppAgent.exe debe estar ahí.
2. Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior.

## Redes
Al igual que todas las extensiones, la de copia de seguridad necesita tener acceso a Internet para funcionar. Si no tiene acceso a Internet, se pueden producir estos problemas:

- Se puede producir un error en la instalación de la extensión.
- Se puede producir un error en las operaciones de copia de seguridad (como al realizar la instantánea de disco).
- Se puede producir un error en la operación para mostrar el estado de la operación de copia de seguridad.

La necesidad de resolver direcciones públicas de Internet se describe [aquí](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Deberá comprobar las configuraciones de DNS para la red virtual y asegurarse de que se puedan resolver los URI de Azure.

Una vez que la resolución de nombres se haya realizado correctamente, también hay que proporcionar acceso a las direcciones IP de Azure. Para desbloquear el acceso a la infraestructura de Azure, siga estos pasos:

1. Obtenga la lista de [IP del centro de datos de Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) que van a formar parte de la lista de direcciones IP aprobadas.
2. Desbloquee las direcciones IP usando el commandlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx). Ejecute este commandlet en la máquina virtual de Azure, en una ventana de PowerShell con privilegios elevados (realice la ejecución como administrador).

<!---HONumber=September15_HO1-->