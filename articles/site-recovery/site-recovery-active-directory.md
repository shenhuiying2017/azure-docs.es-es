---
title: "Protección de Active Directory y DNS con Azure Site Recovery | Microsoft Docs"
description: "Este artículo describe cómo implementar una solución de recuperación ante desastres para Active Directory con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: 5db2d424c176428d31fb99fd8288120f18fcac7a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protección de Active Directory y DNS con Azure Site Recovery
Las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de DNS y AD para funcionar correctamente. Cuando se crea una solución de recuperación ante desastres para aplicaciones, a menudo necesitará recuperar Active Directory y DNS antes que otros componentes de la aplicación para asegurar la correcta funcionalidad de la aplicación.

Con Site Recovery, puede crear un plan completo de recuperación ante desastres automatizada para Active Directory. En el momento en el que se produce una interrupción, puede iniciar la conmutación por error en segundos desde cualquier lugar y hacer que Active Directory vuelva a funcionar en unos minutos. Si ha implementado Active Directory para varias aplicaciones, como SAP y SharePoint, en el sitio principal y desea conmutar por error el sitio completo, puede conmutar por error Active Directory primero mediante Site Recovery y luego conmutar por error las demás aplicaciones con los planes de recuperación específicos de la aplicación.

En este artículo se explica cómo puede crear una solución de recuperación ante desastres para Active Directory, cómo realizar conmutaciones por error con un plan de recuperación de un solo clic, las configuraciones admitidas y los requisitos previos.  Debe estar familiarizado con Active Directory y Azure Site Recovery antes de empezar.

## <a name="prerequisites"></a>requisitos previos
* Un almacén de Recovery Services en una suscripción de Microsoft Azure.
* Si va a replicar en Azure, [prepare](tutorial-prepare-azure.md) los recursos de Azure, entre los que se incluyen una suscripción a Azure, una red virtual de Azure y una cuenta de almacenamiento.
* Revise los requisitos de compatibilidad de todos los componentes.

## <a name="replicating-domain-controller"></a>Replicación de controlador de dominio

Debe configurar la [replicación de Site Recovery](#enable-protection-using-site-recovery) como mínimo en una máquina virtual que hospede el controlador de dominio y DNS. Si tiene [varios controladores de dominio](#environment-with-multiple-domain-controllers) en su entorno, además de replicar la máquina virtual del controlador de dominio con Site Recovery, tendrá que instalar un [controlador de dominio adicional](#protect-active-directory-with-active-directory-replication) en el sitio de destino (Azure o un centro de datos local secundario).

### <a name="single-domain-controller-environment"></a>Entorno con un solo controlador de dominio
Si tiene pocas aplicaciones y un solo controlador de dominio y desea conmutar por error todo el sitio, se recomienda utilizar Site Recovery para replicar el controlador de dominio en el sitio de destino (Azure o un centro de datos local secundario). Se puede usar la misma máquina virtual del controlador de dominio/DNS replicada para la [conmutación por error de prueba](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Entorno con varios controladores de dominio
Si tiene muchas aplicaciones y hay más de un controlador de dominio en el entorno o si planea realizar una conmutación por error de pocas aplicaciones a la vez, además de replicar la máquina virtual del controlador de dominio con Site Recovery, es recomendable que configure un [controlador de dominio adicional](#protect-active-directory-with-active-directory-replication) en el sitio de destino (Azure o un centro de datos secundario local). Para la [conmutación por error de prueba](#test-failover-considerations), puede usar el controlador de dominio replicado por Site Recovery y para la conmutación por error, el controlador de dominio adicional en el sitio de destino.

## <a name="enable-protection-using-site-recovery"></a>Habilitación de la protección con Site Recovery
### <a name="protect-the-virtual-machine"></a>Protección de la máquina virtual
Habilite la protección de la máquina virtual de DNS y del controlador de dominio en Site Recovery. El controlador de dominio que se haya mediante Site Recovery se utiliza para la [conmutación por error de prueba](#test-failover-considerations). Asegúrese de que cumple los requisitos siguientes:

1. El controlador de dominio es un servidor de catálogo global
2. El controlador de dominio debe ser el propietario del rol FSMO para los roles que serán necesarios durante la conmutación por error de prueba (de lo contrario, estos roles deberán [asumirse](http://aka.ms/ad_seize_fsmo) después de la conmutación por error)

### <a name="configure-virtual-machine-network-settings"></a>Configuración de los valores de red de la máquina virtual
Para la máquina virtual del controlador de dominio o DNS, configure los valores de red en la configuración de Proceso y red de la máquina virtual replicada en Site Recovery. Esto sirve para asegurarse de que la máquina virtual se conectará a la red adecuada después de la conmutación por error.

## <a name="protect-active-directory-with-active-directory-replication"></a>Protección de Active Directory con la replicación de Active Directory
### <a name="site-to-site-protection"></a>Protección de sitio a sitio
Cree un controlador de dominio en el sitio secundario. Al promocionar el servidor a un rol de controlador de dominio, especifique el mismo nombre del dominio del sitio principal. Puede usar el complemento **Servicios y sitios de Active Directory** para configurar los valores en el objeto de vínculo del sitio al que se agregan los sitios. Al configurar los valores en un vínculo de sitio, puede controlar cuándo se produce la replicación entre dos o más sitios y con qué frecuencia se produce. Para más información, consulte [Programación de la replicación entre sitios](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Protección del sitio en Azure
Siga estas instrucciones para [crear un controlador de dominio en una red virtual de Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Al promocionar el servidor a un rol de controlador de dominio, especifique el mismo nombre de dominio del sitio principal.

A continuación [vuelva a configurar el servidor DNS para la red virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)para usar el servidor DNS en Azure

![Red de Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Protección de Azure en Azure
Siga estas instrucciones para [crear un controlador de dominio en una red virtual de Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Al promocionar el servidor a un rol de controlador de dominio, especifique el mismo nombre de dominio del sitio principal.

A continuación [vuelva a configurar el servidor DNS para la red virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)para usar el servidor DNS en Azure

## <a name="test-failover-considerations"></a>Consideraciones sobre la conmutación por error de prueba
La conmutación por error de prueba se produce en una red que está aislada de la red de producción para evitar que afecte a las cargas de trabajo de producción.

La mayoría de las aplicaciones también requieren la presencia de un controlador de dominio y un servidor DNS para funcionar. Por lo tanto, antes de conmutar por error una aplicación, tiene que crearse un controlador de dominio en la red aislada que se usará para la conmutación por error de prueba. La manera más fácil de hacerlo es replicar una máquina virtual de controlador/DNS de dominio con Site Recovery. A continuación, ejecute una conmutación por error de prueba de la máquina virtual del controlador de dominio antes de ejecutar una del plan de recuperación para la aplicación. Así es cómo debe hacerlo:

1. [Replique](site-recovery-replicate-vmware-to-azure.md) la máquina virtual de DNS y del controlador de dominio en Site Recovery.
2. Cree una red aislada. Cualquier red virtual que se cree en Azure de forma predeterminada está aislada de otras redes. Se recomienda que el intervalo de dirección IP para esta red sea el mismo que el de la red de producción. No habilite la conectividad de sitio a sitio en esta red.
3. Proporcione una dirección IP de DNS en la red que se ha creado, como la dirección IP que se espera que la máquina virtual DNS obtenga. Si realiza la replicación en Azure, indique la dirección IP de la máquina virtual que se usa en la conmutación por error en la opción **IP de destino** en **Proceso y red** de la máquina virtual replicada.

    ![Red de prueba de Azure](./media/site-recovery-active-directory/azure-test-network.png)

> [!TIP]
> Site Recovery intenta crear máquinas virtuales de prueba en una subred del mismo nombre y con la misma dirección IP que se proporcionó en la opción **Proceso y red** de la máquina virtual. Si no hay disponible una subred con el mismo nombre en la red virtual de Azure proporcionada para la conmutación por error de prueba, la máquina virtual de prueba se crea en la primera subred por orden alfabético. Si la dirección IP de destino forma parte de la subred seleccionada, Site Recovery trata de crear la máquina de virtual de la conmutación por error de prueba con la dirección IP de destino. Si la dirección IP de destino no forma parte de la subred seleccionada, la máquina virtual de la conmutación por error de prueba se creará con la siguiente dirección IP disponible en la subred elegida.
>
>


1. Si realiza la replicación en otro sitio local y usa DHCP, siga las instrucciones para la [configuración de DNS y DHCP para la conmutación por error de prueba](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
2. Pruebe la conmutación por error de la máquina virtual del controlador de dominio en la red aislada. Use el último punto de recuperación **coherente de aplicación** disponible de la máquina virtual del controlador de dominio para realizar la conmutación por error de prueba.
3. Ejecute una conmutación por error de prueba para el plan de recuperación que contiene las máquinas virtuales de la aplicación.
4. Una vez finalizada la prueba, realice una **Cleanup test failover** (Limpieza de conmutación por error de prueba) en la máquina virtual del controlador de dominio. En este paso se elimina el controlador de dominio que se creó para la conmutación por error de prueba.


### <a name="removing-reference-to-other-domain-controllers"></a>Eliminación de la referencia a otros controladores de dominio
Al realizar una conmutación por error de prueba, no se incluirán todos los controladores de dominio en la red de prueba. Para quitar la referencia a los demás controladores de dominio que existan en su entorno de producción, puede que tenga que [asumir los roles FSMO de Active Directory](http://aka.ms/ad_seize_fsmo) y realizar una [limpieza de metadatos](https://technet.microsoft.com/library/cc816907.aspx) para los controladores de dominio que falten.



> [!IMPORTANT]
> Algunas de las configuraciones que se describen en la sección siguiente no son las configuraciones de controlador de dominio estándares o predeterminadas. Si no desea realizar estos cambios en un controlador de dominio de producción, a continuación, puede crear un controlador de dominio específico para la conmutación por error de prueba de Site Recovery y realizar estos cambios en él.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problemas debidos a las medidas de seguridad de la virtualización

A partir de Windows Server 2012, [se han integrado medidas de seguridad adicionales en Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Estas medidas de seguridad ayudan a proteger los controladores de dominio virtualizados frente a la reversión de USN, siempre y cuando la plataforma de hipervisor subyacente admita VM-GenerationID. Azure admite VM-GenerationID, lo que significa que los controladores de dominio que ejecutan Windows Server 2012 o posterior en máquinas virtuales disponen de las medidas de seguridad adicionales.


Cuando se restablece el VM-GenerationID, también se restablece el invocationID de la base de datos de AD DS, se descarta el grupo RID y SYSVOL se marca como no autoritativo. Para más información, consulte [Introducción a la virtualización de Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) y [Safely Virtualizing DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/) (Virtualización segura de DFSR).

La conmutación por error a Azure puede producir el restablecimiento de VM-GenerationID y que se tomen medidas de seguridad adicionales al iniciarse la máquina virtual del controlador de dominio en Azure. Esto puede dar lugar a un **retraso importante** para que el usuario pueda iniciar sesión en la máquina virtual del controlador de dominio. Puesto que este controlador de dominio se utilizaría solo en una conmutación por error de prueba, las medidas de seguridad de virtualización no son necesarias. Para asegurarse de que no cambia el VM-GenerationID de la máquina virtual del controlador de dominio, puede cambiar el valor de DWORD siguiente a 4 en el controlador de dominio local.


        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start


#### <a name="symptoms-of-virtualization-safeguards"></a>Síntomas de las medidas de seguridad de la virtualización

Si se han tomado medidas de seguridad de virtualización después de una conmutación por error de prueba, podría ver uno o varios de los síntomas siguientes:  

Cambio del Id. de generación

![Cambio del Id. de generación](./media/site-recovery-active-directory/Event2170.png)

Cambio del Id. de invocación

![Cambio del Id. de invocación](./media/site-recovery-active-directory/Event1109.png)

Recursos compartidos de Sysvol y Netlogon no disponibles

![Recurso compartido Sysvol](./media/site-recovery-active-directory/sysvolshare.png)

![Ntfrs Sysvol](./media/site-recovery-active-directory/Event13565.png)

Bases de datos DFSR eliminadas

![Base de datos de DFSR eliminada](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Algunas de las configuraciones que se describen en la sección siguiente no son las configuraciones de controlador de dominio estándares o predeterminadas. Si no desea realizar estos cambios en un controlador de dominio de producción, a continuación, puede crear un controlador de dominio específico para la conmutación por error de prueba de Site Recovery y realizar estos cambios en él.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Solución de los problemas de controlador de dominio durante la conmutación por error de prueba


En un símbolo del sistema, ejecute el siguiente comando para comprobar si las carpetas SYSVOL y NETLOGON están compartidas:

    NET SHARE

En el símbolo del sistema, ejecute el siguiente comando para asegurarse de que el controlador de dominio funciona correctamente.

    dcdiag /v > dcdiag.txt

En el registro de salida, busque el siguiente texto para confirmar que el controlador de dominio está funcionando bien.

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

Si se cumplen las condiciones anteriores, es probable que el controlador de dominio funcione correctamente. En caso contrario, realice estos pasos.


* Realice una restauración autoritativa del controlador de dominio.
    * Aunque [no se recomienda usar la replicación FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), si todavía la usa, siga los pasos indicados [aquí](https://support.microsoft.com/kb/290762) para realizar una restauración autoritativa. [Aquí](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/) obtendrá más información acerca de Burflags, mencionado en el vínculo anterior.
    * Si usa la replicación DFSR, siga los pasos descritos [aquí](https://support.microsoft.com/kb/2218556) para realizar una restauración autoritativa. También puede usar las funciones de Powershell disponibles [aquí](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

* Para omitir el requisito de sincronización inicial, establezca la siguiente clave del registro en 0 en el controlador de dominio local. Si esta DWORD no existe, créela en el nodo "Parameters". Puede leer más sobre este tema [aquí](https://support.microsoft.com/kb/2001093).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Deshabilite el requisito de que haya un servidor de catálogo global disponible para validar el inicio de sesión de usuario; para ello, establezca la siguiente clave del registro en 1 en el controlador de dominio local. Si esta DWORD no existe, créela en el nodo "Lsa". Puede leer más sobre este tema [aquí](http://support.microsoft.com/kb/241789).

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS y controlador de dominio en equipos diferentes
Si el DNS no está en la misma máquina virtual que el controlador de dominio, tendrá que crear una máquina virtual de DNS para la conmutación por error de prueba. Si están en la misma máquina virtual, puede omitir esta sección.

Puede utilizar un servidor DNS nuevo y crear todas las zonas necesarias. Por ejemplo, si el dominio de Active Directory es contoso.com, puede crear una zona DNS con el nombre contoso.com. Las entradas correspondientes a Active Directory se deben actualizar en DNS de la forma siguiente:

1. Asegúrese de que está establecida esta configuración antes de incluir cualquier otra máquina virtual en el plan de recuperación:

   * El nombre de la zona depende del nombre de raíz del bosque.
   * La zona debe estar respaldada por archivos.
   * La zona debe habilitarse para actualizaciones seguras y no seguras.
   * La resolución de la máquina virtual del controlador de dominio debe apuntar a la dirección IP de la máquina virtual DNS.
2. Ejecute el siguiente comando en el directorio de la máquina virtual del controlador de dominio:

    `nltest /dsregdns`
3. Agregue una zona en el servidor DNS, permita actualizaciones no seguras y agregue una entrada para ello en DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>pasos siguientes
[Más información](site-recovery-workload.md) sobre la protección de cargas de trabajo empresariales con Azure Site Recovery.
