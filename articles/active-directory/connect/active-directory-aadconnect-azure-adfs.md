---
title: "Servicios de federación de Active Directory en Azure | Microsoft Docs"
description: "En este documento aprenderá cómo implementar AD FS en Azure para alta disponibilidad."
keywords: "implementar AD FS en azure, implementar azure adfs, azure ad fs, implementar adfs, implementar ad fs, adfs en azure, implementar adfs en azure, implementar AD FS en azure, adfs azure, introducción a AD FS, Azure, AD FS en Azure, iaas, ADFS, mover adfs a azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddd29a1230286de8999175498ee793f3b3ea24e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Implementación de Active Directory Federation Services en Azure
AD FS proporciona funcionalidades de una federación de identidades simplificada y protegida, así como de inicio de sesión único (SSO) web. La federación con Azure AD u Office 365 permite a los usuarios autenticarse con credenciales locales y acceder a todos los recursos en la nube. Por tanto, es importante disponer de una infraestructura de AD FS de alta disponibilidad para garantizar el acceso a los recursos locales y en la nube. La implementación de AD FS en Azure puede ayudar a lograr la alta disponibilidad necesaria con el mínimo esfuerzo.
La implementación de AD FS en Azure tiene varias ventajas; a continuación se enumeran algunas:

* **Alta disponibilidad** : con la potencia de Conjuntos de disponibilidad de Azure, se asegura una infraestructura de alta disponibilidad.
* **Fácil de escalar** : ¿necesita más rendimiento? Migre fácilmente a máquinas más eficaces con solo unos clics en Azure.
* **Redundancia geográfica cruzada** : con la redundancia geográfica de Azure puede estar seguro de que su infraestructura tiene alta disponibilidad en todo el mundo.
* **Fácil de administrar** : con opciones de administración muy simplificadas en Azure Portal, administrar su infraestructura es muy fácil y sin complicaciones. 

## <a name="design-principles"></a>Principios de diseño
![Diseño de la implementación](./media/active-directory-aadconnect-azure-adfs/deployment.png)

El diagrama anterior muestra la topología básica recomendada para empezar a implementar la infraestructura de AD FS en Azure. Los principios de los distintos componentes de la topología son los siguientes:

* **Servidores de ADFS/controlador de dominio**: si tiene menos de 1000 usuarios, simplemente puede instalar el rol de AD FS en los controladores de dominio. Si no desea que afecte al rendimiento de los controladores de dominio o si tiene más de 1000 usuarios, implemente AD FS en servidores independientes.
* **Servidor WAP** : es necesario implementar servidores proxy de aplicación web para que los usuarios también puedan acceder a AD FS cuando no están en la red de la empresa.
* **DMZ**: los servidores proxy de aplicación web se colocarán en la red perimetral, y SOLO se permite el acceso a TCP/443 entre dicha red y la subred interna.
* **Equilibradores de carga**: para garantizar la alta disponibilidad de servidores proxy de aplicación web y AD FS se recomienda utilizar un equilibrador de carga interno para servidores AD FS y Azure Load Balancer para los servidores proxy de aplicación web.
* **Conjuntos de disponibilidad**: para proporcionar redundancia a la implementación de AD FS, se recomienda agrupar dos o más máquinas virtuales en un conjunto de disponibilidad para cargas de trabajo similares. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible.
* **Cuentas de almacenamiento**: se recomienda tener dos cuentas de almacenamiento. Tener una cuenta de almacenamiento única puede llevar a la creación de un único punto de error y puede provocar que la implementación no esté disponible en un escenario poco probable, en el que la cuenta de almacenamiento deje de funcionar. Dos cuentas de almacenamiento ayudarán a asociar una cuenta de almacenamiento para cada línea con errores.
* **Segregación de la red**: los servidores proxy de aplicación web se deben implementar en una red perimetral independiente. Puede dividir una red virtual en dos subredes y, a continuación, implementar los servidores proxy de aplicación web en una subred aislada. Simplemente, puede definir la configuración del grupo de seguridad de red para cada subred y permitir solo la comunicación necesaria entre las dos subredes. A continuación se proporcionan más detalles para cada escenario de implementación

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Pasos para implementar AD FS en Azure
Los pasos mencionados en esta sección describen las directrices para implementar la siguiente infraestructura de AD FS en Azure.

### <a name="1-deploying-the-network"></a>1. Implementación de la red
Como se describió anteriormente, puede crear dos subredes en una única red virtual, o bien crear dos redes virtuales completamente diferentes. Este artículo se centrará en la implementación de una única red virtual y la dividirá en dos subredes. Actualmente, este enfoque es más sencillo, ya que dos redes virtuales independientes requerirían una puerta de enlace de red virtual a red virtual para las comunicaciones.

**1.1 Creación de una red virtual**

![Creación de una red virtual](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Seleccione Red virtual en Azure Portal; a continuación, puede implementar la red virtual y una subred inmediatamente con un solo clic. La subred INT también está definida y lista para las máquinas virtuales que se van a agregar.
El siguiente paso es agregar otra subred a la red: la subred DMZ. Para crear la subred DMZ, simplemente:

* Seleccione la red recién creada.
* En las propiedades, seleccione Subredes.
* En el panel Subredes, haga clic en el botón Agregar.
* Proporcione el nombre y la información del espacio de direcciones de la subred para crear la subred.

![Subred](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![Subred DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Creación de grupos de seguridad de red**

Un grupo de seguridad de red (NSG) contiene una enumeración de las reglas de la lista de control de acceso (ACL), que permiten o deniegan el tráfico de red a sus instancias de máquina virtual en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de la ACL se aplican a todas las instancias de la máquina virtual de esa subred.
Para esta guía, crearemos dos NSG: uno para una red interna y otro para una red perimetral. Se denominarán NSG_INT y NSG_DMZ respectivamente.

![Creación de NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Después de crear el NSG, no habrá ninguna regla de entrada ni de salida. Una vez que los roles de los servidores respectivos están instalados y funcionales, las reglas de entrada y de salida se pueden crear según el nivel de seguridad deseado.

![Inicialización de Git](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Después de crear los NSG, asocie NSG_INT a la subred INT y NSG_DMZ a la subred DMZ. A continuación se proporciona una captura de pantalla de ejemplo:

![Configuración de NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Haga clic en Subredes para abrir el panel Subredes.
* Seleccione la subred que se asociará al NSG. 

Después de la configuración, el panel Subredes debe ser similar al siguiente:

![Subredes después GSN](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Creación de una conexión a local**

Necesitamos una conexión local con el fin de implementar el controlador de dominio en Azure. Azure ofrece varias opciones de conectividad para conectar su infraestructura local a la infraestructura de Azure.

* De punto a sitio
* De sitio a sitio de red virtual
* ExpressRoute

Se recomienda usar ExpressRoute. ExpressRoute permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura que está en su entorno local o en un entorno de colocalización. Las conexiones ExpressRoute no pasan por la red pública de Internet. Ofrecen más confiabilidad, velocidades más altas, menores latencias y mayor seguridad que las conexiones habituales a través de Internet.
Aunque se recomienda utilizar ExpressRoute, puede elegir el método de conexión más adecuado para su organización. Para aprender más acerca de ExpressRoute y las distintas opciones de conectividad con ExpressRoute, lea [Información técnica de ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Creación de cuentas de almacenamiento
Para mantener la alta disponibilidad y evitar la dependencia de una sola cuenta de almacenamiento, puede crear dos cuentas de almacenamiento. Divida las máquinas de cada conjunto de disponibilidad en dos grupos y asigne una cuenta de almacenamiento independiente a cada grupo.

![Creación de cuentas de almacenamiento](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Creación de conjuntos de disponibilidad
Para cada rol (DC/AD FS y WAP), cree conjuntos de disponibilidad que contengan dos máquinas como mínimo. Esto le ayudará a lograr una mayor disponibilidad para cada rol. Al crear los conjuntos de disponibilidad, es esencial decidir lo siguiente:

* **Dominios de error**: máquinas virtuales en el mismo dominio de error comparten la misma fuente de alimentación y el conmutador de red física. Se recomienda un mínimo de dos dominios de error. El valor predeterminado es tres, y puede dejarlo así para esta implementación.
* **Dominios de actualización**: las máquinas que pertenecen al mismo dominio de actualización se reinician juntas durante una actualización. Conviene tener como mínimo dos dominios de actualización. El valor predeterminado es cinco y puede dejarlo así para esta implementación.

![Conjuntos de disponibilidad](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Creación de los siguientes conjuntos de disponibilidad

| Conjunto de disponibilidad | Rol | Dominios de error | Dominios de actualización |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4. Implementación de máquinas virtuales
El siguiente paso es implementar las máquinas virtuales que hospedarán los distintos roles en su infraestructura. Se recomienda un mínimo de dos máquinas en cada conjunto de disponibilidad. Cree cuatro máquinas virtuales para la implementación básica.

| Máquina | Rol | Subred | Conjunto de disponibilidad | Cuenta de almacenamiento | Dirección IP |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |Estática |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |Estática |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |Estática |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |Estática |

Como habrá observado, no se ha especificado ningún NSG. Esto es porque Azure permite usar NSG en el nivel de subred. A continuación, puede controlar el tráfico de red de la máquina mediante el NSG individual asociado a la subred o mediante el objeto NIC. Para más información consulte [¿Qué es un grupo de seguridad de red?](https://aka.ms/Azure/NSG)
Se recomienda una dirección IP estática si está administrando el DNS. Puede utilizar DNS de Azure en lugar de los registros de DNS para su dominio; haga referencia a las nuevas máquinas por su nombre de dominio completo de Azure.
Una vez completada la implementación, el panel Máquinas virtuales debe ser similar al siguiente:

![Máquinas virtuales implementadas](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Configuración de los servidores del controlador de dominio o AD FS
 Para autenticar cualquier solicitud entrante, AD FS deberá ponerse en contacto con el controlador de dominio. Para evitar el costoso recorrido de Azure a controlador de dominio local para la autenticación, se recomienda implementar una réplica del controlador de dominio en Azure. Para lograr una alta disponibilidad, se recomienda crear un conjunto de disponibilidad con al menos dos de controladores de dominio.

| Controlador de dominio | Rol | Cuenta de almacenamiento |
|:---:|:---:|:---:|
| contosodc1 |Réplica |contososac1 |
| contosodc2 |Réplica |contososac2 |

* Promueva los dos servidores a controladores de dominio de réplica con DNS.
* Instale el rol AD FS mediante el administrador del servidor para configurar los servidores AD FS.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. Implementación del Equilibrador de carga interno (ILB)
**6.1. Creación del ILB**

Para implementar un ILB, seleccione Equilibradores de carga en Azure Portal y haga clic en Agregar (+).

> [!NOTE]
> Si no ve **Equilibradores de carga** en el menú, haga clic en **Examinar** en la parte inferior izquierda del portal y desplácese hasta que vea **Equilibradores de carga**.  A continuación, haga clic en la estrella amarilla para agregarlo al menú. Ahora, seleccione el nuevo icono del equilibrador de carga para abrir el panel y empezar a configurar el equilibrador de carga.
> 
> 

![Examinar el equilibrador de carga](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nombre**: asigne un nombre adecuado al equilibrador de carga.
* **Esquema**: puesto que este equilibrador de carga se colocará delante de los servidores AD FS y está pensado SOLO para las conexiones de red interna, seleccione "Interno".
* **Red virtual**: elija la red virtual donde está implementando AD FS.
* **Subred**: elija aquí la subred interna.
* **Asignación de dirección IP**: Estática

![Equilibrador de carga interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Tras hacer clic en crear y cuando el ILB se implementa, debería verlo en la lista de equilibradores de carga:

![Equilibradores de carga después del ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

El siguiente paso es configurar el grupo back-end y el sondeo de back-end.

**6.2. Configuración del grupo back-end del ILB**

Seleccione el ILB recién creado en el panel Equilibradores de carga. Se abrirá el panel de configuración. 

1. Seleccione los grupos back-end en el panel de configuración.
2. En el panel Agregar grupo back-end, haga clic en Agregar máquina virtual.
3. Aparecerá un panel donde puede elegir el conjunto de disponibilidad.
4. Elija el conjunto de disponibilidad de AD FS.

![Configuración del grupo back-end del ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. Configuración del sondeo**

En el panel de configuración del ILB, seleccione Sondeos.

1. Haga clic en Agregar.
2. Proporcione la información para el sondeo a. **Nombre**: nombre del sondeo b. **Protocolo**: TCP c. **Puerto**: 443 (HTTPS) d. **Intervalo**: 5 (valor predeterminado). Este es el intervalo en el que el ILB comprobará las máquinas en el grupo back-end e. **Umbral incorrecto**: 2 (valor predeterminado): umbral de errores de sondeo consecutivos después del cual el ILB declara que una máquina del grupo back-end no responde y dejará de enviarle tráfico.

![Configuración del sondeo del ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. Creación de reglas de equilibrio de carga**

Con el fin de equilibrar el tráfico de forma eficaz, el ILB debe configurarse con reglas de equilibrio de carga. Para crear una regla de equilibrio de carga: 

1. Seleccione Reglas de equilibrio de carga en el panel de configuración del ILB
2. Haga clic en Agregar en el panel Reglas de equilibrio de carga.
3. En el panel Agregar regla de equilibrio de carga a. **Nombre**: escriba un nombre para la regla b. **Protocolo**: seleccione TCP c. **Puerto**: 443 d. **Puerto back-end**: 443 e. **Grupo back-end**: seleccione el grupo que creó antes para el clúster de AD FS f. **Sondeo**: seleccione el sondeo que creó antes para los servidores AD FS.

![Configuración de las reglas de equilibrio de carga](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Actualización de DNS con el ILB**

Vaya a su servidor DNS y cree un registro CNAME para el ILB. El registro CNAME debe ser para el servicio de federación con la dirección IP apuntando a la dirección IP del ILB. Por ejemplo, si la dirección DIP de ILB es 10.3.0.8 y el servicio de federación instalado es fs.contoso.com, cree un registro CNAME para fs.contoso.com apuntando a 10.3.0.8.
Esto garantizará que todas las comunicaciones relativas a fs.contoso.com terminen en el ILB y se enruten correctamente.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. Configuración del servidor proxy de aplicación web
**7.1. Configuración de los servidores proxy de aplicación web para acceder a servidores AD FS**

Con el fin de garantizar que los servidores proxy de aplicación web puedan llegar a los servidores AD FS detrás del ILB, cree un registro en %systemroot%\system32\drivers\etc\hosts para el ILB. Tenga en cuenta que el nombre distintivo (DN) debe ser el nombre del servicio de federación; por ejemplo, fs.contoso.com. Y la entrada IP debe ser la de la dirección IP del ILB (en el ejemplo, 10.3.0.8).

**7.2. Instalación del rol de proxy de aplicación web**

Después de asegurarse de que los servidores proxy de aplicación web pueden acceder a los servidores AD FS detrás del ILB puede instalar los servidores proxy de aplicación web. Los servidores proxy de aplicación web no deben unirse al dominio. Instale los roles de proxy de aplicación web en los dos servidores proxy de aplicación web seleccionando el rol de acceso remoto. El administrador del servidor le guiará para completar la instalación de WAP.
Para más información sobre cómo implementar WAP, lea [Instalar y configurar el servidor de Proxy de aplicación web](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  Implementación del equilibrador de carga accesible desde Internet (público)
**8.1.  Creación del equilibrador de carga accesible desde Internet (público)**

En Azure Portal, seleccione Equilibradores de carga y haga clic en Agregar. En el panel Crear equilibrador de carga, escriba la siguiente información:

1. **Nombre**: nombre del equilibrador de carga.
2. **Esquema**: público; esta opción indica a Azure que este equilibrador de carga necesitará una dirección pública.
3. **Dirección IP**: crea una dirección IP (dinámica).

![Equilibrador de carga accesible desde Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Después de la implementación, el equilibrador de carga aparecerá en la lista de equilibradores de carga.

![Lista de equilibradores de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. Asignación de una etiqueta DNS a la dirección IP pública**

Haga clic en la entrada del equilibrador de carga recién creada en el panel Equilibradores de carga para que aparezca el panel de configuración. Siga estos pasos para configurar la etiqueta DNS para la dirección IP pública:

1. Haga clic en la dirección IP pública. Se abrirá el panel de la dirección IP pública y su configuración.
2. Haga clic en Configuración.
3. Proporcione una etiqueta DNS. Esta etiqueta se convertirá en la etiqueta DNS pública a la que puede acceder desde cualquier lugar; por ejemplo, contosofs.westus.cloudapp.azure.com. Puede agregar una entrada en el DNS externo para el servicio de federación (como fs.contoso.com) que se resuelve en la etiqueta DNS del equilibrador de carga externo (contosofs.westus.cloudapp.azure.com).

![Configuración del equilibrador de carga accesible desde Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configuración del equilibrador de carga accesible desde Internet (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Configuración de un grupo back-end para el equilibrador de carga accesible desde Internet (público)** 

Siga los mismos pasos que al crear el equilibrador de carga interno para configurar el grupo back-end para el equilibrador de carga accesible desde Internet (público) como el conjunto de disponibilidad para los servidores WAP. Por ejemplo, contosowapset.

![Configuración del grupo back-end del equilibrador de carga accesible desde Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. Configuración del sondeo**

Siga los mismos pasos que al configurar el equilibrador de carga interno para configurar el sondeo para el grupo back-end de los servidores WAP.

![Configuración del sondeo del equilibrador de carga accesible desde Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. Creación de reglas de equilibrio de carga**

Siga los mismos pasos que en el ILB para configurar la regla de equilibrio de carga para TCP 443.

![Configuración de reglas de equilibrio del equilibrador de carga accesible desde Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. Protección de la red
**9.1. Protección de la subred interna**

En general, necesita las siguientes reglas para proteger eficazmente la subred interna (en el orden en el que aparecen a continuación).

| Regla | Descripción | Flujo |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Permitir la comunicación HTTPS desde la red perimetral |Entrada |
| DenyInternetOutbound |Sin acceso a Internet |Salida |

![Reglas de acceso INT (entrantes)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comentario]: <> (![reglas de acceso INT (entrantes)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comentario]: <> (![reglas de acceso INT (salientes)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))

**9.2. Protección de la subred DMZ**

| Regla | Descripción | Flujo |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Permite HTTPS de Internet a la red perimetral |Entrada |
| DenyInternetOutbound |No se bloquea nada, excepto HTTPS a Internet |Salida |

![Reglas de acceso EXT (entrantes)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comentario]: <> (![reglas de acceso EXT (entrantes)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comentario]: <> (![reglas de acceso EXT (salientes)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

> [!NOTE]
> Si se requiere la autenticación del certificado de usuario del cliente (autenticación de clientTLS mediante certificados de usuario X509), AD FS necesitará que el puerto TCP 49443 esté habilitado para el acceso de entrada.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. Prueba del inicio de sesión de AD FS
La forma más sencilla es probar AD FS mediante la página IdpInitiatedSignon.aspx. Para poder hacerlo, es necesario habilitar IdpInitiatedSignOn en las propiedades de AD FS. Siga estos pasos para comprobar la configuración de AD FS.

1. Ejecute el siguiente cmdlet en el servidor AD FS, con PowerShell, para habilitarlo.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Desde cualquier acceso de equipo externo https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3. Debería ver la página de AD FS como aparece a continuación:

![Prueba de la página de inicio de sesión](./media/active-directory-aadconnect-azure-adfs/test1.png)

Cuando se inicia sesión correctamente, aparecerá un mensaje de confirmación similar al siguiente:

![Prueba correcta](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Plantilla de implementación de AD FS en Azure
La plantilla implementa una instalación de 6 equipos para controladores de dominio, AD FS y WAP (2 para cada uno).

[Plantilla de implementación de AD FS en Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Puede usar una red virtual existente o crear una nueva red virtual durante la implementación de esta plantilla. A continuación se enumeran los distintos parámetros disponibles para personalizar la implementación con la descripción del uso del parámetro en el proceso de implementación. 

| Parámetro | Descripción |
|:--- |:--- |
| Ubicación |La región para implementar los recursos, por ejemplo, este de EE. UU. |
| StorageAccountType |El tipo de la cuenta de almacenamiento creada |
| VirtualNetworkUsage |Indica si se creará una nueva red virtual o se va a utilizar una existente |
| VirtualNetworkName |El nombre de la red virtual a crear, esto es obligatorio tanto en el caso del uso de una nueva red virtual como de una existente |
| VirtualNetworkResourceGroupName |Especifica el nombre del grupo de recursos donde reside la red virtual existente. Cuando se utiliza una red virtual existente, esto se convierte en un parámetro obligatorio para que la implementación pueda encontrar el identificador de dicha red virtual |
| VirtualNetworkAddressRange |El intervalo de direcciones de la nueva red virtual, obligatorio si se crea una nueva red virtual |
| InternalSubnetName |El nombre de la subred interna, obligatorio en ambas opciones de uso de red virtual (nueva o existente) |
| InternalSubnetAddressRange |El intervalo de direcciones de la subred interna, que contiene los controladores de dominio y los servidores ADFS, obligatorio si se crea una nueva red virtual. |
| DMZSubnetAddressRange |El intervalo de direcciones de la subred perimetral, que contiene los servidores proxy de aplicación de Windows, obligatorio si se crea una nueva red virtual. |
| DMZSubnetName |El nombre de la subred interna, obligatorio en ambas opciones de uso de red virtual (nueva o existente). |
| ADDC01NICIPAddress |La dirección IP interna del primer controlador de dominio, esta dirección IP se asignarán estáticamente al controlador de dominio y tiene que ser una dirección IP válida dentro de la subred interna |
| ADDC02NICIPAddress |La dirección IP interna del segundo controlador de dominio, esta dirección IP se asignarán estáticamente al controlador de dominio y tiene que ser una dirección IP válida dentro de la subred interna |
| ADFS01NICIPAddress |La dirección IP interna del primer servidor ADFS, esta dirección IP se asignarán estáticamente al servidor ADFS y tiene que ser una dirección IP válida dentro de la subred interna |
| ADFS02NICIPAddress |La dirección IP interna del segundo servidor ADFS, esta dirección IP se asignarán estáticamente al servidor ADFS y tiene que ser una dirección IP válida dentro de la subred interna |
| WAP01NICIPAddress |La dirección IP interna del primer servidor WAP, esta dirección IP se asignarán estáticamente al servidor WAP y tiene que ser una dirección IP válida dentro de la subred perimetral |
| WAP02NICIPAddress |La dirección IP interna del segundo servidor WAP, esta dirección IP se asignarán estáticamente al servidor WAP y tiene que ser una dirección IP válida dentro de la subred perimetral |
| ADFSLoadBalancerPrivateIPAddress |La dirección IP interna del equilibrador de carga ADFS, esta dirección IP se asignarán estáticamente al equilibrador de carga ADFS y tiene que ser una dirección IP válida dentro de la subred interna |
| ADDCVMNamePrefix |Prefijo del nombre de máquina virtual para los controladores de dominio |
| ADFSVMNamePrefix |Prefijo del nombre de máquina virtual para los servidores ADFS |
| WAPVMNamePrefix |Prefijo del nombre de máquina virtual para los servidores WAP |
| ADDCVMSize |El tamaño de máquina virtual de los controladores de dominio |
| ADFSVMSize |El tamaño de máquina virtual de los servidores ADFS |
| WAPVMSize |El tamaño de máquina virtual de los servidores WAP |
| AdminUserName |El nombre del administrador local de las máquinas virtuales |
| AdminPassword |La contraseña de la cuenta del administrador local de las máquinas virtuales |

## <a name="additional-resources"></a>Recursos adicionales
* [Conjuntos de disponibilidad](https://aka.ms/Azure/Availability) 
* [Equilibrador de carga de Azure](https://aka.ms/Azure/ILB)
* [Equilibrador de carga interno](https://aka.ms/Azure/ILB/Internal)
* [Equilibrador de carga accesible desde Internet](https://aka.ms/Azure/ILB/Internet)
* [Cuentas de almacenamiento](https://aka.ms/Azure/Storage)
* [Redes virtuales de Azure](https://aka.ms/Azure/VNet)
* [AD FS y vínculos de proxy de aplicación web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Pasos siguientes
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
* [Configuración y administración de AD FS con Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Implementación de AD FS en Azure de alta disponibilidad entre regiones geográficas con Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

