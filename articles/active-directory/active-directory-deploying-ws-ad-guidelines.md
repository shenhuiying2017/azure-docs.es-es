---
title: Instrucciones para implementar Windows Server Active Directory en Azure Virtual Machines | Microsoft Azure
description: "Si sabe cómo implementar servicios de dominio de Active Directory y los servicios de federación de AD de forma local, aprenda cómo funcionan en máquinas virtuales de Azure."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 342d9e2787add3d04f1b744152e135db98848179
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Directrices para implementar Windows Server Active Directory en máquinas virtuales de Microsoft Azure
Este artículo explica las importantes diferencias entre implementar Windows Server Active Directory Domain Services (ADDS) y Servicios de federación de Active Directory (ADFS) localmente en comparación con la implementación en máquinas virtuales de Microsoft Azure.

## <a name="scope-and-audience"></a>Ámbito y audiencia
El artículo está destinado a los que ya tienen experiencia en la implementación de Active Directory de forma local. Cubre las diferencias que hay entre la implementación de Active Directory en redes o máquinas virtuales de Microsoft Azure y las implementaciones de Active Directory locales tradicionales. Las máquinas y redes virtuales de Azure forman parte de una oferta de infraestructura como servicio (IaaS) pensada para que las organizaciones aprovechen los recursos informáticos en la nube.

Si no está familiarizado con la implementación de AD, consulte la [Guía de implementación de AD DS](https://technet.microsoft.com/library/cc753963) o el artículo [Planear la implementación de AD FS según corresponda](https://technet.microsoft.com/library/dn151324.aspx).

En este artículo se supone que el lector está familiarizado con los conceptos siguientes:

* Implementación y administración de Windows Server AD DS
* Implementación y configuración de DNS para que admita una infraestructura de Windows Server AD DS
* Implementación y administración de Windows Server AD FS
* Implementación, configuración y administración de aplicaciones de usuarios de confianza (sitios y servicios web) que pueden usar tokens de Windows Server AD FS
* Conceptos generales sobre las máquinas virtuales como, por ejemplo, cómo configurar una máquina virtual, discos virtuales y redes virtuales

Este artículo resalta los requisitos necesarios para un escenario de implementación híbrido en el que Windows Server AD DS o AD FS se implementan en parte de forma local y en parte en máquinas virtuales de Azure. En el documento se explican en primer lugar las diferencias más importantes entre ejecutar Windows Server AD DS y AD FS en máquinas virtuales de Azure en comparación con la implementación en el entorno local, y analiza también importantes elementos de decisión que afectan al diseño y la implementación. El resto del artículo ofrece instrucciones para cada uno de los puntos de decisión más detalladamente y explica cómo aplicarlas a diferentes escenarios de implementación.

Este artículo no explica la configuración de [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), un servicio basado en REST que ofrece funcionalidad de administración de identidades y control de acceso para las aplicaciones en la nube. Sin embargo, Azure Active Directory (Azure AD) y Windows Server AD DS están diseñados para trabajar conjuntamente para proporcionar una solución de administración de identidades y acceso para entornos híbridos de TI y aplicaciones modernas. Para ayudar a comprender las diferencias y relaciones entre Windows Server AD DS y Azure AD, tenga en cuenta lo siguiente:

1. Puede ejecutar Windows Server AD DS en la nube en máquinas virtuales si utiliza Azure para extender el centro de datos local en la nube.
2. Puede usar Azure Active Directory para proporcionar a los usuarios un inicio de sesión único en aplicaciones de software como servicio (SaaS). Por ejemplo, Microsoft Office 365 usa esta tecnología y las aplicaciones que se ejecutan en Azure u otras plataformas en la nube también pueden usarla.
3. Puede utilizar Azure AD (el Servicio de control de acceso) para permitir que los usuarios inicien sesión, mediante identidades de Facebook, Google, Microsoft y otros proveedores de identidades, en aplicaciones hospedadas en la nube o de forma local.

Para más información sobre estas diferencias, consulte el artículo [Aspectos básicos de la administración de identidades de Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados
Puede descargar y ejecutar la herramienta de [evaluación de preparación de máquinas virtuales de Azure](https://www.microsoft.com/download/details.aspx?id=40898). La evaluación inspeccionará automáticamente el entorno local y generará un informe personalizado basado en las instrucciones que encontrará en este tema para ayudarle a migrar el entorno a Azure.

Se recomienda que revise también en primer lugar los tutoriales, guías y vídeos que tratan de los siguientes temas:

* [Creación de una red virtual mediante el Portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)
* [Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS para profesionales de TI: (01) Principios básicos sobre máquinas virtuales](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profesionales de TI: (05) Creación de redes virtuales y conectividad entre instalaciones](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introducción
Los requisitos fundamentales para implementar Windows Server Active Directory en máquinas virtuales de Azure son similares, pero no idénticos, a los de implementarlo en máquinas virtuales (y, en cierta medida, en máquinas físicas) locales. Por ejemplo, en el caso de Windows Server AD DS, si los controladores de dominio (DC) que implementa en las máquinas virtuales de Azure son réplicas en un bosque o dominio corporativo local existente, podrá tratar la implementación de Azure en gran medida de la misma manera que la de cualquier otro sitio adicional de Windows Server Active Directory. Es decir, se deben definir subredes en Windows Server AD DS, crear un sitio, vincular las subredes a ese sitio y conectar a otros sitios con los vínculos adecuados. Sin embargo, hay algunas diferencias que son comunes a todas las implementaciones de Azure y otras que varían según el escenario de implementación concreto. A continuación se presentan dos diferencias fundamentales:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Puede que las máquinas virtuales de Azure necesiten conectividad con la red corporativa local.
Conectar de nuevo máquinas virtuales de Azure a una red corporativa local requiere una red virtual que incluya un componente de red privada virtual (VPN) de sitio a sitio o de sitio a punto que pueda conectar sin ningún problema máquinas virtuales de Azure y equipos locales. Este componente de VPN también puede permitir a equipos miembros del dominio local acceder a un dominio de Windows Server Active Directory cuyos controladores de dominio estén hospedados exclusivamente en máquinas virtuales de Azure. No obstante, es importante que tenga en cuenta que si se produce un error en la VPN, la autenticación y otras operaciones que dependen de Windows Server Active Directory también darán error. Aunque los usuarios podrán iniciar sesión con credenciales ya existentes almacenadas en caché, todos los intentos de autenticación de punto a punto o de cliente a servidor cuyos vales se tienen que emitir aún o se han quedado obsoletos, darán error.

Consulte [Red virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para ver una demostración en vídeo y una lista de tutoriales paso a paso, incluido [Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> También puede implementar Windows Server Active Directory en una red virtual de Azure que no tenga conectividad con una red local. Las instrucciones de este tema, sin embargo, dan por supuesto que se usa una red virtual de Azure, ya que esta proporciona funcionalidades de direccionamiento IP que son esenciales para Windows Server.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Las direcciones IP estáticas se deben configurar con Azure PowerShell.
Las direcciones dinámicas se asignan de forma predeterminada, pero use el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática en su lugar. Eso permitirá establecer una dirección IP estática que se mantendrá durante la recuperación del servicio y el cierre y reinicio de la máquina virtual. Para más información, consulte [Static internal IP address for virtual machines (Dirección IP estática interna para máquinas virtuales)](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Términos y definiciones
La siguiente es una lista no exhaustiva de términos para diversas tecnologías de Azure a las que se hará referencia en este artículo.

* **Máquinas virtuales de Azure**: la oferta de IaaS en Azure que permite a los clientes implementar máquinas virtuales que ejecutan casi cualquier carga de trabajo de servidor local tradicional.
* **Red virtual de Azure**: servicio de red de Azure que permite a los clientes crear y administrar redes virtuales en Azure y vincularlas de forma segura a su propia infraestructura de red local mediante una red privada virtual (VPN).
* **Dirección IP virtual**: una dirección IP accesible desde Internet que no está enlazada a ninguna tarjeta de interfaz de red o equipo específicos. A los servicios en la nube se les asigna una dirección IP virtual para recibir el tráfico de red que se redirige a una máquina virtual de Azure. Una dirección IP virtual es una propiedad de un servicio en la nube que puede contener una o más máquinas virtuales de Azure. Tenga en cuenta que una red virtual de Azure puede contener uno o más servicios en la nube. Las direcciones IP virtuales proporcionan funcionalidades nativas de equilibrio de carga.
* **Dirección IP dinámica**: se trata de la dirección IP que solo es interna. Se debe configurar como una dirección IP estática (mediante el cmdlet Set-AzureStaticVNetIP) para máquinas virtuales que hospedan los roles de servidor DC/DNS.
* **Recuperación del servicio**: proceso por el cual Azure devuelve automáticamente un servicio a un estado de ejecución después de detectar que tuvo errores. La recuperación del servicio es uno de los aspectos de Azure que admite disponibilidad y resistencia. Aunque improbable, el resultado tras un incidente con recuperación del servicio para un controlador de dominio que se ejecuta en una máquina virtual es similar a un reinicio no planeado, pero tiene algunos efectos secundarios:
  
  * Cambiará el adaptador de red virtual en la máquina virtual cambiará
  * Cambiará la dirección MAC del adaptador de red virtual
  * Cambiará el identificador de procesador/CPU de la máquina virtual
  * La configuración de IP del adaptador de red virtual no cambiará siempre y cuando la máquina virtual esté conectada a una red virtual y la dirección IP de la máquina virtual sea estática.
  
  Ninguno de estos comportamientos afecta a Windows Server Active Directory ya que no tiene ninguna dependencia de la dirección MAC ni del identificador de procesador/CPU, y es recomendable que todas las implementaciones de Windows Server Active Directory en Azure se ejecuten en una red virtual de Azure tal y como se ha descrito anteriormente.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>¿Es seguro virtualizar los controladores de dominio de Windows Server Active Directory?
La implementación de controladores de dominio de Windows Server Active Directory en máquinas virtuales de Azure está sujeta a las mismas instrucciones que la ejecución de controladores de dominio locales en una máquina virtual. Ejecutar controladores de dominio virtualizados es una práctica segura siempre y cuando se sigan las instrucciones para realizar copias de seguridad y restaurar dichos controladores de dominio. Para más información sobre las restricciones e instrucciones para ejecutar los controladores de dominio virtualizados, consulte [Controladores de dominio en ejecución en Hyper-V](https://technet.microsoft.com/library/dd363553).

Los hipervisores proporcionan o trivializan tecnologías que pueden causar problemas en muchos sistemas distribuidos, incluido Windows Server Active Directory. Por ejemplo, en un servidor físico, puede clonar un disco o usar métodos no admitidos para revertir el estado de un servidor, incluido el uso de varias SAN, etc, pero hacerlo en un servidor físico es mucho más difícil que restaurar una instantánea de máquina virtual en un hipervisor. Azure ofrece una funcionalidad que puede provocar la misma condición no deseada. Por ejemplo, no debe copiar archivos VHD de controladores de dominio en lugar de realizar copias de seguridad periódicas ya que su restauración puede producir una situación similar al uso de características de restauración de instantáneas.

Tales reversiones presentan burbujas de USN que pueden conducir a estados permanentemente divergentes entre controladores de dominio. Esto puede provocar problemas como:

* Objetos persistentes
* Contraseñas incoherentes
* Valores de atributo incoherentes
* Discordancias de esquema si se revierte el maestro de esquema

Para más información acerca de cómo afecta esto a los controladores de dominio, consulte [USN y reversión de USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

A partir de Windows Server 2012, [se han integrado medidas de seguridad adicionales en AD DS](https://technet.microsoft.com/library/hh831734.aspx). Estas medidas de seguridad ayudan a proteger los controladores de dominio virtualizados frente a los problemas mencionados anteriormente, siempre y cuando la plataforma de hipervisor subyacente admita VM-GenerationID. Azure admite VM-GenerationID, lo que significa que los controladores de dominio que ejecutan Windows Server 2012 o posterior en máquinas virtuales disponen de las medidas de seguridad adicionales.

> [!NOTE]
> Debe apagar y reiniciar la máquina virtual que ejecuta el rol de controlador de dominio en Azure dentro del sistema operativo invitado en lugar de utilizar la opción **Apagar** en Azure Portal. Actualmente, el uso del portal para apagar una máquina virtual hace que la máquina virtual se desasigne. Una máquina virtual desasignada tiene la ventaja de no incurrir en gastos, pero también restablece el VM-GenerationID, lo cual es una situación no deseada para un controlador de dominio. Cuando se restablece el VM-GenerationID, también se restablece el invocationID de la base de datos de AD DS, se descarta el grupo RID y SYSVOL se marca como no autoritativo. Para más información, consulte [Introducción a la virtualización de Active Directory Domain Services (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) y [Safely Virtualizing DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx) (Virtualización segura de DFSR).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>¿Por qué implementar Windows Server AD DS en Máquinas virtuales de Azure?
Muchos escenarios de implementación de Windows Server AD DS son adecuados para la implementación como máquinas virtuales en Azure. Por ejemplo, suponga que tiene una compañía en Europa que necesita autenticar usuarios en una ubicación remota de Asia. La compañía no ha implementado previamente controladores de dominio de Windows Server Active Directory en Asia debido al costo de implementación y a su poca experiencia para administrar el periodo posterior a la implementación de los servidores. Como resultado, los controladores de dominio en Europa atienden las solicitudes de autenticación de Asia con resultados poco óptimos. En este caso, puede implementar un controlador de dominio en la máquina virtual que ha especificado que se debe ejecutar en el centro de datos de Azure en Asia. La asociación de ese controlador de dominio a una red virtual de Azure que esté conectada directamente a la ubicación remota mejorará el rendimiento de la autenticación.

Azure también resulta indicado como sustituto de sitios costosos de recuperación ante desastres (DR). El costo relativamente bajo de hospedar un número pequeño de controladores de dominio y una única red virtual en Azure constituye una alternativa atractiva.

Por último, puede que desee implementar una aplicación de red en Azure, como SharePoint, que requiere Windows Server Active Directory pero no tiene ninguna dependencia de la red local ni del Windows Server Active Directory corporativo. En este caso, la implementación de un bosque aislado en Azure para cumplir los requisitos del servidor de SharePoint es óptima. De nuevo, también se admite la implementación de aplicaciones de red que requieren conectividad con la red local y el Active Directory corporativo.

> [!NOTE]
> Como ofrece una conexión de nivel 3, el componente VPN que proporciona conectividad entre una red virtual de Azure y una red local también puede habilitar a los servidores miembro que se ejecutan de forma local para que aprovechen los controladores de dominio que se ejecutan como máquinas virtuales en la red virtual de Azure. Pero si la VPN no está disponible, la comunicación entre equipos locales y los controladores de dominio basados en Azure no funcionará, lo cual provocará errores de autenticación y otros varios.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Diferencias entre implementar controladores de dominio de Windows Server Active Directory en máquinas virtuales de Azure o hacerlo de forma local
* En cualquier escenario de implementación de Windows Server Active Directory que incluya más de una máquina virtual, es necesario utilizar una red virtual para que las direcciones IP sean coherentes. Tenga en cuenta que en esta guía se supone que los controladores de dominio se ejecutan en una red virtual.
* Al igual que con los controladores de dominio locales, se recomiendan direcciones IP estáticas. Una dirección IP estática solo se puede configurar mediante Azure PowerShell. Para más detalles, consulte [Static internal IP address for VMs (Dirección IP estática interna para máquinas virtuales)](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Si tiene sistemas de supervisión u otras soluciones que comprueban la configuración de la dirección IP estática en el sistema operativo invitado, puede asignar la misma dirección IP estática a las propiedades del adaptador de red de la máquina virtual. Pero tenga en cuenta que el adaptador de red se descartará si la máquina virtual se somete a recuperación del servicio o se cierra en el portal y se desasigna su dirección. En ese caso, tendrá que restablecer la dirección IP estática del sistema operativo invitado.
* La implementación de máquinas virtuales en una red virtual no implica (ni requiere) conectividad a una red local; la red virtual simplemente habilita esa posibilidad. Debe crear una red virtual para la comunicación privada entre Azure y la red local. Debe implementar un punto de conexión de VPN en la red local. La VPN está abierta desde Azure a la red local. Para más información, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md) y [Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Hay una opción disponible para [crear una VPN de punto a sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md) para conectar equipos individuales basados en Windows directamente a una red virtual.
> 
> 

* Independientemente de si crea una red virtual o no, Azure cobrará por el tráfico de salida, pero no por el de entrada. Varias opciones de diseño de Windows Server Active Directory pueden influir en cuánto tráfico de salida genera una implementación. Por ejemplo, la implementación de un controlador de dominio de solo lectura (RODC) limita el tráfico de salida ya que no replica datos de salida. Pero la decisión de implementar un RODC se debe sopesar en comparación con la necesidad de realizar operaciones de escritura en el controlador de dominio y la [compatibilidad](https://technet.microsoft.com/library/cc755190) que las aplicaciones y servicios del sitio tienen con los RODC. Para más información acerca de los gastos por tráfico, consulte [Precios de Azure](http://azure.microsoft.com/pricing/).
* Aunque tiene un control completo sobre qué recursos del servidor utilizará para las máquinas virtuales de forma local, como la RAM, el tamaño de disco, etc., en Azure debe seleccionar de una lista de tamaños preconfigurados de servidor. Para un controlador de dominio, se necesita un disco de datos además del disco de sistema operativo para almacenar la base de datos de Windows Server Active Directory.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>¿Se puede implementar Windows Server AD FS en Máquinas virtuales de Azure?
Sí, puede implementar Windows Server AD FS en Máquinas virtuales de Azure y los [procedimientos recomendados para la implementación de AD FS](https://technet.microsoft.com/library/dn151324.aspx) de forma local son aplicables igualmente a la implementación de AD FS en Azure. Pero algunos de los procedimientos recomendados, como el equilibrio de carga y la alta disponibilidad, requieren tecnologías que van más allá de lo que ofrece AD FS. Estas las debe proporcionar la infraestructura subyacente. Vamos a revisar algunos de los procedimientos recomendados y a ver cómo pueden lograrse mediante el uso de Máquinas virtuales de Azure y una red virtual de Azure.

1. **Nunca exponga los servidores del servicio de token de seguridad (STS) directamente a Internet.**
   
    Esto es importante porque el STS emite tokens de seguridad. Como resultado, los servidores de STS como, por ejemplo, los servidores de AD FS se deben tratar con el mismo nivel de protección que un controlador de dominio. Si un STS está en peligro, los usuarios malintencionados tendrán la capacidad de emitir tokens de acceso que potencialmente contienen notificaciones de su elección para aplicaciones de usuario de confianza y servidores STS en organizaciones de confianza.
2. **Implemente controladores de dominio de Active Directory para todos los dominios de usuario en la misma red que los servidores de AD FS.**
   
    Los servidores de AD FS utilizan los servicios de Dominio de Active Directory para autenticar usuarios. Se recomienda implementar controladores de dominio en la misma red que los servidores de AD FS. Esto garantiza la continuidad del negocio en caso de se rompa el vínculo entre la red de Azure y la red local y permite un mayor rendimiento y una menor latencia en los inicios de sesión.
3. **Implemente varios nodos de AD FS para obtener alta disponibilidad y equilibrar la carga.**
   
    En la mayoría de los casos, no es aceptable un error de una aplicación que habilita AD FS ya que las aplicaciones que requieren tokens de seguridad son a menudo críticas. Como consecuencia, y como AD FS ahora reside en la ruta crítica para tener acceso a aplicaciones críticas, el servicio AD FS debe tener alta disponibilidad a través de varios servidores proxy de AD FS y servidores de AD FS. Para lograr la distribución de solicitudes, se implementan normalmente equilibradores de carga delante de los servidores proxy de AD FS y de los servidores de AD FS.
4. **Implemente uno o más nodos de proxy de aplicación web para acceder a Internet.**
   
    Cuando los usuarios necesitan tener acceso a aplicaciones protegidas por el servicio AD FS, este servicio debe estar disponible desde Internet. Esto se consigue mediante la implementación del servicio de proxy de aplicación web. Se recomienda encarecidamente implementar más de un nodo para obtener una alta disponibilidad y el equilibrio de carga.
5. **Restrinja el acceso de los nodos de proxy de aplicación web a los recursos internos de red.**
   
    Para permitir que los usuarios externos accedan a AD FS desde internet, debe implementar nodos de proxy de aplicación web (o proxy de AD FS en versiones anteriores de Windows Server). Los nodos de proxy de aplicación web se exponen directamente a Internet. No tienen que estar unidos al dominio y solo necesitan acceso a los servidores de AD FS a través de los puertos TCP 443 y 80. Se recomienda encarecidamente que se bloquee la comunicación con cualquier otro equipo (especialmente los controladores de dominio).
   
    Este se consigue normalmente de forma local mediante una red perimetral. Los firewalls usan un modo de funcionamiento mediante lista blanca para restringir el tráfico desde la red perimetral a la red local (es decir, solo se permite el tráfico desde direcciones IP especificadas y a través de los puertos especificados y se bloquea el resto del tráfico).

El siguiente diagrama muestra una implementación tradicional de AD FS de forma local.

![Diagrama de una implementación tradicional de servicios de federación de Active Directory de forma local](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Sin embargo, dado que Azure no proporciona una funcionalidad nativa de firewall completa, se deben utilizar otras opciones para restringir el tráfico. La siguiente tabla muestra cada opción y sus ventajas e inconvenientes.

| Opción | Ventaja | Desventaja |
| --- | --- | --- |
| [ACL de red de Azure](../virtual-network/virtual-networks-acl.md) |Configuración inicial menos costosa y más sencilla |Se necesita configuración adicional de ACL de red si se agregan nuevas máquinas virtuales a la implementación |
| [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) |Modo de funcionamiento mediante lista blanca y no requiere ninguna configuración de ACL de red |Aumento del costo y la complejidad de la instalación inicial |

En este caso, los pasos para implementar AD FS son los siguientes:

1. Crear una red virtual con conectividad entre locales, mediante una VPN o mediante [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Implementar controladores de dominio en la red virtual. Este paso es opcional pero recomendable.
3. Implementar servidores de AD FS unidos a dominio en la red virtual.
4. Crear un [conjunto con equilibrio de carga interno](http://azure.microsoft.com/blog/internal-load-balancing/) que incluya los servidores de AD FS y use una nueva dirección IP privada dentro de la red virtual (una dirección IP dinámica).
   
   1. Actualizar el DNS para crear el FQDN que señale a la dirección IP (dinámica) privada del conjunto con equilibrio de carga interno.
5. Crear un servicio en la nube (o una red virtual independiente) para los nodos de proxy de la aplicación web.
6. Implementar los nodos de proxy de la aplicación web en el servicio en la nube o en la red virtual
   
   1. Crear un conjunto con equilibrio de carga externo que incluya los nodos de proxy de la aplicación web.
   2. Actualice el nombre DNS externo (FQDN) para que señale a la dirección IP pública del servicio en la nube (la dirección IP virtual).
   3. Configurar los proxy de AD FS para que usen el FQDN que corresponde al conjunto con equilibrio de carga interno para los servidores de AD FS.
   4. Actualizar sitios web basados en notificaciones para utilizar el FQDN para su proveedor de notificaciones.
7. Restringir el acceso entre el proxy de la aplicación web y todos los equipos de la red virtual de AD FS.

Para restringir el tráfico, el conjunto con equilibrio de carga establecido para el equilibrador de carga interno de Azure se debe configurar para el tráfico solo hacia los puertos TCP 80 y 443, y se elimina el resto del tráfico a la dirección IP dinámica interna del conjunto con equilibrio de carga.

![Diagrama de las ACL de red de ADFS con TCP 443 + 80 permitido.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

El tráfico hacia los servidores de AD FS se le permite únicamente a través de los siguientes orígenes:

* El equilibrador de carga interno de Azure.
* La dirección IP de un administrador de la red local.

> [!WARNING]
> El diseño debe impedir que los nodos de proxy de la aplicación web lleguen a otras máquinas virtuales de la red virtual de Azure o a cualquier otra ubicación en la red local. Esto se puede hacer mediante la configuración de reglas de firewall en la aplicación local para las conexiones de Express Route o en el dispositivo VPN para las conexiones VPN de sitio a sitio.
> 
> 

Una desventaja de esta opción es la necesidad de configurar las ACL de red para varios dispositivos, incluidos el equilibrador de carga interno, los servidores de AD FS y cualquier otro servidor que se agregue a la red virtual. Si se agrega cualquier dispositivo a la implementación sin configurar las ACL de red para restringir el tráfico hacia la misma, toda la implementación puede estar en peligro. Si alguna vez cambian las direcciones IP de los nodos de proxy de aplicación web, se deben restablecer las ACL de red (lo que significa que los proxy se deben configurar para usar [direcciones IP dinámicas estáticas](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS en Azure con ACL de red.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Otra opción consiste en utilizar la aplicación [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) para controlar el tráfico entre los servidores proxy de AD FS y los servidores de AD FS. Esta opción cumple con los procedimientos recomendados de seguridad y alta disponibilidad y requiere menos administración después de la instalación inicial ya que la aplicación Barracuda NG Firewall proporciona un modo de administración del firewall mediante lista blanca y se puede instalar directamente en una red virtual de Azure. Esto elimina la necesidad de configurar las ACL de red siempre que se agrega un nuevo servidor a la implementación. Pero esta opción agrega complejidad y costo a la implementación inicial.

En este caso, se implementan dos redes virtuales en lugar de una. Las llamaremos VNet1 y VNet2. VNet1 contiene los proxy y VNet2 los STS y la conexión de red a la red corporativa. VNet1 está, por tanto, físicamente (o mejor dicho, virtualmente) aislada de VNet2 y, a su vez, de la red corporativa. A continuación, VNet1 se conecta a VNet2 mediante una tecnología especial de tunelización conocida como arquitectura de red independiente de transporte (TINA). La tunelización TINA se asocia a cada una de las redes virtuales mediante la aplicación Barracuda NG Firewall: una aplicación Barracuda en cada una de las redes virtuales.  Para obtener una alta disponibilidad, se recomienda que implemente dos aplicaciones Barracuda en cada red virtual, una activa, la otra pasiva. Ofrecen una funcionalidad de firewall muy potente que nos permite imitar el funcionamiento de una red perimetral local tradicional en Azure.

![ADFS en Azure con firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para obtener más información, consulte [AD FS: expansión de una aplicación front-end local de notificaciones a Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Alternativa a la implementación de AD FS si el objetivo es solo el inicio de sesión único para Office 365
Hay otra alternativa a la implementación de AD FS si su objetivo es solo permitir el inicio de sesión único para Office 365. En ese caso, simplemente puede implementar DirSync con sincronización local de contraseña y lograr el mismo resultado final con una menor complejidad de implementación, dado que este enfoque no requiere AD FS ni Azure.

La tabla siguiente compara el funcionamiento de los procesos de inicio de sesión tanto si implementa AD FS como si no lo hace.

| Inicio de sesión único de Office 365 mediante AD FS y DirSync | Mismo inicio de sesión único de Office 365 mediante DirSync + sincronización de contraseñas |
| --- | --- |
| 1. El usuario inicia sesión en una red corporativa y se autentica en Windows Server Active Directory. |1. El usuario inicia sesión en una red corporativa y se autentica en Windows Server Active Directory. |
| 2. El usuario intenta obtener acceso a Office 365 (nombre @contoso.com). |2. El usuario intenta obtener acceso a Office 365 (nombre @contoso.com). |
| 3. Office 365 redirige al usuario a Azure AD. |3. Office 365 redirige al usuario a Azure AD. |
| 4. Como Azure AD no puede autenticar al usuario y entiende que hay una relación de confianza con AD FS local, redirige al usuario a AD FS. |4. Azure AD no puede aceptar vales de Kerberos directamente y no existe ninguna relación de confianza así que solicitará al usuario que especifique las credenciales. |
| 5. El usuario envía un vale de Kerberos al STS de AD FS. |5. El usuario escribe la misma contraseña local y Azure AD la valida con el nombre de usuario y contraseña que se sincronizaron con DirSync. |
| 6. AD FS transforma el vale de Kerberos al formato o notificaciones de token necesarios y redirige al usuario a Azure AD. |6. Azure AD redirige al usuario a Office 365. |
| 7. El usuario se autentica en Azure AD (se produce otra transformación). |7. El usuario puede iniciar sesión en Office 365 y OWA con el token de Azure AD. |
| 8. Azure AD redirige al usuario a Office 365. | |
| 9. El usuario se ha conectado en modo silencioso a Office 365. | |

En el escenario de Office 365 con DirSync con sincronización de contraseña (sin AD FS), el inicio de sesión único se reemplaza mediante el "mismo inicio de sesión" lo cual significa que los usuarios deben volver a escribir las mismas credenciales locales al acceder a Office 365. Tenga en cuenta que estos datos se pueden guardar en el explorador del usuario para ayudar a reducir el número de solicitudes posteriores.

### <a name="additional-food-for-thought"></a>Ideas adicionales para reflexionar
* Si implementa un proxy de AD FS en una máquina virtual de Azure, se necesitará conectividad con los servidores de AD FS. Si son locales, se recomienda que aproveche la conectividad VPN de sitio a sitio proporcionada por la red virtual para permitir que los nodos de proxy de la aplicación web se comuniquen con los servidores de AD FS.
* Si implementa un servidor de AD FS en una máquina virtual de Azure, necesitará conectividad con los controladores de dominio, almacenes de atributos y bases de datos de configuración de Windows Server Active Directory y puede requerir también una conexión de ExpressRoute o una conexión VPN de sitio a sitio entre la red virtual de Azure y la red local.
* Se aplican cargos a todo el tráfico procedente de las máquinas virtuales de Azure (tráfico de salida). Si el costo es un factor determinante, es conveniente implementar los nodos de proxy de la aplicación web en Azure, dejando los servidores de AD FS locales. Si se implementan también los servidores de AD FS en máquinas virtuales de Azure, se aplicarán cargos adicionales para autenticar a los usuarios locales. El tráfico de salida supone siempre un costo independientemente de si está atravesando la conexión de ExpressRoute o la conexión VPN de sitio a sitio.
* Si decide utilizar la funcionalidad nativa de Azure de equilibrio de carga del servidor para lograr una alta disponibilidad de los servidores de AD FS, tenga en cuenta que el equilibrio de carga proporciona sondeos que se utilizan para determinar el estado de las máquinas virtuales dentro del servicio en la nube. En el caso de máquinas virtuales (a diferencia de los roles de trabajo o web), debe usarse un sondeo personalizado ya que el agente que responde a los sondeos predeterminados no está presente en las máquinas virtuales de Azure. Para simplificar, puede usar un sondeo TCP personalizado: solo requiere que se establezca una conexión TCP correctamente (se envía un segmento TCP SYN y se responde con un segmento TCP SYN ACK) para determinar el estado de la máquina virtual. Puede configurar el sondeo personalizado para utilizar cualquier puerto TCP al que estén escuchando activamente las máquinas virtuales.

> [!NOTE]
> Las máquinas que necesitan exponer el mismo conjunto de puertos directamente a Internet (por ejemplo, el puerto 80 y el 443) no pueden compartir el mismo servicio en la nube. Por lo tanto, conviene crear un servicio dedicado en la nube para los servidores de Windows Server AD FS con el fin de evitar posibles superposiciones entre los requisitos de puerto para una aplicación y Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Escenarios de implementación
La siguiente sección describe los escenarios de implementación comunes para llamar la atención sobre las consideraciones importantes que deben tenerse en cuenta. Cada escenario contiene vínculos para obtener más detalles acerca de las decisiones y los factores a tener en cuenta.

1. [AD DS: Implementación de una aplicación compatible con AD DS sin necesidad de conectividad de red corporativa](#BKMK_CloudOnly)
   
    Por ejemplo, un servicio de SharePoint a través de Internet se implementa en una máquina virtual de Azure. La aplicación no tiene dependencias de recursos de la red corporativa. La aplicación requiere Windows Server AD DS pero no requiere Windows Server AD DS corporativo.
2. [AD FS: expansión de una aplicación front-end local de notificaciones a Internet](#BKMK_CloudOnlyFed)
   
    Por ejemplo, una aplicación de notificaciones que se ha implementado correctamente de forma local, utilizada por usuarios corporativos, y que debe ser accesible desde Internet. Tanto los socios comerciales mediante sus propias identidades corporativas como los usuarios corporativos ya existentes necesitan acceder a la aplicación directamente a través de Internet.
3. [AD DS: Implementación de una aplicación compatible con Windows Server AD DS que requiere conectividad a la red corporativa](#BKMK_HybridExt)
   
    Por ejemplo, una aplicación compatible con LDAP que admite la autenticación integrada de Windows y usa Windows Server AD DS como repositorio de datos de configuración y de perfil de usuario se implementa en una máquina virtual de Azure. Es conveniente para la aplicación aprovechar la aplicación Windows Server AD DS corporativa ya existente y proporcionar un inicio de sesión único. La aplicación no es compatible con notificaciones.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Implementación de una aplicación compatible con AD DS sin necesidad de conectividad de red corporativa
![Implementación de AD DS solo en la nube](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figura 1**

#### <a name="description"></a>Descripción
SharePoint se implementa en una máquina virtual de Azure y la aplicación no tiene dependencias en recursos de la red corporativa. La aplicación requiere Windows Server AD DS, pero *no* la versión corporativa. No son necesarias relaciones de confianza de Kerberos ni federadas ya que los usuarios se aprovisionan automáticamente a través de la aplicación en el dominio de Windows Server AD DS que también se hospeda en la nube en las máquinas virtuales de Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones sobre el escenario y cómo se aplican las áreas de tecnología al mismo
* [Topología de red](#BKMK_NetworkTopology): cree una red virtual de Azure sin conectividad entre locales (también conocida como "conectividad de sitio a sitio").
* [Configuración de la implementación del controlador de dominio](#BKMK_DeploymentConfig): implemente un nuevo controlador de dominio en un bosque nuevo de Windows Server Active Directory de dominio único. Esto se debe implementar junto con el servidor DNS de Windows.
* [Topología del sitio de Windows Server Active Directory](#BKMK_ADSiteTopology): use el sitio predeterminado de Windows Server Active Directory (todos los equipos estarán en Default-First-Site-Name).
* [Direccionamiento IP y DNS](#BKMK_IPAddressDNS):
  
  * Establezca una dirección IP estática para el controlador de dominio mediante el cmdlet Set-AzureStaticVNetIP de Azure PowerShell.
  * Instale y configure el DNS de Windows Server en los controladores de dominio en Azure.
  * Configure las propiedades de la red virtual con el nombre y la dirección IP de la máquina virtual que hospeda los roles de servidor DC y DNS.
* [Catálogo global](#BKMK_GC): el primer controlador de dominio del bosque debe ser un servidor de catálogo global. También se deben configurar otros controladores de dominio como catálogos globales porque en un bosque de dominio único, el catálogo global no requiere ningún trabajo adicional desde el controlador de dominio.
* [Selección de ubicación de la base de datos de Windows Server AD DS y SYSVOL](#BKMK_PlaceDB): agregue un disco de datos a los controladores de dominio que se ejecutan como máquinas virtuales de Azure para almacenar la base de datos, los registros y SYSVOL de Windows Server Active Directory.
* [Copia de seguridad y restauración](#BKMK_BUR): determine dónde desea almacenar las copias de seguridad del estado de sistema. Si es necesario, agregue otro disco de datos a la máquina virtual del controlador de dominio para almacenar las copias de seguridad.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: expansión de una aplicación front-end local de notificaciones a Internet
![Federación con conectividad entre locales](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figura 2**

#### <a name="description"></a>Descripción
Una aplicación de notificaciones que se ha implementado correctamente de forma local, utilizada por usuarios corporativos, y que debe ser accesible directamente desde Internet. La aplicación actúa como un front-end web para una Base de datos SQL en la que almacena los datos. Los servidores de SQL Server utilizados por la aplicación también se encuentran en la red corporativa. Se han implementado dos STS de Windows Server AD FS y un equilibrador de carga de forma local para proporcionar acceso a los usuarios corporativos. Ahora, tanto los socios comerciales mediante sus propias identidades corporativas como los usuarios corporativos ya existentes necesitan acceder adicionalmente a la aplicación directamente a través de Internet.

En un esfuerzo por simplificar y satisfacer las necesidades de implementación y configuración de este nuevo requisito, se decidió que se instalaran dos front-end web y dos servidores proxy de Windows Server AD FS en las máquinas virtuales de Azure. Las cuatro máquinas virtuales se expondrán directamente a Internet y se proporcionará conectividad a la red local con la funcionalidad VPN de sitio a sitio de la red virtual de Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones sobre el escenario y cómo se aplican las áreas de tecnología al mismo
* [Topología de red](#BKMK_NetworkTopology): cree una red virtual de Azure y [configure la conectividad entre locales](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Para cada uno de los certificados de Windows Server AD FS, asegúrese de que se pueda acceder a la dirección URL definida dentro de la plantilla de certificado y los certificados resultantes a través de las instancias de Windows Server AD FS que se ejecutan en Azure. Esto puede requerir la conectividad entre locales a partes de la infraestructura PKI. Por ejemplo, si el punto de conexión de la CRL está basado en LDAP y se hospeda exclusivamente de forma local, entonces se necesitará conectividad entre locales. Si esto no es conveniente, puede que necesite utilizar certificados emitidos por una entidad de certificación cuya CRL sea accesible a través de Internet.
  > 
  > 
* [Configuración de servicios en la nube](#BKMK_CloudSvcConfig): asegúrese de que tiene dos servicios en la nube para proporcionar dos direcciones IP virtuales de carga equilibrada. La primera dirección IP virtual del servicio en la nube se dirigirá a las dos máquinas virtuales de proxy de Windows Server AD FS en los puertos 80 y 443. Las máquinas virtuales de proxy de Windows Server AD FS se configurarán para que señalen a la dirección IP del equilibrador de carga local que se sitúa frente a los STS de Windows Server AD FS. La segunda dirección IP virtual del servicio en la nube se dirigirá a las dos máquinas virtuales que están ejecutando de nuevo el front-end web en los puertos 80 y 443. Configure un sondeo personalizado para asegurarse de que el equilibrador de carga solo dirige tráfico a máquinas virtuales de proxy y front-end web de Windows Server AD FS.
* [Configuración del servidor de federación](#BKMK_FedSrvConfig): configure Windows Server AD FS como un servidor de federación (STS) para generar tokens de seguridad para el bosque de Windows Server Active Directory creado en la nube. Establezca relaciones federadas de confianza de proveedor de notificaciones con los distintos asociados de los que desea aceptar identidades y configure relaciones con usuarios de confianza con las distintas aplicaciones para las que desea generar tokens.
  
    En la mayoría de los escenarios, los servidores proxy de Windows Server AD FS se implementan en una funcionalidad accesible desde Internet por motivos de seguridad mientras que sus homólogos de federación de Windows Server AD FS permanecen apartados de la conectividad directa a Internet. Independientemente del escenario de implementación, debe configurar el servicio en la nube con una dirección IP virtual que proporcionará una dirección IP y un puerto expuestos públicamente que son capaces de equilibrar la carga entre las dos instancias de STS de Windows Server AD FS o entre las instancias de proxy.
* [Configuración de alta disponibilidad de Windows Server AD FS](#BKMK_ADFSHighAvail): es conveniente implementar una granja de Windows Server AD FS con al menos dos servidores para la conmutación por error y el equilibrio de carga. Podría considerar usar Windows Internal Database (WID) para los datos de configuración de Windows Server AD FS y utilizar la funcionalidad de equilibrio de carga interno de Azure para distribuir las solicitudes entrantes entre los servidores de la granja.

Para obtener más información, consulte la [guía de implementación de AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. AD DS: Implementación de una aplicación compatible con Windows Server AD DS que requiere conectividad a la red corporativa
![Implementación de AD DS entre locales](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figura 3**

#### <a name="description"></a>Descripción
Una aplicación compatible con LDAP se implementa en una máquina virtual de Azure. Admite la autenticación integrada de Windows y usa Windows Server AD DS como repositorio para los datos de configuración y de perfil de usuario. El objetivo para la aplicación es aprovechar la aplicación Windows Server AD DS corporativa ya existente y proporcionar un inicio de sesión único. La aplicación no es compatible con notificaciones. Los usuarios también necesitan tener acceso a la aplicación directamente desde Internet. Para optimizar el rendimiento y el costo, se decide que dos controladores de dominio adicionales que forman parte del dominio corporativo se implementen junto con la aplicación en Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Consideraciones sobre el escenario y cómo se aplican las áreas de tecnología al mismo
* [Topología de red](#BKMK_NetworkTopology): cree una red virtual de Azure con [conectividad entre locales](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Método de instalación](#BKMK_InstallMethod): implemente controladores de dominio de réplica desde el dominio corporativo de Windows Server Active Directory. Para un controlador de dominio de réplica, puede instalar Windows Server AD DS en la máquina virtual y, opcionalmente, usar la característica de instalación desde medios (IFM) para reducir la cantidad de datos que deben replicarse en el nuevo controlador de dominio durante la instalación. Para ver un tutorial, consulte [Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure](active-directory-install-replica-active-directory-domain-controller.md). Aunque utilice la característica IFM, puede resultar más eficaz generar el controlador de dominio virtual de forma local y mover todo el disco duro virtual (VHD) a la nube en lugar de replicar Windows Server AD DS durante la instalación. Por motivos de seguridad, se recomienda eliminar el VHD de la red local una vez que se ha copiado en Azure.
* [Topología del sitio de Windows Server Active Directory](#BKMK_ADSiteTopology): cree un nuevo sitio de Azure en Sitios y servicios de Active Directory. Cree un objeto de subred de Windows Server Active Directory para representar la red virtual de Azure y agregue la subred al sitio. Cree un nuevo vínculo de sitio que incluya el nuevo sitio de Azure y el sitio en el que se encuentra el punto de conexión de la VPN de la red virtual de Azure para controlar y optimizar el tráfico de Windows Server Active Directory hacia y desde Azure.
* [Direccionamiento IP y DNS](#BKMK_IPAddressDNS):
  
  * Establezca una dirección IP estática para el controlador de dominio mediante el cmdlet Set-AzureStaticVNetIP de Azure PowerShell.
  * Instale y configure el DNS de Windows Server en los controladores de dominio en Azure.
  * Configure las propiedades de la red virtual con el nombre y la dirección IP de la máquina virtual que hospeda los roles de servidor DC y DNS.
* [Controladores de dominio distribuidos geográficamente](#BKMK_DistributedDCs): configure tantas redes virtuales adicionales como sea necesario. Si la topología de sitio de Active Directory requiere controladores de dominio en ubicaciones geográficas que se corresponden con regiones de Azure, es recomendable crear sitios de Active Directory según corresponda.
* [Controladores de dominio de solo lectura](#BKMK_RODC): puede implementar un RODC en el sitio de Azure, dependiendo de los requisitos que se establezcan para realizar operaciones de escritura en el controlador de dominio y la compatibilidad de las aplicaciones y servicios del sitio con los RODC. Para obtener más información acerca de la compatibilidad de aplicaciones, consulte la [guía sobre compatibilidad de aplicaciones con controladores de dominio de solo lectura](https://technet.microsoft.com/library/cc755190).
* [Catálogo global](#BKMK_GC): se necesitan catálogos globales para atender las solicitudes de inicio de sesión en los bosques con varios dominios. Si no implementa un catálogo global en el sitio de Azure, incurrirá en costos de tráfico de salida ya que las solicitudes de autenticación generan catálogos globales de consultas en otros sitios. Para minimizar ese tráfico, puede habilitar el almacenamiento en caché de pertenencia al grupo universal para el sitio de Azure en Sitios y servicios de Active Directory.
  
    Si implementa un catálogo global, configure los vínculos de sitios y los costos de los mismos para que el catálogo global del sitio de Azure no sea el controlador de dominio de origen preferido por otros catálogos globales que necesitan replicar las mismas particiones de dominio parciales.
* [Selección de ubicación de la base de datos de Windows Server AD DS y SYSVOL](#BKMK_PlaceDB): agregue un disco de datos a los controladores de dominio que se ejecutan en las máquinas virtuales de Azure para almacenar la base de datos, los registros y SYSVOL de Windows Server Active Directory.
* [Copia de seguridad y restauración](#BKMK_BUR): determine dónde desea almacenar las copias de seguridad del estado de sistema. Si es necesario, agregue otro disco de datos a la máquina virtual del controlador de dominio para almacenar las copias de seguridad.

## <a name="deployment-decisions-and-factors"></a>Factores y decisiones de implementación
Esta tabla resume las áreas de tecnología de Windows Server Active Directory que se ven afectadas en los escenarios anteriores y las decisiones correspondientes a tener en cuenta, con vínculos a información más detallada a continuación. Algunas áreas de tecnología podrían no ser aplicables a todos los escenarios de implementación y algunas áreas de tecnología podrían ser más importantes para un escenario de implementación que otras.

Por ejemplo, si implementa un controlador de dominio de réplica en una red virtual y el bosque tiene un solo dominio, la decisión de implementar un servidor de catálogo global no será crítica en ese caso para el escenario de implementación ya que no creará ningún requisito de replicación adicional. Sin embargo, si el bosque tiene varios dominios, la decisión de implementar un catálogo global en una red virtual podría afectar al ancho de banda disponible, al rendimiento, a la autenticación, a las búsquedas de directorio, etc.

| Área de tecnología en Windows Server Active Directory | Decisiones | Factores |
| --- | --- | --- |
| [Topología de red](#BKMK_NetworkTopology) |¿Crear una red virtual? |<li>Requisitos de acceso a recursos corporativos</li> <li>Autenticación</li> <li>Administración de cuentas</li> |
| [Configuración de la implementación del controlador de dominio](#BKMK_DeploymentConfig) |<li>¿Implementar un bosque independiente sin ninguna relación de confianza?</li> <li>¿Implementar un nuevo bosque con federación?</li> <li>¿Implementar un nuevo bosque con confianza de bosque de Windows Server Active Directory o Kerberos?</li> <li>¿Extender el bosque corporativo implementando un controlador de dominio de réplica?</li> <li>¿Extender el bosque corporativo implementando un nuevo dominio secundario o árbol de dominios?</li> |<li>Seguridad</li> <li>Cumplimiento normativo</li> <li>Coste</li> <li>Resistencia y tolerancia a errores</li> <li>Compatibilidad de aplicación</li> |
| [Topología del sitio en Windows Server Active Directory](#BKMK_ADSiteTopology) |¿Cómo configurar subredes, sitios y vínculos de sitio con Red virtual de Azure para optimizar el tráfico y minimizar los costos? |<li>Definiciones de sitio y de subred</li> <li>Propiedades de vínculo de sitio y notificación de cambio</li> <li>Compresión de replicación</li> |
| [Direccionamiento IP y DNS](#BKMK_IPAddressDNS) |¿Cómo configurar la resolución de nombres y direcciones IP? |<li>Usar el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática</li> <li>Instalar el servidor DNS de Windows Server y configurar las propiedades de la red virtual con el nombre y la dirección IP de la máquina virtual que hospeda los roles de servidor DC y DNS</li> |
| [Controladores de dominio distribuidos geográficamente](#BKMK_DistributedDCs) |¿Cómo replicar en controladores de dominio de redes virtuales independientes? |Si la topología de sitio de Active Directory requiere controladores de dominio en ubicaciones geográficas que se corresponden con regiones de Azure, es recomendable crear sitios de Active Directory según corresponda. [Configure una conexión de red virtual a red virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para efectuar la replicación entre controladores de dominio de redes virtuales independientes. |
| [Controladores de dominio de solo lectura](#BKMK_RODC) |¿Usar controladores de dominio de solo lectura o de escritura? |<li>Filtrar atributos HBI/PII</li> <li>Filtrar secretos</li> <li>Limitar tráfico de salida</li> |
| [Catálogo global](#BKMK_GC) |¿Instalar un catálogo global? |<li>Para el bosque de dominio único, convertir todos los catálogos globales en controladores de dominio</li> <li>Para un bosque con varios dominios, se requieren catálogos globales para la autenticación.</li> |
| [Método de instalación](#BKMK_InstallMethod) |¿Cómo instalar el controlador de dominio en Azure? |Opciones:  <li>Instalar AD DS mediante Windows PowerShell o Dcpromo</li> <li>Trasladar VHD de un controlador de dominio virtual local a la nube</li> |
| [Selección de ubicación de la base de datos y SYSVOL de Windows Server AD DS](#BKMK_PlaceDB) |¿Dónde almacenar la base de datos, los registros y SYSVOL de Windows Server AD DS? |Cambie los valores predeterminados de Dcpromo.exe. Estos archivos críticos de Active Directory *deben* colocarse en discos de datos de Azure en lugar de en discos del sistema operativo que implementan almacenamiento en caché de escritura. |
| [Copia de seguridad y restauración](#BKMK_BUR) |¿Cómo proteger y recuperar datos? |Creación de copias de seguridad del estado del sistema |
| [Configuración del servidor de federación](#BKMK_FedSrvConfig) |<li>¿Implementar un nuevo bosque con federación en la nube?</li> <li>¿Implementar AD FS local y exponer un proxy en la nube?</li> |<li>Seguridad</li> <li>Cumplimiento normativo</li> <li>Coste</li> <li>Acceder a las aplicaciones por parte de asociados comerciales</li> |
| [Configuración de servicios en la nube](#BKMK_CloudSvcConfig) |Un servicio en la nube se implementa de forma implícita la primera vez que crea una máquina virtual. ¿Necesita implementar servicios en la nube adicionales? |<li>¿Una o varias máquinas virtuales requieren exposición directa a Internet?</li> <li> ¿El servicio necesita equilibrio de carga?</li> |
| [Requisitos del servidor de federación para el direccionamiento IP público y privado (IP dinámica frente a IP virtual)](#BKMK_FedReqVIPDIP) |<li>¿Necesita la instancia de Windows Server AD FS ser accesible directamente desde Internet?</li> <li>¿Necesita la aplicación que se va a implementar en la nube su propia dirección IP y puerto accesibles desde Internet?</li> |Creación de un servicio en la nube para cada dirección IP virtual que necesite la implementación |
| [Configuración de alta disponibilidad de Windows Server AD FS](#BKMK_ADFSHighAvail) |<li>¿Cuántos nodos debe tener la granja de servidores de Windows Server AD FS?</li> <li>¿Cuántos nodos se deben implementar en la granja de servidores proxy de Windows Server AD FS?</li> |Resistencia y tolerancia a errores |

### <a name="BKMK_NetworkTopology"></a>Topología de red
Para cumplir con los requisitos de coherencia de direcciones IP y de DNS de Windows Server AD DS, es necesario crear primero una [red virtual de Azure](../virtual-network/virtual-networks-overview.md) y conectar las máquinas virtuales a ella. Durante su creación, debe decidir si desea ampliar la conectividad a la red corporativa local, que conecta de forma transparente máquinas virtuales de Azure a máquinas locales. Esto se logra mediante tecnologías tradicionales de VPN y requiere exponer un punto de conexión de VPN en el borde de la red corporativa. Es decir, la VPN se inicia desde Azure a la red corporativa, no a la inversa.

Tenga en cuenta que se aplican cargos adicionales al extender una red virtual a la red local además de los cargos estándar que se aplican a cada máquina virtual. En concreto, se aplican cargos para el tiempo de CPU de la puerta de enlace de la Red virtual de Azure y para el tráfico de salida generado por cada máquina virtual que se comunica con máquinas locales a través de la VPN. Para obtener más información acerca de los gastos por tráfico de red, consulte [Precios de Azure](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuración de la implementación del controlador de dominio
La forma de configurar el controlador de dominio depende de los requisitos del servicio que desee ejecutar en Azure. Por ejemplo, podría implementar un nuevo bosque, aislado de su propio bosque corporativo, para comprobar una prueba de concepto, una nueva aplicación o algún otro proyecto a corto plazo que requiera servicios de directorio pero no acceso específico a recursos internos corporativos.

Como ventaja, un controlador de dominio de bosque aislado no se replica en controladores de dominio locales, lo cual resulta en menos tráfico de red de salida generado por el propio sistema, reduciendo así directamente los costos. Para obtener más información acerca de los gastos por tráfico de red, consulte [Precios de Azure](http://azure.microsoft.com/pricing/).

Otro ejemplo. Supongamos que tiene requisitos de privacidad para un servicio, pero el servicio depende del acceso a su Windows Server Active Directory interno. Si se le permite hospedar datos para el servicio en la nube, podría implementar un nuevo dominio secundario en el bosque interno en Azure. En este caso, puede implementar un controlador de dominio para el nuevo dominio secundario (sin el catálogo global para ayudar a solucionar problemas de privacidad). Este escenario, junto con una implementación de controlador de dominio de réplica, requiere una red virtual para la conectividad con los controladores de dominio locales.

Si crea un nuevo bosque, elija si desea usar [confianzas de Active Directory](https://technet.microsoft.com/library/cc771397) o [confianzas de federación](https://technet.microsoft.com/library/dd807036). Equilibre los requisitos según los criterios de compatibilidad, seguridad, cumplimiento, costo y resistencia. Por ejemplo, para aprovechar las ventajas de la [autenticación selectiva](https://technet.microsoft.com/library/cc755844) , puede optar por implementar un nuevo bosque en Azure y crear una relación de confianza de Windows Server Active Directory entre el bosque local y el de la nube. Sin embargo, si la aplicación es compatible con notificaciones, puede implementar confianzas de federación en lugar de confianzas de bosque de Active Directory. Otro factor será el costo por replicar más datos extendiendo Windows Server Active Directory local a la nube o por generar más tráfico de salida como resultado de la carga de autenticación y consultas.

Los requisitos de disponibilidad y tolerancia a errores también influyen en su elección. Por ejemplo, si se interrumpe el vínculo, aquellas aplicaciones que aprovechan una confianza de Kerberos o una de federación probablemente dejen de funcionar a no ser que haya implementado una infraestructura suficiente en Azure. Las configuraciones de implementación alternativas como las de controladores de dominio de réplica (grabable o RODC) aumentan la probabilidad de que puedan tolerar interrupciones del vínculo.

### <a name="BKMK_ADSiteTopology"></a>Topología del sitio en Windows Server Active Directory
Debe definir correctamente los sitios y los vínculos de sitios para optimizar el tráfico y minimizar el costo. Los sitios, los vínculos de sitios y las subredes afectan a la topología de replicación entre controladores de dominio y el flujo de tráfico de autenticación. Tenga en cuenta los siguientes gastos por tráfico y, a continuación, implemente y configure los controladores de dominio según los requisitos del escenario de implementación:

* Hay un precio nominal por hora para la puerta de enlace:
  
  * Se puede iniciar y detener como considere oportuno
  * Si se detiene, las máquinas virtuales de Azure estarán aisladas de la red corporativa
* El tráfico de entrada es gratis
* Se cobra por el tráfico saliente, según la lista de [Precios de Azure](http://azure.microsoft.com/pricing/). Puede optimizar las propiedades de vínculos de sitios entre sitios locales y sitios en la nube como sigue:
  
  * Si usa varias redes virtuales, configure los vínculos de sitios y sus costos correctamente para evitar que Windows Server AD DS dé prioridad al sitio de Azure sobre uno que puede proporcionar los mismos niveles de servicio de forma gratuita. También puede deshabilitar la opción Enlazar todos los vínculos de sitios (BASL) (que está habilitada de forma predeterminada). Esto garantiza que solo sitios con conexión directa se replican entre sí. Los controladores de dominio en sitios conectados de forma transitiva ya no se pueden replicar directamente entre sí, sino que se deben replicar a través de sitios comunes. Si los sitios intermedios no están disponibles por algún motivo, no se producirá la replicación entre controladores de dominio en sitios conectados de forma transitiva incluso aunque haya conectividad disponible entre los sitios. Por último, en las secciones en las que aún se desee un comportamiento de replicación transitivo, cree puentes de vínculos de sitios que contengan los sitios y vínculos de sitios adecuados, como los sitios locales o los de la red corporativa.
  * [Configure los costos del vínculo de sitio](https://technet.microsoft.com/library/cc794882) adecuadamente para evitar el tráfico no intencionado. Por ejemplo, si la opción **Intentar siguiente sitio más cercano** está habilitada, asegúrese de que los sitios de red virtual no sean los siguientes sitios más cercanos mediante el aumento del costo asociado del objeto de vínculo de sitio que vuelve a conectar el sitio de Azure a la red corporativa.
  * Configure los [intervalos](https://technet.microsoft.com/library/cc794878) y las [programaciones](https://technet.microsoft.com/library/cc816906) de vínculos de sitios según los requisitos de coherencia y la tasa de cambios de objeto. Alinee la programación de replicación con la tolerancia de latencia. Los controladores de dominio replican solo el último estado de un valor, por lo que disminuir el intervalo de replicación puede ahorrar costos si hay una frecuencia de cambio de objetos suficiente.
* Si la reducción de costos es una prioridad, asegúrese de que se programa la replicación y que no está habilitada la notificación de cambios. Esta es la configuración predeterminada cuando se replica entre sitios. Esto no es importante si va a implementar un RODC en una red virtual ya que el RODC no replicará ningún cambio de salida. Pero si implementa un controlador de dominio de escritura, debe asegurarse de que no esté configurado el vínculo de sitio para replicar actualizaciones con una frecuencia innecesaria. Si implementa un servidor de catálogo global (GC), asegúrese de que todos los sitios que contienen un catálogo global replican las particiones de dominio desde un controlador de dominio de origen en un sitio que está conectado con uno o varios vínculos de sitios que tienen un costo menor que el del catálogo global del sitio de Azure.
* Es posible reducir aún más el tráfico de red generado por la replicación entre sitios mediante el cambio del algoritmo de compresión de replicación. El algoritmo de compresión se controla mediante la entrada de registro REG_DWORD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator compression algorithm. El valor predeterminado es 3, que corresponde al algoritmo de compresión Xpress. Puede cambiar el valor a 2, lo cual cambia el algoritmo a MSZip. En la mayoría de los casos, esto aumentará la compresión, pero a costa de la utilización de la CPU. Para obtener más información, consulte [How Active Directory replication topology works](https://technet.microsoft.com/library/cc755994)(Funcionamiento de la topología de replicación de Active Directory).

### <a name="BKMK_IPAddressDNS"></a>Direccionamiento IP y DNS
A las máquinas virtuales se les asignan "direcciones concedidas por DHCP" de forma predeterminada. Puesto que las direcciones dinámicas de la red virtual se conservan mediante una máquina virtual durante la vigencia de la misma, se cumplen los requisitos de Windows Server AD DS.

Como resultado, cuando utiliza una dirección dinámica en Azure, está utilizando en efecto una dirección IP estática porque es enrutable durante el período de la concesión y el período de la concesión es igual a la duración del servicio en la nube.

Sin embargo, la dirección dinámica se desasigna si la máquina virtual se cierra. Para evitar que la dirección IP se desasigne, puede [usar Set-AzureStaticVNetIP para asignar una dirección IP estática](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Para la resolución de nombres, implemente su propia infraestructura de servidor DNS (o aproveche la existente). El DNS proporcionado por Azure no satisface las necesidades de resolución de nombres avanzada de Windows Server AD DS. Por ejemplo, no admite registros SRV dinámicos, etc. La resolución de nombres es un elemento de configuración crítico para los controladores de dominio y los clientes unidos a un dominio. Los controladores de dominio deben ser capaces de registrar los registros de recursos y resolver los registros de recursos de otros controladores de dominio.
Por razones de rendimiento y tolerancia a errores, lo ideal es instalar el servicio DNS de Windows Server en los controladores de dominio que se ejecutan en Azure. Configure las propiedades de la red virtual de Azure con el nombre y la dirección IP del servidor DNS. Cuando se inician otras máquinas virtuales en la red virtual, las opciones del solucionador de cliente DNS se configurarán con el servidor DNS como parte de la asignación de direcciones IP dinámicas.

> [!NOTE]
> No puede unir equipos locales a un dominio de Active Directory de Windows Server AD DS que se hospeda en Azure directamente a través de Internet. Los requisitos de puerto para Active Directory y para la operación de unión al dominio hacen que no sea práctico exponer directamente los puertos necesarios ni un controlador de dominio completo a Internet.
> 
> 

Las máquinas virtuales registran su nombre DNS automáticamente al iniciarse o cuando hay un cambio de nombre.

Para obtener más información acerca de este ejemplo y otro ejemplo que muestra cómo aprovisionar la primera máquina virtual e instalar AD DS en ella, consulte [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md). Si desea conocer más detalles sobre el uso de Windows PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) y [Azure Management Cmdlets](/powershell/module/azurerm.compute/#virtual_machines) (Cmdlets de administración de Azure).

### <a name="BKMK_DistributedDCs"></a>Controladores de dominio distribuidos geográficamente
Azure ofrece diversas ventajas cuando se hospedan varios controladores de dominio en redes virtuales diferentes:

* Tolerancia a errores de varios sitios
* Proximidad física a sucursales (menor latencia)

Para obtener más información acerca de cómo configurar la comunicación directa entre redes virtuales, consulte [Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controladores de dominio de solo lectura
Debe decidir si desea implementar controladores de dominio de solo lectura o de escritura. Puede que se incline por implementar RODC porque no tendrá control físico sobre ellos, pero los RODC están diseñados para su implementación en ubicaciones donde la seguridad física es un riesgo, como las sucursales.

Azure no presenta el riesgo de la seguridad física de una sucursal, pero los RODC siguen siendo más rentables ya que las características que proporcionan son adecuadas para estos entornos aunque por motivos muy diferentes. Por ejemplo, los RODC no generan ninguna replicación de salida y podrá rellenar selectivamente secretos (contraseñas). Como inconveniente, la falta de estos secretos puede requerir un tráfico de salida a petición para validarlos cuando un usuario o equipo se autentica. Pero los secretos se pueden rellenar previamente de forma selectiva y almacenarlos en caché.

Los RODC proporcionan una ventaja adicional en relación con los datos de alto impacto de negocio (HBI) y los de información de identificación personal (PII) porque se pueden agregar atributos que contengan datos confidenciales al conjunto de atributos filtrados (FAS) de RODC. Este conjunto es un conjunto personalizable de atributos que no se replican en los RODC. Puede utilizar este conjunto como medida de seguridad en caso de que no se le permita o no desee almacenar datos de alto impacto de negocio o información de identificación personal en Azure. Para más información, consulte [RODC filtered attribute set (Conjunto de atributos filtrados de RODC) en [(https://technet.microsoft.com/library/cc753459)]

Asegúrese de que las aplicaciones sean compatibles con los RODC que planea utilizar. Muchas aplicaciones habilitadas para Windows Server Active Directory funcionan bien con los RODC, pero algunas de ellas pueden realizar su función de forma ineficaz o se pueden producir errores si no tiene acceso a un controlador de dominio de escritura. Para obtener más información, consulte la [guía de compatibilidad de aplicaciones con controladores de dominio de solo lectura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global
Debe elegir si desea instalar un catálogo global (GC). En un bosque de dominio único, debe configurar todos los controladores de dominio como servidores de catálogo global. Esto no aumentará los costos porque no habrá ningún tráfico de replicación adicional.

En un bosque con varios dominios, los catálogos globales son necesarios para expandir la pertenencia al grupo universal durante el proceso de autenticación. Si no implementa un catálogo global, las cargas de trabajo en la red virtual que se autentican en un controlador de dominio en Azure generarán indirectamente un tráfico de autenticación de salida para consultar los catálogos globales locales durante cada intento de autenticación.

Los costos asociados con los catálogos globales son menos predecibles porque hospedan todos los dominios (en parte). Si la carga de trabajo hospeda un servicio accesible desde Internet y autentica usuarios en Windows Server AD DS, los costos podrían ser completamente imprevisibles. Para ayudar a reducir las consultas de catálogo global fuera del sitio en la nube durante la autenticación, puede [habilitar el almacenamiento en caché de pertenencia al grupo universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalación
Debe elegir cómo instalar los controladores de dominio en la red virtual:

* Promoción de nuevos controladores de dominio. Para obtener más información, consulte [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md).
* Traslado del VHD de un controlador de dominio virtual local a la nube. En este caso, debe asegurarse de que el controlador de dominio virtual local se "mueva" y no se "copie" o "clone" simplemente.

Utilice solo máquinas virtuales de Azure para los controladores de dominio (en lugar de máquinas virtuales con roles "web" o de "trabajo"). Son duraderas y la durabilidad de estado es un requisito para un controlador de dominio. Las máquinas virtuales de Azure están diseñadas para cargas de trabajo como controladores de dominio.

No utilice SYSPREP para implementar o clonar controladores de dominio. La capacidad para clonar controladores de dominio solo está disponible a partir de Windows Server 2012. La característica de clonación requiere compatibilidad con VMGenerationID en el hipervisor subyacente. Tanto Hyper-V en Windows Server 2012 como Redes virtuales de Azure son compatibles con VMGenerationID, al igual que otros proveedores de software de virtualización de terceros.

### <a name="BKMK_PlaceDB"></a>Selección de ubicación de la base de datos y SYSVOL de Windows Server AD DS
Seleccione dónde desea almacenar la base de datos, los registros y SYSVOL de Windows Server AD DS. Se deben implementar en discos de datos de Azure.

> [!NOTE]
> Los discos de datos de Azure están limitados a 1 TB.
> 
> 

Las unidades de disco de datos no tienen cachés de escritura de forma predeterminada. Las unidades de disco de datos que están conectadas a una máquina virtual usan una caché de escritura a través. La caché de escritura a través garantiza que la escritura se confirma en un Almacenamiento de Azure duradero antes de que la transacción se complete desde la perspectiva del sistema operativo de la máquina virtual. Proporciona durabilidad, a costa de escrituras ligeramente más lentas.

Esto es importante para Windows Server AD DS porque la caché de disco de escritura por detrás invalida los supuestos realizados por el controlador de dominio. Windows Server AD DS intenta deshabilitar la caché de escritura, pero depende del sistema de E/S de disco el admitirlo. El error al deshabilitar la caché de escritura puede, en determinadas circunstancias, introducir una reversión de USN que resulte en objetos persistentes y otros problemas.

Como procedimiento recomendado para controladores de dominio virtuales, realice lo siguiente:

* Establezca la preferencia de caché de host en el disco de datos de Azure en NINGUNA. Esto evita problemas con la caché de escritura para las operaciones de AD DS.
* Almacene la base de datos, los registros y SYSVOL en el mismo disco de datos o bien en discos de datos independientes. Normalmente, se trata de un disco independiente del disco utilizado por el propio sistema operativo. El punto clave es que la base de datos y SYSVOL de Windows Server AD DS no deben almacenarse en un tipo de disco de sistema operativo de Azure. De forma predeterminada, el proceso de instalación de AD DS instala estos componentes en la carpeta %systemroot%, lo cual NO es recomendable para Azure.

### <a name="BKMK_BUR"></a>Copia de seguridad y restauración
Tenga en cuenta lo que es compatible y lo que no a la hora de realizar una copia de seguridad y restauración de un controlador de dominio en general y, más concretamente, de aquellos que se ejecutan en una máquina virtual. Consulte la sección de [consideraciones relacionadas con la copia de seguridad y la restauración para controladores de dominio virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Cree copias de seguridad del estado del sistema solo mediante software que sea compatible específicamente con los requisitos de copia de seguridad para Windows Server AD DS como, por ejemplo, Copias de seguridad de Windows Server.

No copie ni clone archivos VHD de los controladores de dominio en lugar de realizar copias de seguridad periódicas. Si en alguna ocasión se necesita una restauración, hacerlo con VHD clonados o copiados sin Windows Server 2012 y un hipervisor compatible hará que se incluyan burbujas de USN.

### <a name="BKMK_FedSrvConfig"></a>Configuración del servidor de federación
La configuración de servidores de federación (STS) de Windows Server AD FS depende en parte de si las aplicaciones que desea implementar en Azure necesitan tener acceso a recursos de la red local.

Si las aplicaciones cumplen los criterios siguientes, podría implementarlas de forma aislada desde la red local.

* Aceptan tokens de seguridad SAML
* Se pueden exponer en Internet
* No acceden a los recursos locales

En este caso, configure los STS de Windows Server AD FS como sigue:

1. Configure un bosque de dominio único aislado en Azure.
2. Proporcione acceso federado al bosque mediante la configuración de una granja de servidores de federación de Windows Server AD FS.
3. Configure Windows Server AD FS (la granja de servidores de federación y la granja proxy de servidores de federación) en el bosque local.
4. Establezca una relación de confianza de federación entre las instancias locales y de Azure de Windows Server AD FS.

Por otro lado, si las aplicaciones requieren acceso a recursos locales, puede configurar Windows Server AD FS con la aplicación en Azure como sigue:

1. Configure la conectividad entre redes locales y Azure.
2. Configure una granja de servidores de federación de Windows Server AD FS en el bosque local.
3. Configure una granja proxy de servidores de federación de Windows Server AD FS en Azure.

Esta configuración tiene la ventaja de reducir la exposición de recursos locales, ventaja similar a la de la configuración de Windows Server AD FS con aplicaciones en una red perimetral.

Tenga en cuenta que en ambos escenarios, puede establecer relaciones de confianza con varios proveedores de identidades, si es necesaria la colaboración de negocio a negocio.

### <a name="BKMK_CloudSvcConfig"></a>Configuración de servicios en la nube
Los servicios en la nube son necesarios si desea exponer una máquina virtual directamente a Internet o una aplicación de equilibrio de carga accesible a través de Internet. Esto es posible porque cada servicio en la nube ofrece una única dirección IP virtual configurable.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos del servidor de federación para el direccionamiento IP público y privado (IP dinámica frente a IP virtual)
Cada máquina virtual de Azure recibe una dirección IP dinámica. Una dirección IP dinámica es una dirección privada accesible únicamente desde dentro de Azure. En la mayoría de los casos, sin embargo, será necesario configurar una dirección IP virtual para las implementaciones de Windows Server AD FS. La dirección IP virtual es necesaria para exponer los puntos de conexión de Windows Server AD FS a Internet y la utilizarán los socios y clientes federados para la autenticación y la administración continua. Una dirección IP virtual es una propiedad de un servicio en la nube que contiene una o más máquinas virtuales de Azure. Si las aplicaciones compatibles con notificaciones implementadas en Azure y en Windows Server AD FS son accesibles desde Internet y comparten puertos comunes, cada una necesitará una dirección IP virtual propia y, por tanto, será necesario crear un servicio en la nube para la aplicación y otro para Windows Server AD FS.

Para obtener definiciones de los términos "dirección IP virtual" y "dirección IP dinámica", consulte la sección [Términos y definiciones](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuración de alta disponibilidad de Windows Server AD FS
Aunque es posible implementar servicios de federación de Windows Server AD FS independientes, se recomienda implementar una granja con al menos dos nodos para el STS de AD FS y para los servidores proxy para entornos de producción.

Consulte [Consideraciones sobre la topología de implementación de AD FS 2.0](https://technet.microsoft.com/library/gg982489) en la [Guía de diseño de AD FS 2.0](https://technet.microsoft.com/library/dd807036) para decidir qué opciones de configuración de implementación satisfacen mejor sus necesidades específicas.

> [!NOTE]
> Para obtener el equilibrio de carga de los puntos de conexión de Windows Server AD FS en Azure, configure todos los miembros de la granja de Windows Server AD FS en el mismo servicio en la nube y utilice la funcionalidad de equilibrio de carga de Azure para los puertos HTTP (el 80 de forma predeterminada) y HTTPS (el 443 de forma predeterminada). Para obtener más información, consulte [Azure load-balancer probe](https://msdn.microsoft.com/library/azure/jj151530)(Sondeo de equilibrador de carga de Azure).
> No se admite el equilibrio de carga de red (NLB) de Windows Server en Azure.
> 
> 

