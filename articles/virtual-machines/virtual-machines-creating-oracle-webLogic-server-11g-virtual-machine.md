<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Creación de una máquina virtual Oracle WebLogic Server 12c y Oracle Database 12C en Azure" description="Revise paso a paso un ejemplo de cómo crear una imagen de Oracle WebLogic Server 12c y de Oracle Database 12c que se ejecuta en Windows Server 2012 en Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Creación de una máquina Virtual Oracle WebLogic Server 11g en Azure
En el ejemplo siguiente se muestra cómo puede crear una máquina virtual basada en una imagen Oracle WebLogic Server 11g que se ejecuta en Microsoft con Windows Server 2008 R2 en Azure.

##Para crear una máquina virtual Oracle WebLogic Server 11g en Azure

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2. Haga clic en **Marketplace**, haga clic en Proceso y, a continuación, escriba **Oracle** en el cuadro de búsqueda.

3. Seleccione o revise la información sobre esta imagen (por ejemplo, el tamaño mínimo recomendado) y, a continuación, haga clic.

4. Especifique un **Nombre de host** para la máquina virtual.

5. Especifique un **Nombre de usuario** para la máquina virtual. Tenga en cuenta que este usuario permite iniciar sesión de manera remota en la máquina virtual; no es el nombre de usuario de la base de datos de Oracle.

6. Especifique y confirme una contraseña para la máquina virtual o proporcione una clave pública SSH.

7. Tenga en cuenta que los niveles de precios recomendados se muestran de forma predeterminada. Para ver todas las opciones de configuración, haga clic en **Ver todo** en la parte superior derecha.

8. Establezca [Configuración opcional](https://msdn.microsoft.com/library/azure/dn763935.aspx) según sea necesario, con estas consideraciones:

	1. Deje **Cuenta de almacenamiento** tal y como se encuentra para crear una nueva cuenta de almacenamiento con el nombre de la máquina virtual.

	2. Deje **Conjunto de disponibilidad** como "No configurado".

	3. No agregue ningún **extremo** en este momento.

9. Elija o cree un [grupo de recursos](resource-group-portal.md)

10. Elija una **suscripción**

11. Elija una **ubicación**

##Para configurar su máquina virtual Oracle WebLogic Server 11g en Azure

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2. Haga clic en **Máquinas virtuales**.

3. Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.

4. Haga clic en **Conectar**.

5. Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

6. Haga clic en **Inicio de Windows**, haga clic en **Todos los programas**, en **Oracle WebLogic**, en **WebLogic Server 11g R1**, en **Herramientas**, y, a continuación, en **Asistente para configuración**.

7. En el cuadro de diálogo de **Bienvenida**, seleccione **Crear un nuevo dominio de WebLogic** y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image30.png)

8. En el cuadro de diálogo **Seleccionar origen de dominio**, acepte los valores predeterminados y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image31.png)

9. En el cuadro de diálogo **Especificar nombre de dominio y ubicación**, acepte los valores predeterminados y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image32.png)

10. En el cuadro de diálogo **Configurar el nombre de usuario y la contraseña de administrador**:

	1. [Opcional] Cambie el nombre de usuario de **weblogic** a un valor que desee.

	2. Especifique y confirme una contraseña para el administrador del WebLogic Server.

	3. Haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image33.png)

11. En el cuadro de diálogo **Configurar modo de inicio del servidor y JDK**, seleccione **Modo de producción**, seleccione el JDK disponible (o el explorador para un JDK si lo desea) y haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image34.png)

12.	En el cuadro de diálogo **Seleccionar configuración opcional**, no seleccione ninguna opción y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

13.	En el cuadro de diálogo **Resumen de configuración**, haga clic en **Crear**.
	
	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

14.	En el cuadro de diálogo **Crear dominio**, marque la opción **Iniciar el servidor de administración** y, a continuación, haga clic en **Listo**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image37.png)

15.	A continuación, se iniciará un símbolo del sistema para **startWebLogic.cmd**. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

## Para instalar una aplicación en una máquina virtual Oracle WebLogic Server 11g en Azure

1. Si sigue conectado a la máquina virtual, copie el ejemplo de shoppingcart.war que se encuentra disponible en [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) localmente. Por ejemplo, cree una carpeta denominada **c:\\mywar** y guarde el WAR de [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) en **c:\\mywar**.

2. Abra la **consola de administración de WebLogic Server**, [http://localhost:7001/console](http://localhost:7001/console). Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

3. Dentro de la **Consola de administración de WebLogic Server**, haga clic en **Bloquear y editar**, haga clic en **Implementaciones** y, a continuación, haga clic en **Instalar**.

4. En **Ruta de acceso**, escriba `c:\mywar\shoppingcart.war`.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image38.png)

	Haga clic en **Siguiente**.

5. Seleccione **Instalar esta implementación como una aplicación** y, a continuación, haga clic en **Siguiente**.

6. Haga clic en **Finalizar**

7. Dentro de la **Consola de administración de WebLogic Server**, haga clic en **Activar cambios**.

8. Haga clic en **Implementaciones**, seleccione **shoppingcart**, haga clic en **Inicio**, y, a continuación, haga clic en **Atender todas las solicitudes**. Cuando se le solicite confirmar, haga clic en **Sí**.

9. Para ver la aplicación de carro de la compra que se ejecuta localmente, abra un explorador en [http://localhost:7001/shoppingcart](http://localhost:7001/shoppingcart)

10.  Permita una conexión entrante a través del firewall al puerto 7001.

	1. Mientras está conectado a su **Máquina virtual**, haga clic en **Inicio de Windows**, en **Panel de control**, en **Sistema y seguridad**, en **Firewall de Windows**, y, a continuación, en **Configuración avanzada**. De este modo se abre la consola de administración **Firewall de Windows con seguridad avanzada**.

	2. En la consola de administración del firewall, haga clic en **Reglas de entrada** en el panel izquierdo (si no ve la **Regla de entrada**, expanda el nodo superior del panel izquierdo) y, a continuación, haga clic en **Nueva regla** en el panel derecho.

	3. Para **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.

	4. Para **Protocolo y puerto**, seleccione **TCP**, **Puertos locales específicos**, escriba **7001** para el puerto y, a continuación, haga clic en **Siguiente**.

	5. Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	6. Acepte los valores predeterminados para los perfiles a los que se aplica la regla y haga clic en **Siguiente**.

	7. Especifique un nombre para la regla y, opcionalmente, una descripción y, a continuación, haga clic en **Finalizar**.

11. Cree un extremo para la máquina virtual:

	1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

    2. Haga clic en **Examinar**.

    3. Haga clic en **Máquinas virtuales**

    4. Seleccione la máquina virtual.

	5. Haga clic en **Configuración**

    6. Haga clic en **Extremos**.

    7. Haga clic en **Agregar**.

    8. Escriba un nombre para el extremo

		1. Use **TCP** para el protocolo

        2. Use **80** para el puerto público

        3. Use **7001** para el puerto privado.

    9. No cambie el resto de las opciones

	10. Haga clic en **Aceptar**.

12. Para ver la aplicación de carro de la compra que se ejecuta en Internet, abra un explorador en la dirección URL en forma de `http://<<unique_domain_name>>/shoppingcart`. (Puede determinar el valor de `<<unique_domain_name>>` dentro del [Portal de Azure](https://ms.portal.azure.com/) haciendo clic en V**irtual machines** y, a continuación, seleccionando la máquina virtual que está usando para ejecutar Oracle WebLogic Server).

## Recursos adicionales

Ahora que ha configurado la máquina virtual que se está ejecutando en el Oracle WebLogic Server, consulte los temas siguientes para obtener información adicional.

- [Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentación del producto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Imágenes de máquina virtual de Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO2-->