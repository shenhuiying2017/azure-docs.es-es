<properties 
   pageTitle="Creación de una red virtual" 
   description="Recorra los pasos para crear fácilmente una red virtual básica." 
   services="virtual-network" 
   documentationCenter="" 
   authors="cherylmc" 
   manager="adinah" 
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/20/2015"
   ms.author="cherylmc"/>

# Creación de una red virtual 



Cuando crea una red virtual, los servicios y las máquinas virtuales de la VNet pueden comunicarse de manera segura entre sí sin tener que hacerlo a través de Internet. Crear una red virtual dedicada solo en la nube es un proceso relativamente rápido y sencillo. Dado que una red virtual solo en la nube no está diseñada para la conectividad entre entornos, no es necesario adquirir ni configurar un dispositivo VPN ni certificados de autenticación. 

Una vez que crea la red virtual, puede agregarle nuevas máquinas virtuales e instancias de PaaS. Tenga en cuenta que si usa el Portal de administración para crear las máquinas virtuales, asegúrese de seleccionar **De la galería** para poder especificar la red virtual. Esto es importante ya que no podrá volver atrás y colocar una máquina virtual en una red virtual después de haber creado la máquina virtual.

[Azure Note] **Siga este procedimiento para crear una red virtual dedicada solo en la nube**. Dada la mayor complejidad que comporta el crear una configuración entre entornos, no siga este procedimiento para crear una red virtual que más adelante se conectará a la red local. Si desea crear una conexión segura entre entornos Azure y la red local, consulte [Acerca de la conectividad segura entre entornos](https://msdn.microsoft.com/library/azure/dn133798.aspx).

## <a name="CreateyourVNet">Creación de la red virtual</a>

1. Inicie sesión en el **Portal de administración**.
2. En la esquina izquierda inferior de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el asistente para configuración.
3. En la página **Detalles de la red virtual**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  **Nombre:** asigne un nombre a la red virtual. Utilizará este nombre de red virtual al implementar las máquinas virtuales y los servicios, por lo que es preferible que este nombre no sea muy complicado.

	-  **Ubicación:** en la lista desplegable, seleccione la región deseada. La red virtual se creará en el centro de datos de Azure ubicado en la región especificada.



4. En la página **Servidores DNS y conectividad de VPN**, no realice ningún cambio. Simplemente haga clic en la flecha para avanzar a la página siguiente. De modo predeterminado, Azure proporciona una resolución de nombres básica para la red virtual. Es posible que sus requisitos de resolución de nombres sean más complejos de lo que pueda manejar la resolución de nombres básica de Azure. En cuyo caso, puede que desee agregar más adelante una máquina virtual que ejecute el DNS a su red virtual. Para obtener más información acerca de la resolución de nombres de Azure y DNS, vea [Name Resolution](https://msdn.microsoft.com/library/azure/jj156088.aspx). 
5. La página **Espacios de direcciones de la red virtual** es donde introduce el espacio de direcciones que desea usar para esta VNet. A menos que exija un cierto intervalo de direcciones IP internas para las máquinas virtuales o desee crear una subred específica para las máquinas virtuales que recibirán una DIP estática, no es necesario que realice ningún cambio en esta página. Si desea crear varias subredes, puede hacer clic en **agregar subred** en esta página para hacerlo. Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx).

	-  Para obtener más información acerca de la configuración de la página de detalles, consulte la sección **Página Detalles de red virtual** del documento [Acerca de la configuración de una red virtual en el Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx).
	-  Dado que no va a conectar esta red virtual privada a la red local mediante una configuración de VPN entre entornos, no es necesario que coordine estas configuraciones con los intervalos de direcciones IP existentes de la red local. Si cree que puede querer crear una configuración entre entornos más adelante, deberá coordinar los espacios de direcciones ahora con los intervalos que ya existen en el sitio local a fin de evitar problemas de enrutamiento. Cambiar los intervalos más adelante puede ser complicado y generalmente dará lugar a que tenga que repetir la implementación.


6. Haga clic en la marca de verificación de la parte inferior derecha de la página Espacios de direcciones de la red virtual y se empezará a crear la red virtual. Cuando se cree, verá "Created" debajo de "Status" en la página de redes del Portal de administración.
7. Una vez creada la red virtual, puede implementar en su VNet. Por ejemplo, si desea implementar una máquina virtual en la red virtual, consulte [Agregar una máquina virtual a una red virtual](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/). Seleccione **De la galería** al crear una nueva máquina virtual para tener la opción de seleccionar su red virtual. Recuerde que si ya tiene implementadas instancias existentes de máquinas virtuales y PaaS, estas no se pueden mover a su nuevo VNet. Esto se debe a que los valores de configuración de red se configuran para ellas durante la implementación. Tendrá que volver a implementarlos en la nueva VNet.



## Pasos siguientes
-  [Información técnica de la red virtual](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
-  [Agregar una máquina virtual a una red virtual](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [Preguntas frecuentes sobre redes virtuales](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [Configurar una red virtual con los archivos de configuración de red](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

-  [Información general de resolución de nombres de Azure](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
