<properties 
	pageTitle="Creación de una máquina virtual para un proyecto web usando Visual Studio" 
	description="Creación de una máquina virtual para un sitio web" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Creación de una máquina virtual para un sitio web con Visual Studio

Cuando cree un proyecto web para un sitio web de Azure, puede aprovisionar una máquina virtual en Azure. Después, puede configurar la máquina virtual con software adicional o usar dicha máquina para diagnósticos o depuración.

Para crear una máquina virtual cuando cree un sitio web, siga estos pasos:

1. En Visual Studio, elija **Archivo**, **Nuevo proyecto**, elija **Web** y después **Aplicación web ASP.NET**.
2. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, seleccione el tipo de aplicación web que desee y en la sección de Azure de dicho cuadro de diálogo (en la esquina inferior derecha), asegúrese de que la casilla **Host en la nube** está selecciona (esta casilla tiene la etiqueta **Crear recursos remotos** en algunas instalaciones).

	![][0]

3. Elija **Máquina virtual** y después el botón **Aceptar**.
4. Si se le pide, inicie sesión en Azure. Aparecerá el cuadro de diálogo Crear máquina virtual.

	![][2]

5. En el cuadro Nombre DNS, escriba un nombre para la máquina virtual. El nombre DNS debe ser único en Azure. Si el nombre que escriba no está disponible, aparecerá un signo de exclamación rojo.
6. En la lista Imagen, elija la imagen del sistema operativo que desee en la máquina virtual. Puede elegir cualquiera de las imágenes estándar o su propia imagen que haya cargado en Azure.
7. Deje la casilla **Habilitar IIS y Web Deploy** activada a menos que desee instalar un servidor web diferente. No podrá publicar desde Visual Studio si deshabilita Web Deploy. Puede agregar IIS y Web Deploy para cualquiera de las imágenes de Windows Server empaquetadas, incluidas sus propias imágenes personalizadas.
8. En la lista **Tamaño**, seleccione el tamaño de la máquina virtual.
9. Especifique las credenciales de inicio de sesión para esta máquina virtual. Anótelas, ya que las necesitará para acceder a la máquina a través de Escritorio remoto.
10. En la lista **Ubicación**, elija la región, la red virtual o el grupo de afinidad que hospedará la máquina virtual. Puede usar grupos de afinidad para asegurarse de que los recursos de Azure que tienen mucho tráfico de red entre ellos permanecen juntos en el mismo centro de datos; asimismo puede usar regiones para especificar la ubicación exacta del centro de datos.
11. Elija **Aceptar** para iniciar el proceso de creación de la máquina virtual. Puede ver el progreso en la ventana **Resultados**.

	![][3]

12. Cuando la máquina virtual se aprovisiona, los scripts de publicación se crean en un nodo **PublishScripts** en la solución. El script de publicación se ejecuta y aprovisiona en una máquina virtual en Azure. La ventana **Resultados** muestra el estado. El script realiza las siguientes acciones para configurar la máquina virtual:

	* Crea la máquina virtual si todavía no existe.
	* Crea una cuenta de almacenamiento con un nombre que empieza con  `devtest`, pero solamente si no existe tal cuenta de almacenamiento en la región especificada.
	* Crea un servicio en la nube como un contenedor para la máquina virtual y crea un rol web para el sitio web.
	* Configura Web Deploy en la máquina virtual.
	* Configura IIS y ASP.NET en la máquina virtual.

	![][4]

<br/>
13. (Opcional) En el **Explorador de servidores**, expanda el nodo **Máquinas virtuales**, elija el nodo correspondiente a la máquina virtual creada y después elija **Conectarse con Escritorio remoto** para conectarse a la máquina virtual.

# Pasos siguientes

Si desea personalizar los scripts de publicación creados, [aquí](http://msdn.microsoft.com/library/dn642480.aspx) encontrará información más detallada.

[0]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_CreateVM.PNG
[3]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_Provisioning.png
[4]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SolutionExplorer.png

<!--HONumber=45--> 
