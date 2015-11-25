<properties
	pageTitle="Configuración de los servicios de Copia de seguridad de Azure para preparar la copia de seguridad de Windows Server | Microsoft Azure"
	description="Use este tutorial para aprender a utilizar el servicio Copia de seguridad en la nube de Microsoft Azure para crear copias de seguridad de Windows Server en la nube."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/03/2015" ms.author="jimpark"; "aashishr"/>

# Preparación del entorno de copia de seguridad de máquinas virtuales de Azure

Este artículo le guiará por el proceso de habilitación de la característica Copia de seguridad de Azure. Para realizar la copia de seguridad de Windows Server o el cliente de Windows en Azure, necesitará una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

>[AZURE.NOTE] Anteriormente, era necesario crear o adquirir un certificado X.509 v3 con el fin de registrar el servidor de copia de seguridad. Los certificados aún se admiten, pero ahora, para facilitar el proceso de registro de almacenes de Azure en un servidor, puede generar una credencial de almacén directamente en la página Inicio rápido.


## Antes de comenzar
Para hacer una copia de seguridad de los archivos y los datos de Windows Server en Azure, primero debe:

- **Crear un almacén de copia de seguridad**: cree un almacén en la consola de Copia de seguridad de Azure.
- **Descargar las credenciales de almacén**: en Copia de seguridad de Azure, cargue el certificado de administración que creó en el almacén.
- **Instalación de Azure Backup Agent y registro del servidor**: en Copia de seguridad de Azure, instale el agente y registre el servidor en el almacén de copia de seguridad.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Pasos siguientes
- [Copia de seguridad de Windows Server o el cliente de Windows](backup-azure-backup-windows-server.md)
- [Administración de Windows Server o el cliente de Windows](backup-azure-manage-windows-server.md)
- [Restauración de Windows Server o el cliente de Windows desde Azure](backup-azure-restore-windows-server.md)
- [Preguntas más frecuentes de Copia de seguridad de Azure](backup-azure-backup-faq.md)
- [Foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=Nov15_HO3-->