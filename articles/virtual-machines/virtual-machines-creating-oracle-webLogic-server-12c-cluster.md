<properties title="Creating an Oracle WebLogic Server 12c cluster in Azure" pageTitle="Creación de un clúster Oracle WebLogic Server 12c en Azure" description="Revise un ejemplo de creación de un clúster de Oracle WebLogic Server 12c en Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Creación de un clúster Oracle WebLogic Server 12c en Azure
En el ejemplo siguiente se muestra cómo puede crear un clúster Oracle WebLogic Server en Azure, basado en una imagen Oracle WebLogic Server 12c proporcionada por Microsoft que se ejecuta en Windows Server 2012.

Cada instancia de un clúster de WebLogic Server debe utilizar la misma versión de Oracle WebLogic Server. En este ejemplo se usa WebLogic Server 12c Enterprise Edition.

##Crear máquinas virtuales para usar en el clúster
Podrá crear una máquina virtual para usar como servidor de administración de clúster y podrá crear máquinas virtuales adicionales para usar como parte del clúster.

### Crear una máquina virtual para usar como servidor de administración

Cree una máquina virtual utilizando la imagen de **Oracle WebLogic Server 12c Enterprise Edition en Windows Server 2012** disponible en Azure. Los pasos para crear esta máquina virtual pueden encontrarse en [Creación de una máquina virtual Oracle WebLogic Server 12c en Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article). Para los fines de este tutorial, llame a la máquina virtual **MYVM1-ADMIN**. Tome nota del nombre de la red virtual de la máquina virtual. Deberá usar ese valor al crear las máquinas virtuales adicionales para agregar al clúster. (El nombre de la máquina virtual y de la red virtual pueden ser el que desee, siempre y cuando sean único dentro de Azure).

### Crear máquinas virtuales administradas para usar en el clúster

Cree máquinas virtuales adicionales, que serán administradas por el servidor de administración, con la imagen de Oracle WebLogic Server 12c disponible en Azure. Para los fines de este tutorial, asigne a las máquinas virtuales adicionales los nombres **MYVM2-MANAGED** y **MYVM3-MANAGED**. Los pasos para crear estas máquinas virtuales pueden encontrarse en [Creación de una máquina virtual Oracle WebLogic Server 12c en Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article), *excepto* en caso de que se requiera el siguiente cambio.

-  Al crear las máquinas virtuales, no cree una nueva red virtual. Específicamente, en el cuadro de diálogo **Configuración opcional > Red virtual**, en lugar del **Crear una nueva red virtual** predeterminada, seleccione la red virtual creada para la máquina virtual del servidor de administración. Por ejemplo, si durante la creación de su servidor de administración ha creado una red virtual denominada **EJEMPLO**, seleccione **EJEMPLO** al crear las máquinas virtuales de clúster administradas.

##Crear un dominio

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

2. Haga clic en **Máquinas virtuales**.

3. Haga clic en el nombre de la máquina virtual que creó para ser el servidor de administración de clúster (por ejemplo, **MYVM1-ADMIN**).

4. Haga clic en **Conectar**.

5. Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

6. Dentro de **Página 1** del cuadro de diálogo **Asistente para la configuración de Fusion Middleware**, haga clic en **Crear un nuevo dominio** y, a continuación, haga clic en **Siguiente**. (Si el cuadro de diálogo **Asistente para configuración de Fusion Middleware** no está abierto, ábralo haciendo clic en **Inicio de Windows**, escriba **Asistente para configuración**, y, a continuación, haga clic en el icono **Asistente para configuración**).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. Dentro de **Página 2** del cuadro de diálogo **Asistente para configuración de Fusion Middleware**, seleccione la plantilla **Dominio de WebLogic Server básico** y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. En la **Página 3** del **Asistente para configuración de Fusion Middleware**:

	1. [Opcional] Cambie el nombre de usuario de **weblogic** a un valor que desee.
	
	2. Especifique y confirme una contraseña para el administrador del WebLogic Server.
	
	3. Haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. Dentro de **Página 4** del cuadro de diálogo **Asistente de configuración de Fusion Middleware**, seleccione **Producción** para el modo de dominio, seleccione el JDK disponible (o busque un JDK si lo desea) y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  Dentro de **Página 5** del cuadro de diálogo **Asistente para configuración de Fusion Middleware** no seleccione ninguna opción y, a continuación, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  En la **Página 6** del cuadro de diálogo **Asistente para configuración de Fusion Middleware**, haga clic en **Crear**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  En la **Página 7** del cuadro de diálogo **Asistente para configuración de Fusion Middleware**, una vez creado el dominio, haga clic en **Siguiente**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  En la **Página 8** del cuadro de diálogo **Asistente para configuración de Fusion Middleware**, marque Servidor de administración de inicio y, a continuación, haga clic en **Finalizar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  A continuación, se iniciará un símbolo del sistema para **startWebLogic.cmd**. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

##Configuración del clúster

1. Si continúa registrando en la máquina virtual de administración, ejecute la **Consola de administración de WebLogic Server**, <http://localhost:7001/console>. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic Server.

2. En la **Consola de administración de WebLogic Server**, haga clic en **Bloquear y editar**.

3. En el panel **Estructura de dominios**, expanda **Entorno** y, a continuación, haga clic en **Clústeres**.

4. En el cuadro de diálogo **Resumen de clústeres**, haga clic en **Nuevo** y, a continuación, haga clic en **Clúster**.

5. En el cuadro de diálogo **Propiedad de clústeres**:

	1. Escriba un nombre para el clúster.

	2. Seleccione **Unidifusión** para el **Modo de mensajería**.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. Haga clic en **Aceptar**.

6. En el panel **Estructura de dominios**, expanda **Entorno** y, a continuación, haga clic en **Servidores**.

7. Agregue el primer servidor administrado al clúster.

	1. Haga clic en **Nuevo**.

	2. En el cuadro de diálogo **Crear un nuevo servidor**:

		1. Para **Nombre del servidor**, escriba el nombre del primer servidor administrado. Por ejemplo**, MYVM2-MANAGED.**

		2. Para **Dirección de escucha del servidor**, escriba el nombre nuevo.

		3. Para **Puerto de escucha**, escriba **7008**.

		4. Marque **Sí, establecer este servidor como un miembro de un clúster existente**.

		5. En la lista desplegable **Seleccionar un clúster**, seleccione el clúster que creó anteriormente.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. Haga clic en **Siguiente**.

		7. Haga clic en **Finalizar**

8. Agregue el segundo servidor administrado al clúster, siguiendo los pasos anteriores. Para **Nombre del servidor** y **Dirección de escucha del servidor**, use el nombre del segundo equipo administrado. Para **Puerto de escucha**, use **7008**.

9. Si continúa en la consola de administración de WebLogic Server, haga clic en **Activar cambios**.

10. En la máquina virtual del administrador, cree una variable de entorno denominada **SERVER\_HOME** con su valor establecido en **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver**. Puede crear una variable de entorno mediante los siguientes pasos:

	1. Haga clic en **Inicio de Windows**, escriba **Panel de control**, haga clic en el icono del **Panel de control**, haga clic en **Sistema y seguridad**, en **Sistema** y, a continuación, haga clic en **Configuración avanzada del sistema**.

	2. Haga clic en la pestaña **Avanzado** y a continuación, haga clic en **Variables de entorno**.

	3. En la sección **Variables del sistema**, haga clic en **Nuevo** para crear la variable.

	4. En el cuadro de diálogo **Nueva variable del sistema**, escriba **SERVER\_HOME** para el nombre de la variable y escriba **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver** para el valor.

	5. Haga clic en **Aceptar** para guardar la nueva variable de entorno y cierre el cuadro de diálogo **Nueva variable del sistema**.

	6. Cierre los otros cuadros de diálogo que se abrieron mediante el panel de control.

11. Abra un nuevo símbolo del sistema (de modo que la variable de entorno **SERVER\_HOME** esté en vigor).

	>[AZURE.NOTE]Algunos de los pasos restantes requieren el uso de un símbolo del sistema después de que ha iniciado sesión en las máquinas virtuales. Para conocer fácilmente en qué equipo ha iniciado sesión, después de abrir el símbolo del sistema, ejecute **title %COMPUTERNAME%**.
	>
	>**( %COMPUTERNAME%** es una variable de entorno definida por el sistema que se establece automáticamente en el nombre del equipo. La ejecución del comando **% COMPUTERNAME%** de **título** provocará que la barra del título del símbolo del sistema muestre el nombre del equipo).

12. Ejecute el siguiente comando:

		%SERVER\_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar -template\_name="mytestdomain" 

	Este comando crea un archivo jar denominado **c:\\mytestdomain.jar.** Más adelante copiará este archivo jar en las máquinas virtuales administrada en el clúster.

13. Permita una conexión entrante a través del firewall al puerto 7001.

	1. Mientras sigue conectado a la máquina virtual, haga clic en **Inicio de Windows**, escriba **Firewall de Windows con seguridad avanzada,** y, a continuación, haga clic en el icono **Firewall de Windows con seguridad avanzada**. De este modo se abrirá la consola de administración **Firewall de Windows con seguridad avanzada**.

	2. En la consola de administración de firewall, haga clic en **Reglas de entrada** en el panel izquierdo (si no ve **Reglas de entrada**, expanda el nodo superior en el panel izquierdo) y, a continuación, haga clic en **Nueva regla** en el panel derecho.

	3. Para **Tipo de regla**, seleccione **Puerto** y haga clic en **Siguiente**.

	4. Para **Protocolo y puerto**, seleccione **TCP**, **Puertos locales específicos**, escriba **7001** para el puerto y, a continuación, haga clic en **Siguiente**.

	5. Seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	6. Acepte los valores predeterminados para los perfiles a los que se aplica la regla y haga clic en **Siguiente**.

	7. Especifique un nombre para la regla y, opcionalmente, una descripción y, a continuación, haga clic en **Finalizar**.

14. Para cada una de las máquinas virtuales administradas:

	1. Inicie sesión en la máquina virtual.

	2. Cree una variable de entorno denominada **SERVER\_HOME** con su valor establecido en **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver**.

	3. Copie c:\\mytestdomain.jar desde la máquina virtual de administración en c:\\mytestdomain.jar en la máquina virtual administrada.

	4. Abra un símbolo del sistema (y no olvide ejecutar **title %COMPUTERNAME%** en el símbolo del sistema, para dejar claro a qué equipo se está accediendo).

	5. Ejecute el siguiente comando:

			%SERVER\_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar

	6. Cambie el directorio actual del símbolo del sistema a **C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain\\bin.**

	7. Ejecute start<<*NOMBRE\_DE\_EQUIPO*>>.cmd, donde <<*NOMBRE\_DE\_EQUIPO*>> es el nombre del equipo administrado. Por ejemplo, **startMYVM2-MANAGED.**

	8. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic Server.

	9. Permita una conexión entrante a través del firewall al puerto 7008. (Siga los pasos usados para abrir el puerto 7001 en el servidor de administración, pero use 7008 en su lugar para los servidores administrados).

15. En la máquina virtual de administración, abra la **Consola de administración de WebLogic Server**, <http://localhost:7001/console>, y consulte los servidores que se están ejecutando.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. Cree un extremo con equilibrio de carga para las máquinas virtuales administradas:

	1. En el [Portal de Azure](https://ms.portal.azure.com/), en la sección **Máquinas virtuales**, seleccione la primera máquina virtual administrada (como **MYVM2-MANAGED)**.

	2. Haga clic en **Configuración**, en **Extremos** y, por último, en **Agregar**.

	3. Especifique un nombre para el extremo, **TCP** para el protocolo, especifique el puerto público **80** y el puerto privado **7008**. No cambie el resto de las opciones.

	4. Marque **crear un conjunto con equilibrio de carga** y, a continuación, haga clic en **Completar**.

	5. Especifique un nombre para el conjunto de carga equilibrada, acepte los valores predeterminados para los demás parámetros y después haga clic en **Completar. **

17. Cree un extremo para la máquina virtual:

	1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).

	2. Haga clic en **Examinar**.

	3. Haga clic en **Máquinas virtuales**.

	4. Seleccione la máquina virtual

	5. Hacer clic en **Configuración**.

	6. Haga clic en **Conjuntos de carga equilibrada**.

	7. Haga clic en **Unir**.

	8. Establezca el tipo de conjunto de carga equilibrada como **Interno**.

	9. Escriba un nombre para el extremo

		1. Use **TCP** para el protocolo.

		2. Use **80** para el puerto público.

		3. Use **7008** para el puerto de sondeo.

	10. No cambie el resto de las opciones

	11. Haga clic en **Aceptar**.

	12. Espere a que esta máquina virtual se una al conjunto con equilibrio de carga antes de continuar al paso siguiente.

18. En el [Portal de Azure](https://ms.portal.azure.com/), en la sección **Máquinas virtuales**, seleccione la segunda máquina virtual administrada (como **MYVM3-MANAGED**). Siga los pasos anteriores para unirse al conjunto con equilibrio de carga que ha creado para la primera máquina virtual administrada.

##Implementación de una aplicación en el clúster

En este punto, podría implementar su aplicación mediante los siguientes pasos. Supongamos que va a implementar la aplicación shoppingcart de Oracle, disponible para su descarga en <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>.

1. Inicie sesión en la máquina virtual que actúe como administrador del clúster WebLogic Server (por ejemplo, **MYVM1-ADMIN**). 

2. Copie el archivo shoppingcart.war localmente. Por ejemplo, cree una carpeta denominada **c:\\mywar** y guarde el archivo WAR de <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> en **c:\\mywar**.

3. Abra la **Consola de administración de WebLogic Server**, <http://localhost:7001/console>. Cuando se le solicite, proporcione su nombre de usuario y contraseña de WebLogic.

4. En la **Consola de administración de WebLogic Server**, haga clic en **Bloquear y editar**, haga clic en **Implementaciones** y después haga clic en **Instalar**.

5. Para **Ruta de acceso**, escriba **c:\\myway\\shoppingcart.war**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	Haga clic en **Siguiente**.

6. Seleccione **Instalar esta implementación como una aplicación** y después haga clic en **Siguiente**.

7. Haga clic en **Finalizar**

8. Para **Destinos disponibles**, seleccione el clúster creado previamente, asegúrese de haber seleccionado **Todos los servidores del clúster** y después haga clic en **Siguiente**.

9. En **Accesibilidad al origen**, seleccione **Copiar esta aplicación en cada destino para mí** y después haga clic en **Finalizar**.

10.  En la **Consola de administración de WebLogic Server**, haga clic en **Guardar** y después en **Activar cambios**.

11.  Haga clic en **Implementaciones**, seleccione **shoppingcart**, haga clic en **Inicio** y después haga clic en **Atender todas las solicitudes**. Cuando se le solicite confirmar, haga clic en **Sí**.

12.  Para ver la aplicación de carro de la compra ejecutarse en Internet, abra un explorador en la dirección URL en forma de `http://<<unique_domain_name>>/shoppingcart`. (Para determinar el valor de `<<unique_domain_name>>` en el [Portal de Azure](https://ms.portal.azure.com/), haga clic en Máquinas virtuales y después seleccione la máquina virtual que está usando para ejecutar Oracle WebLogic Server).

## Pasos siguientes

Para comprobar que el clúster está funcionando según lo previsto, puede modificar el proyecto shoppingcart.war para mostrar el nombre del equipo que atiende la sesión del explorador, inicie una sesión del explorador, detenga la máquina que atendió la sesión del explorador y actualice la sesión del explorador para ver que un equipo diferente continúa atendiendo la sesión del explorador.

Por ejemplo:

1. Modifique el archivo **DWRHeader1.jspf** para que contenga el código siguiente en la parte superior del archivo:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. Modifique el archivo **weblogic.xml** para que contenga el código siguiente después de la línea de comentario `Insert session descriptor element here`.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. Compile e implemente de nuevo el archivo shoppingcart.war actualizado.

4. Abra una sesión del explorador y ejecute la aplicación shoppingcart. Agregue algunos elementos al carro de la compra y observe qué equipo está atendiendo a la sesión del explorador.

5. En el Portal de Azure, en la interfaz de usuario de **Máquinas virtuales**, seleccione la máquina virtual que atendió la sesión del explorador y haga clic en **Apagar**. Espere hasta que el estado de la máquina virtual sea **Detenido (desasignado)** antes de continuar.

6. Actualice la sesión del explorador que está ejecutando la aplicación shoppingcart y compruebe que una máquina diferente esté atendiendo la sesión del explorador.

7. Haga clic en el vínculo del carro de la compra y compruebe que los elementos que agregó anteriormente todavía están en el carro de la compra.

## Recursos adicionales

Ahora que ha configurado el clúster que se está ejecutando en el Oracle WebLogic Server, consulte los temas siguientes para obtener información adicional.

- [Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentación del producto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Oracle WebLogic Server 12c con Linux en Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=August15_HO6-->