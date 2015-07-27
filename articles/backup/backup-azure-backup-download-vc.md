<properties
   pageTitle="Descarga de credenciales de almacén de Copia de seguridad de Azure | Microsoft Azure"
   description="Aprenda a usar las credenciales de almacén para autenticar su máquina con el almacén de copia de seguridad y el servicio de Copia de seguridad de Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""/> <tags ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Uso de las credenciales de almacén para autenticarse con el servicio de Copia de seguridad de Azure
El servidor local (cliente de Windows, Windows Server o servidor SCDPM) debe autenticarse con un almacén de copia de seguridad antes de crear una copia de seguridad de datos en Azure. La autenticación se realiza mediante las "credenciales de almacén". El concepto de credenciales de almacén es similar al concepto de un archivo de "configuración de publicación" que se usa en Azure PowerShell.

## ¿Qué es el archivo de credenciales de almacén?
El archivo de credenciales de almacén es un certificado que se genera mediante el portal para cada almacén de copia de seguridad. El portal, a continuación, carga la clave pública en el servicio de Control de acceso (o ACS). La clave privada del certificado estará disponible para el usuario como parte del flujo de trabajo que se proporciona como entrada del flujo de trabajo de registro de máquina. Esto autentica la máquina para enviar datos de copia de seguridad a un almacén identificado en el servicio de Copia de seguridad de Azure.

Merece la pena indicar que la credencial de almacén se utiliza solo durante el flujo de trabajo de registro. Es responsabilidad del usuario asegurarse de que el archivo de credenciales de almacén no se ve comprometido. Si cae en manos de un usuario no autorizado, el archivo de credenciales de almacén puede utilizarse para registrar otras máquinas en el mismo almacén. Sin embargo, como los datos de copia de seguridad se cifran mediante una frase de contraseña que pertenece al cliente, los datos de copia de seguridad existentes no pueden estar en peligro. Para mitigar este problema, las credenciales de almacén están definidas para caducar en 48 horas. Puede descargar las credenciales de almacén de un almacén de copia de seguridad cualquier número de veces, pero solo el último archivo de credenciales de almacén es aplicable durante el flujo de trabajo de registro.

## Descargar el archivo de credenciales de almacén
El archivo de credenciales de almacén se descarga a través de un canal seguro desde el Portal de Azure. El servicio de Copia de seguridad de Azure no conoce la clave privada del certificado, y la clave privada no se conserva en el portal o el servicio. Siga estos pasos para descargar la credencial de almacén en una máquina local.

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).
2.  Haga clic en **Servicios de recuperación** en el panel de navegación izquierdo y seleccione el almacén de copia de seguridad que ha creado.
3. Haga clic en el icono de nube para obtener la vista Inicio rápido del almacén de copia de seguridad.

    ![Vista rápida](./media/backup-azure-backup-download-vc/quickview.png)

4.  En la página de Inicio rápido, haga clic en **Descargar credenciales de almacén**. El portal genera el archivo de credenciales del almacén, que está disponible para su descarga.

    ![Descargar](./media/backup-azure-backup-download-vc/downloadvc.png)

5.  El portal generará una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de almacén en la carpeta de descargas de la cuenta local, o seleccione Guardar como desde el menú Guardar para especificar una ubicación para las credenciales de almacén.

## Notas
- Desde marzo de 2015, los usuarios no tienen una forma mediante programación (p. ej.: PowerShell) de descargar credenciales de almacén.
- Asegúrese de que las credenciales de almacén se guardan en una ubicación a la que se puede obtener acceso desde la máquina. Si se almacenan en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.
- El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro.
- El archivo de credenciales de almacén caduca después de 48 horas y puede descargarse desde el portal.
- Para cualquier pregunta sobre el flujo de trabajo, vea las [Preguntas más frecuentes](backup-azure-backup-faq.md) sobre la Copia de seguridad de Azure.

## Pasos siguientes
[Descarga, registro e instalación del agente de Copia de seguridad de Azure](backup-azure-backup-download-register)

<!---HONumber=July15_HO3-->