---
title: "Replicación de una aplicación web basada en IIS de niveles múltiples con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se explica cómo se replican las máquinas virtuales de una granja de servidores web de IIS con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: cff6a7502e80eb4ff447cc99fe31b48cb660c27e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Replicación de una aplicación web basada en IIS de niveles múltiples con Azure Site Recovery

## <a name="overview"></a>Información general


El software de las aplicaciones es el motor que mantiene la productividad del negocio. En una organización, puede haber diversas aplicaciones web con diferentes propósitos. Algunas de ellas, como las aplicaciones financieras, los programas de procesamiento de nóminas y los sitios web destinados a los clientes, pueden resultar extremadamente cruciales para la organización. Por este motivo, es muy importante que estén operativas y en ejecución en todo momento para que la productividad no se vea afectada y, lo que es más importante, para que no se dañe la imagen de marca de la organización.

Las aplicaciones web que resultan esenciales suelen configurarse como aplicaciones de niveles múltiples: Web, base de datos y aplicación. Además de abarcar diferentes niveles, las aplicaciones también pueden utilizar diferentes servidores en cada nivel para equilibrar la carga del tráfico. Por otra parte, las asignaciones entre los diferentes niveles y el servidor web deben basarse en direcciones IP estáticas. Si se produce una conmutación por error, algunas de estas asignaciones deberán actualizarse, especialmente si hay varios sitios web configurados en el servidor web. En el caso de las aplicaciones web que utilizan SSL, deberán actualizarse los enlaces de certificados.

En los métodos de recuperación tradicionales que no utilizan la replicación, es necesario realizar copias de seguridad de diferentes archivos de configuración, opciones del Registro, enlaces, componentes personalizados (COM o .NET), contenidos y certificados, así como recuperar los archivos a través de procedimientos manuales. Estas técnicas resultan engorrosas, suelen generar errores y no son escalables. Por ejemplo, es fácil que olvide realizar copias de seguridad de los certificados y que no le quede otra opción que comprar nuevos certificados para el servidor después de que se produzca una conmutación por error.

Una buena solución de recuperación ante desastres debería permitir que se pudieran desarrollar planes de recuperación para este tipo de arquitecturas de aplicaciones complejas que se describieron anteriormente. También deberían brindar la posibilidad de agregar pasos personalizados para administrar las asignaciones de la aplicación entre diferentes niveles, así como proporcionar una solución confiable de un solo clic en caso de que un desastre produjera un descenso del RTO.


En este artículo se explica cómo proteger una aplicación web basada en IIS mediante [Azure Site Recovery](site-recovery-overview.md). También se describen los procedimientos recomendados para replicar en Azure una aplicación web basada en IIS de tres niveles, cómo llevar a cabo la recuperación ante desastres y cómo conmutar por error la aplicación en Azure.


## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, no olvide informarse sobre las cuestione siguientes:

1. [Replicación de una máquina virtual en Azure](site-recovery-vmware-to-azure.md)
1. [Diseño de una red de recuperación](site-recovery-network-design.md)
1. [Realización de una conmutación por error de prueba en Azure](./site-recovery-test-failover-to-azure.md)
1. [Ejecución de una conmutación por error en Azure](site-recovery-failover.md)
1. [Replicación de un controlador de dominio](site-recovery-active-directory.md)
1. [Replicación de SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modelos de implementación
Por lo general, las aplicaciones web basadas en IIS se ajustan a uno de los siguientes modelos de implementación:

**Modelo de implementación 1 ** Granja de servidores web de IIS con Enrutamiento de solicitud de aplicaciones (ARR), IIS Server y Microsoft SQL Server.

![Modelo de implementación](./media/site-recovery-iis/deployment-pattern1.png)

**Modelo de implementación 2** Granja de servidores web basada IIS con Enrutamiento de solicitud de aplicaciones (ARR), IIS Server, servidor de aplicaciones y Microsoft SQL Server.


![Modelo de implementación](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

Para elaborar este artículo, se utilizaron máquinas virtuales de VMware con IIS Server versión 7.5 en Windows Server 2012 R2 Enterprise. Como la replicación de recuperación del sitio no depende de la aplicación, se pretende que las recomendaciones que se brindan aquí sirvan tanto para los escenarios que se describen a continuación como para las diferentes versiones de IIS.

### <a name="source-and-target"></a>Origen y destino

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | No | Sí
**Las tablas de Azure**|N/D|Sí

## <a name="replicate-virtual-machines"></a>Replicación de máquinas virtuales

Siga [estas directrices](site-recovery-vmware-to-azure.md) para comenzar a replicar en Azure todas las máquinas virtuales de la granja de servidores web de IIS.

Si utiliza una dirección IP estática, especifique la dirección IP que desea utilizar con la máquina virtual en la opción [**IP de destino**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) de la configuración de Compute y Network.

![IP de destino](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Creación de un plan de recuperación

Los planes de recuperación permiten secuenciar la conmutación por error de los diferentes niveles de una aplicación de niveles múltiples y, por tanto, mantener la coherencia de la aplicación. Siga los pasos que se indican a continuación cuando cree un plan de recuperación para una aplicación web de múltiples niveles.  [Aprenda más sobre la creación de un plan de recuperación](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Incorporación de máquinas virtuales a grupos de conmutación por error
Por lo general, una aplicación web de IIS de niveles múltiples se compone de un nivel de base de datos, con máquinas virtuales SQL; del nivel web, compuesto por un servidor IIS, y del nivel de la aplicación. Tal y como se describe a continuación, debe agregar todas estas máquinas virtuales en un grupo diferente según el nivel al que pertenecen. [Aprenda más sobre cómo personalizar los planes de recuperación](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Cree un plan de recuperación. Agregue las máquinas virtuales del nivel de base de datos en Group 1 (Grupo 1) para asegurarse de que es lo último que se desconecta y lo primero que se activa.

1. Agregue las máquinas virtuales del nivel de aplicación en Group 2 (Grupo 2) para que se activen después del nivel de base de datos.

1. Agregue las máquinas virtuales del nivel web en Group 3 (Grupo 3) para que se activen después del nivel de aplicación.

1. Agregue las máquinas virtuales de equilibrio de carga en Group 4 (Grupo 4) para que se activen después del nivel web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación
Es posible que tenga que realizar algunas operaciones en las máquinas virtuales de Azure tras la conmutación por error (real o de prueba) para conseguir que la granja de servidores web de IIS funcione correctamente. Puede automatizar las operaciones posteriores a la conmutación por error, como la actualización de entradas DNS, la modificación de los enlaces del sitio o la modificación de la cadena de conexión, incorporando los scripts correspondientes en el plan de recuperación, tal y como se detalla a continuación. [Aprenda más sobre la incorporación de scripts al plan de recuperación](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Actualización de DNS
Si el DNS está configurado para que se actualice de forma dinámica, por lo general, las máquinas virtuales lo actualizarán con la nueva dirección IP al iniciarse. Si desea incorporar un paso explícito para actualizar DNS con las nuevas direcciones IP de las máquinas virtuales, agregue este [script para actualizar direcciones IP en DNS](https://aka.ms/asr-dns-update) como una acción posterior a los grupos del plan de recuperación.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadena de conexión del archivo web.config de una aplicación
La cadena de conexión especifica la base de datos con la que se comunica el sitio web.

Si la cadena de conexión contiene el nombre de la máquina virtual de la base de datos, no será necesario realizar ningún otro paso tras la conmutación por error y la aplicación podrá comunicarse automáticamente con la base de datos. Asimismo, si la dirección IP de la máquina virtual de la base de datos no se modifica, no será necesario actualizar la cadena de conexión. Si la cadena de conexión hace referencia a la máquina virtual de la base de datos a través de una dirección IP, será necesario actualizarla después de realizar la conmutación por error. Por ejemplo, la siguiente cadena de conexión apunta a la base de datos con la dirección IP 127.0.1.2.

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Puede actualizar la cadena de conexión en el nivel web agregando un [script para actualizar la conexión de IIS](https://aka.ms/asr-update-webtier-script-classic) después de Group 3 (Grupo 3) en el plan de recuperación.

#### <a name="site-bindings-for-the-application"></a>Enlaces del sitio en la aplicación
Todos los sitios contienen información sobre los enlaces, como el tipo de enlace, la dirección IP en la que el servidor IIS escucha las solicitudes del sitio, el número de puertos y los nombres de host del sitio. En el momento en que se produce la conmutación por error, puede ser necesario actualizar estos enlaces si se ha modificado la dirección IP que tienen asociada.

> [!NOTE]
>
> Si marcó la opción ‘Todas sin asignar’ para el enlace del sitio como en el ejemplo siguiente, no tendrá que actualizar este enlace después de la conmutación por error. Asimismo, si después de la conmutación por error no se modificó la dirección IP asociada al sitio, no será necesario actualizar el enlace del sitio (el que la dirección IP se mantenga depende de la arquitectura de red y de las subredes asignadas a los sitios principales y de recuperación; por tanto, su viabilidad dependerá de cómo sea su organización).

![Enlace SSL](./media/site-recovery-iis/sslbinding.png)

Si la dirección IP está asociada a un sitio, tendrá que actualizar todos los enlaces del sitio con la nueva dirección IP. Puede agregar un [script para actualizar el nivel web de IIS](https://aka.ms/asr-web-tier-update-runbook-classic) detrás de Group 3 (Grupo 3) en el plan de recuperación para cambiar los enlaces del sitio.


#### <a name="update-load-balancer-ip-address"></a>Actualización de la dirección IP del equilibrador de carga
Si tiene una máquina virtual de Enrutamiento de solicitud de aplicaciones, agregue un [script de conmutación por error de ARR para IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) detrás de Group 4 (Grupo 4) para actualizar la dirección IP.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Enlace de certificado SSL de una conexión https
Websites puede tener un certificado SSL asociado que les ayude a garantizar una comunicación segura entre el servidor web y el explorador del usuario. Si el sitio web tiene una conexión https y una enlace de sitio https asociado a la dirección IP del servidor IIS con un enlace de certificado SSL, tras la conmutación por error, será necesario agregar un nuevo enlace de sitio para el certificado con la dirección IP de la máquina virtual IIS.

El certificado SSL puede emitirse con arreglo a:

a) El nombre de dominio completo del sitio web<br>
b) El nombre del servidor<br>
c) Un certificado comodín para el nombre de dominio<br>
d) Una dirección IP: si el certificado SSL se emite con arreglo a la dirección IP del servidor IIS, deberá emitirse otro certificado SSL con arreglo a la dirección IP del servidor IIS del sitio de Azure y será necesario crear otro enlace SSL para este certificado. Por tanto, se recomienda no utilizar certificados SSL emitidos con arreglo a la dirección IP. Esta es una opción que apenas se usa y que pronto quedará en desuso a tenor de los nuevos cambios de CA/Browser Forum.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Actualización de la dependencia entre el nivel web y el nivel de aplicación
Si tiene una dependencia concreta de la aplicación basada en la dirección IP de las máquinas virtuales, tendrá que actualizarla tras la conmutación por error.

## <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba
Siga [estas directrices](site-recovery-test-failover-to-azure.md) para llevar a cabo una conmutación por error de prueba.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
1.  Haga clic en el plan de recuperación creado para la granja de servidores web de IIS.
1.  Haga clic en 'Probar conmutación por error'.
1.  Seleccione un punto de recuperación y una red virtual de Azure para iniciar el proceso de conmutación por error de prueba.
1.  Cuando el entorno secundario esté activo, podrá realizar las validaciones.
1.  Una vez que se hayan completado las validaciones, puede seleccionar ‘Validations complete’ (Validaciones completas) para que el entono de la conmutación por error de prueba quede limpio.

## <a name="doing-a-failover"></a>Realización de una conmutación por error
Siga [estas directrices](site-recovery-failover.md) cuando realice una conmutación por error.

1.  Vaya a Azure Portal y seleccione el almacén de Recovery Services.
1.  Haga clic en el plan de recuperación creado para la granja de servidores web de IIS.
1.  Haga clic en 'Conmutación por error'.
1.  Seleccione el punto de recuperación en el que se va a iniciar el proceso de conmutación por error.

## <a name="next-steps"></a>Pasos siguientes
Puede obtener más información sobre la [replicación de otras aplicaciones](site-recovery-workload.md) a través de Site Recovery.
