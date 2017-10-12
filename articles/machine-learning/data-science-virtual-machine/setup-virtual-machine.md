---
title: "Configurar una máquina virtual como servidor de IPython Notebook | Microsoft Docs"
description: "Configure una máquina virtual de Azure para su uso en un entorno de ciencia de datos con el servidor de IPython para realizar análisis avanzados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 818617c1-048e-49c2-b941-f9a983f93998
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: xibingao;bradsev
ms.openlocfilehash: b32aa2325a507f18ffc8b47cddde8637a0a8aabf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configuración de una máquina virtual de Azure como servidor del Bloc de notas de IPython para realizar análisis avanzados
En este tema se muestra cómo aprovisionar y configurar una máquina virtual de Azure para análisis avanzado que se pueda usar como parte de un entorno de ciencia de datos. La máquina virtual Windows se configura con herramientas de compatibilidad como IPython Notebook, Explorador de Azure Storage y AzCopy, así como otras utilidades que son útiles para los proyectos de análisis avanzado. El Explorador de almacenamiento de Azure y AzCopy, por ejemplo, permite cargar de manera cómoda datos en el almacenamiento de blobs de Azure desde la máquina local o descargarlos en el equipo local desde el almacenamiento de blobs.

## <a name="create-vm"></a>Paso 1: Crear un máquina virtual de Azure de propósito general
Si ya tiene una máquina virtual de Azure y solo quiere configurar un servidor de Bloc de notas de IPython en ella, puede omitir este paso y continuar con el [Paso 2: Agregar un extremo para Blocs de notas de IPython a una máquina virtual existente](#add-endpoint).

Antes de comenzar el proceso de creación de una máquina virtual en Azure, deberá determinar el tamaño de la máquina que se necesita para procesar los datos para su proyecto. Las máquinas más pequeñas tienen menos memoria y menos núcleos de CPU que los equipos más grandes, pero también son menos costosas. Para obtener una lista de tipos de equipos y sus precios, consulte la página <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Precios de máquinas virtuales</a>.

1. Inicie sesión en el <a href="https://manage.windowsazure.com" target="_blank">Portal de Azure clásico</a>y haga clic en **Nuevo** en la esquina inferior izquierda. Aparecerá una ventana. Seleccione **PROCESO** -> **MÁQUINA VIRTUAL** -> **DE LA GALERÍA**.
   
    ![Creación del espacio de trabajo][24]
2. Elija una de las siguientes imágenes:
   
   * Windows Server 2012 R2 Datacenter
   * Experiencia con Windows Server Essentials (Windows Server 2012 R2)
     
     A continuación, haga clic en la flecha que señala a la derecha, en la parte inferior derecha, para ir a la siguiente página de configuración.
     
     ![Creación del espacio de trabajo][25]
3. Escriba un nombre para la máquina virtual que quiere crear, seleccione el tamaño de la máquina (Predeterminado: A3) en función del tamaño de los datos que va a procesar y la potencia que desea que tenga el equipo (el tamaño de la memoria y el número de núcleos de procesamiento), escriba un nombre de usuario y una contraseña para la máquina. A continuación, haga clic en la flecha que apunta hacia la derecha para ir a la siguiente página de configuración.
   
    ![Creación del espacio de trabajo][26]
4. Seleccione la **REGIÓN, GRUPO DE AFINIDAD o RED VIRTUAL** que contiene la **CUENTA DE ALMACENAMIENTO** que quiere usar para esta máquina virtual y, después, seleccione esa cuenta de almacenamiento. Agregue un punto de conexión en la parte inferior del campo **PUNTOS DE CONEXIÓN**; para hacerlo, escriba el nombre del punto de conexión (aquí, "IPython"). Puede elegir cualquier cadena como el **NOMBRE** del punto de conexión y cualquier entero entre 0 y 65536 que esté **disponible** como el **PUERTO PÚBLICO**. El **PUERTO PRIVADO** tiene que ser **9999**. Debe **evitar** el uso de los puertos públicos que ya estén asignados a servicios de Internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Puertos para Servicios de Internet</a> ofrece una lista de puertos que se han asignado y deben evitarse.
   
    ![Creación del espacio de trabajo][27]
5. Haga clic en la marca de verificación para iniciar la máquina virtual de proceso de aprovisionamiento.
   
    ![Creación del espacio de trabajo][28]

El proceso de aprovisionamiento de la máquina virtual puede tardar de 15 a 25 minutos en completarse. Una vez creada la máquina virtual, el estado de esta máquina debe aparecer como **En ejecución**.

![Creación del espacio de trabajo][29]

## <a name="add-endpoint"></a>Paso 2: Agregar un extremo para Blocs de notas de IPython a una máquina virtual existente
Si ha creado la máquina virtual siguiendo las instrucciones del paso 1, ya se ha agregado el extremo para Bloc de notas de IPython y se puede omitir este paso.

Si la máquina virtual ya existe y necesita agregar un punto de conexión para IPython Notebook que instalará en el paso 3 que se muestra más adelante, inicie sesión primero en el Portal de Azure clásico, seleccione la máquina virtual y agregue el punto de conexión del servidor de Bloc de notas de IPython. En la siguiente ilustración se muestra una captura de pantalla del portal después de que el extremo para Bloc de notas de IPython se haya agregado a una máquina virtual de Windows.

![Creación del espacio de trabajo][17]

## <a name="run-commands"></a>Paso 3: Instalar Bloc de notas de IPython y otras herramientas de compatibilidad
Después de crear la máquina virtual, use el Protocolo de escritorio remoto (RDP) para iniciar sesión en la máquina virtual de Windows. Para obtener instrucciones, vea [Iniciar sesión en una máquina virtual con Windows Server](../../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Abra el **Símbolo del sistema** (**no la ventana de comandos de PowerShell**) como **administrador** y ejecute el comando siguiente.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Cuando finalice la instalación, el servidor de IPython Notebook se iniciará automáticamente en el directorio *C:\\Usuarios\\\<nombre de usuario\>\\Documentos\\IPython Notebooks*.

Cuando se le pida, escriba una contraseña para Bloc de notas de IPython y la contraseña del administrador de la máquina. Esto permite que Bloc de notas de IPython se ejecute como un servicio en la máquina.

## <a name="access"></a>Paso 4: Acceso a IPython Notebook desde un explorador web
Para acceder al servidor de IPython Notebook, abra un explorador web y escriba *https://&#60;nombre DNS de máquina virtual>:&#60;número de puerto público>* en el cuadro de texto de la dirección URL. En este caso, el *&#60;número de puerto público>* será el número de puerto que ha especificado al agregar el punto de conexión de IPython Notebook.

El *&#60;nombre DNS de la máquina virtual>* se puede obtener en el Portal de Azure clásico. Después de iniciar sesión en el Portal clásico, haga clic en **MÁQUINAS VIRTUALES**, seleccione la máquina que ha creado y, después, seleccione **PANEL**; se mostrará el nombre DNS de esta forma:

![Creación del espacio de trabajo][19]

Verá una advertencia que indica que *Existe un problema con el certificado de seguridad de este sitio web* (Internet Explorer) o *Tu conexión no es privada* (Chrome), como se muestra en las ilustraciones siguientes. Haga clic en **Vaya a este sitio web (no recomendado)** (Internet Explorer) o **Avanzadas** y, después, **Continuar a &#60;*Nombre DNS>* (no seguro)** (Chrome) para continuar. A continuación, escriba la contraseña que especificó anteriormente para tener acceso a Blocs de notas de IPython.

**Internet Explorer:**
![Crear área de trabajo][20]

**Chrome:**
![Crear área de trabajo][21]

Después de iniciar sesión en IPython Notebook, se mostrará en el explorador el directorio *DataScienceSamples*. Este directorio contiene Blocs de notas de IPython de ejemplo que Microsoft comparte para ayudar a los usuarios a realizar tareas de ciencia de datos. Estos blocs de notas de IPython de ejemplo se extraen del [**repositorio de GitHub**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) en las máquinas virtuales durante el proceso de configuración del servidor de IPython Notebook. Microsoft mantiene y actualiza con frecuencia este repositorio. Los usuarios pueden visitar el repositorio de GitHub para obtener los blocs de notas de IPython de ejemplo más recientes.
![Creación del espacio de trabajo][18]

## <a name="upload"></a>Paso 5: Carga de un cuaderno de IPython existente desde un equipo local en el servidor de IPython Notebook
Los Blocs de notas de IPython ofrecen una manera fácil para que los usuarios carguen un Bloc de notas de IPython existente de sus máquinas locales en el servidor de Bloc de notas de IPython de las máquinas virtuales. Después de que los usuarios inicien sesión en IPython Notebook en un explorador web, haga clic en el **directorio** en el que se cargará IPython Notebook. A continuación, seleccione un archivo .ipynb de IPython Notebook para cargarlo desde la máquina local en el **Explorador de archivos**y arrástrelo y colóquelo en el directorio de IPython Notebook, en el explorador web. Haga clic en el botón **Cargar** para cargar el archivo .ipynb en el servidor del Bloc de notas de IPython. Otros usuarios podrán entonces empezar a usarlo desde sus exploradores web.

![Creación del espacio de trabajo][22]

![Creación del espacio de trabajo][23]

## <a name="shutdown"></a>Apagado y desasignación de la máquina virtual cuando no esté en uso
Las máquinas virtuales de Azure tienen unas tarifas del tipo **pague solo por lo que use**. Para asegurarse de que no se le facture cuando no use la máquina virtual, debe estar en el estado **Detenida (desasignada)** .

> [!NOTE]
> Si apaga la máquina virtual desde dentro de la misma (mediante las opciones de energía de Windows), la máquina virtual se detiene pero permanece asignada. Para asegurarse de que no se sigue facturando, detenga siempre las máquinas virtuales desde el [Portal de Azure clásico](http://manage.windowsazure.com/). También puede detener la máquina virtual a través de Powershell, con una llamada a **ShutdownRoleOperation** con "PostShutdownAction" igual a "StoppedDeallocated".
> 
> 

Para apagar y desasignar la máquina virtual:

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com/) con su cuenta.  
2. Seleccione **MÁQUINAS VIRTUALES** en la barra de navegación de la izquierda.
3. En la lista de máquinas virtuales, haga clic en el nombre de la máquina virtual y, a continuación, vaya a la página **PANEL** .
4. En la parte inferior de la página, haga clic en **APAGAR**.

![Apagado de máquina virtual][15]

Se desasignará la máquina virtual, pero no se eliminará. Puede reiniciar la máquina virtual en cualquier momento desde el Portal de Azure clásico.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>La máquina virtual de Azure está lista para su uso: ¿qué es lo siguiente?
La máquina virtual ya está lista para su uso en los ejercicios de ciencia de datos. La máquina virtual también está preparada para su uso como un servidor de IPython Notebook para la exploración y el procesamiento de datos, y otras tareas junto con el Aprendizaje automático de Azure y el proceso de ciencia de datos en la nube.

Los pasos siguientes del proceso de ciencia de datos en equipos se asignan en la [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y puede incluir otros que muevan datos a HDInsight, los procese y muestree en esa plataforma con el fin de prepararlos para el aprendizaje a partir de los datos mediante Aprendizaje automático de Azure.

[15]: ./media/setup-virtual-machine/vmshutdown.png
[17]: ./media/setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/setup-virtual-machine/sample-ipnbs.png
[19]: ./media/setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/setup-virtual-machine/browser-warning-ie.png
[21]: ./media/setup-virtual-machine/browser-warning.png
[22]: ./media/setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/setup-virtual-machine/create-virtual-machine-6.png
