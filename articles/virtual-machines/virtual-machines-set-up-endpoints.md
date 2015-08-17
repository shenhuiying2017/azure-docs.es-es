<properties
	pageTitle="Configuración de extremos en una máquina virtual en Azure"
	description="Aprenda a configurar extremos en el portal para permitir la comunicación con una máquina virtual en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="kathydav"/>

#Configuración de extremos en una máquina virtual

Todas las máquinas virtuales que se crean en Azure pueden comunicarse automáticamente mediante un canal de red privada con otras máquinas virtuales del mismo servicio en la nube o de la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual.

Al crear una máquina virtual en el Portal de Azure, se crean automáticamente extremos para el Escritorio remoto, para el acceso remoto a Windows PowerShell y para Shell seguro (SSH). Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.

Cada extremo cuenta con un puerto público y uno privado:

- El puerto público que usa el equilibrador de carga de Azure para escuchar el tráfico que entra a la máquina virtual desde Internet.
- La máquina virtual usa el puerto privado para escuchar el tráfico entrante, normalmente destinado a una aplicación o servicio que se ejecuta en la máquina virtual.

Se proporcionan los valores predeterminados para el protocolo IP y los puertos TCP o UDP para los protocolos de red conocidos cuando crea extremos con el portal. Para los extremos personalizados, debe especificar el protocolo IP correcto (TCP o UDP) y los puertos públicos y privados. Para distribuir el tráfico entrante de forma aleatoria entre varias máquinas virtuales, debe crear un conjunto con equilibrio de carga que conste de varios extremos.

Tras la creación de un extremo, puede utilizar una lista de control de acceso (ACL) para definir reglas que permitan o denieguen el tráfico entrante al puerto público del extremo, en función de su dirección IP de origen. Sin embargo, si la máquina virtual está en una red virtual de Azure, debería usar grupos de seguridad de red en su lugar. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](https://msdn.microsoft.com/library/azure/dn848316.aspx).

**Importante**: La configuración de firewall para máquinas virtuales de Azure se realiza automáticamente para los puertos asociados con el Escritorio remoto y Shell seguro (SSH) y, en la mayoría de los casos, para la comunicación remota de Windows PowerShell. Para los puertos especificados para todos los demás extremos, no se realiza ninguna configuración automáticamente en el firewall de la máquina virtual. Cuando se crea un extremo para la máquina virtual, deberá asegurarse de que el firewall de la máquina virtual también permite el tráfico para el protocolo y el puerto privado correspondiente a la configuración del extremo.

##Creación de un extremo

1.	Si no lo ha hecho todavía, inicie sesión en el [portal](http://manage.windowsazure.com/).
2.	Haga clic en **Máquinas virtuales** y haga clic en el nombre de la máquina virtual que desea configurar.
3.	Haga clic en **Extremos**. En la página **Extremos** se enumeran todos los extremos actuales de la máquina virtual.

	![Extremos](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	En la barra de tareas, haga clic en **Agregar**.
5.	En la página **Agregar un extremo a una máquina virtual**, elija el tipo de extremo.

	- Si está creando un nuevo extremo que no forma parte de un conjunto con equilibrio de carga o el primer miembro en un nuevo conjunto con equilibrio de carga, elija **Agregar un extremo independiente** y haga clic en la flecha izquierda.
	- De lo contrario, elija **Agregar un extremo a un conjunto de equilibrio de carga existente**, seleccione el nombre del conjunto con equilibrio de carga y haga clic en la flecha izquierda. En la página **Especificar los detalles del extremo**, escriba un nombre para el extremo en **Nombre** y haga clic en la marca de verificación para crear el extremo.

6.	En la página **Especificar los detalles del extremo**, escriba un nombre para el extremo en **Nombre**. También puede elegir un nombre de protocolo de red de la lista, que rellenará los valores iniciales de **Protocolo**, **Puerto público** y **Puerto privado**.
7.	Para un extremo personalizado, en **Protocolo**, elija **TCP** o **UDP**.
8.	Para puertos personalizados, en **Puerto público**, escriba el número de puerto para el tráfico entrante desde Internet. En **Puerto privado**, escriba el número de puerto en el que escucha la máquina virtual. Estos números de puerto pueden ser diferentes. Asegúrese de que se ha configurado el firewall en la máquina virtual para permitir el tráfico correspondiente al protocolo (en el paso 7) y el puerto privado.
9.	Si este extremo será el primero en un conjunto con equilibrio de carga, haga clic en **Crear un conjunto con equilibrio de carga** y, a continuación, haga clic en la flecha derecha. En la página **Configurar el conjunto con equilibrio de carga**, especifique un nombre de conjunto de carga equilibrada, un puerto y protocolo de sondeo, y el intervalo de sondeo y el número de sondeos enviados. El equilibrador de carga de Azure envía sondeos a las máquinas virtuales en un conjunto con equilibrio de carga para supervisar su disponibilidad. El equilibrador de carga de Azure no reenvía el tráfico a las máquinas virtuales que no responden al sondeo. Haga clic en la flecha derecha.
10.	Haga clic en la marca de verificación para crear el extremo.

Ahora verá el extremo en la página **Endpoints**.

![Creación correcta del extremo](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

Para usar un cmdlet de Azure PowerShell para configurar esta opción, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

##Administración de la ACL en un extremo

Para definir el conjunto de equipos que pueden enviar tráfico, la ACL en un extremo puede restringir el tráfico en función de la dirección IP de origen. Siga estos pasos para agregar, modificar o quitar una ACL en un extremo.

> [AZURE.NOTE]Si el extremo forma parte de un conjunto con equilibrio de carga, los cambios que realice en la ACL en un extremo se aplican a todos los extremos del conjunto.

Si la máquina virtual está en una red virtual de Azure, debería usar grupos de seguridad de red en su lugar. Para obtener más información, consulte [Información sobre los grupos de seguridad de red](https://msdn.microsoft.com/library/azure/dn848316.aspx).


1.	Si no lo ha hecho todavía, inicie sesión en el portal.
2.	Haga clic en **Máquinas virtuales** y haga clic en el nombre de la máquina virtual que desea configurar.
3.	Haga clic en **Extremos**. En la página **Extremos** aparecen todos los extremos para la máquina virtual.

    ![Lista de ACL](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

4.	Seleccione el extremo apropiado de la lista.
5.	En la barra de tareas, haga clic en **Administrar dirección ACL**. Aparecerá el cuadro de diálogo **Especifique los detalles de ACL**.

    ![Especificar los detalles de ACL](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6.	Use las filas de la lista para agregar, eliminar o editar las reglas de una ACL y cambiar su orden. El valor de la **subred remota** es un intervalo de direcciones IP para el tráfico entrante de Internet que el equilibrador de carga de Azure permitirá o denegará, en función de la dirección IP de origen del tráfico. Debe especificar el intervalo de direcciones IP en formato CIDR, también conocido como formato de prefijo de dirección. Un ejemplo es 131.107.0.0/16.

Puede usar reglas para permitir solo el tráfico desde equipos específicos correspondientes a los equipos en Internet o para denegar el tráfico desde intervalos concretos de direcciones conocidas.

Las reglas se evalúan en orden, comenzando por la primera regla y terminando por la última. Esto significa que las reglas deben estar ordenadas de menos restrictivas a más restrictivas. Para obtener ejemplos y más información, consulte [Acerca de las listas de control de acceso (ACL) de red](http://go.microsoft.com/fwlink/p/?linkid=303816).

Para usar un cmdlet de Azure PowerShell para configurar esto, consulte [Administración de las listas de control de acceso (ACL) de los extremos mediante PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

## Recursos adicionales

[Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-load-balance.md)

[Acerca de las listas de control de acceso (ACL) de red](http://go.microsoft.com/fwlink/p/?linkid=303816)

[Información sobre los grupos de seguridad de red](https://msdn.microsoft.com/library/azure/dn848316.aspx)

<!---HONumber=August15_HO6-->