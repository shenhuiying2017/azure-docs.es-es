<properties urlDisplayName="SharePoint on Azure" pageTitle="Implementación de SharePoint 2010 en máquinas virtuales de Azure" metaKeywords="" description="Descripción de los escenarios admitidos para usar SharePoint 2010 en máquinas virtuales de Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="SharePoint 2010 Deployment on Azure Virtual Machines" authors="josephd" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/16/2014" ms.author="josephd" />




<h1>Implementación de SharePoint 2010 en máquinas virtuales de Azure</h1>
<h2>Resumen ejecutivo</h2>

Microsoft SharePoint Server 2010 ofrece una flexibilidad completa en la implementación, lo que ayuda a las organizaciones a determinar los escenarios de implementación correctos para alinearlos con sus necesidades y objetivos empresariales. Hospedada y administrada en la nube, la oferta de máquinas virtuales de Azure proporciona una infraestructura completa, confiable y disponible para admitir diversas cargas de trabajo de base de datos y aplicaciones a petición, como implementaciones de Microsoft SQL Server y SharePoint.


Aunque las máquinas virtuales de Azure son compatibles con diversas cargas de trabajo, este documento se centra en las implementaciones de SharePoint. Las máquinas virtuales de Azure permiten que las organizaciones creen y administren su infraestructura de SharePoint a través de un aprovisionamiento rápido y un acceso a prácticamente cualquier host de manera universal. Permite tener un completo control y administración sobre procesadores, RAM, intervalos de CPU y otros recursos de máquinas virtuales de SharePoint.

Las máquinas virtuales de Azure disminuyen la necesidad de hardware, por lo que las organizaciones pueden dedicar su atención a crear y administrar infraestructura a escala, en lugar de enfrentar la complejidad y el alto coste por adelantado. Esto significa que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

<h3>¿A quiénes está dirigido este documento?</h3>

Este documento está dirigido a profesionales de las tecnologías de información. Además, los responsables de toma de decisiones técnicas, como arquitectos y administradores de sistemas, pueden utilizar esta información y los escenarios proporcionados para planear y diseñar una infraestructura virtualizada de SharePoint en Azure.

<h3>¿Cuál es la finalidad de este documento?</h3>

Este documento explica la manera en que las organizaciones pueden configurar e implementar SharePoint dentro de máquinas virtuales de Azure. También se analiza la razón por la que este tipo de implementación puede ser beneficioso para organizaciones de muchos tamaños.

<h2>Cambio a la informática en nube</h2>

Según Gartner, la informática en nube se define como "un estilo de informática en el que las capacidades escalables de TI se entregan de manera masiva "como servicio" a clientes externos a través de las tecnologías de Internet". Las palabras importantes en esta definición son "escalables", "servicio" e "Internet". En resumen, la informática en la nube puede definirse como servicios informáticos <strong>implementados y entregados a través de Internet</strong> y que son <strong>escalables a petición</strong>.

Sin lugar a dudas, la informática en nube representa un cambio importante en las tecnologías de la información actuales. Antes, se hablaba principalmente sobre la consolidación y el coste. En la actualidad, las conversaciones giran en torno a la nueva clase de beneficios que puede brindar la informática en nube. Todo se refiere a transformar la manera en que la TI sirve a las organizaciones al aprovechar una nueva variedad de potencialidad. La informática en nube está cambiando fundamentalmente el mundo de la TI, incidiendo sobre cada rol, desde los proveedores de servicios y arquitectos de sistemas hasta desarrolladores y usuarios finales.

Las investigaciones muestran que la agilidad, el foco y el ahorro son tres importantes impulsores para la adopción de la nube:

<ul>
<li><p><strong>Agilidad</strong>: La informática en nube puede acelerar la capacidad de una organización de capitalizar las oportunidades nuevas y de responder a los cambios en las demandas empresariales.</p></li>
<li><p><strong>Foco</strong>: La informática en nube permite que los departamentos de TI reduzcan considerablemente los costes de infraestructura. La infraestructura se abstrae y los recursos se agrupan, por lo que la TI funciona más como una utilidad que como una recopilación de complicados servicios y sistemas. Además, ahora la TI puede avanzar a roles más innovadores y estratégicos.</p></li>
<li><p><strong>Ahorro</strong>: La informática en nube reduce el coste de proporcionar la TI y aumenta la utilización y la eficiencia del centro de datos. Los costes de entrega disminuyen debido a que, con la informática en nube, las aplicaciones y los recursos tienen características de autoservicio; además, el uso de esos recursos puede ahora medirse de nuevas y precisas maneras. La utilización de hardware también aumenta, dado que los recursos de infraestructura (almacenamiento, proceso y red) ahora están agrupados y son abstractos.</p></li>
</ul>

<h2>Modelos de entrega para los servicios en la nube</h2>

En palabras simples, la informática en nube es la abstracción de los servicios de TI. Estos servicios pueden ir desde la infraestructura básica hasta aplicaciones completas. Los usuarios finales solicitan y consumen servicios abstractos sin la necesidad de administrar lo que constituye dichos servicios, o incluso de conocerlos totalmente. En la actualidad, el sector reconoce tres modelos de entrega de servicios en la nube, y cada uno de ellos brinda un equilibrio distinto entre control/flexibilidad y el coste total:

<ul>
<li><p><strong>Infraestructura como servicio</strong> (IaaS): Una infraestructura virtual que hospeda máquinas virtuales y la mayoría de las aplicaciones existentes.</p></li>
<li><p><strong>Plataforma como servicio</strong> (PaaS): Una infraestructura de aplicaciones en la nube que proporciona un entorno de hospedaje de aplicaciones a petición.</p></li>
<li><p><strong>Software como servicio</strong> (SaaS): Un modelo de servicios en la nube en que una aplicación se entrega a través de Internet y donde los clientes pagan por uso (por ejemplo, Microsoft Office 365 o Microsoft CRM Online).</p></li>
</ul>

La ilustración 1 muestra la taxonomía de los servicios en la nube y cómo se asigna a los componentes en una infraestructura de TI. Con un modelo local, el cliente es responsable de administrar toda la pila, desde la conectividad de red hasta las aplicaciones. Con IaaS, un proveedor administra los niveles inferiores de la pila, mientras que el cliente es responsable de administrar el sistema operativo a través de las aplicaciones. Con PaaS, un proveedor de plataforma proporciona y administra todo, desde la conectividad de red hasta el tiempo de ejecución. El cliente solo debe administrar aplicaciones y datos. (La oferta de Azure se ajusta mejor en este modelo). Finalmente, con SaaS, un proveedor proporciona las aplicaciones y abstrae todos los servicios de todos los componentes subyacentes.

<p class="caption">Ilustración 1: Taxonomía de los servicios en la nube</p>

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

<h2>Máquinas virtuales de Azure</h2>

Las máquinas virtuales de Azure presentan una funcionalidad que permite un completo control y administración de máquinas virtuales, además de amplias redes virtuales. Esta oferta puede brindar a las organizaciones sólidos beneficios, como:

<ul>
<li><p><strong>Administración</strong>: Administre las máquinas virtuales de manera centralizada en la nube con un control completo para configurar y mantener la infraestructura.</p></li>
<li><p><strong>Movilidad de las aplicaciones</strong>: Mueva una y otra vez discos duros virtuales entre entornos locales y basados en la nube. No es necesario recompilar aplicaciones para su ejecución en la nube.</p></li>
<li><p><strong>Acceso a aplicaciones de Microsoft Server</strong>: Ejecute las mismas aplicaciones locales e infraestructura en la nube, incluido Microsoft SQL Server, SharePoint Server, Windows Server y Active Directory.</p></li>
</ul>

Máquinas virtuales de Azure es una plataforma potente, simple, abierta y flexible que permite que las organizaciones implementen y ejecuten máquinas virtuales de Linux y Windows Server en minutos:

<ul>
<li><p><strong>Sencilla</strong>: Con máquinas virtuales de Azure, es simple y sencillo crear, migrar, implementar y administrar máquinas virtuales en la nube. Las organizaciones pueden migrar cargas de trabajo a Azure sin tener que cambiar el código existente, o bien pueden configurar nuevas máquinas virtuales en Azure con solo unos pocos clics. La oferta también presta ayuda para desarrollar nuevas aplicaciones en la nube al integrar las funcionalidades de IaaS y PaaS de Azure.</p></li>

<li><p><strong>Abierta y flexible</strong>: Azure es una plataforma abierta que brinda flexibilidad a las organizaciones. Estas pueden comenzar desde una imagen prediseñada de la biblioteca de imágenes, o bien pueden crear y utilizar discos duros virtuales locales y personalizados y cargarlos a la biblioteca de imágenes. También se encuentran disponibles las versiones comunitarias y comerciales de Linux.</p></li>

<li><p><strong>Potente</strong>: Azure es una plataforma de nube preparada para el ámbito empresarial para ejecutar aplicaciones como SQL Server, SharePoint Server o Active Directory en la nube. Las organizaciones pueden crear soluciones locales y en la nube híbridas con conectividad de VPN entre el centro de datos de Azure y sus propias redes.</p></li>
</ul>

<h2>SharePoint en máquinas virtuales de Azure</h2>

La flexibilidad de SharePoint 2010 es compatible con la mayoría de las cargas de trabajo en una implementación de máquinas virtuales de Azure. Las máquinas virtuales de Azure se ajustan perfectamente a escenarios de desarrollo y FIS (SharePoint Server para sitios de Internet). Del mismo modo, también son compatibles las cargas de trabajo centrales de SharePoint. Si una organización desea administrar y controlar su propia implementación de SharePoint 2010 mientras se capitalizan las opciones para la virtualización en la nube, la implementación de máquinas virtuales de Azure es la opción ideal.

La oferta de máquinas virtuales de Azure se hospeda y administra en la nube. Brinda flexibilidad en la implementación y disminuye los costes al reducir los gastos de capital generados por la adquisición de hardware. Con una mayor agilidad en la infraestructura, las organizaciones pueden implementar SharePoint Server en horas, en lugar de hacerlo en días o semanas. Las máquinas virtuales de Azure también permiten que las organizaciones implementen cargas de trabajo de SharePoint en la nube con un modelo de "pago por uso". A medida que crecen las cargas de trabajo de SharePoint, una organización puede expandir rápidamente la infraestructura; luego, cuando disminuyen las necesidades informáticas, puede devolver los recursos que ya no son necesarios, pagando así solo lo que se utiliza.

<h3>Cambio en el foco de TI</h3>

Muchas organizaciones subcontratan los componentes comunes de su administración e infraestructura de TI, como hardware, sistemas operativos, seguridad, almacenamiento de datos y copia de seguridad, mientras mantienen el control de aplicaciones críticas, como SharePoint Server. Al delegar todas las capas de servicio no críticas de sus plataformas de TI en un proveedor virtual, las organizaciones pueden cambiar el foco de su TI hacia los servicios críticos centrales de SharePoint y brindar valor empresarial con proyectos de SharePoint, en lugar de dedicar más tiempo a la configuración de infraestructura.

<h3>Implementación más rápida</h3>

Admitir e implementar una infraestructura de SharePoint de gran tamaño puede dificultar la capacidad de TI de avanzar rápidamente para responder a los requisitos del negocio. El tiempo que se requiere para crear, probar y preparar servidores y granjas de SharePoint e implementarlos en un entorno de producción puede demorar semanas o incluso meses, dependiendo de los procesos y restricciones de la organización. Las máquinas virtuales de Azure permiten que las organizaciones implementen rápidamente sus cargas de trabajo de SharePoint sin gastos de capital para hardware. De esta manera, las organizaciones pueden sacar provecho de la agilidad de la infraestructura para implementar en horas, en lugar de hacerlo en días o semanas.

<h3>Escalabilidad</h3>

Sin la necesidad de implementar, probar y preparar granjas y servidores físicos de SharePoint, las organizaciones pueden expandir y contratar capacidad de proceso a petición inmediatamente. A medida que los requisitos de carga de trabajo de SharePoint crecen, una organización puede expandir rápidamente su infraestructura en la nube. Del mismo modo, cuando disminuyen las necesidades informáticas, la organización puede reducir los recursos, pagando solo lo que utiliza. Las máquinas virtuales de Azure disminuyen los gastos iniciales y los compromisos a largo plazo, lo que permite que las organizaciones creen y administren infraestructuras de SharePoint a escala. Esto significa, nuevamente, que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

<h3>Uso medido</h3>

Las máquinas virtuales de Azure proporcionan potencia de proceso, memoria y almacenamiento para escenarios de SharePoint, con precios que normalmente se basan en el consumo de los recursos. Las organizaciones solo pagan por lo que utilizan y el servicio proporciona toda la capacidad necesaria para ejecutar la infraestructura de SharePoint. Para obtener más información sobre precios y facturación, vaya a <a href="/en-us/pricing/details/">Información sobre precios de Azure</a>. Observe que existen gastos nominales para almacenar y trasladar datos fuera de la nube de Azure desde una red local. Sin embargo, Azure no cobra por cargar datos.

<h3>Flexibilidad</h3>

Las máquinas virtuales de Azure brindan a los desarrolladores la flexibilidad para elegir su entorno de tiempo de ejecución o lenguaje de preferencia, con compatibilidad oficial para .NET, Node.js, Java y PHP. Los desarrolladores también pueden elegir sus herramientas, con compatibilidad para Microsoft Visual Studio, WebMatrix, Eclipse y editores de texto. Además, Microsoft ofrece una ruta de acceso a bajo coste y con bajo riesgo a la nube y ofrece también aprovisionamiento e implementación simples y rentables para las necesidades de la nube, brindando acceso a inteligencia empresarial entre distintos dispositivos y ubicaciones. Finalmente, con la oferta de Azure, los usuarios no solo pueden trasladar discos duros virtuales a la nube, sino que también pueden volver a copiar un disco duro virtual y ejecutarlo localmente o a través de un proveedor de nube, siempre que tengan la licencia adecuada.

<h2>Proceso de aprovisionamiento</h2>

Esta subsección analiza el principal punto fuerte de Azure. La <strong>biblioteca de imágenes</strong> de Azure proporciona la lista de máquinas virtuales preconfiguradas disponibles. Los usuarios pueden publicar discos duros virtuales o imágenes ISO de SharePoint Server, SQL Server, Windows Server y otros en la biblioteca de imágenes. Para simplificar la creación de máquinas virtuales, las imágenes base se crean y publican en la biblioteca. Los usuarios autorizados pueden utilizar estas imágenes para generar la máquina virtual deseada. Para obtener más información, consulte <a href="/en-us/manage/windows/tutorials/virtual-machine-from-gallery/">Creación de una máquina virtual con Windows Server 2008 R2</a> en el sitio de Azure. La ilustración 2 muestra los pasos básicos para crear una máquina virtual mediante el Portal de administración de Azure:

<p class="caption">Ilustración 2: Información general de los pasos para crear una máquina virtual</p>
![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

Los usuarios también pueden cargar una imagen preparada con SysPrep en el Portal de administración de Azure. Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/upload-a-vhd/">Creación y carga de un disco duro virtual</a>. La ilustración 3 muestra los pasos básicos para cargar una imagen a fin de crear una máquina virtual:

<p class="caption">Ilustración 3: Información general de los pasos para cargar una imagen</p>
![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

<h3>Implementación de SharePoint 2010 en Azure</h3>

Siga estos pasos para poder implementar SharePoint 2010 en Azure:

<ol>
<li>Log on to the <a href="http://manage.windowsazure.com/">Portal de administración de Azure</a> a través de la cuenta de suscripción de Azure.
<ul>

<li>Si no tiene una cuenta de Azure, <a href="http://www.windowsazure.com/en-us/pricing/free-trial/">suscríbase para tener acceso a una prueba gratuita de Azure</a>.</li>
</ul>
</li>

<li>Para crear una máquina virtual con el sistema operativo de base, en el Portal de administración de Azure, haga clic en <strong>NUEVO</strong>, en <strong>PROCESO</strong>, en <strong>MÁQUINA VIRTUAL</strong>y, a continuación, haga clic en <strong>DESDE LA GALERÍA</strong>.</li>

<li>A continuación, se abre el <strong>cuadro de diálogo</strong> Elegir una imagen. Haga clic en la imagen de la plataforma <strong>Windows Server 2008 R2 SP1</strong> y, a continuación, haga clic en la flecha derecha.</li>

<li>A continuación, se abre <strong><em>el cuadro de diálogo </em></strong>Configuración de la máquina virtual. Proporcione la siguiente información:

<ul>
<li>Escriba un <strong>NOMBRE DE MÁQUINA VIRTUAL</strong>.
</li>
<li>Seleccione el <strong>TAMAÑO</strong>adecuado.
<ul>
<li>Para un entorno de producción (base de datos y servidor de aplicación de SharePoint), se recomienda usar A3 <em>(4 núcleos, 7 GB de memoria)</em>.</li>
</ul>
</li>
<li>En <strong>NOMBRE DE USUARIO NUEVO</strong>, escriba el nombre de la cuenta de administrador local.</li>
<li>En el cuadro <strong>CONTRASEÑA NUEVA</strong> , escriba una contraseña segura.</li>
<li>En el cuadro <strong>CONFIRMAR</strong> vuelva a escribir la contraseña y, a continuación, haga clic en la flecha derecha.</li>
</ul>
<li>A continuación, se abre el segundo <strong>cuadro de diálogo</strong> Configuración de la máquina virtual. Proporcione la siguiente información:
<ul>
<li>En el cuadro <strong>SERVICIO EN LA NUBE</strong> , elija una de las siguientes opciones:
<ul>
<li><strong>Crear un nuevo servicio en la nube</strong>, en cuyo caso también debe proporcionar un nombre DNS de servicio en la nube.</li>
<li>Seleccione un servicio en la nube existente.</li>
</ul>
<li>En el cuadro <strong>REGION/GRUPO DE AFINIDAD/RED VIRTUAL </strong>, seleccione la región en la que se hospedará la imagen virtual.</li>
<li>En la clase <strong>STORAGE ACCOUNT </strong>, elija una de las siguientes opciones:
<ul>
<li><strong>Use una cuenta de almacenamiento generada de forma automática</strong>.</li>
<li>Seleccione un nombre de cuenta de almacenamiento existente.</li>
<ul>
<li>Solo se crea una cuenta de almacenamiento por región de manera automática. Todas las demás máquinas virtuales creadas con esta configuración están ubicadas en esta cuenta de almacenamiento.</li>
<li>Tiene un límite de 20 cuentas de almacenamiento.</li>
<li>Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/upload-a-vhd/#createstorage">Creación de una cuenta de almacenamiento en Azure</a>.</li>
</ul>
</li>
<li>En el cuadro <strong>CONJUNTO DE DISPONIBILIDAD</strong> , seleccione <STRONG>(none)</STRONG>y, a continuación, haga clic en la flecha derecha.</li>
</ul>
</li>
</ul>
</li>
<li>En el tercer cuadro de diálogo <strong>Configuración de la máquina virtual </strong>, haga clic en la marca de verificación para crear la máquina virtual.</li>


<li>Para conectarse con la máquina virtual:
<ul>
<li>Abra la máquina virtual utilizando el Escritorio remoto.</li>
<li>En el Portal de administración de Azure, seleccione su máquina virtual y, a continuación, la página <strong>PANEL</strong> .</li>
<li>Haga clic en <strong>Conectar</strong>.</li>
</ul>
</li>
<li>Cree la máquina virtual de SQL Server con cualquiera de las siguientes opciones:
<ul>
<li>Cree una máquina virtual de SQL Server 2012; para ello, siga los pasos del 1 al 7 anteriores, salvo el <strong>paso 3</strong>, use la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/install-sql-server/">Aprovisionamiento de una máquina virtual de SQL Server en Azure</a>.
<ul>
<li>Al elegir esta opción, el proceso de aprovisionamiento conserva una copia de los archivos de instalación de SQL Server 2012 en la ruta de acceso al directorio <em>C:\SQLServer_11.0_Full</em> , a fin de que pueda personalizar la instalación. Por ejemplo, puede convertir la instalación de evaluación de SQL Server 2012 en una versión con licencia si utiliza su clave de licencia.</li>
</ul>
</li>
<li>Utilice la herramienta de preparación del sistema SQL Server (SysPrep) para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7). Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/ee210664.aspx">Instalar SQL Server 2012 mediante SysPrep</a>.</li>
<li>Utilice el símbolo del sistema para instalar SQL Server. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/ms144259.aspx#SysPrep">Instalar SQL Server 2014 desde el símbolo del sistema</a>.</li>
<li>Utilice medios compatibles de SQL Server y su clave de licencia para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7).</li>
</ul>
</li>
<li>Cree la granja de SharePoint con los siguientes pasos secundarios:
<ul>
<li>Paso secundario 1: Configure la suscripción de Azure con archivos de script.</li>
<li>Paso secundario 2: Aprovisione los servidores de SharePoint mediante la creación de otra máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7). Para crear un servidor de SharePoint en esta máquina virtual, elija una de las siguientes opciones:
<ul>
<li>Aprovisionamiento con GUI de SharePoint:
<ul>
<li>Para crear y aprovisionar una granja de SharePoint, vaya a <a href="http://technet.microsoft.com/en-us/library/ee805948.aspx#CreateConfigure">Creación de una granja de servidores de Microsoft SharePoint.</a>.</li>
<li>Para agregar un servidor web o de aplicaciones a la granja, vaya a <a href="http://technet.microsoft.com/en-us/library/cc261752.aspx">Agregar un servidor web o de aplicaciones a la granja de servidores en SharePoint Server 2010</a>.</li>
<li>
<p>Para agregar un servidor de base de datos a una granja existente, vaya a <a href="http://technet.microsoft.com/en-us/library/cc262781">Agregar un servidor de base de datos a una granja existente</a>.</p>
<ul>
<li>Para utilizar SQL Server 2012 para su granja de SharePoint, debe descargar e instalar el Service Pack 1 para SharePoint Server 2010 después de instalar la aplicación y elegir no configurar el servidor. Para obtener más información, consulte <a href="http://www.microsoft.com/en-us/download/details.aspx?id=26623">Service Pack 1 para SharePoint Server 2010</a>.</li>
<li>Para aprovechar las características de Business Intelligence SQL Server, se recomienda instalar SharePoint Server como granja de servidores en lugar de un servidor independiente. Para obtener más información, consulte <a href="http://technet.microsoft.com/en-us/library/hh231681(v=sql.110).aspx">Instalar las características de SQL Server 2012 Business Intelligence</a>.</li>
</ul>
</li>
</ul>
</li>
<li>Aprovisionamiento mediante Microsoft Windows PowerShell: Puede utilizar la herramienta de línea de comandos Psconfig como una interfaz alternativa para realizar varias operaciones que controlan la manera en que se aprovisionan los productos de SharePoint 2010. Para obtener más información, consulte <a href="http://technet.microsoft.com/en-us/library/cc263093.aspx">Referencia de la línea de comandos de Psconfig</a>.</li>
</ul>
</li>
<li>Paso secundario 3: Configure SharePoint. Cuando cada máquina virtual de SharePoint tenga un estado Listo, configure SharePoint Server en cada servidor mediante una de las siguientes opciones:
<ul>
<li>Configure SharePoint desde la GUI.</li>
<li>Configure SharePoint mediante Windows PowerShell. Para obtener más información, consulte <a href="http://technet.microsoft.com/en-us/library/cc262839.aspx">Instalación de SharePoint Server 2010 mediante Windows PowerShell</a>.
<ul>
<li>También puede utilizar AutoSPInstaller del proyecto de CodePlex, que consta de scripts de Windows PowerShell, un archivo XML de entrada y un archivo por lotes de Microsoft Windows estándar. AutoSPInstaller brinda un marco para un script de instalación de SharePoint 2010 basado en Windows PowerShell. Para obtener más información, consulte <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.

<strong>Nota:</strong>: asegúrese de configurar la seguridad en el extremo del Portal de administración y de definir un puerto de entrada en el firewall de Windows de la máquina virtual. Luego, confirme que puede iniciar una sesión remota de Windows PowerShell en uno de los servidores de aplicaciones de SharePoint al abrir una sesión de Windows PowerShell con credenciales de administrador.
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>Una vez que se complete el script, conéctese a la máquina virtual a través del panel de la máquina virtual.</li>
<li>Compruebe la configuración de SharePoint: Inicie sesión en el servidor de SharePoint y, a continuación, utilice la administración central para comprobar la configuración.</li>
</ol>

<h3>Creación y carga de un disco duro virtual</h3>

También puede crear sus propias imágenes y cargarlas a Azure como un archivo VHD. Siga estos pasos para crear y cargar un archivo VHD en Azure:

<ol>
<li>Create the Hyper-V-enabled image: Use Hyper-V Manager to create the Hyper-V-enabled VHD. For more information, go to <a href="http://technet.microsoft.com/en-us/library/cc742509">Cree discos duros virtuales.</a>.</li>
<li>Cree una cuenta de almacenamiento en Azure: Se requiere una cuenta de almacenamiento en Azure para cargar un archivo VHD que se pueda utilizar para crear una máquina virtual. Esta cuenta se puede crear mediante el Portal de administración de Azure. Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/upload-a-vhd/">Creación de una cuenta de almacenamiento en Azure</a>.</li>
<li>Prepare la imagen que se va a cargar: Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando SysPrep. Para obtener más información, consulte <a href="http://technet.microsoft.com/en-us/library/bb457073.aspx">Introducción al uso de SysPrep</a>.</li>
<li>Cargue la imagen en Azure: Para cargar una imagen contenida en un archivo VHD, debe crear e instalar un certificado de administración. Obtenga la huella digital del certificado y el identificador de suscripción. Establezca la conexión y cargue el archivo VHD con la herramienta de línea de comandos CSUpload. Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/upload-a-vhd/">Cargar la imagen en Azure</a>.</li>
</ol>

<h2>Escenarios de uso</h2>

En esta sección se analizan algunos importantes escenarios de cliente para implementaciones de SharePoint mediante máquinas virtuales de Azure. Cada escenario se divide en dos partes: una breve descripción del escenario y algunos pasos para comenzar.

<h3>Escenario 1: Entorno simple de desarrollo y prueba de SharePoint</h3>

<h4>Descripción</h4>

Las organizaciones buscan maneras más ágiles de crear aplicaciones de SharePoint y configurar entornos de SharePoint para prueba y desarrollo internos/externos. Fundamentalmente, las organizaciones desean disminuir el tiempo que se requiere para configurar los proyectos de desarrollo de aplicaciones de SharePoint y reducir el coste a través del aumento del uso de sus entornos de prueba. Por ejemplo, es posible que una organización desee realizar una prueba de carga a petición en SharePoint Server y ejecutar una prueba de aceptación del usuario (UAT) con más usuarios simultáneos en distintas ubicaciones geográficas. De manera similar, integrar equipos internos/externos es una necesidad empresarial cada vez más importante para muchas de las organizaciones actuales.

Este escenario explica cómo las organizaciones pueden utilizar granjas de SharePoint preconfiguradas para cargas de trabajo de desarrollo y prueba. Una topología de implementación de SharePoint tiene el mismo aspecto que tendría en una implementación virtualizada local. Las aptitudes de TI existentes se traducen 1:1 en una implementación de máquinas virtuales de Azure, donde el principal beneficio es un cambio casi completo en los costes desde los gastos de capital a los gastos operativos, sin que sea necesaria una compra de servidores físicos por adelantado. Las organizaciones pueden eliminar el coste de capital referente a hardware de servidor y lograr una flexibilidad al disminuir considerablemente el tiempo de aprovisionamiento que se requiere para crear, configurar o ampliar una granja de SharePoint para un entorno de prueba y desarrollo. La TI puede agregar y quitar de manera dinámica capacidad para enfrentar las cambiantes necesidades de prueba y desarrollo. Además, la TI también puede centrarse más en entregar valor para el negocio con proyectos de SharePoint y menos en la administración de la infraestructura.

Para utilizar completamente las máquinas para prueba de carga, las organizaciones pueden configurar máquinas de desarrollo y prueba virtualizadas de SharePoint en Azure con compatibilidad de sistema operativo para Windows Server 2008 R2. Este permite a los equipos de desarrollo crear y probar aplicaciones y migrar con facilidad a entornos de producción locales o en la nube sin cambios en el código. Los mismos marcos y conjuntos de herramientas se pueden utilizar localmente y en la nube, lo que permite que el equipo distribuido tenga acceso al mismo entorno. Los usuarios también pueden tener acceso a datos y aplicaciones locales si se establece una conexión VPN directa.

<h4>Tareas iniciales</h4>

La ilustración 4 muestra un entorno de desarrollo y prueba de SharePoint en una máquina virtual de Azure. Para crear esta implementación, comience usando el mismo entorno de desarrollo y prueba local de SharePoint que se utiliza para desarrollar aplicaciones. Luego, cargue e implemente las aplicaciones a la máquina virtual de Azure para prueba y desarrollo. Si la organización decide volver a trasladar la aplicación al entorno local, puede hacerlo sin tener que modificarla.

<p class="caption">Ilustración 4: Entorno de desarrollo y prueba de SharePoint en máquinas virtuales de Azure</p>

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

<h4>Configuración del entorno de escenario</h4>

Siga estos pasos para implementar un entorno de desarrollo y prueba de SharePoint en Azure:

<ol>
<li><em>Provision</em>: First, provision a VPN connection between on-premises and Azure using Azure Virtual Network. (Because Active Directory is not being used here, a VPN tunnel is needed.) For more information, go to <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx">Red virtual de Azure (Consideraciones de diseño y escenarios de conexiones seguras)</a>. Luego, utilice el Portal de administración para aprovisionar una máquina virtual nueva a través de una imagen de archivo desde la biblioteca de imágenes.
<ul>
<li>Puede cargar las máquinas virtuales de desarrollo y prueba locales de SharePoint en su cuenta de almacenamiento de Azure y hacer referencia a esas máquinas virtuales a través de la biblioteca de imágenes para crear el entorno requerido.</li>
<li>Puede utilizar la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, consulte <a href="/en-us/manage/windows/common-tasks/install-sql-server/">Aprovisionamiento de una máquina virtual de SQL Server en Azure</a>.</li>
</ul>
</li>
<li><em>Instalación</em>: Instale SharePoint Server, Visual Studio y SQL Server en las máquinas virtuales a través de una conexión de Escritorio remoto.
<ul>
<li>Elija una opción para instalar SharePoint Server:
<ul>
<li>Use el script de configuración simple de SharePoint 2010 para crear una máquina para el desarrollador de SharePoint. Para obtener más información, consulte <a href="http://www.microsoft.com/en-us/download/details.aspx?id=23415">Script de configuración sencilla de SharePoint 2010</a>.</li>
<li>Utilice Windows PowerShell. Para obtener más información, consulte <a href="http://technet.microsoft.com/en-us/library/cc262839.aspx">Instalación de SharePoint Server 2010 mediante Windows PowerShell</a>.</li>
<li>Utilice AutoSPInstaller del proyecto de CodePlex. Para obtener más información, consulte <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
</ul>
</li>
<li>Instale Visual Studio. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx">Instalación de Visual Studio</a>.</li>
<li>Instale SQL Server. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/ee210664.aspx">Instalar SQL Server mediante SysPrep</a>.
<ul>
<li>Consulte el laboratorio práctico para crear y configurar SQL Server 2012 para una implementación de granja de SharePoint: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuración de SQL Server 2012 para SharePoint en Azure.</a>.</li>
<li>Consulte el laboratorio práctico para crear una granja de SharePoint a través de la configuración de Active Directory y el uso de una sola base de datos de SQL Server: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Implementación de una granja de SharePoint con Máquinas virtuales de Azure.</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Desarrollo de paquetes y scripts de implementación para aplicaciones y bases de datos</em>: Si tiene previsto utilizar una máquina virtual disponible desde la biblioteca de imágenes, es posible implementar las aplicaciones y bases de datos locales en máquinas virtuales de Azure:
<ul>
<li>Cree paquetes de implementación para las aplicaciones y bases de datos locales existentes mediante SQL Server Data Tools y Visual Studio.</li>
<li>Utilice estos paquetes para implementar las aplicaciones y bases de datos en máquinas virtuales de Azure.</li>
</ul>
</li>
<li><em>Implementación de aplicaciones y bases de datos de SharePoint</em>:
<ul>
<li>Configure la seguridad en el extremo del Portal de administración y defina un puerto de entrada en el firewall de Windows de la máquina virtual.</li>
<li>Implemente aplicaciones y bases de datos de SharePoint en máquinas virtuales de Azure a través de los paquetes y scripts de implementación que se crearon en el paso 3.</li>
<li>Pruebe las aplicaciones y bases de datos implementadas.</li>
</ul>
</li>
<li><em>Administración de máquinas virtuales</em>:
<ul>
<li>Supervise las máquinas virtuales a través del Portal de administración.</li>
<li>Supervise las aplicaciones mediante Visual Studio y SQL Server Management Studio.</li>
<li>También puede supervisar y administrar las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.</li>
</ul>
</li>
</ol>
<h3>Escenario 2: Granja de SharePoint de acceso público con personalización</h3>

<h4>Descripción</h4>

Las organizaciones desean establecer una presencia en Internet que esté hospedada en la nube y que sea fácilmente escalable según la necesidad y la demanda. También desean crear sitios web de extranet de asociados para colaboración e implementar un proceso simple para la creación y aprobación distribuida del contenido de los sitios web. Finalmente, para controlar el incremento de las cargas, estas organizaciones desean proporcionar capacidad a petición a sus sitios web.

En este escenario, SharePoint Server se utiliza como la base para hospedar un sitio web de acceso público. Permite que las organizaciones implementen, personalicen y hospeden rápidamente sus sitios web empresariales en una infraestructura en la nube segura y escalable. Con sitios web de acceso público de SharePoint en Azure, las organizaciones pueden escalar a medida que crece el tráfico y solo pagar por lo que utilizan. Se pueden utilizar herramientas comunes, similares a las que se utilizan de manera local, para la creación de contenido, el flujo de trabajo y la aprobación con SharePoint en Azure.

Además, a través de máquinas virtuales de Azure, las organizaciones pueden configurar fácilmente entornos de ensayo y producción que se ejecuten en máquinas virtuales. Es posible crear copias de seguridad de las máquinas virtuales de SharePoint de acceso público creadas en Azure en el almacenamiento virtual. Además, para fines de recuperación ante desastres, la característica de replicación geográfica continua permite que las organizaciones creen automáticamente copias de seguridad de máquinas virtuales que operan en un centro de datos en otro centro de datos a kilómetros de distancia. (Para obtener más información sobre la replicación geográfica, consulte <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx">Introducción sobre la replicación geográfica para Almacenamiento de Azure</a>).

Las máquinas en la infraestructura de Azure están validadas y son compatibles para trabajar con otros productos de Microsoft, como SQL Server y SharePoint Server. Azure y SharePoint Server funcionan mejor en conjunto: Ambos forman parte de la familia de Microsoft y están perfectamente integrados, son compatibles y está probado que, en conjunto, brindan una experiencia óptima. Ambos tienen un único punto de respaldo para la aplicación de SharePoint y la infraestructura de Azure.

<h4>Tareas iniciales</h4>

En este escenario, se deben agregar más servidores web front-end para SharePoint Server a fin de admitir el tráfico adicional. Estos servidores requieren mayor seguridad y los controladores de dominio de los Servicios de dominio de Active Directory para admitir la autorización y la autenticación del usuario. La ilustración 5 muestra el diseño de este escenario.

<p class="caption">Ilustración 5: Granja de SharePoint de acceso público con personalización</p>
![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

<h4>Configuración del entorno de escenario</h4>

Siga estos pasos para implementar una granja de SharePoint de acceso público en Azure:

<ol>
<li><em>Deploy Active Directory</em>: The fundamental requirements for deploying Active Directory on Azure Virtual Machines are similar"but not identical"to deploying it on VMs (and, to some extent, physical machines) on-premises. For more information about the differences, as well as guidelines and other considerations, go to <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156090">Directrices para implementar Active Directory en máquinas virtuales de Microsoft Azure</a>. Para implementar Active Directory en Azure:
<ul>
<li>Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configuración de redes virtuales</a>.</li>
<li>Utilice el Portal de administración para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Implementación y creación de un controlador de dominios</a>.
<ul>
<li>Puede hacer referencia al script de Windows PowerShell para implementar un dominio independiente en la nube mediante Máquinas virtuales de Azure y Red virtual de Azure. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Implementación de Active Directory en Azure (Windows PowerShell)</a>.</li>
<li>Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a <a href="/en-us/manage/services/networking/active-directory-forest/">Instalación de un nuevo bosque de Active Directory en Azure.</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Aprovisionamiento de una máquina virtual</em>: Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.</li>
<li><em>Implementación de una granja de SharePoint</em>:
<ul>
<li>Utilice la máquina virtual recientemente aprovisionada para instalar SharePoint y generar una imagen reutilizable. Para obtener más información sobre la instalación de SharePoint Server, vaya a <a href="http://technet.microsoft.com/en-us/library/cc262839.aspx">Instalación y configuración de SharePoint Server 2010 mediante Windows PowerShell</a> o <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Configure la máquina virtual de SharePoint para crear y conectarse con la granja de SharePoint.</li>
<li>Utilice el Portal de administración para configurar el equilibrio de carga.
<ul>
<li>Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.</li>
<li>Agregue otra máquina virtual web front-end a la granja existente de SharePoint para el tráfico adicional.</li>
</ul>
</li>
</ul>
</li>
<li><em>Administración de máquinas virtuales</em>:
<ul>
<li>Supervise las máquinas virtuales a través del Portal de administración.</li>
<li>Supervise la granja de SharePoint mediante la administración central.</li>
</ul>
</li>
</ol>

<h3>Escenario 3: Granja con escalamiento horizontal para servicios adicionales de Business Intelligence</h3>

<h4>Descripción</h4>

Business Intelligence es fundamental para obtener información clave y tomar decisiones sólidas y rápidas. Cuando las organizaciones hacen la transición desde un enfoque local, no desean realizar cambios en el entorno de BI mientras implementan aplicaciones de BI existentes en la nube. Desean hospedar informes desde SQL Server Analysis Services (SSAS) o SQL Server Reporting Services (SSRS) en un entorno altamente durable y disponible, mientras mantiene el control completo de la aplicación de Business Intelligence, todo sin dedicar mucho tiempo y presupuesto al mantenimiento.

Este escenario describe la manera en que las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones críticas de Business Intelligence. Las organizaciones pueden implementar granjas de SharePoint en máquinas virtuales de Azure y escalar horizontalmente los componentes de BI de la máquina virtual del servidor de aplicaciones, como SSRS o Excel Services. A través del escalado de componentes que requieren muchos recursos en la nube, pueden admitir mejor y con más facilidad cargas de trabajo especializadas. Observe que SQL Server en máquinas virtuales de Azure funciona bien, puesto que es fácil escalar las instancias de SQL Server, que van desde instalaciones pequeñas a muy grandes. Esto proporciona una elasticidad que permite que las organizaciones aprovisionen (amplíen) o desaprovisionen (reduzcan) de manera dinámica instancias de Business Intelligence según los requisitos inmediatos de carga de trabajo.

La migración de aplicaciones existentes de BI a Azure proporciona un mejor escalado. Con la potencia de SSAS, SSRS y SharePoint Server, las organizaciones pueden crear poderosas aplicaciones de BI e informes y paneles que aumentan o disminuyen. Estas aplicaciones y paneles también se pueden integrar de manera más segura con datos y aplicaciones locales. Azure garantiza que los centros de datos se atienen a la norma ISO 27001. Para obtener más información, consulte <a href="/en-us/support/trust-center/compliance/">Centro de confianza de Microsoft Azure</a>.

<h4>Tareas iniciales</h4>

Para escalar horizontalmente la implementación de componentes de BI, se debe instalar un nuevo servidor de aplicaciones con servicios como PowerPivot, Power View, Excel Services o PerformancePoint Services. O bien, se deben agregar instancias de BI de SQL Server como SSAS o SSRS a la granja existente para admitir un procesamiento adicional de consultas. El servidor se puede agregar como una máquina virtual nueva de Azure con SharePoint 2010 Server o SQL Server instalado. Luego se pueden instalar, implementar y configurar en ese servidor los componentes de BI (ilustración 6).

<p class="caption">Ilustración 6: Granja de SharePoint con escalamiento horizontal para servicios adicionales de BI</p>

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

<h4>Configuración del entorno de escenario</h4>

Siga estos pasos para escalar horizontalmente un entorno de BI en Azure:

<ol>
<li><em>Provision</em>:
<ul>
<li>Provision a VPN connection between on premises and Azure using Azure Virtual Network. For more information, go to <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx">Red virtual de Azure (Consideraciones de diseño y escenarios de conexiones seguras)</a>.</li>
<li>Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.
<ul>
<li>Puede cargar imágenes de carga de trabajo de BI de SharePoint Server o SQL Server en la biblioteca de imágenes y cualquier usuario autorizado puede elegir esas máquinas virtuales de componentes de BI para crear el entorno de escalamiento horizontal.</li>
</ul>
</li>
</ul>
</li>
<li><em>Instalación</em>: Si su organización no tiene imágenes prediseñadas de componentes de BI de SharePoint Server o SQL Server, instale SharePoint Server y SQL Server en las máquinas virtuales a través de la conexión de Escritorio remoto.
<ul>
<li>Para obtener más información sobre la instalación de SharePoint, vaya a <a href="http://technet.microsoft.com/en-us/library/cc262839.aspx">Instalación de SharePoint Server 2010 mediante Windows PowerShell</a> o <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Para obtener más información sobre la instalación de SQL Server, vaya a <a href="http://msdn.microsoft.com/en-us/library/ee210664.aspx">Instalar SQL Server mediante SysPrep</a>.</li>
<li>Consulte el laboratorio práctico para crear y configurar SQL Server 2012 para una implementación de granja de SharePoint: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint">Configuración de SQL Server 2012 para SharePoint en Azure.</a>.</li>
<li>Consulte el laboratorio práctico para crear una granja de SharePoint a través de la configuración de Active Directory y el uso de una sola base de datos de SQL Server: <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs">Implementación de una granja de SharePoint con Máquinas virtuales de Azure.</a>.</li>
</ul>
</li>
<li><em>Incorporación de la máquina virtual de BI</em>:
<ul>
<li>Configure la seguridad en el extremo del Portal de administración y defina un puerto de entrada en el firewall de Windows de la máquina virtual.</li>
<li>Agregue la máquina virtual de BI recientemente creada a la granja existente de SharePoint o SQL Server.</li>
</ul>
</li>
<li><em>Administración de máquinas virtuales</em>:
<ul>
<li>Supervise las máquinas virtuales a través del Portal de administración.</li>
<li>Supervise la granja de SharePoint mediante la administración central.</li>
<li>Supervise y administre las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.</li>
</ul>
</li>
</ol>
<h3>Escenario 4: Sitio web basado en SharePoint completamente personalizado</h3>

<h4>Descripción</h4>

Cada vez más, las organizaciones desean crear en la nube sitios web de SharePoint completamente personalizados. Necesitan un entorno altamente duradero y disponible que ofrezca control completo para mantener las aplicaciones complejas en ejecución en la nube, pero no desean dedicar una gran cantidad de tiempo ni de presupuesto.

En este escenario, una organización puede implementar toda su granja de SharePoint en la nube y escalar dinámicamente todos los componentes para obtener capacidad adicional, o bien puede extender su implementación local a la nube para aumentar la capacidad y mejorar el rendimiento cada vez que sea necesario. El escenario se centra en organizaciones que desean obtener la "experiencia de SharePoint" completa para el desarrollo de aplicaciones y la administración de contenido empresarial. Los sitios más complejos también pueden incluir una mayor generación de informes, Power View, PerformancePoint, PowerPivot, gráficas más detalladas y la mayoría de las demás funcionalidades de sitios de SharePoint para obtener una funcionalidad completa de un extremo a otro.

Las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones personalizadas y componentes asociados en una infraestructura de nube rentable y altamente segura. También pueden utilizar Microsoft System Center local como una herramienta de administración común para aplicaciones locales y en la nube.

<h4>Tareas iniciales</h4>

Para implementar un sitio web completamente personalizado de SharePoint en Azure, una organización debe implementar un dominio de Active Directory en la nube y aprovisionar nuevas máquinas virtuales en este dominio. Luego, se debe crear y configurar una máquina virtual que ejecute SQL Server 2012 como parte de una granja de SharePoint. Finalmente, es necesario crear una granja de SharePoint, equilibrar su carga y conectarla a Active Directory y SQL Server (ilustración 7).

<p class="caption">Ilustración 7: Sitio web basado en SharePoint completamente personalizado</p>

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

<h4>Configuración del entorno de escenario</h4>

Los siguientes pasos muestran cómo crear un entorno de granjas de SharePoint personalizado a partir de imágenes prediseñadas disponibles en la biblioteca de imágenes. Sin embargo, tenga en cuenta que puede cargar máquinas virtuales de la granja de SharePoint en la biblioteca de imágenes, y los usuarios autorizados pueden elegir esas máquinas virtuales para crear la granja de SharePoint requerida en Azure.

<ol>
<li>Deploy Active Directory<em>: The fundamental requirements for deploying Active Directory on Azure Virtual Machines are similar"but not identical"to deploying it on VMs (and, to some extent, physical machines) on premises. For more information about the differences, as well as guidelines and other considerations, go to <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156090">Directrices para implementar Active Directory en máquinas virtuales de Microsoft Azure</a>. Para implementar Active Directory en Azure:</em>
<ul>
<li>Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Configuración de redes virtuales</a>.</li>
<li>Utilice el Portal de administración para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md">Implementación y creación de un controlador de dominios</a>.
<ul>
<li>Puede hacer referencia al script de Windows PowerShell para implementar un dominio independiente en la nube mediante Máquinas virtuales de Azure y Red virtual de Azure. Para obtener más información, consulte <a href="https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS">Implementación de Active Directory en Azure (Windows PowerShell)</a>.</li>
<li>Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a <a href="/en-us/manage/services/networking/active-directory-forest/">Instalación de un nuevo bosque de Active Directory en Azure.</a>.</li>
</ul>
</li>
</ul>
</li>
<li><em>Implementación de SQL Server</em>:
<ul>
<li>Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.</li>
<li>Configure SQL Server en la máquina virtual. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/ee210664.aspx">Instalar SQL Server mediante SysPrep</a>.</li>
<li>Una la máquina virtual al dominio de Active Directory recién creado.</li>
</ul>
</li>
<li><em>Implementación de una granja multiservidor de SharePoint</em>:
<ul>
<li>Cree una red virtual. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx">Red virtual de Azure (Consideraciones de diseño y escenarios de conexiones seguras)</a>.
<ul>
<li>Cuando implementa las máquinas virtuales de SharePoint, necesita subredes para SharePoint Server para que las direcciones de DNS en el cuadro de Active Directory local estén disponibles durante el aprovisionamiento.</li>
</ul>
</li>
<li>Utilice el Portal de administración para crear una máquina virtual.</li>
<li>Instale SharePoint Server en esta máquina virtual y genere una imagen reutilizable. Para obtener más información sobre la instalación de SharePoint Server, vaya a <a href="http://technet.microsoft.com/en-us/library/cc262839.aspx">Instalación y configuración de SharePoint Server 2010 mediante Windows PowerShell</a> o <a href="http://autospinstaller.codeplex.com/">CodePlex: AutoSPInstaller</a>.</li>
<li>Configure la máquina virtual de SharePoint para crear y conectarse con la granja de SharePoint mediante el comando <a href="http://technet.microsoft.com/en-us/library/ff607979.aspx">Join-SharePointFarm</a> .</li>
<li>Utilice el Portal de administración para configurar el equilibrio de carga:
<ul>
<li>Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.
<ul>
<li>Para obtener más información acerca de la implementación de granjas de SharePoint en máquinas virtuales de Azure, vea este vídeo de <a href="http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327">TechEd North America 2012.</a>.</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><em>Administración de la granja de SharePoint mediante System Center</em>:
<ul>
<li>Utilice el agente de Operations Manager y el nuevo paquete de integración de Azure para conectar el System Center local con Máquinas virtuales de Azure.</li>
<li>Utilice App Controller y Orchestrator locales para las funciones de administración.</li>
</ul>
</li>
</ol>

<h2>Conclusión</h2>
La informática en nube está transformando la manera en que la TI sirve a las organizaciones. El motivo es que la informática en nube puede aprovechar una nueva clase de beneficios, incluida la considerable disminución del coste, además de un mayor foco, agilidad y flexibilidad de la TI. Azure lidera la forma en que la informática en nube brinda una infraestructura virtual simple, abierta, flexible y poderosa. La máquinas virtuales de Azure disminuyen la necesidad de hardware, por lo que las organizaciones pueden reducir el coste y la complejidad al crear infraestructura a escala, con un control completo y una administración optimizada.

Las máquinas virtuales de Azure proporcionan una continuación completa de las implementaciones de SharePoint. Es un servicio completamente respaldado y probado para proporcionar una experiencia óptima junto con otras aplicaciones de Microsoft. De este modo, las organizaciones pueden configurar e implementar fácilmente SharePoint Server dentro de Azure, ya sea para aprovisionar infraestructura para una nueva implementación de SharePoint o para expandir una existente. A medida que las cargas de trabajo de negocios crecen, las organizaciones pueden expandir rápidamente su infraestructura de SharePoint. Del mismo modo, si las necesidades de carga de trabajo disminuyen, las organizaciones pueden contratar recursos a petición, para así pagar solo lo que utilizan. Las máquinas virtuales de Azure brindan una infraestructura excepcional para una amplia variedad de requisitos del negocio, tal como se describe en los cuatro escenarios basados en SharePoint analizados en este documento.

La correcta implementación de SharePoint Server en Máquinas virtuales de Azure requiere una planificación sólida, especialmente teniendo en cuenta la variedad de opciones de implementación y arquitectura de granja críticas. Los enfoques y procedimientos recomendados que detalla este documento pueden ayudar a guiar las decisiones para llevar a cabo una implementación informada de SharePoint.

<h2>Recursos adicionales</h2>

<ul>
<li>
<p>Kit de formación para Azure: Laboratorio práctico y presentación</p>
<p><a href="http://windowsazure-trainingkit.github.com/labs.htm">http://windowsazure-trainingkit.github.com/labs.htm</a></p>
</li>
<li>
<p>Introducción a Azure PowerShell</p>
<p><a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj156055">http://msdn.microsoft.com/en-us/library/windowsazure/jj156055</a></p>
</li>
<li>
<p>Azure Management Cmdlets</p>
<p><a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj152841">http://msdn.microsoft.com/en-us/library/windowsazure/jj152841</a></p>
</li>
<li>
<p>Herramientas de línea de comandos y cmdlets de PowerShell para distintos sistemas operativos</p>
<p><a href="/en-us/manage/downloads/">https://www.windowsazure.com/en-us/manage/downloads/</a></p>
</li>
<li>
<p>Guías de procedimientos y documentación sobre procedimientos recomendados</p>
<p><a href="/en-us/manage/windows/">https://www.windowsazure.com/en-us/manage/windows/</a></p>
</li>
</ul>


<!--HONumber=35.1-->
