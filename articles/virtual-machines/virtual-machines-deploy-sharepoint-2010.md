<properties
	pageTitle="SharePoint Server 2010 en máquinas virtuales de Azure | Microsoft Azure"
	description="Obtenga información sobre el valor de SharePoint en máquinas virtuales de Azure, el proceso paso a paso y los escenarios admitidos para el uso de SharePoint 2010."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="josephd"/>


# Implementación de SharePoint 2010 en máquinas virtuales de Azure

Microsoft SharePoint Server 2010 ofrece una flexibilidad completa en la implementación, lo que ayuda a las organizaciones a determinar los escenarios de implementación adecuados para alinearlos con sus necesidades y objetivos empresariales. Hospedada y administrada en Microsoft Azure, la oferta de máquinas virtuales de Azure proporciona una infraestructura completa, confiable y disponible para admitir diversas cargas de trabajo de base de datos y aplicaciones a petición, como implementaciones de Microsoft SQL Server y SharePoint.

Aunque las máquinas virtuales de Azure son compatibles con diversas cargas de trabajo, este documento se centra en las implementaciones de SharePoint. Las máquinas virtuales de Azure permiten que las organizaciones creen y administren su infraestructura de SharePoint a través de un aprovisionamiento rápido y un acceso a prácticamente cualquier host de manera universal. Permite tener un completo control y administración sobre procesadores, RAM, intervalos de CPU y otros recursos de máquinas virtuales de SharePoint.

Las máquinas virtuales de Azure disminuyen la necesidad de hardware, por lo que las organizaciones pueden dedicar su atención a crear y administrar infraestructura a escala, en lugar de enfrentar la complejidad y el alto coste por adelantado. Esto significa que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

> [AZURE.NOTE]Para obtener información acerca de cómo implementar SharePoint 2013 en Azure, consulte [Planeación para SharePoint 2013 en los servicios de infraestructura de Azure](https://msdn.microsoft.com/library/dn275958.aspx) y [Granjas de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md).

## SharePoint en máquinas virtuales de Azure

La flexibilidad de SharePoint 2010 es compatible con la mayoría de las cargas de trabajo en una implementación de máquinas virtuales de Azure. Las máquinas virtuales de Azure se ajustan perfectamente a escenarios de desarrollo y FIS (SharePoint Server para sitios de Internet). También son compatibles las cargas de trabajo centrales de SharePoint. Si una organización desea administrar y controlar su propia implementación de SharePoint 2010 mientras se capitalizan las opciones para la virtualización en una infraestructura de nube, la implementación de máquinas virtuales de Azure es la opción ideal.

La oferta de máquinas virtuales de Azure se hospeda y administra en la nube. Brinda flexibilidad en la implementación y disminuye los costes al reducir los gastos de capital generados por la adquisición de hardware. Con una mayor agilidad en la infraestructura, las organizaciones pueden implementar SharePoint Server en horas, en lugar de hacerlo en días o semanas. Las máquinas virtuales de Azure también permiten que las organizaciones implementen cargas de trabajo de SharePoint en la nube con un modelo de "pago por uso". A medida que las cargas de trabajo de SharePoint crecen, una organización puede expandir rápidamente la infraestructura. Luego, cuando disminuyen las necesidades informáticas, puede devolver los recursos que ya no son necesarios, pagando así solo lo que se utiliza.

### Cambio en el foco de TI

Muchas organizaciones subcontratan los componentes comunes de su administración e infraestructura de TI, como hardware, sistemas operativos, seguridad, almacenamiento de datos y copia de seguridad, mientras mantienen el control de aplicaciones críticas, como SharePoint Server. Al delegar todas las capas de servicio no críticas de sus plataformas de TI en un proveedor virtual, las organizaciones pueden cambiar el foco de su TI hacia los servicios críticos centrales de SharePoint y brindar valor empresarial con proyectos de SharePoint, en lugar de dedicar más tiempo a la configuración de infraestructura.

### Implementación más rápida

Admitir e implementar una infraestructura de SharePoint de gran tamaño puede dificultar la capacidad de TI de avanzar rápidamente para responder a los requisitos del negocio. El tiempo que se requiere para crear, probar y preparar servidores y granjas de SharePoint e implementarlos en un entorno de producción puede demorar semanas o incluso meses, dependiendo de los procesos y restricciones de la organización. Las máquinas virtuales de Azure permiten que las organizaciones implementen rápidamente sus cargas de trabajo de SharePoint sin gastos de capital para hardware. De esta manera, las organizaciones pueden sacar provecho de la agilidad de la infraestructura para implementar en horas, en lugar de hacerlo en días o semanas.

### Escalabilidad

Sin la necesidad de implementar, probar y preparar granjas y servidores físicos de SharePoint, las organizaciones pueden expandir y contratar capacidad de proceso a petición inmediatamente. A medida que los requisitos de carga de trabajo de SharePoint crecen, una organización puede expandir rápidamente su infraestructura en de nube. Del mismo modo, cuando disminuyen las necesidades informáticas, la organización puede reducir los recursos, pagando solo lo que utiliza. Las máquinas virtuales de Azure disminuyen los gastos iniciales y los compromisos a largo plazo, lo que permite que las organizaciones creen y administren infraestructuras de SharePoint a escala. Esto significa, nuevamente, que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

### Uso medido

Las máquinas virtuales de Azure proporcionan potencia de proceso, memoria y almacenamiento para escenarios de SharePoint, con precios que normalmente se basan en el consumo de los recursos. Las organizaciones solo pagan por lo que utilizan y el servicio proporciona toda la capacidad necesaria para ejecutar la infraestructura de SharePoint. Para obtener más información sobre precios y facturación, vaya a [Información sobre precios de Azure](http://azure.microsoft.com/pricing/). Observe que existen gastos nominales para almacenamiento y transferencias datos desde Azure a una red local. Sin embargo, Azure no cobra por cargar datos.

### Flexibilidad

Las máquinas virtuales de Azure brindan a los desarrolladores la flexibilidad para elegir su entorno de tiempo de ejecución o lenguaje de preferencia, con compatibilidad oficial para .NET, Node.js, Java y PHP. Los desarrolladores también pueden elegir sus herramientas, con compatibilidad para Microsoft Visual Studio, WebMatrix, Eclipse y editores de texto. Además, Microsoft ofrece una ruta de acceso a bajo coste y con bajo riesgo a la nube y ofrece también aprovisionamiento e implementación sencillas y rentables para las necesidades de informes de la nube, a la vez que se ofrece acceso a inteligencia empresarial entre distintos dispositivos y ubicaciones. Finalmente, con la oferta de Azure, los usuarios no solo pueden trasladar discos duros virtuales a la nube, sino que también pueden volver a copiar un disco duro virtual y ejecutarlo localmente o a través de un proveedor de nube, siempre que tengan la licencia adecuada.

## Proceso de aprovisionamiento

La biblioteca de imágenes en Azure proporciona la lista de máquinas virtuales preconfiguradas disponibles. Los usuarios pueden publicar discos duros virtuales o imágenes ISO de SharePoint Server, SQL Server, Windows Server y otros en la biblioteca de imágenes. Para simplificar la creación de máquinas virtuales, las imágenes base se crean y publican en la biblioteca. Los usuarios autorizados pueden utilizar estas imágenes para generar la máquina virtual deseada. Para obtener más información, vaya a [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](virtual-machines-windows-tutorial.md). La ilustración 1 muestra los pasos básicos para crear una máquina virtual mediante el portal de Azure.

**Ilustración 1: Información general de los pasos para crear una máquina virtual**

Los usuarios también pueden cargar una imagen preparada con SysPrep en el Portal de administración de Azure. Para obtener más información, vaya a [Crear y cargar un VHD de Windows Server a Azure](virtual-machines-create-upload-vhd-windows-server.md). La ilustración 2 muestra los pasos básicos para cargar una imagen a fin de crear una máquina virtual.

En el portal de Azure, los usuarios también pueden cargar una imagen en la que ya se haya ejecutado la herramienta SysPrep. Para obtener más información, vaya a [Crear y cargar un VHD de Windows Server a Azure](virtual-machines-create-upload-vhd-windows-server.md). En la ilustración 2 se muestra los pasos básicos para cargar una imagen a fin de crear una máquina virtual.

**Ilustración 2: Información general de los pasos para cargar una imagen**

## Implementación de SharePoint 2010 en Azure 

Siga estos pasos para poder implementar SharePoint 2010 en Azure:

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/) a través de la cuenta de suscripción de Azure. Si no tiene una cuenta de Azure, [regístrese para obtener una evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
2. Para crear una máquina virtual con el sistema operativo base, en el portal de Azure, haga clic en **Nuevo > Cálculo > Máquina virtual > Desde galería.**
3. Aparecerá el cuadro de diálogo **Elegir una imagen**. Haga clic en la imagen de la plataforma **Windows Server 2008 R2 SP1** y, a continuación, en la flecha derecha.
4. Aparece el cuadro de diálogo **Configuración de la máquina virtual**. Proporcione la siguiente información:
	- Escriba un **Nombre de la máquina virtual**.
	- Seleccione el tamaño adecuado. Para un entorno de producción (base de datos y servidor de aplicación de SharePoint), se recomienda usar A3 [4 núcleos, 7 gigabytes (GB) de memoria] o mayor.
	- En **Nuevo nombre de usuario**, escriba un nombre de cuenta de administrador local.
	- En el cuadro **Contraseña nueva**, escriba una contraseña segura.
	- En **Confirmar**, vuelva a escribir la contraseña y, a continuación, haga clic en la flecha derecha.
5. Aparece el segundo cuadro de diálogo **Configuración de la máquina virtual**. Proporcione la siguiente información:
	- En **Servicio en la nube**, seleccione **Crear un nuevo servicio en la nube**, en cuyo caso debe también proporcionar un nombre DNS del servicio de nube, o bien seleccione un servicio de nube existente.
	- En el cuadro **Región/Grupo de afinidad/Red virtual**, seleccione una región donde se hospedará esta imagen virtual.
	- En **Cuenta de almacenamiento**, haga clic en **Use una cuenta de almacenamiento generada de forma automática** o seleccione una cuenta de almacenamiento existente. Solo se crea una cuenta de almacenamiento por región de manera automática. Todas las demás máquinas virtuales creadas con esta configuración se ubican en esta cuenta de almacenamiento. Tiene un límite de 20 cuentas de almacenamiento. Para obtener más información, vaya a [Creación de una cuenta de almacenamiento en Azure](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure).
	- En el cuadro **Conjunto de disponibilidad**, seleccione **(ninguno)** y, a continuación, haga clic en la flecha derecha.
6. En el tercer cuadro de diálogo **Configuración de la máquina virtual**, haga clic en la marca de verificación para crear la máquina virtual.

Para conectarse a la máquina virtual, consulte [Inicio de sesión en una máquina virtual con Windows Server](virtual-machines-log-on-windows-server.md).

Cree la máquina virtual de SQL Server con cualquiera de las siguientes opciones:

- Cree una máquina virtual de SQL Server 2012 siguiendo los pasos 1 a 7 anteriormente descritos, excepto en el paso 3, donde debe utilizar la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, vaya a [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).
	- Cuando haga clic en esta opción, el proceso de aprovisionamiento conserva una copia de los archivos de configuración de SQL Server 2012 en la ruta de acceso al directorio C:\\SQLServer\_11.0\_Full para que pueda personalizar la instalación. Por ejemplo, puede convertir la instalación de evaluación de SQL Server 2012 en una versión con licencia si utiliza su clave de licencia.

- Use la herramienta de preparación del sistema SQL Server (SysPrep) para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7). Para obtener más información, vaya a [Instalar SQL Server 2012 mediante SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).

- Utilice el símbolo del sistema para instalar SQL Server. Para obtener más información, vaya a [Instalar SQL Server 2012 desde el símbolo del sistema](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep).

- Utilice medios compatibles de SQL Server y su clave de licencia para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7).

Cree la granja de SharePoint con los siguientes pasos:

Paso 1. Configure la suscripción de Azure con archivos de script.

Paso 2: Aprovisione los servidores de SharePoint mediante la creación de otra máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 6). Para crear un servidor de SharePoint en esta máquina virtual, elija una de las siguientes opciones:

- Aprovisionamiento con GUI de SharePoint:
	- Para crear y aprovisionar una granja de SharePoint, vaya a [Creación de una granja de servidores de Microsoft SharePoint](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure).
	- Para agregar un servidor web o de aplicaciones a la granja, vaya a [Agregar un servidor web o de aplicaciones a la granja de servidores](http://technet.microsoft.com/library/cc261752.aspx).
	- Para agregar un servidor de base de datos a una granja existente, vaya a [Agregar un servidor de base de datos a una granja existente](http://technet.microsoft.com/library/cc262781).
	- Para utilizar SQL Server 2012 para su granja de SharePoint, debe descargar e instalar el Service Pack 1 para SharePoint Server 2010 después de instalar la aplicación y elegir no configurar el servidor. Para obtener más información, vaya a [Service Pack 1 para SharePoint Server 2010](http://www.microsoft.com/download/details.aspx?id=26623).
	- Para aprovechar las características de Business Intelligence SQL Server, recomendamos instalar SharePoint Server como granja de servidores en lugar de un servidor independiente. Para obtener más información, vaya a [Instalar las características de Business Intelligence SQL Server 2012](http://technet.microsoft.com/library/hh231681.aspx).

- Aprovisionamiento mediante Windows PowerShell: puede utilizar la herramienta de la línea de comandos Psconfig como una interfaz alternativa para realizar varias operaciones que controlan la manera en que se aprovisionan los productos de SharePoint 2010. Para obtener más información, vaya a [Referencia de la línea de comandos de Psconfig](http://technet.microsoft.com/library/cc263093.aspx).

Paso 3: Configure SharePoint. Cuando cada máquina virtual de SharePoint tenga un estado Listo, configure SharePoint Server en cada servidor mediante una de las siguientes opciones:

- Configure SharePoint desde la GUI.
- Configure SharePoint mediante Windows PowerShell. Para obtener más información, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx).
- También puede utilizar AutoSPInstaller del proyecto de CodePlex, que consta de scripts de Windows PowerShell, un archivo XML de entrada y un archivo por lotes de Microsoft Windows estándar. AutoSPInstaller brinda un marco para un script de instalación de SharePoint 2010 basado en Windows PowerShell. Para obtener más información, vaya a [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).

Paso 4 Cuando finalice el script, conéctese a la máquina virtual mediante el panel de la máquina virtual.

Para comprobar la configuración de SharePoint, inicie sesión en el servidor de SharePoint y, a continuación, utilice la administración central.

> [AZURE.NOTE]asegúrese de configurar la seguridad en el extremo del portal de Azure y de definir un puerto de entrada en el firewall de Windows de la máquina virtual. Luego, confirme que puede iniciar una sesión remota de Windows PowerShell en uno de los servidores de aplicaciones de SharePoint al abrir una sesión de Windows PowerShell con credenciales de administrador.

### Creación y carga de un disco duro virtual

También puede crear sus propias imágenes y cargarlas a Azure como un archivo VHD. Para obtener información, consulte [Crear y cargar un VHD de Windows Server a Azure](virtual-machines-create-upload-vhd-windows-server.md).

## Escenarios de uso

En esta sección se analizan algunos importantes escenarios de cliente para implementaciones de SharePoint mediante máquinas virtuales de Azure.

### Escenario 1: Entorno simple de desarrollo y prueba de SharePoint

Las organizaciones buscan maneras más ágiles de crear aplicaciones de SharePoint y configurar entornos de SharePoint para prueba y desarrollo internos/externos. Fundamentalmente, las organizaciones desean disminuir el tiempo que se requiere para configurar los proyectos de desarrollo de aplicaciones de SharePoint y reducir el coste a través del aumento del uso de sus entornos de prueba. Por ejemplo, es posible que una organización desee realizar una prueba de carga a petición en SharePoint Server y ejecutar una prueba de aceptación del usuario (UAT) con más usuarios simultáneos en distintas ubicaciones geográficas. De manera similar, integrar equipos internos/externos es una necesidad empresarial cada vez más importante para muchas de las organizaciones actuales.

Este escenario explica cómo las organizaciones pueden utilizar granjas de SharePoint preconfiguradas para cargas de trabajo de desarrollo y prueba. Una topología de implementación de SharePoint tiene el mismo aspecto que tendría en una implementación virtualizada local. Las aptitudes de TI existentes se traducen 1:1 en una implementación de máquinas virtuales de Azure, donde el principal beneficio es un cambio casi completo en los costes desde los gastos de capital a los gastos operativos. No se requiere ninguna adquisición de servidores físicos por adelantado. Las organizaciones pueden eliminar el coste de capital referente a hardware de servidor y lograr una flexibilidad al disminuir considerablemente el tiempo de aprovisionamiento que se requiere para crear, configurar o ampliar una granja de SharePoint para un entorno de prueba y desarrollo. La TI puede agregar y quitar de manera dinámica capacidad para enfrentar las cambiantes necesidades de prueba y desarrollo. Además, la TI también puede centrarse más en entregar valor para el negocio con proyectos de SharePoint y menos en la administración de la infraestructura.

Para utilizar completamente los equipos para prueba de carga, las organizaciones pueden configurar equipos de desarrollo y prueba virtualizadas de SharePoint en Azure con compatibilidad de sistema operativo para Windows Server 2008 R2. Este permite a los equipos de desarrollo crear y probar aplicaciones y migrar con facilidad a entornos de producción locales o en la nube sin cambios en el código. Los mismos marcos y conjuntos de herramientas se pueden utilizar localmente y en la nube, lo que permite que el equipo distribuido tenga acceso al mismo entorno. Los usuarios también pueden tener acceso a datos y aplicaciones locales si se establece una conexión VPN directa.

La ilustración 3 muestra un entorno de desarrollo y prueba de SharePoint en una máquina virtual de Azure. Para crear esta implementación, comience usando el mismo entorno de desarrollo y prueba local de SharePoint que se utiliza para desarrollar aplicaciones. Luego, cargue e implemente las aplicaciones a la máquina virtual de Azure para prueba y desarrollo. Si la organización decide volver a trasladar la aplicación al entorno local, puede hacerlo sin tener que modificarla.

**Ilustración 3: Entorno de desarrollo y prueba de SharePoint en máquinas virtuales de Azure**

Siga estos pasos para implementar un entorno de desarrollo y prueba de SharePoint en Azure:

1. Aprovisionamiento: Primero, aprovisione una conexión VPN entre la instalación local y Azure a través de Red virtual de Azure. (Como aquí no se usa Active Directory, se necesita un túnel VPN). Para obtener más información, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md). Luego, use el portal de Azure para aprovisionar una máquina virtual nueva a través de una imagen de archivo desde la biblioteca de imágenes.
	- Puede cargar las máquinas virtuales de desarrollo y prueba locales de SharePoint en su cuenta de almacenamiento de Azure y hacer referencia a esas máquinas virtuales a través de la biblioteca de imágenes para crear el entorno requerido.
	- Puede utilizar la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, vaya a [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

2. Instalación: Instale SharePoint Server, Visual Studio y SQL Server en las máquinas virtuales a través de una conexión de Escritorio remoto.
	- Use el script de configuración simple de SharePoint 2010 para crear una máquina para el desarrollador de SharePoint. Para obtener más información, vaya a [Script de configuración sencilla de SharePoint 2010](http://www.microsoft.com/download/details.aspx?id=23415). Utilice Windows PowerShell. Para obtener más información, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx). Utilice AutoSPInstaller del proyecto de CodePlex. Para obtener más información, vaya a [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Instale Visual Studio. Para obtener más información, vaya a [Instalación de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).
	- Instale SQL Server. Para obtener más información, vaya a [Instalar SQL Server mediante SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Desarrollo de paquetes y scripts de implementación para aplicaciones y bases de datos: si tiene previsto usar una máquina virtual disponible desde la biblioteca de imágenes, es posible implementar las aplicaciones y bases de datos locales en máquinas virtuales de Azure:
	- Cree paquetes de implementación para las aplicaciones y bases de datos locales existentes mediante SQL Server Data Tools y Visual Studio.
	- Utilice estos paquetes para implementar las aplicaciones y bases de datos en máquinas virtuales de Azure.
4. Implementación de aplicaciones y bases de datos de SharePoint:
	- Configure la seguridad en el extremo del portal de Azure y de definir un puerto de entrada en el firewall de Windows de la máquina virtual.
	- Implemente aplicaciones y bases de datos de SharePoint en máquinas virtuales de Azure a través de los paquetes y scripts de implementación que se crearon en el paso 3.
- Pruebe las aplicaciones y bases de datos implementadas.
5. Administrar máquinas virtuales:
	- Supervise las máquinas virtuales mediante el portal de Azure.
	- Supervise las aplicaciones mediante Visual Studio y SQL Server Management Studio.
	- También puede supervisar y administrar las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.

### Escenario 2: Granja de SharePoint de acceso público con personalización

Las organizaciones desean establecer una presencia en Internet que esté hospedada en la nube y que sea fácilmente escalable según la necesidad y la demanda. También desean crear sitios web de extranet de asociados para colaboración e implementar un proceso simple para la creación y aprobación distribuida del contenido de los sitios web. Finalmente, para controlar el incremento de las cargas, estas organizaciones desean proporcionar capacidad a petición a sus sitios web.

En este escenario, SharePoint Server se utiliza como la base para hospedar un sitio web de acceso público. Permite que las organizaciones implementen, personalicen y hospeden rápidamente sus sitios web empresariales en una infraestructura en la nube segura y escalable. Con sitios web de acceso público de SharePoint en Azure, las organizaciones pueden escalar a medida que crece el tráfico y solo pagar por lo que utilizan. Se pueden utilizar herramientas comunes, similares a las que se utilizan de manera local, para la creación de contenido, el flujo de trabajo y la aprobación con SharePoint en Azure.

Además, a través de máquinas virtuales de Azure, las organizaciones pueden configurar fácilmente entornos de ensayo y producción que se ejecuten en máquinas virtuales. Es posible crear copias de seguridad de las máquinas virtuales de SharePoint de acceso público creadas en Azure en el almacenamiento virtual. Además, para fines de recuperación ante desastres, la característica de replicación geográfica continua permite que las organizaciones creen automáticamente copias de seguridad de máquinas virtuales que se encuentran en un centro de datos en otro centro de datos a kilómetros de distancia.

Las máquinas virtuales de la infraestructura de Azure están validadas y son compatibles para trabajar con otros productos de Microsoft, como SQL Server y SharePoint Server. Azure y SharePoint Server funcionan mejor en conjunto: ambos están perfectamente integrados, son compatibles y está probado que, en conjunto, brindan una experiencia óptima. Ambos tienen un único punto de respaldo para la aplicación de SharePoint y la infraestructura de Azure.

En este escenario, se deben agregar más servidores web front-end para SharePoint Server a fin de admitir el tráfico adicional. Estos servidores requieren mayor seguridad y los controladores de dominio de los Servicios de dominio de Active Directory para admitir la autorización y la autenticación del usuario. La ilustración 4 muestra el diseño de este escenario.

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

**Ilustración 4: Granja de SharePoint de acceso público con personalización**

Siga estos pasos para implementar una granja de SharePoint de acceso público en Azure:

1. Implementación de Active Directory: Los requisitos fundamentales para implementar Active Directory en máquinas virtuales de Azure son similares, pero no idénticos, a los de implementar en máquinas virtuales (y, en cierta medida, en máquinas físicas) locales. Para obtener más información acerca de las diferencias, directrices y otras consideraciones, vaya a [Directrices para implementar Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/library/jj156090). Para implementar Active Directory en Azure:
	- Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas.
	- Use el portal de Azure para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure. Puede hacer referencia al script de Windows PowerShell para implementar un dominio independiente en la nube mediante Máquinas virtuales de Azure y Red virtual de Azure. Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a [Instalación de un nuevo bosque de Active Directory en Azure](active-directory-new-forest-virtual-machine.md).
2. Aprovisionar una máquina virtual: use el portal de Azure para aprovisionar una nueva máquina virtual desde una imagen de archivo de la biblioteca de imágenes.
3. Implemente una granja de SharePoint.
	- Use el portal de Azure para configurar el equilibrio de carga. Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.
	- Agregue otra máquina virtual web front-end a la granja existente de SharePoint para el tráfico adicional.
3. Administrar máquinas virtuales:
	- Supervise las máquinas virtuales mediante el portal de Azure.
	- Supervise la granja de SharePoint mediante la administración central.

### Escenario 3: Granja con escalamiento horizontal para servicios adicionales de Business Intelligence

Business Intelligence (BI) es fundamental para obtener información clave y tomar decisiones sólidas y rápidas. Cuando las organizaciones hacen la transición desde un enfoque local, no desean realizar cambios en el entorno de BI mientras implementan aplicaciones de BI existentes en la nube. Desean hospedar informes desde SQL Server Analysis Services (SSAS) o SQL Server Reporting Services (SSRS) en un entorno altamente durable y disponible, mientras mantiene el control completo de la aplicación de Business Intelligence, todo sin dedicar mucho tiempo y presupuesto al mantenimiento.

Este escenario describe la manera en que las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones críticas de Business Intelligence. Las organizaciones pueden implementar granjas de SharePoint en máquinas virtuales de Azure y escalar horizontalmente los componentes de BI de la máquina virtual del servidor de aplicaciones, como SSRS o Excel Services. A través del escalado de componentes que requieren muchos recursos en la nube, pueden admitir mejor y con más facilidad cargas de trabajo especializadas. Observe que SQL Server en máquinas virtuales de Azure funciona bien, puesto que es fácil escalar las instancias de SQL Server, que van desde instalaciones pequeñas a muy grandes. Esto proporciona una elasticidad que permite que las organizaciones aprovisionen (amplíen) o desaprovisionen (reduzcan) de manera dinámica instancias de Business Intelligence según los requisitos inmediatos de la carga de trabajo.

La migración de aplicaciones existentes de BI a Azure proporciona un mejor escalado. Con la potencia de SQL Server Analysis Services (SSAS), SQL Server Reporting Service (SSRS) y SharePoint Server, las organizaciones pueden crear poderosas aplicaciones de BI e informes y paneles que escalan o reducen verticalmente. Estas aplicaciones y paneles también se pueden integrar de manera más segura con datos y aplicaciones locales. Azure garantiza que los centros de datos se atienen a la norma ISO 27001. Para obtener más información, vaya al [Centro de confianza de Azure](http://azure.microsoft.com/support/trust-center/compliance/).

Para escalar horizontalmente la implementación de componentes de BI, se debe instalar un nuevo servidor de aplicaciones con servicios como PowerPivot, Power View, Excel Services o PerformancePoint Services. O bien, se deben agregar instancias de BI de SQL Server como SSAS o SSRS a la granja existente para admitir un procesamiento adicional de consultas. El servidor se puede agregar como una máquina virtual nueva de Azure con SharePoint 2010 Server o SQL Server instalado. Luego se pueden instalar, implementar y configurar en ese servidor los componentes de BI (ilustración 5).

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

**Ilustración 5: Granja de SharePoint con escalamiento horizontal para servicios adicionales de BI**

Siga estos pasos para escalar horizontalmente un entorno de BI en Azure:

1. Aprovisionamiento:
	- Aprovisione una conexión VPN entre la instalación local y Azure a través de Red virtual de Azure. Para obtener más información, consulte [Información general sobre redes virtuales](http://msdn.microsoft.com/library/jj156007.aspx).
	- Use el portal de Azure para aprovisionar una máquina virtual nueva a través de una imagen de archivo desde la biblioteca de imágenes. Puede cargar imágenes de carga de trabajo de BI de SharePoint Server o SQL Server en la biblioteca de imágenes y cualquier usuario autorizado puede elegir esas máquinas virtuales de componentes de BI para crear el entorno de escalamiento horizontal.
2. Instalación: 
	- Si su organización no tiene imágenes prediseñadas de componentes de BI de SharePoint Server o SQL Server, instale SharePoint Server y SQL Server en las máquinas virtuales a través de la conexión de Escritorio remoto.
	- Para obtener más información acerca de la instalación de SharePoint, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) o [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Para obtener más información acerca de la instalación de SQL Server, vaya a [Instalar SQL Server mediante SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
3. Agregar la máquina virtual de BI:
	- Configure la seguridad en el extremo del portal de Azure y de definir un puerto de entrada en el firewall de Windows de la máquina virtual.
	- Agregue la máquina virtual de BI recientemente creada a la granja existente de SharePoint o SQL Server.
4. Administrar máquinas virtuales:
	- Supervise las máquinas virtuales mediante el portal de Azure.
	- Supervise la granja de SharePoint mediante la administración central.
	- Supervise y administre las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.

### Escenario 4: Sitio web basado en SharePoint completamente personalizado

Cada vez más, las organizaciones desean crear en la nube sitios web de SharePoint completamente personalizados. Necesitan un entorno altamente duradero y disponible que ofrezca control completo para mantener las aplicaciones complejas en ejecución en la nube, pero no desean dedicar una gran cantidad de tiempo ni de presupuesto.

En este escenario, una organización puede implementar toda su granja de SharePoint en la nube y escalar dinámicamente todos los componentes para obtener capacidad adicional, o bien puede extender su implementación local a la nube para aumentar la capacidad y mejorar el rendimiento cada vez que sea necesario. El escenario se centra en organizaciones que desean obtener la experiencia de SharePoint completa para el desarrollo de aplicaciones y la administración de contenido empresarial. Los sitios más complejos también pueden incluir una mayor generación de informes, Power View, PerformancePoint, PowerPivot, gráficas más detalladas y la mayoría de las demás funcionalidades de sitios de SharePoint para obtener una funcionalidad completa de un extremo a otro.

Las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones personalizadas y componentes asociados en una infraestructura de nube rentable y altamente segura. También pueden utilizar Microsoft System Center local como una herramienta de administración común para aplicaciones locales y en la nube.

Para implementar un sitio web completamente personalizado de SharePoint en Azure, una organización debe implementar un dominio de Active Directory en la nube y aprovisionar nuevas máquinas virtuales en este dominio. Luego, se debe crear y configurar una máquina virtual que ejecute SQL Server 2012 como parte de una granja de SharePoint. Finalmente, es necesario crear una granja de SharePoint, equilibrar su carga y conectarla a Active Directory y SQL Server (ilustración 6).

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

**Figura 6: Sitio web basado en SharePoint completamente personalizado**

Los siguientes pasos muestran cómo crear un entorno de granjas de SharePoint personalizado a partir de imágenes prediseñadas disponibles en la biblioteca de imágenes. Sin embargo, tenga en cuenta que puede cargar máquinas virtuales de la granja de SharePoint en la biblioteca de imágenes, y los usuarios autorizados pueden elegir esas máquinas virtuales para crear la granja de SharePoint requerida en Azure.

1. Implementación de Active Directory: Los requisitos fundamentales para implementar Active Directory en máquinas virtuales de Azure son similares, pero no idénticos, a los de implementar en máquinas virtuales (y, en cierta medida, en máquinas físicas) locales. Para obtener más información acerca de las diferencias, directrices y otras consideraciones, vaya a [Directrices para implementar Active Directory en máquinas virtuales de Azure](http://msdn.microsoft.com/library/jj156090). Para implementar Active Directory en Azure:
	- Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas.
	- Use el Portal de Azure para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure.
	- Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a [Instalación de un nuevo bosque de Active Directory en Azure](active-directory-new-forest-virtual-machine).
2. Implementación de SQL Server:
	- Use el portal de Azure para aprovisionar una máquina virtual nueva a través de una imagen de archivo desde la biblioteca de imágenes.
	- Configure SQL Server en la máquina virtual. Para obtener más información, vaya a [Instalar SQL Server mediante SysPrep](http://msdn.microsoft.com/library/ee210664.aspx).
	- Una la máquina virtual al dominio de Active Directory recién creado.
3. Implemente una granja multiservidor de SharePoint:
	- Cree una red virtual. Para obtener más información, consulte [Información general sobre redes virtuales](http://msdn.microsoft.com/library/jj156007.aspx).
	- Cuando implementa las máquinas virtuales de SharePoint, necesita subredes para SharePoint Server para que las direcciones de DNS en el cuadro de Active Directory local estén disponibles durante el aprovisionamiento.
	- Use el portal de Azure para crear una máquina virtual.
	- Instale SharePoint Server en esta máquina virtual y genere una imagen reutilizable. Para obtener más información acerca de la instalación de SharePoint Server, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell](http://technet.microsoft.com/library/cc262839.aspx) o [CodePlex: AutoSPInstaller](http://autospinstaller.codeplex.com/).
	- Configure la máquina virtual de SharePoint para crear y conectarse con la granja de SharePoint a través del comando [Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx).
	- Use el portal de Azure para configurar el equilibrio de carga: Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.
4. Administración de la granja de SharePoint mediante System Center:
	- Utilice el agente de Operations Manager y el nuevo paquete de integración de Azure para conectar el System Center local con máquinas virtuales de Azure.
	- Utilice App Controller y Orchestrator locales para las funciones de administración.

## Resumen

Las máquinas virtuales de Azure proporcionan una continuación completa de las implementaciones de SharePoint. Es un servicio completamente respaldado y probado para proporcionar una experiencia óptima junto con otras aplicaciones de Microsoft. De este modo, las organizaciones pueden configurar e implementar fácilmente SharePoint Server dentro de Azure, ya sea para aprovisionar infraestructura para una nueva implementación de SharePoint o para expandir una existente. A medida que las cargas de trabajo de negocios crecen, las organizaciones pueden expandir rápidamente su infraestructura de SharePoint. Del mismo modo, si las necesidades de carga de trabajo disminuyen, las organizaciones pueden contratar recursos a petición, para así pagar solo lo que utilizan. Las máquinas virtuales de Azure brindan una infraestructura excepcional para una amplia variedad de requisitos del negocio, tal como se describe en los cuatro escenarios basados en SharePoint analizados en este artículo.

La correcta implementación de SharePoint Server en Máquinas virtuales de Azure requiere una planificación sólida, especialmente teniendo en cuenta la variedad de opciones de implementación y arquitectura de granja críticas. Los enfoques y procedimientos recomendados que detalla este artículo pueden ayudar a guiar las decisiones para llevar a cabo una implementación informada de SharePoint.

## Recursos adicionales

[SharePoint en máquinas virtuales de Azure](http://msdn.microsoft.com/library/dn275955.aspx)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Carga de trabajo de servicios de infraestructura de Azure: conjunto de servidores de SharePoint de Intranet](virtual-machines-workload-intranet-sharepoint-farm)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO9-->