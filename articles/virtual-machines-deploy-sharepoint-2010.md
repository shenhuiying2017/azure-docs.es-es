<properties linkid="manage-windows-other-resource-sharepoint-wp" urlDisplayName="SharePoint on Azure" pageTitle="SharePoint 2010 Deployment on Azure Virtual Machines" metaKeywords="" description="Understand the supported scenarios for using SharePoint 2010 on Azure virtual machines." metaCanonical="" services="virtual-machines" documentationCenter="" title="SharePoint Deployment on Azure Virtual Machines" authors="josephd" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="josephd"></tags>

# Implementación de SharePoint en máquinas virtuales de Azure

## Resumen ejecutivo

Microsoft SharePoint Server 2010 ofrece una flexibilidad completa en la implementación, lo que ayuda a las organizaciones a determinar los escenarios de implementación correctos para alinearlos con sus necesidades y objetivos empresariales. Hospedada y administrada en la nube, la oferta de máquinas virtuales de Azure proporciona una infraestructura completa, confiable y disponible para admitir diversas cargas de trabajo de base de datos y aplicaciones a petición, como implementaciones de Microsoft SQL Server y SharePoint.

Aunque las máquinas virtuales de Azure son compatibles con diversas cargas de trabajo, este documento se centra en las implementaciones de SharePoint. Las máquinas virtuales de Azure permiten que las organizaciones creen y administren su infraestructura de SharePoint a través de un aprovisionamiento rápido y un acceso a prácticamente cualquier host de manera universal. Permite tener un completo control y administración sobre procesadores, RAM, intervalos de CPU y otros recursos de máquinas virtuales de SharePoint.

Las máquinas virtuales de Azure disminuyen la necesidad de hardware, por lo que las organizaciones pueden dedicar su atención a crear y administrar infraestructura a escala, en lugar de enfrentar la complejidad y el alto coste por adelantado. Esto significa que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

### ¿A quiénes está dirigido este documento?

Este documento está dirigido a profesionales de las tecnologías de información. Además, los responsables de toma de decisiones técnicas, como arquitectos y administradores de sistemas, pueden utilizar esta información y los escenarios proporcionados para planear y diseñar una infraestructura virtualizada de SharePoint en Azure.

### ¿Cuál es la finalidad de este documento?

Este documento explica la manera en que las organizaciones pueden configurar e implementar SharePoint dentro de máquinas virtuales de Azure. También se analiza la razón por la que este tipo de implementación puede ser beneficioso para organizaciones de muchos tamaños.

## Cambio a la informática en nube

Según Gartner, la informática en nube se define como "un estilo de informática en el que las capacidades escalables de TI se entregan de manera masiva "como servicio" a clientes externos a través de las tecnologías de Internet". Las palabras importantes en esta definición son "escalables", "servicio" e "Internet". En pocas palabras, la informática en nube se puede definir como servicios que se **implementan y entregan a través de Internet** y que son **escalables a petición**.

Sin lugar a dudas, la informática en nube representa un cambio importante en las tecnologías de la información actuales. Antes, se hablaba principalmente sobre la consolidación y el coste. En la actualidad, las conversaciones giran en torno a la nueva clase de beneficios que puede brindar la informática en nube. Todo se refiere a transformar la manera en que la TI sirve a las organizaciones al aprovechar una nueva variedad de potencialidad. La informática en nube está cambiando fundamentalmente el mundo de la TI, incidiendo sobre cada rol, desde los proveedores de servicios y arquitectos de sistemas hasta desarrolladores y usuarios finales.

Las investigaciones muestran que la agilidad, el foco y el ahorro son tres importantes impulsores para la adopción de la nube:

-   **Agilidad**: La informática en nube puede acelerar la capacidad de una organización de capitalizar las oportunidades nuevas y de responder a los cambios en las demandas empresariales.

-   **Foco**: La informática en nube permite que los departamentos de TI reduzcan considerablemente los costes de infraestructura. La infraestructura se abstrae y los recursos se agrupan, por lo que la TI funciona más como una utilidad que como una recopilación de complicados servicios y sistemas. Además, ahora la TI puede avanzar a roles más innovadores y estratégicos.

-   **Ahorro**: La informática en nube reduce el coste de proporcionar la TI y aumenta la utilización y la eficiencia del centro de datos. Los costes de entrega disminuyen debido a que, con la informática en nube, las aplicaciones y los recursos tienen características de autoservicio; además, el uso de esos recursos puede ahora medirse de nuevas y precisas maneras. La utilización de hardware también aumenta, dado que los recursos de infraestructura (almacenamiento, proceso y red) ahora están agrupados y son abstractos.

## Modelos de entrega para los servicios en la nube

En palabras simples, la informática en nube es la abstracción de los servicios de TI. Estos servicios pueden ir desde la infraestructura básica hasta aplicaciones completas. Los usuarios finales solicitan y consumen servicios abstractos sin la necesidad de administrar lo que constituye dichos servicios, o incluso de conocerlos totalmente. En la actualidad, el sector reconoce tres modelos de entrega de servicios en la nube, y cada uno de ellos brinda un equilibrio distinto entre control/flexibilidad y el coste total:

-   **Infraestructura como servicio** (IaaS): Una infraestructura virtual que hospeda máquinas virtuales y la mayoría de las aplicaciones existentes.

-   **Plataforma como servicio** (PaaS): Una infraestructura de aplicaciones en la nube que proporciona un entorno de hospedaje de aplicaciones a petición.

-   **Software como servicio** (SaaS): Un modelo de servicios en la nube en que una aplicación se entrega a través de Internet y donde los clientes pagan por uso (por ejemplo, Microsoft Office 365 o Microsoft CRM Online).

La ilustración 1 muestra la taxonomía de los servicios en la nube y cómo se asigna a los componentes en una infraestructura de TI. Con un modelo local, el cliente es responsable de administrar toda la pila, desde la conectividad de red hasta las aplicaciones. Con IaaS, un proveedor administra los niveles inferiores de la pila, mientras que el cliente es responsable de administrar el sistema operativo a través de las aplicaciones. Con PaaS, un proveedor de plataforma proporciona y administra todo, desde la conectividad de red hasta el tiempo de ejecución. El cliente solo debe administrar aplicaciones y datos. (La oferta de Azure se ajusta mejor en este modelo). Finalmente, con SaaS, un proveedor proporciona las aplicaciones y abstrae todos los servicios de todos los componentes subyacentes.

Ilustración 1: Taxonomía de los servicios en la nube

![azure-sharepoint-wp-1][]

## Máquinas virtuales de Azure

Las máquinas virtuales de Azure presentan una funcionalidad que permite un completo control y administración de máquinas virtuales, además de amplias redes virtuales. Esta oferta puede brindar a las organizaciones sólidos beneficios, como:

-   **Administración**: Administre las máquinas virtuales de manera centralizada en la nube con un control completo para configurar y mantener la infraestructura.

-   **Movilidad de las aplicaciones**: Mueva una y otra vez discos duros virtuales entre entornos locales y basados en la nube. No es necesario recompilar aplicaciones para su ejecución en la nube.

-   **Acceso a aplicaciones de Microsoft Server**: Ejecute las mismas aplicaciones locales e infraestructura en la nube, incluido Microsoft SQL Server, SharePoint Server, Windows Server y Active Directory.

Máquinas virtuales de Azure es una plataforma potente, simple, abierta y flexible que permite que las organizaciones implementen y ejecuten máquinas virtuales de Linux y Windows Server en minutos:

-   **Simple**: Con máquinas virtuales de Azure, es simple y sencillo crear, migrar, implementar y administrar máquinas virtuales en la nube. Las organizaciones pueden migrar cargas de trabajo a Azure sin tener que cambiar el código existente, o bien pueden configurar nuevas máquinas virtuales en Azure con solo unos pocos clics. La oferta también presta ayuda para desarrollar nuevas aplicaciones en la nube al integrar las funcionalidades de IaaS y PaaS de Azure.

-   **Abierta y flexible**: Azure es una plataforma abierta que brinda flexibilidad a las organizaciones. Estas pueden comenzar desde una imagen prediseñada de la biblioteca de imágenes, o bien pueden crear y utilizar discos duros virtuales locales y personalizados y cargarlos a la biblioteca de imágenes. También se encuentran disponibles las versiones comunitarias y comerciales de Linux.

-   **Potente**: Azure es una plataforma de nube preparada para el ámbito empresarial para ejecutar aplicaciones como SQL Server, SharePoint Server o Active Directory en la nube. Las organizaciones pueden crear soluciones locales y en la nube híbridas con conectividad de VPN entre el centro de datos de Azure y sus propias redes.

## SharePoint en máquinas virtuales de Azure

La flexibilidad de SharePoint 2010 es compatible con la mayoría de las cargas de trabajo en una implementación de máquinas virtuales de Azure. Las máquinas virtuales de Azure se ajustan perfectamente a escenarios de desarrollo y FIS (SharePoint Server para sitios de Internet). Del mismo modo, también son compatibles las cargas de trabajo centrales de SharePoint. Si una organización desea administrar y controlar su propia implementación de SharePoint 2010 mientras se capitalizan las opciones para la virtualización en la nube, la implementación de máquinas virtuales de Azure es la opción ideal.

La oferta de máquinas virtuales de Azure se hospeda y administra en la nube. Brinda flexibilidad en la implementación y disminuye los costes al reducir los gastos de capital generados por la adquisición de hardware. Con una mayor agilidad en la infraestructura, las organizaciones pueden implementar SharePoint Server en horas, en lugar de hacerlo en días o semanas. Las máquinas virtuales de Azure también permiten que las organizaciones implementen cargas de trabajo de SharePoint en la nube con un modelo de "pago por uso". A medida que crecen las cargas de trabajo de SharePoint, una organización puede expandir rápidamente la infraestructura; luego, cuando disminuyen las necesidades informáticas, puede devolver los recursos que ya no son necesarios, pagando así solo lo que se utiliza.

### Cambio en el foco de TI

Muchas organizaciones subcontratan los componentes comunes de su administración e infraestructura de TI, como hardware, sistemas operativos, seguridad, almacenamiento de datos y copia de seguridad, mientras mantienen el control de aplicaciones críticas, como SharePoint Server. Al delegar todas las capas de servicio no críticas de sus plataformas de TI en un proveedor virtual, las organizaciones pueden cambiar el foco de su TI hacia los servicios críticos centrales de SharePoint y brindar valor empresarial con proyectos de SharePoint, en lugar de dedicar más tiempo a la configuración de infraestructura.

### Implementación más rápida

Admitir e implementar una infraestructura de SharePoint de gran tamaño puede dificultar la capacidad de TI de avanzar rápidamente para responder a los requisitos del negocio. El tiempo que se requiere para crear, probar y preparar servidores y granjas de SharePoint e implementarlos en un entorno de producción puede demorar semanas o incluso meses, dependiendo de los procesos y restricciones de la organización. Las máquinas virtuales de Azure permiten que las organizaciones implementen rápidamente sus cargas de trabajo de SharePoint sin gastos de capital para hardware. De esta manera, las organizaciones pueden sacar provecho de la agilidad de la infraestructura para implementar en horas, en lugar de hacerlo en días o semanas.

### Escalabilidad

Sin la necesidad de implementar, probar y preparar granjas y servidores físicos de SharePoint, las organizaciones pueden expandir y contratar capacidad de proceso a petición inmediatamente. A medida que los requisitos de carga de trabajo de SharePoint crecen, una organización puede expandir rápidamente su infraestructura en la nube. Del mismo modo, cuando disminuyen las necesidades informáticas, la organización puede reducir los recursos, pagando solo lo que utiliza. Las máquinas virtuales de Azure disminuyen los gastos iniciales y los compromisos a largo plazo, lo que permite que las organizaciones creen y administren infraestructuras de SharePoint a escala. Esto significa, nuevamente, que pueden innovar, experimentar e iterar en horas, en lugar de hacerlo en días y semanas como ocurre con las implementaciones tradicionales.

### Uso medido

Las máquinas virtuales de Azure proporcionan potencia de proceso, memoria y almacenamiento para escenarios de SharePoint, con precios que normalmente se basan en el consumo de los recursos. Las organizaciones solo pagan por lo que utilizan y el servicio proporciona toda la capacidad necesaria para ejecutar la infraestructura de SharePoint. Para obtener más información sobre precios y facturación, vaya a [Información sobre precios de Azure][]. Observe que existen gastos nominales para almacenar y trasladar datos fuera de la nube de Azure desde una red local. Sin embargo, Azure no cobra por cargar datos.

### Flexibilidad

Las máquinas virtuales de Azure brindan a los desarrolladores la flexibilidad para elegir su entorno de tiempo de ejecución o lenguaje de preferencia, con compatibilidad oficial para .NET, Node.js, Java y PHP. Los desarrolladores también pueden elegir sus herramientas, con compatibilidad para Microsoft Visual Studio, WebMatrix, Eclipse y editores de texto. Además, Microsoft ofrece una ruta de acceso a bajo coste y con bajo riesgo a la nube y ofrece también aprovisionamiento e implementación simples y rentables para las necesidades de la nube, brindando acceso a inteligencia empresarial entre distintos dispositivos y ubicaciones. Finalmente, con la oferta de Azure, los usuarios no solo pueden trasladar discos duros virtuales a la nube, sino que también pueden volver a copiar un disco duro virtual y ejecutarlo localmente o a través de un proveedor de nube, siempre que tengan la licencia adecuada.

## Proceso de aprovisionamiento

Esta subsección analiza el principal punto fuerte de Azure. La **biblioteca de imágenes** en Azure proporciona la lista de máquinas virtuales preconfiguradas disponibles. Los usuarios pueden publicar discos duros virtuales o imágenes ISO de SharePoint Server, SQL Server, Windows Server y otros en la biblioteca de imágenes. Para simplificar la creación de máquinas virtuales, las imágenes base se crean y publican en la biblioteca. Los usuarios autorizados pueden utilizar estas imágenes para generar la máquina virtual deseada. Para obtener más información, vaya a [Creación de una máquina virtual que ejecuta Windows Server 2008 R2][] en el sitio de Azure. La ilustración 2 muestra los pasos básicos para crear una máquina virtual mediante el Portal de administración de Azure:

Ilustración 2: Información general de los pasos para crear una máquina virtual

![azure-sharepoint-wp-2][]

Los usuarios también pueden cargar una imagen preparada con SysPrep en el Portal de administración de Azure. Para obtener más información, vaya a [Creación y carga de un disco duro virtual][]. La ilustración 3 muestra los pasos básicos para cargar una imagen a fin de crear una máquina virtual:

Ilustración 3: Información general de los pasos para cargar una imagen

![azure-sharepoint-wp-3][]

### Implementación de SharePoint 2010 en Azure

Siga estos pasos para poder implementar SharePoint 2010 en Azure:

1.  Inicie sesión en el [Portal de administración de Azure][] con su cuenta.
    -   Si no tiene una cuenta de Azure, [regístrese para obtener una evaluación gratuita de Azure][].

2.  Cree una máquina virtual con sistema operativo base: En el Portal de administración de Azure, haga clic en **+NEW**, luego en **MÁQUINA VIRTUAL** y, a continuación, en **FROM GALLERY**. ![azure-sharepoint-wp-4](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-4.png)
3.  Aparecerá el cuadro de diálogo **VM OS Selection**. Haga clic en **Platform Images** y seleccione la imagen de plataforma **Windows Server 2008 R2 SP1**. ![azure-sharepoint-wp-5](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-5.png)
4.  Aparecerá el cuadro de diálogo ***VM Configuration***. Proporcione la siguiente información:
    -   Escriba un valor en **VIRTUAL MACHINE NAME**.
        -   El nombre de esta máquina debe ser único a nivel global.
    -   Deje el cuadro **NEW USER NAME** como Administrador.
    -   En el cuadro **NEW PASSWORD**, escriba una contraseña segura.
    -   En el cuadro **CONFIRM PASSWORD**, vuelva a escribir la contraseña.
    -   Seleccione el valor adecuado en **SIZE**.
        -   Para un entorno de producción (base de datos y servidor de aplicación de SharePoint), se recomienda usar Grande *(cuatro núcleos, 7 GB de memoria)*.

    ![azure-sharepoint-wp-6](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-6.png)
5.  Aparecerá el cuadro de diálogo ***VM Mode***. Proporcione la siguiente información:
    -   Seleccione Standalone Virtual Machine.
    -   En el cuadro **DNS NAME**, proporcione la primera parte del nombre de un DNS de su elección.
        -   Esta parte completará un nombre con el formato MyService1.cloudapp.net.
    -   En el cuadro **CUENTA DE ALMACENAMIENTO**, elija una de las siguientes opciones:
        -   Seleccione una cuenta de almacenamiento donde se almacena el archivo VHD.
        -   Elija que se cree automáticamente una cuenta de almacenamiento.
            -   Solo se crea una cuenta de almacenamiento por región de manera automática. Todas las demás máquinas virtuales creadas con esta configuración están ubicadas en esta cuenta de almacenamiento.
            -   Tiene un límite de 20 cuentas de almacenamiento.
            -   Para obtener más información, vaya a [Creación de una cuenta de almacenamiento en Azure][].
    -   En el cuadro **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, seleccione la región en la que se hospedará la imagen virtual.

    ![azure-sharepoint-wp-7](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-7.png)
6.  Aparecerá el cuadro de diálogo ***VM Options***. Proporcione la siguiente información:
    -   En el cuadro **CONJUNTO DE DISPONIBILIDAD**, seleccione (none).
    -   Lea y acepte los términos legales.
    -   Haga clic en la marca de verificación para crear la máquina virtual.

    ![azure-sharepoint-wp-8](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-8.png)
7.  Aparecerá la página ***VM Instances***. Compruebe que la máquina virtual se creó correctamente. ![azure-sharepoint-wp-9](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-9.png)
8.  Complete la configuración de la máquina virtual:
    -   Abra la máquina virtual utilizando el Escritorio remoto.
    -   En el Portal de administración de Azure, seleccione su máquina virtual y, a continuación, la página **PANEL**.
    -   Haga clic en **Connect**.

    ![azure-sharepoint-wp-10](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-10.png)
9.  Cree la máquina virtual de SQL Server con cualquiera de las siguientes opciones:
    -   Cree una máquina virtual de SQL Server 2012 siguiendo los pasos 1 a 7 anteriormente descritos, excepto en el **paso 3**, donde debe utilizar la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, vaya a [Aprovisionamiento de una máquina virtual de SQL Server en Azure][].
        -   Cuando elige esta opción, el proceso de aprovisionamiento conserva una copia de los archivos de configuración de SQL Server 2012 en la ruta de acceso al directorio *C:\\SQLServer\_11.0\_Full* para que pueda personalizar la instalación. Por ejemplo, puede convertir la instalación de evaluación de SQL Server 2012 en una versión con licencia si utiliza su clave de licencia.
    -   Utilice la herramienta de preparación del sistema SQL Server (SysPrep) para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7). Para obtener más información, vaya a [Instalar SQL Server 2012 mediante SysPrep][].
    -   Utilice el símbolo del sistema para instalar SQL Server. Para obtener más información, vaya a [Instalar SQL Server 2012 desde el símbolo del sistema][].
    -   Utilice medios compatibles de SQL Server y su clave de licencia para instalar SQL Server en la máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7).

10. Cree la granja de SharePoint con los siguientes pasos secundarios:
    -   Paso secundario 1: Configure la suscripción de Azure con archivos de script.
    -   Paso secundario 2: Aprovisione los servidores de SharePoint mediante la creación de otra máquina virtual con sistema operativo base (como se mostró anteriormente en los pasos 1 a 7). Para crear un servidor de SharePoint en esta máquina virtual, elija una de las siguientes opciones:
        -   Aprovisionamiento con GUI de SharePoint:
            -   Para crear y aprovisionar una granja de SharePoint, vaya a [Creación de una granja de servidores de Microsoft SharePoint][].
            -   Para agregar un servidor web o de aplicaciones a la granja, vaya a [Agregar un servidor web o de aplicaciones a la granja de servidores (SharePoint Server 2010)][].
            -   Para agregar un servidor de base de datos a una granja existente, vaya a [Agregar un servidor de base de datos a una granja existente][].

                -   Para utilizar SQL Server 2012 para su granja de SharePoint, debe descargar e instalar el Service Pack 1 para SharePoint Server 2010 después de instalar la aplicación y elegir no configurar el servidor. Para obtener más información, vaya a [Service Pack 1 para SharePoint Server 2010][].
                -   Para aprovechar las características de Business Intelligence SQL Server, se recomienda instalar SharePoint Server como granja de servidores en lugar de un servidor independiente. Para obtener más información, vaya a [Instalar las características de Business Intelligence SQL Server 2012][].

        -   Aprovisionamiento mediante Microsoft Windows PowerShell: Puede utilizar la herramienta de línea de comandos Psconfig como una interfaz alternativa para realizar varias operaciones que controlan la manera en que se aprovisionan los productos de SharePoint 2010. Para obtener más información, vaya a [Referencia de la línea de comandos de Psconfig][].
    -   Paso secundario 3: Configure SharePoint. Cuando cada máquina virtual de SharePoint tenga un estado Listo, configure SharePoint Server en cada servidor mediante una de las siguientes opciones:
        -   Configure SharePoint desde la GUI.
        -   Configure SharePoint mediante Windows PowerShell. Para obtener más información, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell][].
            -   También puede utilizar AutoSPInstaller del proyecto de CodePlex, que consta de scripts de Windows PowerShell, un archivo XML de entrada y un archivo por lotes de Microsoft Windows estándar. AutoSPInstaller brinda un marco para un script de instalación de SharePoint 2010 basado en Windows PowerShell. Para obtener más información, vaya a [CodePlex: AutoSPInstaller][]. **Nota**: asegúrese de configurar la seguridad en el extremo del Portal de administración y de definir un puerto de entrada en el firewall de Windows de la máquina virtual. Luego, confirme que puede iniciar una sesión remota de Windows PowerShell en uno de los servidores de aplicaciones de SharePoint al abrir una sesión de Windows PowerShell con credenciales de administrador.

11. Una vez que se complete el script, conéctese a la máquina virtual a través del panel de la máquina virtual.
12. Compruebe la configuración de SharePoint: Inicie sesión en el servidor de SharePoint y, a continuación, utilice la administración central para comprobar la configuración.

### Creación y carga de un disco duro virtual

También puede crear sus propias imágenes y cargarlas a Azure como un archivo VHD. Siga estos pasos para crear y cargar un archivo VHD en Azure:

1.  Cree la imagen compatible con Hyper-V: Utilice el Administrador de Hyper-V para crear el disco duro virtual compatible con Hyper-V. Para obtener más información, vaya a [Crear discos duros virtuales][].
2.  Cree una cuenta de almacenamiento en Azure: Se requiere una cuenta de almacenamiento en Azure para cargar un archivo VHD que se pueda utilizar para crear una máquina virtual. Esta cuenta se puede crear mediante el Portal de administración de Azure. Para obtener más información, vaya a [Creación de una cuenta de almacenamiento en Azure][Creación y carga de un disco duro virtual].
3.  Prepare la imagen que se va a cargar: Antes de cargar la imagen en Azure, es necesario generalizarla mediante el comando SysPrep. Para obtener más información, consulte [How to Use SysPrep: An Introduction][].
4.  Cargue la imagen en Azure: Para cargar una imagen contenida en un archivo VHD, debe crear e instalar un certificado de administración. Obtenga la huella digital del certificado y el identificador de suscripción. Establezca la conexión y cargue el archivo VHD con la herramienta de línea de comandos CSUpload. Para obtener más información, vaya a [Upload the Image to Azure][Creación y carga de un disco duro virtual].

## Escenarios de uso

En esta sección se analizan algunos importantes escenarios de cliente para implementaciones de SharePoint mediante máquinas virtuales de Azure. Cada escenario se divide en dos partes: una breve descripción del escenario y algunos pasos para comenzar.

### Escenario 1: Entorno simple de desarrollo y prueba de SharePoint

#### Descripción

Las organizaciones buscan maneras más ágiles de crear aplicaciones de SharePoint y configurar entornos de SharePoint para prueba y desarrollo internos/externos. Fundamentalmente, las organizaciones desean disminuir el tiempo que se requiere para configurar los proyectos de desarrollo de aplicaciones de SharePoint y reducir el coste a través del aumento del uso de sus entornos de prueba. Por ejemplo, es posible que una organización desee realizar una prueba de carga a petición en SharePoint Server y ejecutar una prueba de aceptación del usuario (UAT) con más usuarios simultáneos en distintas ubicaciones geográficas. De manera similar, integrar equipos internos/externos es una necesidad empresarial cada vez más importante para muchas de las organizaciones actuales.

Este escenario explica cómo las organizaciones pueden utilizar granjas de SharePoint preconfiguradas para cargas de trabajo de desarrollo y prueba. Una topología de implementación de SharePoint tiene el mismo aspecto que tendría en una implementación virtualizada local. Las aptitudes de TI existentes se traducen 1:1 en una implementación de máquinas virtuales de Azure, donde el principal beneficio es un cambio casi completo en los costes desde los gastos de capital a los gastos operativos, sin que sea necesaria una compra de servidores físicos por adelantado. Las organizaciones pueden eliminar el coste de capital referente a hardware de servidor y lograr una flexibilidad al disminuir considerablemente el tiempo de aprovisionamiento que se requiere para crear, configurar o ampliar una granja de SharePoint para un entorno de prueba y desarrollo. La TI puede agregar y quitar de manera dinámica capacidad para enfrentar las cambiantes necesidades de prueba y desarrollo. Además, la TI también puede centrarse más en entregar valor para el negocio con proyectos de SharePoint y menos en la administración de la infraestructura.

Para utilizar completamente las máquinas para prueba de carga, las organizaciones pueden configurar máquinas de desarrollo y prueba virtualizadas de SharePoint en Azure con compatibilidad de sistema operativo para Windows Server 2008 R2. Este permite a los equipos de desarrollo crear y probar aplicaciones y migrar con facilidad a entornos de producción locales o en la nube sin cambios en el código. Los mismos marcos y conjuntos de herramientas se pueden utilizar localmente y en la nube, lo que permite que el equipo distribuido tenga acceso al mismo entorno. Los usuarios también pueden tener acceso a datos y aplicaciones locales si se establece una conexión VPN directa.

#### Tareas iniciales

La ilustración 4 muestra un entorno de desarrollo y prueba de SharePoint en una máquina virtual de Azure. Para crear esta implementación, comience usando el mismo entorno de desarrollo y prueba local de SharePoint que se utiliza para desarrollar aplicaciones. Luego, cargue e implemente las aplicaciones a la máquina virtual de Azure para prueba y desarrollo. Si la organización decide volver a trasladar la aplicación al entorno local, puede hacerlo sin tener que modificarla.

Ilustración 4: Entorno de desarrollo y prueba de SharePoint en máquinas virtuales de Azure

![azure-sharepoint-wp-11][]

#### Configuración del entorno de escenario

Siga estos pasos para implementar un entorno de desarrollo y prueba de SharePoint en Azure:

1.  *Aprovisionamiento*: Primero, aprovisione una conexión VPN entre la instalación local y Azure a través de Red virtual de Azure. (Como aquí no se utiliza Active Directory, se necesita un túnel VPN). Para obtener más información, vaya a [Red virtual de Azure (Consideraciones sobre el diseño y escenarios de conexión seguros)][]. Luego, utilice el Portal de administración para aprovisionar una máquina virtual nueva a través de una imagen de archivo desde la biblioteca de imágenes.
    -   Puede cargar las máquinas virtuales de desarrollo y prueba locales de SharePoint en su cuenta de almacenamiento de Azure y hacer referencia a esas máquinas virtuales a través de la biblioteca de imágenes para crear el entorno requerido.
    -   Puede utilizar la imagen de SQL Server 2012 en lugar de la imagen de Windows Server 2008 R2 SP1. Para obtener más información, vaya a [Aprovisionamiento de una máquina virtual de SQL Server en Azure][].

2.  *Instalación*: Instale SharePoint Server, Visual Studio y SQL Server en las máquinas virtuales a través de una conexión de Escritorio remoto.
    -   Elija una opción para instalar SharePoint Server:
        -   Use el script de configuración simple de SharePoint 2010 para crear una máquina para el desarrollador de SharePoint. Para obtener más información, vaya a [SharePoint 2010 Easy Setup Script][].
        -   Utilice Windows PowerShell. Para obtener más información, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell][].
        -   Utilice AutoSPInstaller del proyecto de CodePlex. Para obtener más información, vaya a [CodePlex: AutoSPInstaller][].
    -   Instale Visual Studio. Para obtener más información, vaya a [Instalar Visual Studio][].
    -   Instale SQL Server. Para obtener más información, vaya a [Instalar SQL Server mediante SysPrep][Instalar SQL Server 2012 mediante SysPrep].
        -   Consulte el laboratorio práctico para crear y configurar SQL Server 2012 para una implementación de granja de SharePoint: [Configuring SQL Server 2012 for SharePoint in Azure][].
        -   Consulte el laboratorio práctico para crear una granja de SharePoint a través de la configuración de Active Directory y el uso de una sola base de datos de SQL Server: [Deploying a SharePoint Farm with Azure Virtual Machines][].

3.  *Desarrollo de paquetes y scripts de implementación para aplicaciones y bases de datos*: Si tiene previsto utilizar una máquina virtual disponible desde la biblioteca de imágenes, es posible implementar las aplicaciones y bases de datos locales en máquinas virtuales de Azure:
    -   Cree paquetes de implementación para las aplicaciones y bases de datos locales existentes mediante SQL Server Data Tools y Visual Studio.
    -   Utilice estos paquetes para implementar las aplicaciones y bases de datos en máquinas virtuales de Azure.

4.  *Implementación de aplicaciones y bases de datos de SharePoint*:
    -   Configure la seguridad en el extremo del Portal de administración y defina un puerto de entrada en el firewall de Windows de la máquina virtual.
    -   Implemente aplicaciones y bases de datos de SharePoint en máquinas virtuales de Azure a través de los paquetes y scripts de implementación que se crearon en el paso 3.
    -   Pruebe las aplicaciones y bases de datos implementadas.

5.  *Administración de máquinas virtuales*:
    -   Supervise las máquinas virtuales a través del Portal de administración.
    -   Supervise las aplicaciones mediante Visual Studio y SQL Server Management Studio.
    -   También puede supervisar y administrar las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.

### Escenario 2: Granja de SharePoint de acceso público con personalización

#### Descripción

Las organizaciones desean establecer una presencia en Internet que esté hospedada en la nube y que sea fácilmente escalable según la necesidad y la demanda. También desean crear sitios web de extranet de asociados para colaboración e implementar un proceso simple para la creación y aprobación distribuida del contenido de los sitios web. Finalmente, para controlar el incremento de las cargas, estas organizaciones desean proporcionar capacidad a petición a sus sitios web.

En este escenario, SharePoint Server se utiliza como la base para hospedar un sitio web de acceso público. Permite que las organizaciones implementen, personalicen y hospeden rápidamente sus sitios web empresariales en una infraestructura en la nube segura y escalable. Con sitios web de acceso público de SharePoint en Azure, las organizaciones pueden escalar a medida que crece el tráfico y solo pagar por lo que utilizan. Se pueden utilizar herramientas comunes, similares a las que se utilizan de manera local, para la creación de contenido, el flujo de trabajo y la aprobación con SharePoint en Azure.

Además, a través de máquinas virtuales de Azure, las organizaciones pueden configurar fácilmente entornos de ensayo y producción que se ejecuten en máquinas virtuales. Es posible crear copias de seguridad de las máquinas virtuales de SharePoint de acceso público creadas en Azure en el almacenamiento virtual. Además, para fines de recuperación ante desastres, la característica de replicación geográfica continua permite que las organizaciones creen automáticamente copias de seguridad de máquinas virtuales que operan en un centro de datos en otro centro de datos a kilómetros de distancia. (Para obtener más información sobre la replicación geográfica, vaya a [Introducing Geo-replication for Azure Storage][]).

Las máquinas en la infraestructura de Azure están validadas y son compatibles para trabajar con otros productos de Microsoft, como SQL Server y SharePoint Server. Azure y SharePoint Server funcionan mejor en conjunto: Ambos forman parte de la familia de Microsoft y están perfectamente integrados, son compatibles y está probado que, en conjunto, brindan una experiencia óptima. Ambos tienen un único punto de respaldo para la aplicación de SharePoint y la infraestructura de Azure.

#### Tareas iniciales

En este escenario, se deben agregar más servidores web front-end para SharePoint Server a fin de admitir el tráfico adicional. Estos servidores requieren mayor seguridad y los controladores de dominio de los Servicios de dominio de Active Directory para admitir la autorización y la autenticación del usuario. La ilustración 5 muestra el diseño de este escenario.

Ilustración 5: Granja de SharePoint de acceso público con personalización

![azure-sharepoint-wp-12][]

#### Configuración del entorno de escenario

Siga estos pasos para implementar una granja de SharePoint de acceso público en Azure:

1.  *Implementación de Active Directory*: Los requisitos fundamentales para implementar Active Directory en máquinas virtuales de Azure son similares, pero no idénticos, a los de implementar en máquinas virtuales (y, en cierta medida, en máquinas físicas) locales. Para obtener más información acerca de las diferencias, además de directrices y otras consideraciones, vaya a [Directrices para implementar Active Directory en máquinas virtuales de Azure][]. Para implementar Active Directory en Azure:
    -   Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas. Para obtener más información, vaya a [Configure Virtual Networking][].
    -   Utilice el Portal de administración para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure. Para obtener más información, vaya a [Deploying and Creating the Domain Controller][Configure Virtual Networking].
        -   Puede hacer referencia al script de Windows PowerShell para implementar un dominio independiente en la nube mediante Máquinas virtuales de Azure y Red virtual de Azure. Para obtener más información, vaya a [Deploying Active Directory in Azure (Windows PowerShell)][].
        -   Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a [Instalación de un nuevo bosque de Active Directory en Azure][].

2.  *Aprovisionamiento de una máquina virtual*: Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.
3.  *Implementación de una granja de SharePoint*:
    -   Utilice la máquina virtual recientemente aprovisionada para instalar SharePoint y generar una imagen reutilizable. Para obtener más información acerca de la instalación de SharePoint Server, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell][] o [CodePlex: AutoSPInstaller][].
    -   Configure la máquina virtual de SharePoint para crear y conectarse con la granja de SharePoint.
    -   Utilice el Portal de administración para configurar el equilibrio de carga.
        -   Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.
        -   Agregue otra máquina virtual web front-end a la granja existente de SharePoint para el tráfico adicional.

4.  *Administración de máquinas virtuales*:
    -   Supervise las máquinas virtuales a través del Portal de administración.
    -   Supervise la granja de SharePoint mediante la administración central.

### Escenario 3: Granja con escalamiento horizontal para servicios adicionales de Business Intelligence

#### Descripción

Business Intelligence es fundamental para obtener información clave y tomar decisiones sólidas y rápidas. Cuando las organizaciones hacen la transición desde un enfoque local, no desean realizar cambios en el entorno de BI mientras implementan aplicaciones de BI existentes en la nube. Desean hospedar informes desde SQL Server Analysis Services (SSAS) o SQL Server Reporting Services (SSRS) en un entorno altamente durable y disponible, mientras mantiene el control completo de la aplicación de Business Intelligence, todo sin dedicar mucho tiempo y presupuesto al mantenimiento.

Este escenario describe la manera en que las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones críticas de Business Intelligence. Las organizaciones pueden implementar granjas de SharePoint en máquinas virtuales de Azure y escalar horizontalmente los componentes de BI de la máquina virtual del servidor de aplicaciones, como SSRS o Excel Services. A través del escalado de componentes que requieren muchos recursos en la nube, pueden admitir mejor y con más facilidad cargas de trabajo especializadas. Observe que SQL Server en máquinas virtuales de Azure funciona bien, puesto que es fácil escalar las instancias de SQL Server, que van desde instalaciones pequeñas a muy grandes. Esto proporciona una elasticidad que permite que las organizaciones aprovisionen (amplíen) o desaprovisionen (reduzcan) de manera dinámica instancias de Business Intelligence según los requisitos inmediatos de carga de trabajo.

La migración de aplicaciones existentes de BI a Azure proporciona un mejor escalado. Con la potencia de SSAS, SSRS y SharePoint Server, las organizaciones pueden crear poderosas aplicaciones de BI e informes y paneles que aumentan o disminuyen. Estas aplicaciones y paneles también se pueden integrar de manera más segura con datos y aplicaciones locales. Azure garantiza que el centro de datos cumple con la norma ISO 27001. Para obtener más información, vaya al [Centro de confianza de Azure][].

#### Tareas iniciales

Para escalar horizontalmente la implementación de componentes de BI, se debe instalar un nuevo servidor de aplicaciones con servicios como PowerPivot, Power View, Excel Services o PerformancePoint Services. O bien, se deben agregar instancias de BI de SQL Server como SSAS o SSRS a la granja existente para admitir un procesamiento adicional de consultas. El servidor se puede agregar como una máquina virtual nueva de Azure con SharePoint 2010 Server o SQL Server instalado. Luego se pueden instalar, implementar y configurar en ese servidor los componentes de BI (ilustración 6).

Ilustración 6: Granja de SharePoint con escalamiento horizontal para servicios adicionales de BI

![azure-sharepoint-wp-13][]

#### Configuración del entorno de escenario

Siga estos pasos para escalar horizontalmente un entorno de BI en Azure:

1.  *Aprovisionamiento*:
    -   Aprovisione una conexión VPN entre la instalación local y Azure a través de Red virtual de Azure. Para obtener más información, vaya a [Red virtual de Azure (Consideraciones sobre el diseño y escenarios de conexión seguros)][].
    -   Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.
        -   Puede cargar imágenes de carga de trabajo de BI de SharePoint Server o SQL Server en la biblioteca de imágenes y cualquier usuario autorizado puede elegir esas máquinas virtuales de componentes de BI para crear el entorno de escalamiento horizontal.

2.  *Instalación*: Si su organización no tiene imágenes prediseñadas de componentes de BI de SharePoint Server o SQL Server, instale SharePoint Server y SQL Server en las máquinas virtuales a través de la conexión de Escritorio remoto.
    -   Para obtener más información acerca de la instalación de SharePoint, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell][] o [CodePlex: AutoSPInstaller][].
    -   Para obtener más información acerca de la instalación de SQL Server, vaya a [Instalar SQL Server mediante SysPrep][Instalar SQL Server 2012 mediante SysPrep].
    -   Consulte el laboratorio práctico para crear y configurar SQL Server 2012 para una implementación de granja de SharePoint: [Configuring SQL Server 2012 for SharePoint in Azure][].
    -   Consulte el laboratorio práctico para crear una granja de SharePoint a través de la configuración de Active Directory y el uso de una sola base de datos de SQL Server: [Deploying a SharePoint Farm with Azure Virtual Machines][].

3.  *Incorporación de la máquina virtual de BI*:
    -   Configure la seguridad en el extremo del Portal de administración y defina un puerto de entrada en el firewall de Windows de la máquina virtual.
    -   Agregue la máquina virtual de BI recientemente creada a la granja existente de SharePoint o SQL Server.

4.  *Administración de máquinas virtuales*:
    -   Supervise las máquinas virtuales a través del Portal de administración.
    -   Supervise la granja de SharePoint mediante la administración central.
    -   Supervise y administre las máquinas virtuales mediante software de administración local, como Microsoft System Center - Operations Manager.

### Escenario 4: Sitio web basado en SharePoint completamente personalizado

#### Descripción

Cada vez más, las organizaciones desean crear en la nube sitios web de SharePoint completamente personalizados. Necesitan un entorno altamente duradero y disponible que ofrezca control completo para mantener las aplicaciones complejas en ejecución en la nube, pero no desean dedicar una gran cantidad de tiempo ni de presupuesto.

En este escenario, una organización puede implementar toda su granja de SharePoint en la nube y escalar dinámicamente todos los componentes para obtener capacidad adicional, o bien puede extender su implementación local a la nube para aumentar la capacidad y mejorar el rendimiento cada vez que sea necesario. El escenario se centra en organizaciones que desean obtener la "experiencia de SharePoint" completa para el desarrollo de aplicaciones y la administración de contenido empresarial. Los sitios más complejos también pueden incluir una mayor generación de informes, Power View, PerformancePoint, PowerPivot, gráficas más detalladas y la mayoría de las demás funcionalidades de sitios de SharePoint para obtener una funcionalidad completa de un extremo a otro.

Las organizaciones pueden utilizar máquinas virtuales de Azure para hospedar aplicaciones personalizadas y componentes asociados en una infraestructura de nube rentable y altamente segura. También pueden utilizar Microsoft System Center local como una herramienta de administración común para aplicaciones locales y en la nube.

#### Tareas iniciales

Para implementar un sitio web completamente personalizado de SharePoint en Azure, una organización debe implementar un dominio de Active Directory en la nube y aprovisionar nuevas máquinas virtuales en este dominio. Luego, se debe crear y configurar una máquina virtual que ejecute SQL Server 2012 como parte de una granja de SharePoint. Finalmente, es necesario crear una granja de SharePoint, equilibrar su carga y conectarla a Active Directory y SQL Server (ilustración 7).

Ilustración 7: Sitio web basado en SharePoint completamente personalizado

![azure-sharepoint-wp-14][]

#### Configuración del entorno de escenario

Los siguientes pasos muestran cómo crear un entorno de granjas de SharePoint personalizado a partir de imágenes prediseñadas disponibles en la biblioteca de imágenes. Sin embargo, tenga en cuenta que puede cargar máquinas virtuales de la granja de SharePoint en la biblioteca de imágenes, y los usuarios autorizados pueden elegir esas máquinas virtuales para crear la granja de SharePoint requerida en Azure.

1.  Implementación de Active Directory*: Los requisitos fundamentales para implementar Active Directory en máquinas virtuales de Azure son similares, pero no idénticos, a los de implementar en máquinas virtuales (y, en cierta medida, en máquinas físicas) locales. Para obtener más información acerca de las diferencias, además de directrices y otras consideraciones, vaya a [Directrices para implementar Active Directory en máquinas virtuales de Azure][]. Para implementar Active Directory en Azure:*
    -   Defina y cree una red virtual donde las máquinas virtuales se puedan asignar a subredes específicas. Para obtener más información, vaya a [Configure Virtual Networking][].
    -   Utilice el Portal de administración para crear e implementar el controlador de dominio en una máquina virtual nueva en Azure. Para obtener más información, vaya a [Deploying and Creating the Domain Controller][Configure Virtual Networking].
        -   Puede hacer referencia al script de Windows PowerShell para implementar un dominio independiente en la nube mediante Máquinas virtuales de Azure y Red virtual de Azure. Para obtener más información, vaya a [Deploying Active Directory in Azure (Windows PowerShell)][].
        -   Para obtener más información acerca de la creación de un nuevo bosque de Active Directory en una máquina virtual en Red virtual de Azure, vaya a [Instalación de un nuevo bosque de Active Directory en Azure][].

2.  *Implementación de SQL Server*:
    -   Utilice el Portal de administración para aprovisionar una máquina virtual nueva desde una imagen de archivo en la biblioteca de imágenes.
    -   Configure SQL Server en la máquina virtual. Para obtener más información, vaya a [Instalar SQL Server mediante SysPrep][Instalar SQL Server 2012 mediante SysPrep].
    -   Una la máquina virtual al dominio de Active Directory recién creado.

3.  *Implementación de una granja multiservidor de SharePoint*:
    -   Cree una red virtual. Para obtener más información, vaya a [Red virtual de Azure (Consideraciones sobre el diseño y escenarios de conexión seguros)][].
        -   Cuando implementa las máquinas virtuales de SharePoint, necesita subredes para SharePoint Server para que las direcciones de DNS en el cuadro de Active Directory local estén disponibles durante el aprovisionamiento.
    -   Utilice el Portal de administración para crear una máquina virtual.
    -   Instale SharePoint Server en esta máquina virtual y genere una imagen reutilizable. Para obtener más información acerca de la instalación de SharePoint Server, vaya a [Instalación de SharePoint Server 2010 mediante Windows PowerShell][] o [CodePlex: AutoSPInstaller][].
    -   Configure la máquina virtual de SharePoint para crear y conectarse con la granja de SharePoint a través del comando [Join-SharePointFarm][].
    -   Utilice el Portal de administración para configurar el equilibrio de carga:
        -   Configure los extremos de la máquina virtual, seleccione la opción de equilibrar la carga del tráfico en un extremo existente y, a continuación, especifique el nombre de la máquina virtual con equilibrio de carga.
            -   Para obtener más información acerca de la implementación de granjas de SharePoint en máquinas virtuales de Azure, vea este [vídeo de TechEd North America 2012][].

4.  *Administración de la granja de SharePoint mediante System Center*:
    -   Utilice el agente de Operations Manager y el nuevo paquete de integración de Azure para conectar el System Center local con Máquinas virtuales de Azure.
    -   Utilice App Controller y Orchestrator locales para las funciones de administración.

## Conclusión

La informática en nube está transformando la manera en que la TI sirve a las organizaciones. El motivo es que la informática en nube puede aprovechar una nueva clase de beneficios, incluida la considerable disminución del coste, además de un mayor foco, agilidad y flexibilidad de la TI. Azure lidera la forma en que la informática en nube brinda una infraestructura virtual simple, abierta, flexible y poderosa. La máquinas virtuales de Azure disminuyen la necesidad de hardware, por lo que las organizaciones pueden reducir el coste y la complejidad al crear infraestructura a escala, con un control completo y una administración optimizada.

Las máquinas virtuales de Azure proporcionan una continuación completa de las implementaciones de SharePoint. Es un servicio completamente respaldado y probado para proporcionar una experiencia óptima junto con otras aplicaciones de Microsoft. De este modo, las organizaciones pueden configurar e implementar fácilmente SharePoint Server dentro de Azure, ya sea para aprovisionar infraestructura para una nueva implementación de SharePoint o para expandir una existente. A medida que las cargas de trabajo de negocios crecen, las organizaciones pueden expandir rápidamente su infraestructura de SharePoint. Del mismo modo, si las necesidades de carga de trabajo disminuyen, las organizaciones pueden contratar recursos a petición, para así pagar solo lo que utilizan. Las máquinas virtuales de Azure brindan una infraestructura excepcional para una amplia variedad de requisitos del negocio, tal como se describe en los cuatro escenarios basados en SharePoint analizados en este documento.

La correcta implementación de SharePoint Server en Máquinas virtuales de Azure requiere una planificación sólida, especialmente teniendo en cuenta la variedad de opciones de implementación y arquitectura de granja críticas. Los enfoques y procedimientos recomendados que detalla este documento pueden ayudar a guiar las decisiones para llevar a cabo una implementación informada de SharePoint.

## Recursos adicionales

-   Kit de formación para Azure: Laboratorio práctico y presentación

    <http://windowsazure-trainingkit.github.com/labs.htm>

-   Introducción a Azure PowerShell

    <http://msdn.microsoft.com/es-es/library/windowsazure/jj156055>

-   Azure Management Cmdlets

    <http://msdn.microsoft.com/es-es/library/windowsazure/jj152841>

-   Herramientas de línea de comandos y cmdlets de PowerShell para distintos sistemas operativos

    [https://www.windowsazure.com/es-es/manage/downloads/][]

-   Guías de procedimientos y documentación sobre procedimientos recomendados

    [https://www.windowsazure.com/es-es/manage/windows/][]

  [azure-sharepoint-wp-1]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png
  [Información sobre precios de Azure]: /es-es/pricing/details/
  [Creación de una máquina virtual que ejecuta Windows Server 2008 R2]: /es-es/manage/windows/tutorials/virtual-machine-from-gallery/
  [azure-sharepoint-wp-2]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png
  [Creación y carga de un disco duro virtual]: /es-es/manage/windows/common-tasks/upload-a-vhd/
  [azure-sharepoint-wp-3]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [regístrese para obtener una evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/
  [Creación de una cuenta de almacenamiento en Azure]: /es-es/manage/windows/common-tasks/upload-a-vhd/#createstorage
  [Aprovisionamiento de una máquina virtual de SQL Server en Azure]: /es-es/manage/windows/common-tasks/install-sql-server/
  [Instalar SQL Server 2012 mediante SysPrep]: http://msdn.microsoft.com/es-es/library/ee210664.aspx
  [Instalar SQL Server 2012 desde el símbolo del sistema]: http://msdn.microsoft.com/es-es/library/ms144259.aspx#SysPrep
  [Creación de una granja de servidores de Microsoft SharePoint]: http://technet.microsoft.com/es-es/library/ee805948.aspx#CreateConfigure
  [Agregar un servidor web o de aplicaciones a la granja de servidores (SharePoint Server 2010)]: http://technet.microsoft.com/es-es/library/cc261752.aspx
  [Agregar un servidor de base de datos a una granja existente]: http://technet.microsoft.com/es-es/library/cc262781
  [Service Pack 1 para SharePoint Server 2010]: http://www.microsoft.com/es-es/download/details.aspx?id=26623
  [Instalar las características de Business Intelligence SQL Server 2012]: http://technet.microsoft.com/es-es/library/hh231681(v=sql.110).aspx
  [Referencia de la línea de comandos de Psconfig]: http://technet.microsoft.com/es-es/library/cc263093.aspx
  [Instalación de SharePoint Server 2010 mediante Windows PowerShell]: http://technet.microsoft.com/es-es/library/cc262839.aspx
  [CodePlex: AutoSPInstaller]: http://autospinstaller.codeplex.com/
  [Crear discos duros virtuales]: http://technet.microsoft.com/es-es/library/cc742509
  [How to Use SysPrep: An Introduction]: http://technet.microsoft.com/es-es/library/bb457073.aspx
  [azure-sharepoint-wp-11]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png
  [Red virtual de Azure (Consideraciones sobre el diseño y escenarios de conexión seguros)]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156007.aspx
  [SharePoint 2010 Easy Setup Script]: http://www.microsoft.com/es-es/download/details.aspx?id=23415
  [Instalar Visual Studio]: http://msdn.microsoft.com/es-es/library/e2h7fzkw.aspx
  [Configuring SQL Server 2012 for SharePoint in Azure]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint
  [Deploying a SharePoint Farm with Azure Virtual Machines]: https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs
  [Introducing Geo-replication for Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [azure-sharepoint-wp-12]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png
  [Directrices para implementar Active Directory en máquinas virtuales de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156090
  [Configure Virtual Networking]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md
  [Deploying Active Directory in Azure (Windows PowerShell)]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS
  [Instalación de un nuevo bosque de Active Directory en Azure]: /es-es/manage/services/networking/active-directory-forest/
  [Centro de confianza de Azure]: /es-es/support/trust-center/compliance/
  [azure-sharepoint-wp-13]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png
  [azure-sharepoint-wp-14]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png
  [Join-SharePointFarm]: http://technet.microsoft.com/es-es/library/ff607979.aspx
  [vídeo de TechEd North America 2012]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327
  [https://www.windowsazure.com/es-es/manage/downloads/]: /es-es/manage/downloads/
  [https://www.windowsazure.com/es-es/manage/windows/]: /es-es/manage/windows/
