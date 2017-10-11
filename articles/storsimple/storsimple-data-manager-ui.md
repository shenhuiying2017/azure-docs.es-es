---
title: Interfaz de usuario de Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: "Describe cómo se usa la interfaz de usuario del servicio StorSimple Data Manager (versión preliminar privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager (versión preliminar privada)

En este artículo se explica cómo se puede usar la interfaz de usuario de StorSimple Data Manager para realizar la transformación de datos que residen en los dispositivos de la serie StorSimple 8000. Posteriormente, los datos transformados pueden consumirlos otros servicios de Azure, como Azure Media Services, Azure HDInsight, Azure Machine Learning y Azure Search. 


## <a name="use-storsimple-data-transformation"></a>Uso de transformación de datos de StorSimple

StorSimple Data Manager es el recurso en el que se pueden crear instancias de transformación de datos. El servicio de transformación de datos permite mover los datos de un dispositivo StorSimple local a blobs de Azure Storage. De ahí que en el flujo de trabajo sea preciso especificar los detalles del dispositivo StorSimple y los datos de interés que se desean mover a la cuenta de almacenamiento.

### <a name="create-a-storsimple-data-manager-service"></a>Creación de un servicio StorSimple Data Manager

Siga estos pasos para crear un servicio StorSimple Data Manager.

1. Para crear un servicio StorSimple Data Manager, vaya a [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Haga clic en el icono **+** y busque StorSimple Data Manager. Haga clic en el servicio StorSimple Data Manager y, después, en **Create** (Crear).

3. Si la suscripción está habilitada para la creación de este servicio, verá la siguiente hoja.

    ![Creación de un recurso de StorSimple Data Manager](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Escriba la información que debe introducir y haga clic en **Create** (Crear). La ubicación especificada debe ser la que aloje las cuentas de almacenamiento y el servicio StorSimple Manager. Actualmente, solo se admiten las regiones oeste de EE. UU. y Europa occidental. Por lo tanto, el servicio StorSimple Manager, el servicio Data Manager y la cuenta de almacenamiento asociada deben estar en las regiones admitidas anteriores. El servicio tarda aproximadamente un minuto en crearse.

### <a name="create-a-data-transformation-job-definition"></a>Creación de una definición del trabajo de transformación de datos

En un servicio StorSimple Data Manager, es preciso crear una definición del trabajo de transformación de datos. Una definición del trabajo especifica los detalles de los datos que esté interesado en mover a una cuenta de almacenamiento en el formato nativo. 

Realice los pasos siguientes para crear una nueva definición del trabajo de transformación de datos.

1.  Navegue hasta el servicio que ha creado. Haga clic en **+ Job Definition** (+ Definición de trabajo).

    ![Haga clic en + Job Definition (+ Definición de trabajo)](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Se abre la hoja de definición de trabajo nueva. Asigne un nombre a la definición de trabajo y haga clic en **Origen**. En la hoja **Configurar origen de datos**, especifique los detalles del dispositivo StorSimple y los datos de interés.

    ![Creación de una definición de trabajo](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Al ser un servicio Data Manager nuevo, no se configuran repositorios de datos. Para agregar StorSimple Manager como un repositorio de datos, haga clic en **Add new** (Agregar nuevo) en la lista desplegable de repositorios de datos y, después, en **Add Data Repository** (Agregar repositorio de datos).

4. Elija **StorSimple 8000 series Manager** como tipo de repositorio y especifique las propiedades de **StorSimple Manager**. En el campo **Resource Id** (Id. de recurso), es preciso escribir el número delante del signo **:** en la clave de registro de StorSimple manager.

    ![Creación de un origen de datos](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Pulse **OK** (Aceptar) cuando haya terminado. Esto guarda el repositorio de datos y StorSimple Manager se pueden reutilizar en otras definiciones de trabajo sin tener que volver a escribir estos parámetros. Unos segundos después de hacer clic en **OK** (Aceptar) StorSimple Manager aparece en la lista desplegable.

6.  En la hoja **Configurar origen de datos**, escriba el nombre del dispositivo y el nombre del volumen que tiene los datos que le interesan.

7.  En la subsección **Filtro**, escriba el directorio raíz que contiene los datos de interés (el campo debe empezar por una barra inclinada `\`). Aquí también se pueden agregar los filtros de archivo.

8.  El servicio de transformación de datos funciona en los datos que se insertan en Azure a través de instantáneas. Al ejecutar este trabajo, puede elegir realizar una copia de seguridad cada vez que se ejecuta este trabajo (para trabajar con los datos más recientes) o utilizar la última copia de seguridad existente en la nube (si trabaja con datos archivados).

    ![Detalles del origen de datos nuevo](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. A continuación, es preciso configurar los valores del destino. Se admiten dos 2 tipos de destinos: cuentas de Azure Storage y cuentas de Azure Media Services. Elija el primero si desea colocar los archivos en blobs de esa cuenta. Elija el segundo si desea colocar los archivos en recursos de esa cuenta. Una vez más, es preciso agregar un repositorio. En la lista desplegable, seleccione **Agregar nuevo** y, después, **Parámetros de configuración**.

    ![Crear el receptor de datos](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Aquí puede seleccionar el tipo de repositorio que desea agregar y los restantes parámetros asociados con el repositorio. En ambos casos, cuando se ejecuta el trabajo se crea una cola de almacenamiento. Esta cola se rellena con mensajes de los blobs transformados a medida que están listos. El nombre de esta cola es el mismo que el de la definición del trabajo. Si selecciona **Media Services** como tipo de repositorio, también puede especificar las credenciales de la cuenta de almacenamiento en que se crea la cola.

    ![Detalles del receptor de datos nuevo](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Después de agregar el repositorio de datos (se tarda unos segundos), encontrará el repositorio en la lista desplegable de **Target account name** (Nombre de cuenta de destino).  Elija el destino que necesita.

12. Haga clic en **OK** (Aceptar) para crear la definición del trabajo. La definición de trabajo ya está configurada. Esta definición de trabajo se puede usar varias veces a través de la interfaz de usuario.

    ![Agregar una definición de trabajo nueva](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Ejecución de la definición de trabajo

Siempre que tenga que mover los datos de StorSimple a la cuenta de almacenamiento que ha especificado en la definición del trabajo, será preciso que la invoque. Hay cierta flexibilidad a la hora de cambiar los parámetros cada vez que se invoca el trabajo. Los pasos son los siguientes:

1. Seleccione el servicio StorSimple Data Manager y vaya a **Monitoring** (Supervisión). Haga clic en **Run now** (Ejecutar ahora).

    ![Desencadenar la definición de trabajo](./media/storsimple-data-manager-ui/run-now.png)

2. Elija la definición de trabajo que desea ejecutar. Haga clic en **Ejecutar Configuración** para modificar los parámetros que desea cambiar para la ejecución de este trabajo.

    ![Ejecutar configuración de trabajo](./media/storsimple-data-manager-ui/run-settings.png)

3. Haga clic en **Aceptar** y, luego, haga clic en **Ejecutar** para iniciar el trabajo. Para supervisar este trabajo, vaya a la página **Jobs** (Trabajos) de StorSimple Data Manager.

    ![Lista y estado de los trabajos](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. Además de la supervisión de la hoja **Trabajos**, también puede escuchar la cola de almacenamiento a la que se agrega un mensaje cada vez que se mueve un archivo desde StorSimple a la cuenta de almacenamiento.


## <a name="next-steps"></a>Pasos siguientes

[Use the .Net SDK to initiate data transformation](storsimple-data-manager-dotnet-jobs.md) (Uso del SDK de .NET para iniciar la transformación de datos).