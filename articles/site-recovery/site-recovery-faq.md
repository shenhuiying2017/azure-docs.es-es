<properties 
	pageTitle="Azure Site Recovery: preguntas más frecuentes" 
	description="Este artículo analiza las preguntas más frecuentes acerca del uso de Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="06/02/2015" 
	ms.author="lauraa"/>


# Azure Site Recovery: preguntas más frecuentes
## Información acerca de este artículo

En este artículo se incluyen las preguntas más frecuentes sobre Azure Site Recovery. Para obtener una introducción a los escenarios de implementación de Site Recovery y relacionados, consulte [Información general sobre Site Recovery](site-recovery-overview.md).

Si tiene alguna pregunta después de leer este artículo, publíquela en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## General
### ¿Es seguro ASR? ¿Qué datos se envían a Azure?

Sí, ASR es seguro. No intercepta los datos de la aplicación ni tiene información sobre lo que se ejecuta dentro de las máquinas virtuales. No se necesita conexión a Internet, ya sea en los hosts de Hyper-V o en las máquinas virtuales.

Puesto que la replicación se produce entre sus propios sitios de proveedor de servicios y empresariales, no se envían datos de aplicación a Azure. Solo se envían a Azure los metadatos, como los nombres de máquina virtual o la nube, que se necesitan para orquestar la conmutación por error. ASR no tiene la capacidad de interceptar los datos de su aplicación, y los datos siempre permanecen en local.

ASR está certificado según la norma ISO 27001:2005 y está completando sus evaluaciones de HIPAA, DPA y FedRAMP JAB.

### Los requisitos de cumplimiento requieren que incluso los metadatos de nuestros entornos locales permanezcan dentro de la misma región geográfica. ¿Puede asegurar ASR que se cumple este requisito?

Sí. Al crear un almacén de Site Recovery en una región de su elección, garantizamos que todos los metadatos que necesitamos para habilitar y orquestar la instalación de recuperación ante desastres permanecen dentro del límite geográfico de esa región.

### ¿Hay un SDK que pueda usar para automatizar el flujo de trabajo de ASR?

Sí. Los flujos de trabajo de ASR se pueden automatizar mediante la API de REST, PowerShell o Azure SDK. Puede encontrar más detalles en la publicación del blog titulado [Introducing PowerShell support for Azure Site Recovery](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/) (Introducción a la compatibilidad de PowerShell para Azure Site Recovery).

## Compatibilidad de versiones

### ¿Qué versiones de hosts y clústeres de Windows Server son compatibles?
Windows Server 2012 y Windows Server 2012 R2 pueden usarse cuando se elige la réplica de Hyper-V para permitir la replicación y protección entre sitios de Hyper-V.


### ¿Qué versiones de sistemas operativos invitados de Hyper-V son compatibles?
La lista más actualizada de los sistemas operativos invitados compatibles está disponible en el tema [Acerca de las máquinas virtuales y los sistemas operativos invitados](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### ¿Puedo configurar la protección de máquinas virtuales cuando se ejecuta Hyper-V en un sistema operativo cliente?

No puede configurar Hyper-V en ejecución en un sistema operativo cliente para replicar máquinas virtuales en Azure. La réplica de Hyper-V solo se admite en sistemas operativos de servidor. Puede replicar los equipos cliente físicos a Azure con [este artículo](site-recovery-vmware-to-azure.md).

### ¿ASR admite máquinas de generación 2?

ASR admite actualmente la replicación de máquinas virtuales de generación 2 de Hyper-V en Azure. ASR se convierte de la generación 2 a la generación 1 durante una conmutación por error. En la conmutación por recuperación, la máquina se convierte de nuevo a la generación 1. [Obtenga más información](http://azure.microsoft.com/updates/azure-site-recovery-supports-gen-2-vm-protection-in-west-us-north-europe-and-japan-west/) sobre la compatibilidad actual.


## Implementación entre sitios de proveedor de servicio 

### ¿Esta solución funciona para los modelos de infraestructura compartida o dedicada?
Sí, ASR admite ambos modelos de infraestructura, dedicados y compartidos.

### ¿Azure comparte la identidad de mi inquilino?
No. En realidad, los inquilinos tienen que acceder al portal de ASR. Solo el administrador del proveedor de servicio realiza acciones en el portal de ASR en Azure.

### ¿Recibirán mis inquilinos una factura de Azure?
No. La relación de facturación de Microsoft Azure continúa directamente con el proveedor de servicios. Los proveedores de servicios son responsables de generar facturas específicas para sus inquilinos.

### ¿Los datos de la aplicación de mis inquilinos llegarán a Azure?
Al utilizar un sitio propiedad del proveedor de servicios para la recuperación ante desastres, los datos de la aplicación nunca llegan a Azure. Los datos se cifran y se replican directamente entre los sitios de proveedor de servicios. Al usar Azure como sitio de recuperación ante desastres, los datos de la aplicación se envía a Azure. Los datos inactivos y en tránsito permanecen cifrados. También puede utilizar Internet público, ExpressRoute o VPN para establecer la conectividad a Azure.

### ¿Al utilizar Azure como un sitio de recuperación ante desastres, tenemos que ejecutar máquinas virtuales en Azure en todo momento?
No, ASR está diseñado como una solución DRaaS de nube pública y de primera clase. Los datos se replican a una cuenta de almacenamiento de Azure con redundancia geográfica en su propia suscripción. Al realizar una obtención de detalles de recuperación ante desastres o una conmutación por error real, ASR crea automáticamente las máquinas virtuales en su suscripción.

### ¿Se garantiza el aislamiento de nivel de inquilino cuando uso Azure como un sitio de recuperación ante desastres?
Sí.

### ¿Qué plataformas se admiten actualmente?
Admitimos el paquete de Azure, el sistema de plataforma en la nube e Hyper 2012 basado en System Center e implementaciones superiores. Para obtener más información acerca de la integración de ASR con el paquete de Azure, consulte [Configurar la protección para máquinas virtuales](https://technet.microsoft.com/library/dn850370.aspx).

### ¿También admite un paquete de Azure e implementaciones de servidor VMM individuales?
Sí, las implementaciones de SCVMM individuales se admiten para ambos escenarios: la recuperación ante desastres entre sitios del proveedor de servicios y la recuperación ante desastres entre un sitio del proveedor de servicios y Azure.

También se admite una implementación del paquete de Azure solo cuando se habilita la recuperación ante desastres entre sitios del proveedor de servicios; sin embargo, no está disponible para esta topología de integración de Runbook de ASR.

## Implementación entre sitios de Hyper-V y Azure (sin VMM)

### ¿ASR requiere habilitar VPN de sitio a sitio?
No, no es obligatorio; ASR funciona también a través de Internet público. Sin embargo, si ha configurado la VPN de sitio a sitio, podrá acceder a los errores en las máquinas virtuales de la misma manera que antes. Para obtener más información acerca de las consideraciones de red cuando se habilita la recuperación ante desastres en Azure, consulte el blog [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Configuración de la infraestructura de red para Microsoft Azure como un sitio de recuperación ante desastres).

### ¿Tengo que pagar algo además del gasto por la protección de ASR?
En estado estable, replicamos los cambios a Almacenamiento de Azure con redundancia geográfica y no es necesario pagar los gastos de la máquina virtual de IaaS de Azure (una ventaja considerable). Cuando se realiza una conmutación por error en Azure, ASR crea automáticamente las máquinas virtuales de IaaS de Azure, después de lo cual se factura por los recursos informáticos que se consumen en Azure.

### Tengo una sucursal donde no tengo instalado VMM. ¿Puedo habilitar la protección de las cargas de trabajo de la sucursal en Azure? ¿Cuáles son las ventajas principales de utilizar ASR para hacer eso?
Sí, puede usar ASR para proteger las máquinas virtuales que se ejecutan en los hosts de Hyper-V que no están administrados por SCVMM.

Cuando utilice ASR para administrar las necesidades de recuperación ante desastres de las sucursales, obtendrá una vista unificada de todas las cargas de trabajo de la sucursal en una ubicación central. También se obtiene la administración de la recuperación ante desastres y de la conmutación por error de un solo clic de todas las sucursales desde su oficina central, sin que lleguen a la sucursal. Actualmente, la asignación de redes y el cifrado en reposo no se admiten en la recuperación ante desastres de la sucursal.

### ¿Para las sucursales hay un límite en el número de máquinas virtuales que puedo proteger?
No. El soporte técnico permanece igual que en nuestro escenario de empresa.

### ¿Es necesario instalar un agente en las máquinas virtuales que deseo proteger?

No necesita ningún agente en las máquinas virtuales. Debe instalar el proveedor de Site Recovery y el agente de Servicios de recuperación en cada servidor de Hyper-V que ejecute las máquinas virtuales que desea proteger. Estos dos componentes se instalan con un único archivo de proveedor que puede descargarse durante la implementación de Site Recovery.

### ¿El proveedor que se ejecuta en el servidor de Hyper-V necesita alguna configuración de firewall especial?

No se requiere ninguna configuración específica. Los componentes de proveedor en el servidor se comunican a través de una conexión HTTPS con Azure y usan la configuración de Internet predeterminada en el servidor host de Hyper-V a menos que se especifique lo contrario.

### ¿El servidor de Hyper-V debe ser miembro del dominio?

No, el servidor no necesita estar en un dominio.

### ¿Qué versiones de hosts y clústeres de Windows Server son compatibles?
Se pueden usar Windows Server 2012 y Windows Server 2012 R2 cuando se utiliza la replicación de ASR e Hyper-V entre sitios de Hyper-V y Azure.

### ¿Qué versiones de sistemas operativos invitados son compatibles?
La lista más actualizada de los sistemas operativos invitados compatibles está disponible en el artículo [Acerca de las máquinas virtuales y los sistemas operativos invitados](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

## Implementación entre dos centros de datos VMM

### ¿Puedo replicar una máquina virtual que se ha replicado en un sitio secundario en Azure? 

No, no se admite este tipo de replicación encadenada.


## Implementación entre dos centros de datos VMM con SAN

### ¿Qué sucede si ya tengo configurada una replicación basada en SAN y no deseo cambiarla?
No hay problema. ASR admite el escenario en el que la replicación puede estar ya configurada, en cuyo caso puede aprovechar ASR para la orquestación de la recuperación ante desastres centrada en la máquina virtual, la configuración de las redes de recuperación ante desastres, los planes de recuperación compatible con aplicaciones, simulaciones de recuperación ante desastres y los requisitos de cumplimiento e informes de auditoría.


### ¿Necesito VMM? ¿Necesito VMM en ambos lados?
Sí. Necesitamos que la matriz de SAN la administre VMM mediante un proveedor de SMI-S específico de la matriz.

Admitimos las implementaciones individuales de HA de VMM en función del tipo de matriz, aunque la configuración recomendada es usar servidores VMM independientes para administrar los sitios.


### ¿Qué ocurre si no estoy seguro acerca de la administración de almacenamiento?
Trabajamos con la replicación existente configurada por el administrador de almacenamiento, lo que significa que el administrador de almacenamiento no necesita realizar ningún cambio en las matrices. Sin embargo, las organizaciones que desean automatizar la administración de almacenamiento de información a través de SCVMM también pueden aprovisionar el almacenamiento mediante ASR y VMM.

### ¿Se admite la replicación sincrónica? ¿Clústeres de invitado? ¿Almacenamiento compartido?
¡Sí, sí y sí!

### Red-red: ¿Qué necesito para instalarlo de forma local para que funcione?
Al utilizar la replicación basada en matriz (SAN) para habilitar la replicación y la protección entre sitios de Hyper-V, todo lo que necesita es instalar el proveedor de recuperación ante desastres de ASR en los servidores de SCVMM. Recuerde que también es el *único* host que necesita conectividad a Internet.


Las matrices también deben detectarse por SCVMM mediante un proveedor de SMI-S actualizado puesto a disposición por los proveedores de almacenamiento respectivos.

## Implementación entre servidores físicos y Azure

### ¿Puedo proteger mi servidor local físico en Azure?

Sí, puede replicar un servidor físico con ASR y ejecutarlo como máquina virtual en Azure en caso de que el servidor local deje de funcionar. La conmutación por recuperación a un servidor físico local no está admitida actualmente. Puede conmutar por recuperación a una máquina virtual que se ejecuta en Hyper-V o VMware.


## Conmutación por error

### ¿Qué ocurre si Azure está inactivo? ¿Se puede desencadenar una conmutación por error desde VMM?

Aunque Azure está diseñado para su resistencia de servicio, nos gusta planear lo peor. ASR ya se ha diseñado para la conmutación por error en un centro de datos de Azure secundario según el SLA de Azure. También garantizamos que aunque esto suceda, los metadatos y el almacén de ASR permanecerán dentro de la misma región geográfica en la que eligió el almacén.


### ¿Qué ocurre si mi ISP del centro de datos principal también experimenta una interrupción inesperada durante un desastre? ¿Qué ocurre si mi ISP del centro de datos secundario también experimenta una interrupción?

Puede usar el portal de ASR para realizar una conmutación por error no planeada con un solo clic. No se necesita conectividad del centro de datos principal para realizar la conmutación por error.

Es probable que las aplicaciones que deban ejecutarse en el centro de datos secundario después de una conmutación por error necesiten algún tipo de conectividad a Internet. ASR asume que, aunque el sitio primario y el ISP pueden verse afectados durante un desastre, el servidor SCVMM del sitio secundario seguirá conectado al ASR.

### ¿ASR admite la conmutación por recuperación?
Sí. La conmutación por recuperación desde Azure al sitio local es sencillo mediante el proceso de un solo clic a través del portal de ASR.

### ¿Es posible la conmutación por error automática?

No hay ninguna opción de conmutación por error automática. Puede presionar un botón único en el portal para desencadenar una conmutación por error o puede usar [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) para desencadenar una conmutación por error. Se usa Orchestrator u Operations Manager local para atestiguar un error de la máquina virtual y decidir luego si se desencadena la conmutación por error mediante el SDK. Esto hace que sea un proceso automático.




## Supervisión

### ¿Qué soluciones ofrece para que pueda supervisar la replicación en curso y la configuración?
Puede suscribirse a alertas de correo electrónico directamente desde el servicio de ASR para recibir notificaciones acerca de los problemas que necesitan atención. También puede supervisar el mantenimiento de la replicación en curso mediante SCOM.

## Pasos siguientes

Para empezar a implementar ASR:

- [Configuración de la protección entre un sitio de VMM local y Azure](site-recovery-vmm-to-azure.md) 
- [Configuración de la protección entre un sitio de Hyper-V local y Azure](site-recovery-hyper-v-site-to-azure.md) 
- [Configuración de la protección entre dos sitios VMM locales](site-recovery-vmm-to-vmm.md) 
- [Configuración de la protección entre dos sitios de VMM locales con SAN](site-recovery-vmm-san.md) 
- [Configuración de la protección con un único servidor VMM](site-recovery-single-vmm.md) 

 

<!---HONumber=58_postMigration-->