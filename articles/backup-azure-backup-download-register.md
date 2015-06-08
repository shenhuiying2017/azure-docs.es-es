<properties
	pageTitle="Descargar, instalar y registrar el agente de Copia de seguridad de Azure"
	description="Aprenda cómo y dónde descargar el agente de Copia de seguridad de Azure, los pasos de instalación y cómo registrar el agente de Copia de seguridad de Azure mediante las credenciales de almacén"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Descargar, instalar y registrar el agente de Copia de seguridad de Azure

Después de crear el almacén de Copia de seguridad de Azure, debe instalarse un agente en cada uno de sus servidores locales (Windows Server, el cliente de Windows o el servidor de System Center Data Protection Manager) que le permita crear copias de seguridad de datos y aplicaciones en Azure. Este artículo describe los pasos necesarios para configurar el agente de Copia de seguridad de Azure de un servidor de Windows o de una máquina cliente de Windows.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).

2. Haga clic en **Servicios de recuperación** y seleccione el almacén de copia de seguridad que desee registrar en un servidor. Aparecerá la página de inicio rápido del almacén de copia de seguridad. <br/> ![Inicio rápido][1]

3. En la página de inicio rápido, haga clic en **Para Windows Server o cliente de System Center Data Protection Manager o de Windows** en la opción **Descargar agente**. Haga clic en Guardar para copiarlo en la máquina local. <br/> ![Guardar agente][2]

4. Una vez que el agente está instalado, haga doble clic en MARSAgentInstaller.exe para iniciar la instalación del agente de Copia de seguridad de Azure. Elija la carpeta de instalación y la carpeta temporal requerido para el agente. La ubicación de caché especificada debe tener un espacio libre de al menos el 5% de los datos de copia de seguridad.

5.	Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy**, especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.

6.	El agente de Copia de seguridad de Azure instalará .NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.

7.	Una vez que el agente está instalado, haga clic en el botón **Continuar con el registro** para continuar con el flujo de trabajo. <br/> ![Registro][3]

8. En la pantalla de credenciales del almacén, busque y seleccione el archivo de almacén de credenciales que se descargó anteriormente. <br/> ![Credenciales de almacén][4] <br/> <br/>
> [AZURE.NOTE]El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo "El archivo de credenciales de almacén proporcionado ha caducado"), inicie sesión en el portal de Azure y vuelva a descargar el archivo de almacén de credenciales.
>
> Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.
>
> Si se produce un error de credencial de almacén no válida (por ejemplo, "Credenciales de almacén proporcionadas no válidas. El archivo está dañado o no tiene las últimas credenciales asociadas al servicio de recuperación", vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción Descargar credencial de almacén en el portal de Azure, en sucesión rápida. En este caso, solo es válido el segundo archivo de credenciales de almacén.

9. En la pantalla **Configuración de cifrado**, podría generar una frase de contraseña o proporcionar una frase de contraseña (mínimo de 16 caracteres); no olvide guardar la frase de contraseña en una ubicación segura. <br/> ![Cifrado][5] <br/> <br/>
> [AZURE.WARNING]Si la frase de contraseña se pierde u olvida; Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no tiene ninguna visibilidad de la frase de contraseña que utiliza el usuario final. Guarde el archivo en una ubicación, ya que puede ser necesario durante una operación de recuperación.

10. Al hacer clic en el botón **Finalizar**, la máquina se ha registrado correctamente en el almacén y ahora está lista para iniciar la copia de seguridad en Microsoft Azure. Puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Cambiar propiedades** en el complemento de mmc de Copia de seguridad de Azure. <br/> ![Cambiar propiedades][6]

## Pasos siguientes
+ Vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre instalación y configuración de la copia de seguridad de Azure si tiene alguna pregunta sobre el flujo de trabajo.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir-->