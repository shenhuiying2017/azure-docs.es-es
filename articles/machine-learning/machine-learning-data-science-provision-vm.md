<properties 
	pageTitle="Aprovisionamiento de una Data Science Virtual Machine | Microsoft Azure" 
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
	ms.date="11/04/2015" 
	ms.author="bradsev" />


# Aprovisionamiento de una Data Science Virtual Machine

## Introducción

Data Science Virtual Machine es una imagen de máquina virtual de Azure preinstalada y configurada con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. Las herramientas incluidas son:

- Revolution R Open
- Anaconda Python Distribution
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express Edition
- SDK de Azure


La ciencia de datos implica una iteración en una secuencia de tareas: encontrar, cargar y preprocesar datos, crear y probar modelos e implementar los modelos para su uso en aplicaciones inteligentes. No es raro que los científicos de datos usen diversas herramientas para realizar estas tareas. Puede ser bastante lento encontrar las versiones del software adecuadas, descargarlas e instalarlas. Use Data Science Virtual Machine para facilitar esta carga.

Data Science Virtual Machine pone en marcha el proyecto de análisis. Le permite trabajar en tareas en una variedad de lenguajes, incluidos R, Python, SQL y C#. Visual Studio proporciona un IDE para desarrollar y probar el código que es fácil de usar. El SDK de Azure incluido en la máquina virtual permite crear aplicaciones con varios servicios en la plataforma en la nube de Microsoft.


## Requisitos previos

Antes de poder crear una máquina virtual de Azure, debe tener lo siguiente:

- **Una suscripción a Azure**: para conseguir una, vea [Obtener una versión de evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Una cuenta de almacenamiento de Azure**: para crear una, consulte [Creación de una cuenta de almacenamiento de Azure](storage-whatis-account.md). También puede crear una cuenta de almacenamiento como parte del proceso de creación de la máquina virtual si no desea usar una cuenta existente.


## Creación de su máquina virtual de ciencia de datos

Estos son los pasos para crear una instancia de Data Science Virtual Machine:

1.	Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).
2.	Vaya a **Nuevo** -> **Proceso** -> **Marketplace** y busque *Data Science Virtual Machine*.![provision-data-science-vm](./media/machine-learning-data-science-provision-vm/provision-data-science-virtual-machine.png)
3.	Seleccione la máquina virtual llamada “**Máquina virtual para ciencia de datos**”, publicada por Microsoft para ver un panel que describe la máquina virtual para ciencia de datos. 	
4.	 Haga clic en el botón **Crear**, ubicado en la parte inferior, para tener acceso a un asistente.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
5.	 Las secciones siguientes proporcionan las **entradas** para cada uno de los **5 pasos** (enumerados a la derecha de la ilustración anterior) en el asistente que se usó para crear Data Science Virtual Machine. Estas son las entradas necesarias para configurar cada uno de estos pasos:

 **1. Básico**:

- **Nombre**: nombre del servidor de ciencia de datos que está creando
- **Nombre de usuario**: identificador de inicio de sesión de la cuenta del administrador
- **Contraseña**: contraseña de la cuenta del administrador
- **Suscripción**: si tiene más de una suscripción, seleccione en la que se creará y facturará la máquina
- **Grupo de recursos**: puede crear uno nuevo o usar un grupo ya existente
- **Ubicación**: seleccione el centro de datos más adecuado Normalmente es el centro de datos que tenga la mayoría de los datos o que esté más cercano a su ubicación física para un acceso más rápido a la red

 **2. Tamaño**:

- Seleccione uno de los tipos de servidor que cumpla sus requisitos funcionales y las restricciones de costo. Para obtener más opciones de tamaños de la máquina virtual, seleccione "Ver todo"

 **3. Configuración**

- **Tipo de disco**: elija Premium si prefiere una unidad de estado sólido (SSD), de lo contrario elija "Estándar".
- **Cuenta de almacenamiento**: puede crear una nueva cuenta de almacenamiento de Azure en su suscripción o usar uno existente en la misma *ubicación* que ha elegido en el paso Básico del asistente.
- **Otros parámetros**: en la mayoría de los casos, usará simplemente los valores predeterminados. Puede mover el puntero sobre el vínculo informativo para obtener ayuda sobre los campos específicos en caso de que desee considerar el uso de valores no predeterminados.

 **4. Resumen**:

- Compruebe que toda la información que ha especificado es correcta.

 **5. Comprar**:

- Haga clic en **Comprar** para iniciar el aprovisionamiento. Se proporciona un vínculo a los términos de la transacción. La máquina virtual no tiene ningún cargo adicional más allá del proceso para el tamaño del servidor que eligió en el paso **Tamaño**. 


El aprovisionamiento tardará entre 10 y 20 minutos. El estado del aprovisionamiento se muestra en el Portal de Azure.

## Acceso a Data Science Virtual Machine

Una vez creada la máquina virtual, puede iniciar sesión en ella mediante el escritorio remoto con las credenciales de la cuenta del administrador que creó en la sección Básico del paso 4.

Una vez creada y aprovisionada la máquina virtual, está listo para comenzar a usar las herramientas que se instalan y configuran en ella. Hay iconos del escritorio e iconos del menú inicio para muchas de las herramientas.

## Herramientas instaladas en Data Science Virtual Machine

### R
Si desea usar R para su análisis, la máquina virtual tiene Revolution R Open (RRO) instalado. Se trata de una distribución de código abierto de R totalmente compatible con CRAN-R. Contiene el motor de código abierto de R más reciente, junto con la biblioteca Intel Math Kernel Library. Un IDE llamado “RRO RGui” también está empaquetado en la máquina virtual. Puede descargar y usar otros IDE, así como también [RStudio](http://www.rstudio.com).

### Python
Para el desarrollo con Python, se ha instalado Anaconda Python Distribution 2.7. Esta distribución contiene Python base, junto con aproximadamente 300 de los paquetes de matemáticas, ingeniería y análisis de datos más populares. Puede usar IDE agrupados con Anaconda, como IDLE o Spyder. Para iniciar uno de ellos, busque en la barra de búsqueda (tecla **Win** + **S**).

### Bloc de notas de IPython
La distribución de Anaconda también incluye un IPython Notebook, un entorno para compartir código y análisis. Se ha preconfigurado un servidor de IPython Notebook. Hay un icono del escritorio para iniciar el explorador para tener acceso al servidor del notebook. Si está en la máquina virtual a través de un escritorio remoto, también puede visitar [https://localhost:9999/](https://localhost:9999/) para tener acceso al servidor de IPython Notebook (nota: si se recibe alguna advertencia de certificado, simplemente continúe).

### Visual Studio 2015 Community Edition
Visual Studio Community Edition instalado en la máquina virtual. Es una versión gratuita del popular IDE de Microsoft que puede usar para fines de evaluación y para equipos muy pequeños. Puede revisar los términos de licencia [aquí](https://www.visualstudio.com/support/legal/mt171547). Haga doble clic en el icono del escritorio o en el menú **Inicio** para abrir Visual Studio. También puede buscar programas con **Win** + **S** y escribiendo "Visual Studio".

Nota: puede obtener un mensaje que indica que el período de evaluación ha caducado. Puede escribir las credenciales de la cuenta de Microsoft o crear una cuenta y volver a escribirlas para obtener acceso a Visual Studio Community Edition. Una ahí, puede crear proyectos en lenguajes como C#, Python

### SQL Server Express
También se ha empaquetado una versión limitada de SQL Server con Visual Studio Community Edition. Para tener acceso a SQL Server, inicie **SQL Server Management Studio**. El nombre de la máquina virtual se rellenará como Nombre del servidor. Use Autenticación de Windows cuando inicie sesión como administrador en Windows. Una vez que esté en SQL Server Management Studio, puede crear otros usuarios, crear bases de datos, importar datos y ejecutar consultas SQL.

### Las tablas de Azure 
Varias herramientas de Azure están instaladas en la máquina virtual: - Hay un acceso directo de escritorio para acceder a la documentación del SDK de Azure. - **AzCopy** se usa para pasar datos a y de la cuenta de Almacenamiento de Microsoft Azure. - **Explorador de almacenamiento de Azure** se usa para explorar los objetos que haya almacenado en la cuenta de Almacenamiento de Azure. - **Microsoft Azure Powershell** - un lenguaje de scripts que permite administrar los recursos de Azure en un lenguaje de scripts también está instalado en la máquina virtual.

###Power BI

Para ayudarle a crear paneles y visualizaciones excelentes, se instaló **Power BI Desktop**. Use esta herramienta para extraer datos de orígenes diferentes, para crear los paneles e informes y publicarlos en la nube. Para obtener información, consulte el sitio [Power BI](http://powerbi.microsoft.com).

Nota: se necesita una cuenta de Office 365 para tener acceso a Power BI.

## Herramientas de desarrollo de Microsoft adicionales
Puede usar el [**Instalador de plataforma web de Microsoft**](https://www.microsoft.com/web/downloads/platform.aspx) para detectar y descargar otras herramientas de desarrollo de Microsoft. También hay un acceso directo a la herramienta que se incluye en el escritorio de la máquina virtual.

<!---HONumber=Nov15_HO2-->