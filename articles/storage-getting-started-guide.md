<properties 
	pageTitle="Introducción de 5 minutos a Azure blobs, tablas y colas en 5 minutos" 
	description="Mejore sus habilidades rápidamente en Microsoft Azure blobs, tablas y colas con las guías de inicio rápido de Azure y Visual Studio." 
	services="storage" 
	documentationCenter=".net" 
	authors="Selcin" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/18/2015" 
	ms.author="selcint"/>

# Introducción de 5 minutos a Azure blobs, tablas y colas en 5 minutos 

En este tutorial le mostraremos cómo programar rápidamente **blobs de Almacenamiento de Azure**, **tablas** y **colas**, desarrollando una simple aplicación de Azure en Visual Studio. 

El tutorial incluye dos escenarios principales para aprender rápidamente a utilizar Almacenamiento de Azure:

- Ejecutar su primera aplicación de Almacenamiento de Azure en el Emulador de almacenamiento de Azure
- Ejecutar su primera aplicación de Almacenamiento de Azure en el Servicio de almacenamiento de Azure

Si desea obtener información sobre Almacenamiento de Azure antes de comenzar con el código, consulte la sección [Pasos siguientes](#next-steps).

## Ejecución de su primera aplicación de Almacenamiento de Azure en el Emulador de almacenamiento de Azure

En esta sección le mostraremos cómo programar los **blobs de Almacenamiento de Azure**, **tablas** y **colas** mediante el desarrollo de una aplicación de ejemplo accediendo al [Emulador de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx). El emulador de almacenamiento de Microsoft Azure proporciona un entorno local que emula los servicios de Azure de blob, cola y tabla para fines de desarrollo. Mediante el emulador de almacenamiento, puede probar la aplicación en los servicios de almacenamiento local sin ningún gasto.

Para completar esta sección, asegúrese de realizar primero las siguientes tareas:

1. Para compilar y crear la aplicación, debe tener [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) instalado en el equipo. Si no tiene instalado Visual Studio, puede instalar Visual Studio Express para Web cuando instale el [SDK 2.5 de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o posterior. 
2. Asegúrese de que tiene el [SDK 2.5 de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o una versión posterior instalada en el equipo, ya que incluye los proyectos de ejemplo de esta guía rápida de Azure y la [Biblioteca de cliente de almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Compruebe si tiene [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado en el equipo ya que los proyectos de ejemplo de la guía rápida de Azure lo necesitarán. Si no está seguro de qué versión de .NET Framework está instalada en su equipo, consulte [Cómo saber qué versiones de .NET Framework tengo instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx) o pulse el botón **Inicio** o la tecla de Windows y escriba **Panel de Control**. A continuación, haga clic en **Programas** > **Programas y características**. Compruebe si .NET Framework 4.5 aparece en la lista donde están todos los programas instalados.

Ahora crearemos una sencilla aplicación de Almacenamiento de Azure con uno de los proyectos de ejemplo de la guía rápida de Azure en Visual Studio. Este tutorial se centra en los proyectos de ejemplo **Almacenamiento de blobs de Azure**, **Almacenamiento de tablas de Azure** y en **Almacenamiento de colas de Azure**. Por cada proyecto de ejemplo se aplicarán las siguientes instrucciones, a no ser que escoja una plantilla diferente en el paso 3.a:

1. Pulse el botón **Inicio** o la tecla de Windows y escriba Visual Studio 2013 o VS 2013 Express para Web. Haga clic en el programa para iniciarlo.
2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Instalados** > **Plantillas** > **Visual C#** > **Nube** > **Inicio rápido** > **Servicios de datos**.
	- 3.a.  Elija una de las siguientes plantillas: Almacenamiento de blobs de Azure, Almacenamiento de tablas de Azure o Almacenamiento de colas de Azure. 
	- 3.b. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.	
	- 3.c. Nombre la aplicación según la plantilla que elija; esto es, **DataBlobStorage**, **DataTableStorage** o **DataStorageQueue**. Haga clic en **Aceptar**. Esto creará una nueva solución de Visual Studio. Consulte la siguiente captura de pantalla a modo de ejemplo:
	
	![Azure QuickStarts][Image1]

Le recomendamos que revise el código fuente para aprender a programar en Almacenamiento de Azure antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Vista** en Visual Studio. A continuación, haga doble clic en el archivo Program.cs. 

Ahora , ejecute la aplicación de ejemplo usando el [emulador de Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) que ha instalado como parte del SDK de Azure:

1.	Inicie el emulador de Almacenamiento de Azure: Pulse el botón **Inicio** o la tecla de Windows, y busque el emulador de Almacenamiento de Azure. Selecciónelo de la lista de aplicaciones para iniciarlo.
2.	En Visual Studio, haga clic en **Crear solución** en el menú **Crear**. 
3.	En el menú **Depurar**, pulse **F11** para ejecutar la solución paso a paso o pulse **F5** para ejecutar la solución.

## Ejecución de su primera aplicación de almacenamiento de Azure en Almacenamiento de Azure en la nube
En esta sección le mostraremos cómo programar los **Blobs de almacenamiento de Azure**, **Tablas** y **Colas** mediante el desarrollo de una aplicación de ejemplo accediendo al [Servicio de almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)

Para completar esta sección, asegúrese de realizar primero las siguientes tareas:

1. Para compilar y crear la aplicación, debe tener [Visual Studio](http://www.visualstudio.com/visual-studio-homepage-vs.aspx) instalado en el equipo. Si no tiene instalado Visual Studio, puede instalar Visual Studio Express para Web cuando instale el [SDK 2.5 de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o posterior. 
2. Asegúrese de que tiene el [SDK 2.5 de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) o una versión posterior instalada en el equipo, ya que incluye los proyectos de ejemplo de esta guía rápida de Azure y la [Biblioteca de cliente de almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).  
3. Compruebe si tiene [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) instalado en el equipo ya que los proyectos de ejemplo de la guía rápida de Azure lo necesitarán. Si no está seguro de qué versión de .NET Framework está instalada en su equipo, consulte [Cómo saber qué versiones de .NET Framework tengo instaladas](https://msdn.microsoft.com/vstudio/hh925568.aspx) o pulse el botón **Inicio** o la tecla de Windows y escriba **Panel de Control**. A continuación, haga clic en **Programas** > **Programas y características**. Compruebe si .NET Framework 4.5 aparece en la lista donde están todos los programas instalados.
4.	Consiga una suscripción de Azure (si no la tiene ya) y cree una cuenta de **almacenamiento estándar**:
	- Para obtener una suscripción de Azure, consulte [Prueba gratuita](, http://azure.microsoft.com/pricing/free-trial/)[Opciones de compra](http://azure.microsoft.com/pricing/purchase-options/) y [Ofertas para miembros](http://azure.microsoft.com/pricing/member-offers/) (para miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).
	- Para crear una cuenta de **almacenamiento estándar** en Azure, consulte [Cómo crear, administrar o eliminar una cuenta de almacenamiento](storage-create-storage-account.md). **Nota:** Existen dos tipos de cuentas de almacenamiento en Azure: La cuenta de almacenamiento estándar y la cuenta de almacenamiento premium. Una cuenta de almacenamiento estándar le proporcionará acceso al almacenamiento de blob, tabla y cola de Azure. Las cuentas de almacenamiento premium están solo disponibles para almacenar datos en discos que utilizan las máquinas virtuales de Azure. Para más información, consulte [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage-preview-portal.md).

Ahora crearemos una sencilla aplicación de Almacenamiento de Azure con uno de los proyectos de ejemplo de la guía rápida de Azure en Visual Studio. Este tutorial se centra en los proyectos de ejemplo **Almacenamiento de blobs de Azure**, **Almacenamiento de tablas de Azure** y en **Almacenamiento de colas de Azure**. Por cada proyecto de ejemplo se aplicarán las siguientes instrucciones, a no ser que escoja una plantilla diferente en el paso 3.a:

1. Pulse el botón **Inicio** o la tecla de Windows y escriba Visual Studio 2013 o VS 2013 Express para Web. Haga clic en el programa para iniciarlo.
2. En el menú **Archivo**, haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Instalados** > **Plantillas** > **Visual C#** > **Nube** > **Inicio rápido** > **Servicios de datos**.
	- 3.a. Elija una de las siguientes plantillas: Almacenamiento de blobs de Azure, Almacenamiento de tablas de Azure o Almacenamiento de colas de Azure. 
	- 3.b. Asegúrese de que **.NET Framework 4.5** sea el marco de trabajo de destino seleccionado.
	- 3.c. Nombre la aplicación según la plantilla que elija; esto es, **DataBlobStorage**, **DataTableStorage** o **DataStorageQueue**. Haga clic en **Aceptar**. Esto creará una nueva solución de Visual Studio. 

Le recomendamos que revise el código fuente para aprender a programar en Almacenamiento de Azure antes de ejecutar la aplicación. Para revisar el código, seleccione **Explorador de soluciones** en el menú **Vista** en Visual Studio. A continuación, haga doble clic en el archivo Program.cs. 

Ejecute la aplicación de ejemplo:

1.	En Visual Studio, seleccione **Explorador de soluciones** en el menú**Vista**. Haga doble clic en el archivo App.config y marque como comentario la cadena de conexión del Emulador de almacenamiento del SDK de Azure: 

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	Quite el comentario de la cadena de conexión del Servicio de almacenamiento de Azure y proporcione la clave de acceso y el nombre de la cuenta de almacenamiento en el archivo App.config:
	`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"` 

	Para buscar la clave de acceso y el nombre de la cuenta de almacenamiento, consulte [¿Qué es una cuenta de almacenamiento?](storage-whatis-account.md). 

3.	Una vez haya proporcionado el nombre de la cuenta de almacenamiento y la clave de acceso, en el menú **Archivo** haga clic en **Guardar todo** para guardar todos los archivos del proyecto. 
4.	En el menú **Crear**, haga clic en **Crear solución**. 
5.	En el menú **Depurar**, pulse **F11** para ejecutar la solución paso a paso o pulse **F5** para ejecutar la solución.


## Pasos siguientes
En este tutorial ha aprendido a programar en Almacenamiento de blobs de Azure, Almacenamiento de tablas de Azure y Almacenamiento de colas de Azure. 

Si desea obtener más información acerca de ellas, consulte estos vínculos:

* [Introducción a Almacenamiento de Microsoft Azure](storage-introduction.md)
* [Uso del almacenamiento de blobs en .NET](storage-dotnet-how-to-use-blobs.md)
* [Uso del almacenamiento de tablas en .NET](storage-dotnet-how-to-use-tables.md)
* [Uso del almacenamiento en cola en .NET](storage-dotnet-how-to-use-queues.md)
* [Documentación de almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)
* [Referencia MSDN de Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
* [Biblioteca de cliente de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [API de REST de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


<!--HONumber=52-->