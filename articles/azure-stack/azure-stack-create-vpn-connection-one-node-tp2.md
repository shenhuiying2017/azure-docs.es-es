---
title: "Creación de una conexión VPN de sitio a sitio entre dos redes virtuales en diferentes entornos de POC de Azure Stack | Microsoft Docs"
description: "Procedimiento paso a paso que permite a un administrador en la nube crear una conexión VPN de sitio a sitio entre dos entornos de POC de un nodo en TP2."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Creación de una conexión VPN de sitio a sitio entre dos redes virtuales en diferentes entornos de POC de Azure Stack
## <a name="overview"></a>Información general
En este artículo se muestra cómo crear una conexión VPN de sitio a sitio entre dos redes virtuales en dos entornos independientes de prueba de concepto (POC) de Azure Stack. Al configurar las conexiones, obtendrá información sobre cómo funcionan las puertas de enlace de VPN en Azure Stack.

> [!NOTE]
> Este documento se aplica específicamente a la POC de Azure Stack TP2.
> 
> 

### <a name="connection-diagram"></a>Diagrama de conexión
El diagrama siguiente muestra el aspecto que debería tener la configuración cuando haya terminado:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Antes de empezar
Para completar esta configuración, asegúrese de tener los elementos siguientes antes de empezar:

* Dos servidores que cumplan los requisitos de hardware de POC de Azure Stack que se definen en [Azure Stack Deployment Prerequisites](azure-stack-deploy.md) (Requisitos previos a la implementación de Azure Stack) y los demás requisitos previos que se definen en ese documento.
* El paquete de implementación de Azure Stack Technical Preview 2.

## <a name="deploy-the-poc-environments"></a>Implementación de los entornos de POC
Implementará dos entornos de POC de Azure Stack para completar esta configuración.

* Para cada POC que implemente, puede seguir las instrucciones que se detallan en el artículo [Deploy Azure Stack POC](azure-stack-run-powershell-script.md) (Implementación de POC de Azure Stack).
  Cada entorno de POC de este documento se denomina genéricamente *POC1* y *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Configuración de las cuotas de proceso, red y almacenamiento
Primero debe configurar las *cuotas* de proceso, red y almacenamiento. Estos servicios se pueden asociar a un *plan* y luego a una *oferta* a la que puedan suscribirse los inquilinos.

> [!NOTE]
> Necesitará realizar estos pasos para cada entorno de POC de Azure Stack.
> 
> 

La creación de cuotas para los servicios es distinta de la de TP1. Los pasos para crear cuotas en TP2 pueden encontrarse en <http://aka.ms/mas-create-quotas>. Puede aceptar los valores predeterminados para todas las configuraciones de cuota de este ejercicio.

## <a name="create-a-plan-and-offer"></a>Creación de un plan y una oferta
Los [planes](azure-stack-key-features.md) son agrupaciones de uno o varios servicios. Como proveedor puede crear planes y ofrecérselos a sus inquilinos. A su vez, los inquilinos se suscriben a las ofertas para usar los planes y servicios que incluyen.

> [!NOTE]
> Tiene que realizar estos pasos para cada entorno de POC de Azure Stack.
> 
> 

1. En primer lugar cree un plan. Para ello, puede seguir los pasos descritos en el artículo en línea de [Creación de un plan](azure-stack-create-plan.md).
2. Cree una oferta siguiendo los pasos descritos en [Creación de una oferta en Azure Stack](azure-stack-create-offer.md).
3. Inicie sesión en el portal como un administrador de inquilinos y [suscríbase a la oferta que creó](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Creación de los recursos de red en POC 1
Ahora lo que tiene que hacer es crear los recursos que necesita para establecer la configuración. Los siguientes pasos muestran lo que tiene que hacer. Estas instrucciones muestran cómo crear recursos mediante el portal, pero se puede hacer lo mismo con PowerShell.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Inicio de sesión como un inquilino
Un administrador de servicios puede iniciar sesión como un inquilino para probar los planes, ofertas y suscripciones que pueden usar sus inquilinos. Si aún no tiene una, [cree una cuenta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sesión.

### <a name="create-the-virtual-network--vm-subnet"></a>Creación de la red virtual y de la subred de máquina virtual
1. Inicie sesión con una cuenta de inquilino.
2. En Azure Portal, haga clic en **Nuevo**.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Seleccione **Redes** en el menú de Marketplace.
4. Haga clic en el elemento **Red virtual** del menú.
5. Haga clic en **Crear** cerca de la parte inferior de la hoja de descripción de recursos. Escriba los siguientes valores en los campos correspondientes de acuerdo con esta tabla.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nombre |vnet-01 |
   | Espacio de direcciones |10.0.10.0/23 |
   | Nombre de subred |subnet-01 |
   | Intervalo de direcciones de subred |10.0.10.0/24 |
6. Debería ver la suscripción que creó anteriormente rellena en el campo **Suscripción**.
7. Para Grupo de recursos, puede crear un nuevo grupo de recursos o si ya tiene uno, seleccione **Use existing** (Usar existente).
8. Compruebe la ubicación predeterminada.
9. Haga clic en **Create**(Crear).

### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace
1. Abra el recurso de red virtual que acaba de crear (Vnet-01) desde el panel.
2. En la hoja Configuración, seleccione **Subredes**.
3. Haga clic en **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada.
   Las subredes de puerta de enlace son especiales y tienen que tener este nombre específico para funcionar correctamente.
5. En el campo **Intervalo de direcciones**, escriba **10.0.11.0/24**.
6. Haga clic en **Crear** para crear la subred de puerta de enlace.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual
1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. Revise la descripción y haga clic en **Crear**.
5. En el campo **Nombre** escriba **GW1**.
6. Haga clic en el elemento **Red virtual** para seleccionar una red virtual.
   Seleccione **Vnet-01** en la lista.
7. Haga clic el elemento de menú **Dirección IP pública**. Cuando la hoja **Elegir dirección IP pública** se abra, haga clic en **Crear nuevo**.
8. En el campo **Nombre**, escriba **GW1 PiP** y haga clic en **Aceptar**.
9. El **Tipo de puerta de enlace** debe tener seleccionada la opción **VPN** de forma predeterminada. Mantenga este ajuste.
10. El **Tipo de VPN** debe tener seleccionada la opción **Route-based** (Basada en enrutamiento) de forma predeterminada.
    Mantenga este ajuste.
11. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Si lo desea, puede anclar el recurso en el panel. Haga clic en **Create**(Crear).

### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local
La implementación de una *puerta de enlace de red local* en esta implementación de evaluación de Azure Stack es ligeramente diferente a la de una implementación real de Azure.

Al igual que en Azure, tiene el concepto de una puerta de enlace de red local. Sin embargo, en una implementación de Azure una puerta de enlace de red local representa un dispositivo físico local (en el inquilino) que se usa para conectar a una puerta de enlace de red virtual en Azure. Pero en esta implementación de evaluación de Azure Stack, ambos extremos de la conexión son puertas de enlace de red virtual.

Una manera de ver esto de forma más genérica es que el recurso de la puerta de enlace de red local está siempre pensado para indicar la puerta de enlace remota en el otro extremo de la conexión. Debido al modo en que se diseñó la POC, es necesario proporcionar la dirección IP del adaptador de red externo en la máquina virtual de NAT de la otra POC como la dirección IP pública de la puerta de enlace de red local. A continuación, creará asignaciones en la máquina virtual de NAT para asegurarse de que ambos extremos están conectados correctamente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obtención de la dirección IP del adaptador externo de la máquina virtual de NAT
1. Inicie sesión en la máquina física de Azure Stack para POC2.
2. Presione la [tecla Windows] + R para abrir el menú **Ejecutar**, escriba **mstsc** y presione ENTRAR.
3. En el campo **Equipo** escriba el nombre **MAS-BGPNAT01** y haga clic en  **Conectar**.
4. Haga clic en menú Inicio, luego haga clic con el botón derecho en **Windows PowerShell** y seleccione **Ejecutar como administrador**.
5. Escriba **ipconfig/all**.
6. Encuentre el adaptador de Ethernet que está conectado a la red local y tome nota de la dirección IPv4 enlazada a ese adaptador. En nuestro entorno de ejemplo, es **10.16.167.195** pero en el suyo será algo diferente.
7. Anote esta dirección. Esto es lo que va a usar como la dirección IP pública del recurso de la puerta de enlace de red local que cree en POC1.

### <a name="create-the-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local
1. Inicie sesión en la máquina física de Azure Stack para POC1.
2. En el campo **Equipo** escriba el nombre **MAS-CON01** y haga clic en **Conectar**.
3. En Azure Portal, haga clic en **Nuevo**.
   
4. Seleccione **Redes** en el menú de Marketplace.
5. Seleccione **local network gateway** (puerta de enlace de red local) en la lista de recursos de red.
6. En el campo **Nombre** escriba **POC2-GW**.
7. No conoce aún la dirección IP de la otra puerta de enlace, pero no importa porque puede regresar y cambiarla más adelante. Por ahora, escriba **10.16.167.195** en el campo **Dirección IP**.
8. En el campo **Espacio de direcciones** escriba el espacio de direcciones de la red virtual que va a crear en POC2. Este va a ser **10.0.20.0/23** así que escriba ese valor.
9. Compruebe que la **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y haga clic en **Crear**.

### <a name="create-the-connection"></a>Creación de la conexión
1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Conexión** en la lista de recursos.
4. En la hoja **Básico**, elija **Site-to-site (IPSec)** [Sitio a sitio (IPSec)] como el **Tipo de conexión**.
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
6. En la hoja **Configuración**, elija la **Puerta de enlace de red Virtual** (**GW1**) que creó anteriormente.
7. Elija la **puerta de enlace de red local** (**POC2-GW**) que creó anteriormente.
8. En el campo **Nombre de la conexión**, escriba **POC1- POC2**.
9. En el campo **Clave compartida (PSK)**escriba **12345** y haga clic en **Aceptar**.

### <a name="create-a-vm"></a>Crear una VM
Para validar los datos que se desplazan a través de la conexión VPN, es necesario que las máquinas virtuales envíen y reciban datos en cada POC. Cree una máquina virtual en POC1 ahora y colóquela en la subred de máquina virtual en la red virtual.

1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Máquinas virtuales** en el menú de Marketplace.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2012 R2 Datacenter**.
4. En la hoja **Datos básicos**, en el campo **Nombre** escriba **VM01**.
5. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
7. En la hoja **Tamaño** elija un tamaño de máquina virtual para esta instancia y, a continuación, haga clic en **Seleccionar**.
8. En la hoja **Configuración** puede aceptar los valores predeterminados; solo tiene que comprobar que la red virtual seleccionada es **VNET-01** y que la subred está establecida en **10.0.10.0/24**. Haga clic en **Aceptar**.
9. Revise la configuración en la hoja **Resumen** y haga clic en **Aceptar**.

## <a name="create-the-network-resources-in-poc-2"></a>Creación de los recursos de red en POC 2
### <a name="log-in-as-a-tenant"></a>Inicio de sesión como un inquilino
Un administrador de servicios puede iniciar sesión como un inquilino para probar los planes, ofertas y suscripciones que pueden usar sus inquilinos. Si aún no tiene una, [cree una cuenta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sesión.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creación de la red virtual y la subred de máquina virtual
1. Inicie sesión con una cuenta de inquilino.
2. En Azure Portal, haga clic en **Nuevo**.
   
3. Seleccione **Redes** en el menú de Marketplace.
4. Haga clic en **Red virtual** en el menú.
5. Haga clic en **Crear** cerca de la parte inferior de la hoja de descripción de recursos. Escriba los siguientes valores para los campos correspondientes que aparecen en la tabla siguiente.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nombre |vnet-02 |
   | Espacio de direcciones |10.0.20.0/23 |
   | Nombre de subred |subnet-02 |
   | Intervalo de direcciones de subred |10.0.20.0/24 |
6. Debería ver la suscripción que creó anteriormente rellena en el campo **Suscripción**.
7. Para Grupo de recursos, puede crear un nuevo grupo de recursos o si ya tiene uno, seleccione **Use existing** (Usar existente).
8. Compruebe la **ubicación** predeterminada. Si lo desea, también puede anclar la red virtual en el panel para facilitar el acceso.
9. Haga clic en **Create**(Crear).

### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace
1. Abra el recurso de red virtual que acaba de crear (**Vnet-02**) desde el panel.
2. En la hoja **Configuración**, seleccione **Subredes**.
3. Haga clic en **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.
   
4. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada.
   Las subredes de puerta de enlace son especiales y tienen que tener este nombre específico para funcionar correctamente.
5. En el campo **Intervalo de direcciones**, escriba **10.0.20.0/24**.
6. Haga clic en **Crear** para crear la subred de puerta de enlace.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual
1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. Revise la descripción y haga clic en **Crear**.
5. En el campo **Nombre** escriba **GW2**.
6. Haga clic en **Red virtual** para elegir una red virtual.
   Seleccione **Vnet-02** en la lista.
7. Haga clic en **Dirección IP pública**. Cuando la hoja **Elegir dirección IP pública** se abra, haga clic en **Crear nuevo**.
8. En el campo **Nombre**, escriba **GW2-PiP** y haga clic en **Aceptar**.
9. El **Tipo de puerta de enlace** debe tener seleccionada la opción **VPN** de forma predeterminada. Mantenga este ajuste.
10. El **Tipo de VPN** debe tener seleccionada la opción **Route-based** (Basada en enrutamiento) de forma predeterminada.
    Mantenga este ajuste.
11. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Si lo desea, puede anclar el recurso en el panel. Haga clic en **Crear**.

### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obtención de la dirección IP del adaptador externo de la máquina virtual de NAT
1. Inicie sesión en la máquina física de Azure Stack para POC1.
2. Presione y mantenga presionada la [tecla Windows] + R para abrir el menú **Ejecutar**, escriba **mstsc** y presione ENTRAR.
3. En el campo **Equipo** escriba el nombre **MAS-BGPNAT01** y haga clic en  **Conectar**.
4. Haga clic en menú Inicio, luego haga clic con el botón derecho en **Windows PowerShell** y seleccione **Ejecutar como administrador**.
5. Escriba **ipconfig/all**.
6. Encuentre el adaptador de Ethernet que está conectado a la red local y tome nota de la dirección IPv4 enlazada a ese adaptador. En nuestro entorno de ejemplo, es **10.16.169.131** pero en el suyo será algo diferente.
7. Anote esta dirección. Esto es lo que va a usar más tarde como dirección IP pública del recurso de la puerta de enlace de red local que cree en POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Creación del recurso de puerta de enlace de red local
1. Inicie sesión en la máquina física de Azure Stack para POC2.
2. En el campo **Equipo** escriba el nombre **MAS-CON01** y haga clic en **Conectar**.
3. En Azure Portal, haga clic en **Nuevo**.
   
4. Seleccione **Redes** en el menú de Marketplace.
5. Seleccione **local network gateway** (puerta de enlace de red local) en la lista de recursos de red.
6. En el campo **Nombre** escriba **POC1-GW**.
7. Ahora necesita la dirección IP pública que registró para la puerta de enlace de red virtual en POC1. Escriba **10.16.169.131** en el campo **Dirección IP**.
8. En el campo **Espacio de direcciones** escriba el espacio de direcciones de **Vnet-01** de POC1 - **10.0.0.0/16**.
9. Compruebe que la **Suscripción**, **Grupo de recursos** y **Ubicación** son correctos y haga clic en **Crear**.

## <a name="create-the-connection"></a>Creación de la conexión
1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Conexión** en la lista de recursos.
4. En la hoja **Básico**, elija **Site-to-site (IPSec)** [Sitio a sitio (IPSec)] como el **Tipo de conexión**.
5. Seleccione la **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
6. En la hoja **Configuración**, elija la **Puerta de enlace de red Virtual** (**GW1**) que creó anteriormente.
7. Elija la **Puerta de enlace de red local** (**POC1-GW**) que creó anteriormente.
8. En el campo **Nombre de la conexión**, escriba **POC2- POC1**.
9. En el campo **Clave compartida (PSK)**escriba **12345**. Si elige un valor diferente, recuerde que TIENE QUE coincidir con el valor de clave compartida que creó en POC1. Haga clic en **Aceptar**.

## <a name="create-a-vm"></a>Crear una VM
Cree una máquina virtual en POC1 ahora y colóquela en la subred de máquina virtual en la red virtual.

1. En Azure Portal, haga clic en **Nuevo**.
   
2. Seleccione **Máquinas virtuales** en el menú de Marketplace.
3. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2012 R2 Datacenter**.
4. En la hoja **Datos básicos**, en el campo **Nombre** escriba **VM02**.
5. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
7. En la hoja **Tamaño** elija un tamaño de máquina virtual para esta instancia y, a continuación, haga clic en **Seleccionar**.
8. En la hoja Configuración puede aceptar los valores predeterminados; solo tiene que comprobar que la red virtual seleccionada es **VNET-02** y que la subred está establecida en **20.0.0.0/24**. Haga clic en **Aceptar**.
9. Revise la configuración en la hoja **Resumen** y haga clic en **Aceptar**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Configuración de la máquina virtual de NAT en cada POC para el cruce seguro de puerta de enlace
Dado que la POC se ha diseñado para ser independiente y aislada de la red en la que se implementa el host físico, la red de VIP "Externa" a la que están conectadas las puertas de enlace no es realmente externa, sino que está oculta detrás de un enrutador que realiza la traducción de direcciones de red (NAT). El enrutador es realmente una máquina virtual de Windows Server (**MAS-BGPNAT01**) que ejecuta el rol de enrutamiento y los servicios de acceso remoto (RRAS) en la infraestructura de la POC. Tiene que configurar NAT en la máquina virtual MAS-BGPNAT01 para permitir la conexión de VPN de sitio a sitio para conectarse en ambos extremos. Para ello, tiene que crear una asignación de NAT estática que asigne la interfaz externa en la máquina virtual BGPNAT a la dirección VIP del grupo de puerta de enlace de perímetro para los puertos necesarios para una conexión VPN.

> [!NOTE]
> Esta configuración es necesaria solo para los entornos de POC.
> 
> 

### <a name="configure-nat"></a>Configuración de NAT
Tiene que seguir estos pasos en AMBOS entornos de POC.

1. Inicie sesión en la máquina física de Azure Stack para POC1.
2. Presione y mantenga presionada la [tecla Windows] + R para abrir el menú **Ejecutar**, escriba **mstsc** y presione **Entrar**.
3. En el campo **Equipo** escriba el nombre **MAS-BGPNAT01** y haga clic en **Conectar**.
4. Haga clic en menú Inicio, luego haga clic con el botón derecho en **Windows PowerShell** y seleccione **Ejecutar como administrador**.
5. Escriba **ipconfig/all**.
6. Encuentre el adaptador de Ethernet que está conectado a la red local y tome nota de la dirección IPv4 enlazada a ese adaptador. En nuestro entorno de ejemplo, es **10.16.169.131** (marcado en rojo a continuación), pero en el suyo será algo diferente.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Escriba el siguiente comando de PowerShell para designar la dirección NAT externa para los puertos de la autenticación IKE. No olvide cambiar la dirección IP para que coincida con la de su entorno.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. A continuación, cree una asignación de NAT estática para asignar la dirección externa a la dirección IP pública de puerta de enlace para asignar el puerto 500 ISAKMP para la FASE 1 del túnel IPSEC.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> El parámetro `-InternalAddress` aquí es la dirección IP pública de la puerta de enlace de red virtual que creó anteriormente.  Para buscar esta dirección IP, mire en las propiedades de la hoja de la puerta de enlace de red virtual y busque el valor de la dirección IP pública.       

9. Por último, tiene que configurar el cruce seguro de NAT que utiliza el puerto 4500 para establecer correctamente el túnel IPEC completo sobre los dispositivos NAT.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> El parámetro `-InternalAddress` aquí es la dirección IP pública de la puerta de enlace de red virtual que creó anteriormente.  Para buscar esta dirección IP, mire en las propiedades de la hoja de la puerta de enlace de red virtual y busque el valor de la dirección IP pública.       

10. Repita los pasos del 1 al 9 en POC2.

## <a name="test-the-connection"></a>Comprobación de la conexión
Ahora que se ha establecido la conexión de sitio a sitio, debe comprobar que puede hacer que fluya tráfico a través de ella. Esta tarea es simple, no tiene más que iniciar una sesión en una de las máquinas virtuales creadas en cualquiera de los dos entornos POC y hacer ping a la máquina virtual que creó en el otro entorno. Para confirmar que el tráfico pasa a través de la conexión de sitio a sitio, tiene que asegurarse de que hace ping a la dirección IP directa (DIP) de la máquina virtual en la subred remota, no a la dirección VIP. Para ello, tiene que encontrar y anotar la dirección en el otro extremo de la conexión.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Inicio de sesión en la máquina virtual del inquilino en POC1
1. Inicie sesión en la máquina física de Azure Stack para POC1 e inicie sesión en el Portal mediante una cuenta de inquilino.
2. En la barra de navegación de la izquierda, haga clic en **Máquinas virtuales**.
3. Encuentre la máquina **VM01** que creó anteriormente en la lista de máquinas virtuales y haga clic en ella.
4. En la hoja de la máquina virtual, haga clic en **Conectar**.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Abra un símbolo del sistema desde dentro de la máquina virtual y escriba **ipconfig/all**.
6. Encuentre la **dirección IPv4** en la salida y anótela. Esta es la dirección a la que hará ping desde POC2. En el entorno de ejemplo, la dirección es **10.0.10.4**, pero en su entorno puede ser diferente. De todas formas, debe estar dentro de la subred **10.0.10.0/24** que se creó anteriormente.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Inicio de sesión en la máquina virtual del inquilino en POC2
1. Inicie sesión en la máquina física de Azure Stack para POC2 e inicie sesión en el portal mediante una cuenta de inquilino.
2. En la barra de navegación de la izquierda, haga clic en **Máquinas virtuales**.
3. Encuentre la máquina **VM02** que creó anteriormente en la lista de máquinas virtuales y haga clic en ella.
4. En la hoja de la máquina virtual, haga clic en **Conectar**.
   
5. Abra un símbolo del sistema desde dentro de la máquina virtual y escriba **ipconfig/all**.
6. Debería ver una dirección IPv4 que se encuentre dentro de 10.0.20.0/24. En el entorno de ejemplo, la dirección es 10.0.20.4, pero en su entorno puede ser diferente.
7. Ahora, desde la máquina virtual en POC2 tiene que hacer ping a la máquina virtual en POC1, a través del túnel. Para ello haga ping a la DIP que anotó de VM01.
   En el entorno de ejemplo la dirección es 10.0.10.4, pero no olvide hacer ping a la dirección que haya anotado en su laboratorio. Debe ver un resultado con el siguiente aspecto:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Una respuesta de la máquina virtual remota indica que la prueba ha dado un resultado correcto. Puede cerrar la ventana de conexión de máquina virtual. O puede intentar realizar alguna otra transferencia de datos, como una copia de archivo, para probar la conexión.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualización de las estadísticas de transferencia de datos a través de la conexión de puerta de enlace
Si desea saber qué cantidad de datos pasa a través de la conexión de sitio a sitio, esta información está disponible en la hoja de la conexión. Esta prueba también es otra buena forma de comprobar que el ping que acaba de enviar ha ido a través de la conexión VPN.

1. Sin cerrar la sesión de inquilino de máquina virtual en POC2, inicie sesión en el **portal de POC de Microsoft Azure Stack** con su cuenta de inquilino.
2. Haga clic en el elemento de menú **Examinar** y seleccione **Conexiones**.
3. Haga clic en la conexión **POC2-POC1** en la lista.
4. En la hoja de la conexión, puede ver las estadísticas de **Data in** (Datos de entrada) y **Data out** (Datos de salida). En la siguiente captura de pantalla verá que algunos números son más grandes que los que crearía un simple ping. Esto es debido a que se han realizado también algunas transferencias de archivo adicionales. Debería ver algunos valores distintos de cero.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


