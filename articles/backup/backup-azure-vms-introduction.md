<properties
	pageTitle="Introducción a la copia de seguridad de máquina virtual de Azure | Microsoft Azure"
	description="Una introducción a la copia de seguridad de máquinas virtuales en Azure mediante el servicio de copia de seguridad de Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="trinadhk";"aashishr";"jimpark"/>

# Copia de seguridad de máquina virtual de Azure

Esta sección proporciona una introducción del uso de copia de seguridad de Microsoft Azure para proteger máquinas virtuales de Azure. Al leerla, aprenderá acerca de:

- Cómo funciona la copia de seguridad de máquinas virtuales de Azure
- El procedimiento para hacer copia de seguridad de máquinas virtuales de Azure
- Los requisitos previos para lograr una experiencia fluida de la copia de seguridad
- Los errores típicos y cómo resolverlos
- La lista de escenarios no admitidos y cómo afectan los cambios en el producto

Para más información acerca de las máquinas virtuales de Azure, vea la [documentación de máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/).

## ¿Por qué realizar la copia de seguridad de las máquinas virtuales de Azure?
La informática en nube permite a las aplicaciones ejecutarse en un entorno escalable y altamente disponible, que es el motivo por el cual Microsoft desarrolló las máquinas virtuales de Azure. Los datos generados a partir de estas máquinas virtuales de Azure son importantes y necesitan someterse a copia de seguridad por motivos de seguridad. Los escenarios típicos que requieren datos para restaurar a partir de copias de seguridad son:

- Eliminación accidental o malintencionada de archivos
- Daños en la máquina virtual durante una actualización de revisión
- Eliminación accidental o malintencionada de toda la máquina virtual

Los datos de estas máquinas virtuales pueden someterse a copia de seguridad de dos maneras diferentes:

- Copia de seguridad de los orígenes de datos individuales desde dentro de la máquina virtual
- Copia de seguridad de toda la máquina virtual

La copia de seguridad de toda la máquina virtual es popular porque es mucho más fácil de administrar y facilita una fácil restauración del sistema operativo y toda la aplicación. La Copia de seguridad de Azure puede utilizarse para la copia de seguridad de datos en el invitado o de la máquina virtual completa.

Las ventajas empresariales del uso de la Copia de seguridad de Azure para la copia de seguridad de máquinas virtuales son:

- Automatización de los flujos de trabajo de copia de seguridad y recuperación para las máquinas virtuales.
- Las copias de seguridad coherentes con la aplicación para asegurarse de que los datos se recuperan se inician a partir de un estado coherente.
- No se requiere tiempo de inactividad durante la copia de seguridad de la máquina virtual.
- Se pueden realizar copias de seguridad de máquinas virtuales de Windows o de Linux.
- Los puntos de recuperación están disponibles para una restauración sencilla en el almacén de copia de seguridad de Azure.
- Recopilación automática de elementos no utilizados y de eliminación de puntos de recuperación antiguos.

## ¿Cómo funciona la copia de seguridad de máquinas virtuales de Azure?
Para hacer una copia de seguridad de una máquina virtual, primero se necesita una instantánea de los datos en un momento específico. El servicio de Copia de seguridad de Azure inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión para tomar una instantánea de copia de seguridad. La extensión de copia de seguridad se coordina con el servicio VSS en el invitado para conseguir coherencia y llama a la API de instantánea de blob del servicio de almacenamiento de Azure cuando se alcanza la coherencia. Esto se hace para obtener una instantánea coherente de los discos de la máquina virtual, sin tener que apagarla.

Una vez tomada la instantánea, los datos se transfieren por el servicio de Copia de seguridad de Azure al almacén de copia de seguridad. El servicio se encarga de identificar y transferir solo los bloques que han cambiado desde la última copia de seguridad, lo que hace que el almacenamiento de copias de seguridad sea eficaz. Una vez completada la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación. Este punto de recuperación se puede ver en el Portal de administración de Azure.

![Arquitectura de copia de seguridad de máquinas virtuales de Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Para las máquinas virtuales de Linux, solo es posible la copia de seguridad coherente con archivos.

## Calcular instancias protegidas
Las máquinas virtuales de Azure sometidas a copia de seguridad mediante la Copia de seguridad de Azure estarán sujetas a los [precios de Copia de seguridad de Azure](http://azure.microsoft.com/pricing/details/backup/). El cálculo de instancias protegidas se basa en el tamaño *real* de la máquina virtual, que es la suma de todos los datos de la máquina virtual, excepto el disco de recursos. *No* se le facturará en función del tamaño máximo admitido para cada disco de datos conectado a la máquina virtual, sino de los datos reales almacenados en el disco de datos. De forma similar, los requisitos de almacenamiento de copia de seguridad se basan en la cantidad de datos almacenados con copias de seguridad de Azure, que es la suma de los datos reales de cada punto de recuperación.

Por ejemplo, veamos una máquina virtual de tamaño estándar A2 con dos discos de datos adicionales con un tamaño máximo de 1 TB cada uno. La tabla siguiente proporciona los datos almacenados en cada uno de estos discos:

|Tipo de disco|Tamaño máximo|Datos reales presentes|
|---------|--------|------|
| Disco del sistema operativo | 1023 GB | 17 GB |
| Disco local o disco de recursos | 135 GB | 5GB (no incluidos para la copia de seguridad) |
| Disco de datos 1 |	1023 GB | 30 GB |
| Disco de datos 2 | 1023 GB | 0 GB |

El tamaño *real* en este caso es el tamaño de la máquina virtual de 17 GB + 30 GB + 0 GB = 47 GB. Esto se convierte en el tamaño de instancia protegida en el que se basa la factura mensual. A medida que crece la cantidad de datos en la máquina virtual, el tamaño de instancia protegida usado para la facturación también cambiará en consecuencia.

La facturación no se inicia hasta que se complete la primera copia de seguridad correcta. En este punto, se iniciará la facturación de almacenamiento y las instancias protegidas. La facturación continúa mientras haya *datos de cualquier copia de seguridad almacenados con la Copia de seguridad de Azure* para la máquina virtual. Al realizar la operación de suspender la protección no se detiene la facturación si se conservan los datos de copia de seguridad. La facturación de una máquina virtual especificada se suspenderá solo si se ha detenido la protección *y* se eliminan los datos de copia de seguridad. Cuando no hay ningún trabajo de copia de seguridad activo (cuando se ha detenido la protección), el tamaño de la máquina virtual en el momento de la última copia de seguridad correcta se convierte en el tamaño de instancia protegida en el que se basa la factura mensual.

## Requisitos previos
### 1\. Almacén de copia de seguridad
Para iniciar la copia de seguridad de sus máquinas virtuales de Azure, deberá crear primero un almacén de copia de seguridad. El almacén es una entidad que almacena todas las copias de seguridad y los puntos de recuperación que se han creado con el tiempo. El almacén contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales de una copia de seguridad.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure:![Relaciones y entidades de copia de seguridad de Azure](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Para crear un almacén de copia de seguridad

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/).

2. Haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida**. Si tiene varias suscripciones asociadas a su cuenta profesional, elija la suscripción adecuada que se asociará al almacén de copia de seguridad. En cada suscripción de Azure puede tener varios almacenes de copia de seguridad para organizar las máquinas virtuales que se están protegiendo.

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. Esto debe ser único para cada suscripción.

4. En **Región**, seleccione la región geográfica del almacén. Tenga en cuenta que el almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en diferentes regiones, cree un almacén en cada uno de ellos. No es necesario especificar cuentas de almacenamiento para almacenar datos de copia de seguridad: el almacén de copia de seguridad y el servicio de Copia de seguridad de Azure lo controlarán automáticamente. ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

5. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal. ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-introduction/creating-vault.png)

6. Un mensaje confirma que el almacén se ha creado correctamente y se mostrará en la página de servicios de recuperación como activo. Asegúrese de que se ha elegido la opción de redundancia de almacenamiento apropiada justo después de que se ha creado el almacén. Obtenga más información sobre cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](../backup-azure-backup-create-vault.md#storage-redundancy-options). ![Lista de copias de seguridad](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Al hacer clic en el almacén de copia de seguridad, se dirige a la página **Inicio rápido**, donde se muestran las instrucciones para la copia de seguridad de máquinas virtuales de Azure. ![Instrucciones de copia de seguridad de máquina virtual en la página del panel](./media/backup-azure-vms-introduction/vmbackup-instructions.png)


### 2\. Agente de máquina virtual de Azure
Antes de empezar a realizar copias de seguridad de la máquina virtual de Azure, asegúrese de que el agente de VM de Azure está instalado correctamente en la máquina virtual. Para realizar la copia de seguridad de la máquina virtual, el servicio Copia de seguridad de Azure instala una extensión para el agente de VM. Puesto que el agente de VM es un componente opcional en el momento que se crea la máquina virtual, deberá asegurarse de que está activada la casilla para el agente de VM antes de que se aprovisione la máquina virtual.

Obtenga información acerca del [Agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y de [cómo instalarlo](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Limitaciones

- No se admite la copia de seguridad de máquinas virtuales basadas en el Administrador de recursos de Azure (también conocido como IaaS V2).
- No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
- No se admite la copia de seguridad de máquinas virtuales con el almacenamiento Premium.
- No se admite la copia de seguridad de máquinas virtuales con varias direcciones IP reservadas.
- No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y ningún extremo definido.
- No se admite la copia de seguridad de máquinas virtuales mediante varios NIC o en una configuración con equilibrio de carga.
- No se admite el reemplazo de una máquina virtual existente durante la restauración. Primero, elimine la máquina virtual existente y los discos asociados y, a continuación, restaure los datos de copia de seguridad.
- No se admite la restauración y copia de seguridad entre regiones.
- La copia de seguridad de máquinas virtuales con el servicio Copia de seguridad de Azure se admite en todas las regiones públicas de Azure. Seguidamente se ofrece una [lista de comprobación](http://azure.microsoft.com/regions/#services) de las regiones admitidas. Si la región que está buscando hoy en día no es compatible, no aparecerá en la lista desplegable durante la creación del almacén.
- La copia de seguridad de máquinas virtuales con el servicio de Copia de seguridad de Azure solo se admite en determinadas versiones de sistemas operativos:
  - **Linux**: la lista de distribuciones aprobadas por Azure está disponible [aquí](../virtual-machines-linux-endorsed-distributions.md). Otras distribuciones con la iniciativa «traiga su propio Linux» también deberían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual.
  - **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.
- La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#multiple-dcs)

Si hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes
Para empezar a usar la copia de seguridad de máquina virtual, aprenda cómo:

- [Realizar copia de seguridad de máquinas virtuales](backup-azure-vms.md)
- [Restauración de máquinas virtuales](backup-azure-restore-vms.md)
- [Administrar copia de seguridad de máquina virtual](backup-azure-manage-vms.md)

<!---HONumber=Sept15_HO3-->