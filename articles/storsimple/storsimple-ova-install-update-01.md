<properties 
   pageTitle="Instalación de actualizaciones en la matriz virtual de StorSimple | Microsoft Azure"
   description="Se describe cómo usar la interfaz de usuario web de la matriz virtual de StorSimple para aplicar actualizaciones mediante el portal y el método de revisiones."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/16/2016"
   ms.author="alkohli" />

# Instalación de actualizaciones en la matriz virtual de StorSimple

## Información general

En este artículo se describen los pasos necesarios para instalar actualizaciones en la matriz virtual de StorSimple mediante la interfaz de usuario web local. Este procedimiento tarda menos de 2 minutos en completarse. Deberá aplicar alguna actualización o revisión de software para mantener actualizada la matriz virtual de StorSimple.

Tenga en cuenta que al instalar una actualización o revisión, se reiniciará el dispositivo. Dado que la matriz virtual de StorSimple es un dispositivo de nodo único, se interrumpirá cualquier operación de E/S que esté en curso y el dispositivo permanecerá un rato inactivo.

Antes de aplicar una actualización, se recomienda que desconecte primero los volúmenes o recursos compartidos en el host y, luego, el dispositivo. Esto minimizará la posibilidad de daños en los datos.

## Uso de la interfaz de usuario web local 
Para instalar la actualización más reciente, debe utilizar la interfaz de usuario web local para aplicar revisiones o actualizaciones (en este momento, no puede utilizar el Portal de Azure clásico para instalar la actualización).

Se pueden seguir dos pasos con la interfaz de usuario web local:

- Descargar la actualización o la revisión
- Instalar la actualización o la revisión

### Descargar la actualización o la revisión

Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

#### Para descargar la actualización o la revisión

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update.
   
	![Instalación del catálogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base correspondiente a la revisión que quiera descargar. Escriba **3160441** para Update 0.2 o **3160441** para Update 0.1 y luego haga clic en **Buscar**.

    Aparece la lista de revisiones, por ejemplo, **StorSimple Virtual Array Update 0.1**.

    ![Búsqueda de catálogo](./media/storsimple-ova-install-update-01/download1.png)

4. Haga clic en **Agregar**. La actualización se agrega a la cesta.

5. Haga clic en **Ver cesta**.

6. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezcan las descargas. Las actualizaciones se descargan en la ubicación especificada y se colocan en una subcarpeta con el mismo nombre que la actualización. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.


### Instalar la actualización o la revisión

Antes de instalar la actualización o la revisión, asegúrese de que tiene la actualización o la revisión descargada de forma local en el host o que puede acceder a ella a través de un recurso compartido de red. Realice los pasos siguientes para instalar la actualización o revisión.

#### Instalar la actualización o la revisión

1. En la interfaz de usuario web local, vaya a **Mantenimiento** > **Actualización de software**.

2. En **Update file path** (Ruta de acceso del archivo de actualización), escriba el nombre del archivo de actualización o de revisión. Asimismo, también puede acceder al archivo de instalación de la actualización o de la revisión si está en un recurso compartido de red. Haga clic en **Apply**.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

3.  Se mostrará una advertencia. Dado que se trata de un dispositivo de nodo único, una vez aplicada la actualización, se reiniciará el dispositivo y habrá un tiempo de inactividad. Haga clic en el icono de marca de verificación.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update4m.png)

4. Se iniciará la actualización. Una vez que el dispositivo se actualice correctamente, este se reiniciará. La interfaz de usuario local no será accesible durante este tiempo.

    ![actualizar dispositivo](./media/storsimple-ova-install-update-01/update6m.png)

4. Una vez completado el reinicio, se le llevará a la página de inicio de sesión. A continuación, podrá comprobar la versión del software. Vaya a **Mantenimiento** > **Actualización de software**. La versión de software mostrada debe ser **10.0.10280.0** para Update 0.2 o **10.0.10279.0** para Update 0.1.

	> [AZURE.NOTE] Las versiones de software se muestran de forma ligeramente distinta en la interfaz de usuario web local y el Portal de Azure clásico. Por ejemplo, la misma versión aparece como **10.0.0.0.10279** en la interfaz de usuario web local y como **10.0.10279.0** en el Portal de Azure clásico.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Pasos siguientes

Más información sobre la [administración de la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->