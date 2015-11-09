<properties
	pageTitle="Preparación del entorno para realizar copias de seguridad de máquinas virtuales de Azure | Microsoft Azure"
	description="Asegúrese de que su entorno esté preparado para la copia de seguridad de máquinas virtuales de Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Preparación del entorno de copia de seguridad de máquinas virtuales de Azure
Antes de realizar la copia de seguridad de una máquina virtual de Azure, debe completar estos requisitos previos para preparar el entorno. Si ya lo ha hecho, puede empezar a realizar la [copia de seguridad de las máquinas virtuales](backup-azure-vms.md); en caso contrario, continúe con los pasos siguientes para asegurarse de que su entorno está preparado.

## 1\. Almacén de copia de seguridad
Para iniciar la copia de seguridad de sus máquinas virtuales de Azure, primero debe crear un almacén de copia de seguridad. Un almacén es una entidad que almacena todas las copias de seguridad y los puntos de recuperación que se han creado con el tiempo. El almacén contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales de una copia de seguridad.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure: ![Relaciones y entidades de copia de seguridad de Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/).

2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida**. Si tiene varias suscripciones asociadas a su cuenta profesional, elija la suscripción adecuada que se asociará al almacén de copia de seguridad. En cada suscripción de Azure puede tener varios almacenes de copia de seguridad para organizar las máquinas virtuales que se están protegiendo.

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. Esto debe ser único para cada suscripción.

4. En **Región**, seleccione la región geográfica del almacén. El almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en diferentes regiones, cree un almacén en cada uno de ellos. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlarán automáticamente.

    ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-prepare/creating-vault.png)

6. Un mensaje confirma que el almacén se ha creado correctamente y se mostrará en la página de servicios de recuperación como activo. Asegúrese de que se ha elegido la opción de redundancia de almacenamiento apropiada justo después de que se ha creado el almacén. Lea más sobre cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de copias de seguridad](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Haga clic en el almacén de copia de seguridad para ir a la página **Inicio rápido**, donde se muestran las instrucciones para realizar la copia de seguridad de las máquinas virtuales de Azure.

    ![Instrucciones de copia de seguridad de máquina virtual en la página del panel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\. Agente de máquina virtual de Azure
Antes de empezar a realizar la copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure esté instalado correctamente en la máquina virtual. Puesto que el agente de máquina virtual es un componente opcional en el momento de crearse la máquina virtual, asegúrese de que la casilla correspondiente al agente de máquina virtual esté activada antes de aprovisionar la máquina virtual.

Obtenga información acerca del [Agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y de [cómo instalarlo](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extensión de copia de seguridad
Para realizar la copia de seguridad de la máquina virtual, el servicio Copia de seguridad de Azure instala una extensión al agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

Si se ejecuta la máquina virtual, se instala la extensión de copia de seguridad. Una máquina virtual en ejecución también ofrece la mayor probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure continuará realizando una copia de seguridad de la máquina virtual, incluso si está desactivada y la extensión no se ha podido instalar (lo que se conoce también como máquina virtual sin conexión). En este caso, el punto de recuperación será *Coherente frente a bloqueos*, como se ha explicado anteriormente.

## 3\. Conexión de red
La extensión de copia de seguridad necesita conectividad a Internet para funcionar correctamente, puesto que envía comandos a un punto de conexión de Almacenamiento de Azure (dirección URL HTTP) para administrar las instantáneas de la máquina virtual. Sin conectividad a Internet, estas solicitudes HTTP de la máquina virtual agotarán el tiempo de espera y la operación de copia de seguridad dará error.

## Limitaciones

- No se admite la copia de seguridad de máquinas virtuales basadas en el Administrador de recursos de Azure (también conocido como IaaS V2).
- No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
- No se admite la copia de seguridad de máquinas virtuales con el almacenamiento Premium.
- No se admite la copia de seguridad de máquinas virtuales con varias direcciones IP reservadas.
- No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y ningún punto de conexión definido.
- No se admite la copia de seguridad de máquinas virtuales con varias NIC.
- No se admite la copia de seguridad de máquinas virtuales en una configuración con equilibrio de carga (interna o accesible desde Internet).
- No se admite el reemplazo de una máquina virtual existente durante la restauración. Primero, elimine la máquina virtual existente y los discos asociados y, a continuación, restaure los datos de copia de seguridad.
- No se admite la restauración y copia de seguridad entre regiones.
- La copia de seguridad de máquinas virtuales con el servicio Copia de seguridad de Azure se admite en todas las regiones públicas de Azure. Seguidamente se ofrece una [lista de comprobación](http://azure.microsoft.com/regions/#services) de las regiones admitidas. Si la región que está buscando hoy en día no es compatible, no aparecerá en la lista desplegable durante la creación del almacén.
- La copia de seguridad de máquinas virtuales con el servicio Copia de seguridad de Azure solo se admite en determinadas versiones de sistemas operativos:
  - **Linux**: la lista de distribuciones aprobadas por Azure está disponible [aquí](../virtual-machines-linux-endorsed-distributions.md). Otras distribuciones con la iniciativa «traiga su propio Linux» también deberían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual.
  - **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.
- La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes

- [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
- [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
- [Administrar copia de seguridad de máquina virtual](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->