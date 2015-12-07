<properties 
	pageTitle="Aprovisionamiento de Microsoft Data Science Virtual Machine | Microsoft Azure" 
	description="Configuro y creación de Data Science Virtual Machine en Azure para realizar análisis y aprendizaje de equipo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="bradsev" />


# Aprovisionamiento de Microsoft Data Science Virtual Machine

## Introducción

Microsoft Data Science Virtual Machine es una imagen de máquina virtual (VM) de Azure preinstalada y configurada con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. Las herramientas incluidas son:

- Revolution R Open
- Anaconda Python Distribution
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- SDK de Azure


La ciencia de datos implica una iteración en una secuencia de tareas: encontrar, cargar y preprocesar datos, crear y probar modelos e implementar los modelos para su uso en aplicaciones inteligentes. No es raro que los científicos de datos usen diversas herramientas para realizar estas tareas. Puede ser bastante lento encontrar las versiones del software adecuadas, descargarlas e instalarlas. Microsoft Data Science Virtual Machine puede facilitar esta carga.

El salto de Microsoft Data Science Virtual Machine inicia el proyecto de análisis. Le permite trabajar en tareas en una variedad de lenguajes, incluidos R, Python, SQL y C#. Visual Studio proporciona un IDE para desarrollar y probar el código que es fácil de usar. El SDK de Azure incluido en la máquina virtual permite crear aplicaciones con varios servicios en la plataforma en la nube de Microsoft.

No hay ningún cargo de software para esta imagen de VM de ciencia de datos. Solo paga por las cuotas de uso de Azure, que dependen del tamaño de la máquina virtual que aprovisionará con esta imagen de VM. Puede encontrar más detalles sobre las cuotas de proceso [aquí](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Requisitos previos

Antes de poder crear una Microsoft Data Science Virtual Machine, debe tener lo siguiente:

- **Una suscripción a Azure**: para conseguir una, vea [Obtención de una evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Una cuenta de almacenamiento de Azure**: para crear una, consulte [Creación de una cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account). También puede crear una cuenta de almacenamiento como parte del proceso de creación de la VM si no desea usar una cuenta existente.


## Creación de su Microsoft Data Science Virtual Machine

Estos son los pasos para crear una instancia de Microsoft Data Science Virtual Machine:

1.	Navegue a la lista de máquinas virtuales en el [portal de Azure](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Haga clic en el botón **Crear** ubicado en la parte inferior para acceder a un asistente.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Las secciones siguientes proporcionan las **entradas** para cada uno de los **5 pasos** (enumerados a la derecha de la ilustración anterior) en el asistente que se usó para crear la Microsoft Data Science Virtual Machine. Estas son las entradas necesarias para configurar cada uno de estos pasos:

  **a. Básico**:

    - **Name**: Name of your data science server you are creating.
    - **User Name**: Admin account login id
    - **Password**: Admin account password
    - **Subscription**: If you have more than one subscription, select the one on which the machine will be created and billed
    - **Resource Group**: You can create a new one or use an existing group
    - **Location**: Select the data center that is most appropriate. Usually it is the data center that has most of your data or is closest to your physical location for fastest network access

  **b. Tamaño**:

    - Select one of the server types that meets your functional requirement and cost constraints. You can get more choices of VM sizes by selecting “View All”

  **c. Configuración**

    - **Disk Type**: Choose Premium if you prefer a solid state drive (SSD), else choose “Standard”.
    - **Storage Account**: You can create a new Azure storage account in your subscription or use an existing one in the same *Location* that was chosen on the Basics step of the wizard.
    - **Other parameters**: In most cases you will just use the default values. You can hover over the informational link for help on the specific fields in case you want to consider the use of non-default values.

  **d. Resumen**:

    - Verify that all information you entered is correct.

  **e. Comprar**:

    - Click on **Buy** to start the provisioning. A link is provided to the terms of the transaction. The VM does not have any additional charges beyond the compute for the server size you chose in the **Size** step. 


El aprovisionamiento tardará entre 10 y 20 minutos. El estado del aprovisionamiento se muestra en el Portal de Azure.

## Acceso a Microsoft Data Science Virtual Machine

Una vez creada la máquina virtual, puede iniciar sesión en ella mediante el escritorio remoto con las credenciales de la cuenta del administrador que creó en la sección Básico del paso 4.

Una vez creada y aprovisionada la máquina virtual, está listo para comenzar a usar las herramientas que se instalan y configuran en ella. Hay iconos del escritorio e iconos del menú inicio para muchas de las herramientas.

## Herramientas instaladas en Microsoft Data Science Virtual Machine

### R
Si desea usar R para su análisis, la máquina virtual tiene Revolution R Open (RRO) instalado. Se trata de una distribución de código abierto de R totalmente compatible con CRAN-R. Contiene el motor de código abierto de R más reciente, junto con la biblioteca Intel Math Kernel Library. Un IDE llamado “RRO RGui” también está empaquetado en la máquina virtual. Puede descargar y usar otros IDE, así como [RStudio](http://www.rstudio.com).

### Python
Para el desarrollo con Python, se ha instalado Anaconda Python Distribution 2.7. Esta distribución contiene Python base, junto con aproximadamente 300 de los paquetes de matemáticas, ingeniería y análisis de datos más populares. Puede usar Herramientas de Python para Visual Studio (PTVS) que se instala en la edición de Visual Studio 2015 Community o uno de los IDE incluidos con Anaconda, como IDLE o Spyder. Para iniciar uno de ellos, busque en la barra de búsqueda (tecla **Win** + **S**).

### Bloc de notas de IPython
La distribución de Anaconda también incluye un IPython Notebook, un entorno para compartir código y análisis. Se ha preconfigurado un servidor de IPython Notebook. Hay un icono del escritorio para iniciar el explorador para tener acceso al servidor del notebook. Si está en la VM a través de un escritorio remoto, también puede visitar [https://localhost:9999/](https://localhost:9999/) para tener acceso al servidor de IPython Notebook (nota: si se recibe alguna advertencia de certificado, simplemente continúe).

### Visual Studio 2015 Community Edition
Visual Studio Community Edition instalado en la máquina virtual. Es una versión gratuita del popular IDE de Microsoft que puede usar para fines de evaluación y para equipos muy pequeños. Puede revisar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547). Haga doble clic en el icono del escritorio o en el menú **Inicio** para abrir Visual Studio. También puede buscar programas con **Win** + **S** y escribiendo "Visual Studio".

Nota: puede obtener un mensaje que indica que el período de evaluación ha caducado. Puede escribir las credenciales de la cuenta de Microsoft o crear una cuenta y volver a escribirlas para obtener acceso a Visual Studio Community Edition. Una ahí, puede crear proyectos en lenguajes como C#, Python

### SQL Server Express
También se ha empaquetado una versión limitada de SQL Server con Visual Studio Community Edition. Para acceder a SQL Server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellenará como Nombre del servidor. Use Autenticación de Windows cuando inicie sesión como administrador en Windows. Una vez que esté en SQL Server Management Studio, puede crear otros usuarios, crear bases de datos, importar datos y ejecutar consultas SQL.

### Las tablas de Azure 
Varias herramientas de Azure están instaladas en la máquina virtual: - Hay un acceso directo de escritorio para acceder a la documentación del SDK de Azure. - **AzCopy** se usa para pasar datos a y desde la cuenta de Almacenamiento de Microsoft Azure. - **Explorador de almacenamiento de Azure** se usa para explorar los objetos que haya almacenado en la cuenta de Almacenamiento de Azure. - **Microsoft Azure Powershell** es un lenguaje de scripts que permite administrar los recursos de Azure en un lenguaje de scripts que también está instalado en su VM.

###Power BI

Para ayudarle a crear paneles y visualizaciones excelentes, se instaló **Power BI Desktop**. Use esta herramienta para extraer datos de orígenes diferentes, para crear los paneles e informes y publicarlos en la nube. Para obtener información, consulte el sitio de [Power BI](http://powerbi.microsoft.com).

Nota: se necesita una cuenta de Office 365 para tener acceso a Power BI.

## Herramientas de desarrollo de Microsoft adicionales
Puede usar el [**Instalador de plataforma web de Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) para detectar y descargar otras herramientas de desarrollo de Microsoft. También hay un acceso directo a la herramienta que se proporciona en el escritorio de Microsoft Data Science Virtual Machine.

<!---HONumber=AcomDC_1125_2015-->