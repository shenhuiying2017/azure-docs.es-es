<properties 
   pageTitle="Instalación la actualización 0.1 en la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe cómo usar la interfaz de usuario web de la matriz virtual de StorSimple para aplicar la actualización 0.1 mediante el Portal y el método de la revisión"
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
   ms.date="05/24/2016"
   ms.author="alkohli" />

# Instalación la actualización 0.1 en la matriz virtual de StorSimple

## Información general

En este artículo se describen los pasos necesarios para instalar la actualización 0.1 en la matriz virtual de StorSimple. Asimismo, se explican los procedimientos de actualización mediante el Portal de Azure clásico y a través de la interfaz de usuario web local. Cada procedimiento de actualización tarda menos de 2 minutos en completarse.

Lo habitual es que deba aplicar alguna actualización o revisión de software para mantener actualizada la matriz virtual de StorSimple. Normalmente le recomendamos que instale las actualizaciones a través del Portal de Azure clásico. Sin embargo, si no tiene el portal disponible, puede usar la interfaz de usuario web local para aplicar revisiones o actualizaciones.

Tenga en cuenta que, si instala una actualización o revisión, se reiniciará el dispositivo. Dado que la matriz virtual de StorSimple es un dispositivo de nodo único, se interrumpirá cualquier operación de E/S que esté en curso y el dispositivo permanecerá un rato inactivo.

Antes de aplicar una actualización, se recomienda que desconecte primero los volúmenes o recursos compartidos en el host y, luego, el dispositivo. Esto minimizará la posibilidad de daños en los datos.

## Uso del Portal de Azure clásico

Recomendamos que instale las actualizaciones a través del Portal de Azure clásico. El procedimiento del Portal requiere que el usuario examine, descargue e instale las actualizaciones. Realice los pasos siguientes para instalar la actualización o revisión.

#### Para instalar actualizaciones a través del Portal de Azure clásico

1. En la página **Dispositivos**, seleccione el dispositivo en el que desea instalar las actualizaciones.

2. Vaya a **Dispositivos** > **Mantenimiento** > **Actualizaciones de software**.

3. Verá un mensaje si hay actualizaciones de software disponibles. Para buscar actualizaciones, también puede hacer clic en **Buscar actualizaciones** en la parte inferior de la página.

	![Buscar actualizaciones](./media/storsimple-ova-install-update-01/aupdate1m.png)

4. En la parte inferior de la página, haga clic en **Descargar actualizaciones**. Un cuadro de diálogo notifica al usuario que la actualización puede interrumpir los procesos en curso. Dado que la matriz virtual de StorSimple es un dispositivo de nodo único, el dispositivo se reiniciará después una vez actualizado. Esto interrumpirá cualquier proceso de E/S en curso. Haga clic en el icono de verificación para iniciar un trabajo a fin de descargar las actualizaciones disponibles.

	![Confirmar la descarga de actualizaciones](./media/storsimple-ova-install-update-01/aupdate3m.png)

	Recibirá una notificación una vez descargadas las actualizaciones.

	![Descargar actualizaciones](./media/storsimple-ova-install-update-01/aupdate5m.png)

5. En la parte inferior de la página, haga clic en **Instalar actualizaciones** para iniciar la actualización del dispositivo. Aparecerá de nuevo el cuadro de diálogo. Haga clic en el icono de verificación para iniciar un trabajo a fin de instalar las actualizaciones.
 
 	![Confirmar la instalación de actualizaciones](./media/storsimple-ova-install-update-01/aupdate6m.png)
 
 
	Recibirá una notificación cuando el trabajo se cree.

	![Instalar actualizaciones](./media/storsimple-ova-install-update-01/aupdate8m.png)
	
6. Haga clic en el vínculo **Ver trabajo** para ir a la página **Trabajos** y supervisar el estado de la instalación. Puede hacer clic en **Detalles** en cualquier momento para obtener información detallada sobre el trabajo de actualización.

	![Supervisar actualizaciones](./media/storsimple-ova-install-update-01/aupdate9m.png)

6. Una vez que la instalación esté completa (podrá comprobarlo cuando el estado del trabajo esté al 100 %), vaya a **Dispositivos** > **Mantenimiento** > **Actualizaciones de software**. La versión de software que aparece debe ser **10.0.10279.0**.

	![Comprobar actualizaciones](./media/storsimple-ova-install-update-01/aupdate13m.png)

## Uso de la interfaz de usuario web local 

Use la interfaz de usuario web local para aplicar actualizaciones cuando el Portal de Azure clásico no esté disponible. Se pueden seguir dos pasos con la interfaz de usuario web local:

- Descargar la actualización o la revisión
- Instalar la actualización o la revisión

### Descargar la actualización o la revisión

Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

#### Para descargar la actualización o la revisión

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si esta es la primera vez que utiliza el Catálogo de Microsoft Update en este equipo, haga clic en **Instalar** cuando se le solicite que instale el complemento del Catálogo de Microsoft Update.
   
	![Instalación del catálogo](./media/storsimple-ova-install-update-01/install-catalog.png)

3. En el cuadro de búsqueda del Catálogo de Microsoft Update, escriba el número de Knowledge Base de la revisión que quiera descargar (por ejemplo, **3160441**) y haga clic en **Buscar**.

    Aparece la lista de revisiones, por ejemplo, **Actualización 0.1 de la matriz virtual de Microsoft Azure StorSimple**.

    ![Búsqueda de catálogo](./media/storsimple-ova-install-update-01/download1.png)

4. Haga clic en **Agregar**. La actualización se agrega a la cesta.

5. Haga clic en **Ver cesta**.

6. Haga clic en **Descargar**. Especifique o **busque** una ubicación local en la que quiera que aparezcan las descargas. Las actualizaciones se descargan en la ubicación especificada y se colocan en una subcarpeta con el mismo nombre que la actualización. La carpeta también se puede copiar en un recurso compartido de red que sea accesible desde el dispositivo.


### Instalar la actualización o la revisión

Antes de instalar la actualización o la revisión, asegúrese de que tiene la actualización o la revisión descargada de forma local en el host o que puede acceder a ella a través de un recurso compartido de red. Realice los pasos siguientes para instalar la actualización o revisión.

#### Instalar la actualización o la revisión

1. En la interfaz de usuario web local, vaya a **Mantenimiento** > **Actualización de software**.

2. En la opción **Update file path** (Ruta de acceso del archivo de actualización), escriba el nombre del archivo de actualización o de revisión. Asimismo, también puede acceder al archivo de instalación de la actualización o de la revisión si está en un recurso compartido de red. Haga clic en **Apply**.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

3.  Se mostrará una advertencia. Dado que se trata de un dispositivo de nodo único, una vez aplicada la actualización, se reiniciará el dispositivo y habrá un tiempo de inactividad. Haga clic en el icono de marca de verificación.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update4m.png)

4. Se iniciará la actualización. Una vez que el dispositivo se actualice correctamente, este se reiniciará. La interfaz de usuario local no será accesible durante este tiempo.

    ![actualizar dispositivo](./media/storsimple-ova-install-update-01/update6m.png)

4. Una vez completado el reinicio, se le llevará a la página de inicio de sesión. A continuación, podrá comprobar la versión del software. Vaya a **Mantenimiento** > **Actualización de software**. El número de compilación debe ser **10.0.0.0.10279**.

	> [AZURE.NOTE] Las versiones de software se muestran de forma ligeramente distinta en la interfaz de usuario web local y el Portal de Azure clásico. La misma versión aparece como **10.0.0.0.10279** en la interfaz de usuario web local y como **10.0.10279.0** en el Portal de Azure clásico.

	![actualizar dispositivo](./media/storsimple-ova-install-update-01/update9m.png)

## Pasos siguientes

Obtenga más información sobre la [administración de la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0525_2016-->