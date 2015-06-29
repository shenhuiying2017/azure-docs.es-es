<properties
	pageTitle="Creación de una máquina virtual para un proyecto web usando Visual Studio"
	description="Creación de una máquina virtual para un sitio web"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kempb"/>

# Creación de una máquina virtual para un sitio web con Visual Studio

Cuando cree un proyecto web para un sitio web de Azure, puede aprovisionar una máquina virtual en Azure. Después, puede configurar la máquina virtual con software adicional o usar dicha máquina para diagnósticos o depuración.

Para crear una máquina virtual cuando cree un sitio web, siga estos pasos:

1. En Visual Studio, elija **Archivo**, **Nuevo proyecto**, elija **Web** y después **Aplicación web ASP.NET** (en los nodos **Visual C#** o **Visual Basic**).
2. En el cuadro de diálogo** Nuevo proyecto ASP.NET**, seleccione el tipo de aplicación web que desee y en la sección de Azure de dicho cuadro de diálogo (en la esquina inferior derecha), asegúrese de que la casilla **Host en la nube** está seleccionada (esta casilla tiene la etiqueta **Crear recursos remotos** en algunas instalaciones).

	![][0]

3. En el cuadro de lista desplegable en Microsoft Azure, elija **Máquina virtual** y, a continuación, elija el botón **Aceptar**.
4. Inicie sesión en Azure si se le solicita. Aparecerá el cuadro de diálogo **Crear máquina virtual**.

	![][2]

5. En el cuadro Nombre DNS, especifique un nombre para la máquina virtual. El nombre DNS debe ser único en Azure. Si el nombre que escriba no está disponible, aparecerá un signo de exclamación rojo.
6. En la lista **Imagen**, elija la imagen de máquina virtual en la que desee basar la máquina virtual. Puede elegir cualquiera de las imágenes de máquinas virtuales de Azure estándar o su propia imagen que haya cargado en Azure.
7. Deje la casilla **Habilitar IIS y Web Deploy** activada a menos que desee instalar un servidor web diferente. No podrá publicar desde Visual Studio si deshabilita Web Deploy. Puede agregar IIS y Web Deploy para cualquiera de las imágenes de Windows Server empaquetadas, incluidas sus propias imágenes personalizadas.
8. En la lista **Tamaño**, seleccione el tamaño de la máquina virtual.
9. Especifique las credenciales de inicio de sesión para esta máquina virtual. Anótelas, ya que las necesitará para acceder a la máquina a través de Escritorio remoto.
10. En la lista **Ubicación**, elija la región en la que se hospedará la máquina virtual.
11. Elija el botón **Aceptar** para comenzar a crear la máquina virtual. Puede seguir el progreso de la operación en la ventana **Resultados **. 
	![][3]

12. Cuando la máquina virtual se aprovisiona, los scripts de publicación se crean en un nodo **PublishScripts** en la solución. El script de publicación se ejecuta y aprovisiona en una máquina virtual en Azure. La ventana **Resultados** muestra el estado. El script realiza las siguientes acciones para configurar la máquina virtual:

	* Crea la máquina virtual si todavía no existe.
	* Crea una cuenta de almacenamiento con un nombre que empieza con `devtest`, pero solamente si no existe tal cuenta de almacenamiento en la región especificada.
	* Crea un servicio en la nube como un contenedor para la máquina virtual y crea un rol web para el sitio web.
	* Configura Web Deploy en la máquina virtual.
	* Configura IIS y ASP.NET en la máquina virtual.

	![][4]

<br/> 13. (Opcional) Puede conectarse a la nueva máquina virtual. En el **Explorador de servidores**, expanda el nodo **Máquinas virtuales**, elija el nodo correspondiente a la máquina virtual creada y, en su menú contextual, elija **Conectarse con Escritorio remoto**.

	![][5]

## Pasos siguientes

Si desea personalizar los scripts de publicación creados, [aquí](http://msdn.microsoft.com/library/dn642480.aspx) encontrará información más detallada.

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_CreateVM_Connect.png
 

<!---HONumber=58_postMigration-->