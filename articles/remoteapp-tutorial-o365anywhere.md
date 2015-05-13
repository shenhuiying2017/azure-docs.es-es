<properties
   pageTitle="Obtención de la misma experiencia de Office 365 en cualquier dispositivo"
   description="Aprenda a compartir cualquier aplicación de Office 365 con sus usuarios mediante RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/14/2015"
   ms.author="guscatal"/>


Obtención de la misma experiencia de Office 365 en cualquier dispositivo
===================


Este artículo tratará cómo implementar Office 365 en cualquier dispositivo de su empresa. Los usuarios pueden obtener las mismas capacidades y experiencia de interfaz de usuario en Android, Apple y Windows.

Para conseguirlo, usaremos Azure RemoteApp alojando Office 365 en máquinas virtuales escalables en Azure a las que pueden conectarse los usuarios. Este conjunto de máquinas virtuales es conocido como "colección en la nube".

----------


Creación de una colección en la nube
-------------
En primer lugar, después de que haber creado una cuenta de Azure, vaya a "RemoteApp" haciendo clic en el vínculo en el lado izquierdo. ![Visualización de RemoteApp de Azure en el Portal de Azure](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continúe haciendo clic en "Nuevo" en la parte inferior y en "Creación rápida" para crear así una colección. Proporcione un nombre, la región, la suscripción, el plan y la imagen "Office Proffesional 2013" que proporcionamos. ![Cuadro de diálogo Crear](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

Una vez que finalice el formulario, debería comenzar el proceso de creación de la colección. Esto puede tardar hasta una hora aproximadamente.

![En espera](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

Cuando el proceso haya finalizado, tendrá un aspecto similar al siguiente. Si hacemos clic en "Publicación", podemos ver que la mayoría de las aplicaciones de Office ya están publicadas. ![Colección creada](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![Aplicaciones publicadas](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

En este momento también puede agregar más usuarios para que tengan acceso a esta colección. Para ello, haga clic en "Acceso de usuario" ![Configurar el acceso del usuario](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG).

Ahora vamos a intentar la conexión a Office 365.

Conexión a Office 365
-------------
Vaya a https://www.remoteapp.windowsazure.com/ y haga clic en "instalar el cliente" para instalar el cliente de Azure RemoteApp en el dispositivo en que se encuentra. Las capturas de pantalla siguientes corresponden a Windows.

Una vez que se inicie la aplicación, se le pedirá que inicie sesión con su Live ID, utilice por ahora el mismo que el de su cuenta de Azure. Cuando haya iniciado sesión debería ver una notificación acerca de las nuevas invitaciones; haga clic ahí y aparecerá una lista como la que se muestra a continuación. Acepte la invitación que coincida con su correo electrónico de propietario de cuenta de Azure.

![Nueva invitación](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

Este es lo que aparece cuando hay invitaciones nuevas.

![Aceptar una aplicación](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Una vez que acepte la invitación, debería ver todas las aplicaciones de Office en el cliente de Azure RemoteApp.

![Lista de aplicaciones](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Al hacer clic en cualquiera de estas, la aplicación debería empezar en la máquina virtual de Azure y, con ello, habrá terminado. ¡Disfrute!

![iniciando](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![powerpoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)

<!--HONumber=52-->
