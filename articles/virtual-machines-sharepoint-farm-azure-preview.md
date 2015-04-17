<properties 
	pageTitle="Granja de servidores SharePoint" 
	description="Describe la nueva característica Granja de servidores de SharePoint disponible en el Portal de vista previa de Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="josephd"/>

#Granja de servidores SharePoint

Con Granja de servidores SharePoint, el portal de Microsoft Azure en vista previa crea automáticamente una granja de servidores SharePoint Server 2013 preconfigurados. Esto puede suponer un importante ahorro de tiempo si necesita una granja de SharePoint básica o de alta disponibilidad para un entorno de desarrollo y pruebas, o si va a evaluar SharePoint Server 2013 como solución de colaboración para su organización.

La granja de SharePoint básica consta de tres máquinas virtuales con esta configuración.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

Utilice esta configuración de granja simplificada para el desarrollo de aplicaciones de SharePoint en su primera evaluación de SharePoint 2013.

La granja de servidores de SharePoint de alta disponibilidad consta de nueve máquinas virtuales con esta configuración.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

Use esta configuración de granja para probar cargas de cliente más altas, la alta disponibilidad del sitio externo de SharePoint y la característica AlwaysOn de SQL Server para una granja de SharePoint. También puede usar esta configuración para el desarrollo de aplicaciones de SharePoint en un entorno de alta disponibilidad.
 
Para obtener los detalles de configuración de ambas granjas, consulte[Detalles de configuración de una granja de servidores SharePoint](../virtual-machines-sharepoint-farm-config-azure-preview/).

##Configuración paso a paso##

Para crear una granja de SharePoint con la plantilla de la granja de servidores de SharePoint, haga lo siguiente:

1. En el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/), haga clic en **Nuevo** > **Granja de servidores de SharePoint**.
2. En el panel **Crear una granja de SharePoint**, escriba el nombre de un grupo de recursos.
3. Escriba un nombre de usuario y una contraseña para una cuenta de administrador local en cada máquina virtual de la granja. Elija un nombre y una contraseña que sea difícil de adivinar, regístrelos y almacénelos en una ubicación segura.
4. Si desea una granja de alta disponibilidad, haga clic en **Habilitar alta disponibilidad**.
5. Para configurar controladores de dominio, haga clic en la flecha. Puede especificar un prefijo de nombre de host (el predeterminado es el nombre del grupo de recursos), un nombre de dominio raíz del bosque (el predeterminado es contoso.com) y el tamaño de los controladores de dominio (el predeterminado es A1).
6. Para configurar servidores SQL Server, haga clic en la flecha. Puede especificar un prefijo de nombre de host (el predeterminado es el nombre del grupo de recursos), el tamaño de los servidores SQL Server (el predeterminado es A5), un nombre y una contraseña para la cuenta de acceso a la base de datos (la opción predeterminada es la cuenta de administrador) y un nombre de cuenta de servicio (el predeterminado es sqlservice) y una contraseña (la predeterminada es la misma que la de la cuenta de administrador) del servidor SQL Server.
7. Para configurar servidores SharePoint, haga clic en la flecha. Puede especificar un prefijo de nombre de host (el predeterminado es el nombre del grupo de recursos), el tamaño de los servidores SharePoint (el predeterminado es A2), una cuenta de usuario (el predeterminado es sp_setup) y una contraseña de SharePoint, un nombre de cuenta para la granja de SharePoint (el predeterminado es sp_farm) y una contraseña y una frase de contraseña para la granja de SharePoint. La opción predeterminada es usar la contraseña de administrador para la cuenta de usuario de SharePoint, la cuenta para la granja y la frase de contraseña.
8. Para establecer la configuración opcional (red virtual, cuenta de almacenamiento, diagnósticos), haga clic en la flecha.
9. Para especificar la suscripción, haga clic en la flecha.
10. Cuando haya terminado, haga clic en **Crear**.

##Acceso y administración de granjas de servidores de SharePoint##

Las granjas de SharePoint tienen un extremo previamente configurado para permitir el tráfico web no autenticado (puerto TCP 80) al servidor web de SharePoint para un equipo cliente conectado a Internet. Este extremo es para un sitio de equipo preconfigurado. Para obtener acceso a este sitio de equipo:

1.	En el Portal de vista previa de Azure, haga clic en **Examinar** y, a continuación, haga clic en **Grupos de recursos**. 
2.	En la lista de grupos de recursos, haga clic en el nombre del grupo de recursos de su granja de SharePoint.
3.	En el panel del grupo de recursos de su granja de SharePoint, haga clic en **Historial de implementaciones**. 
4.	En el panel **Historial de implementaciones**, haga clic en **Microsoft.SharePointFarm**.
5.	En el panel **Microsoft.SharePointFarm**, seleccione la URL del campo SHAREPOINTSITEURL y cópiela. 
6.	Desde su explorador de Internet, pegue la URL en el campo de direcciones.
7.	Cuando el sistema lo solicite, escriba las credenciales de cuenta de usuario que especificó cuando creó la granja.

En el sitio de Administración central de SharePoint, puede configurar Mis sitios, aplicaciones de SharePoint y más funcionalidad. Para obtener más información, consulte [Configuración de SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). Para obtener acceso al sitio de SharePoint de administración central:

1.	En el Portal de vista previa de Azure, haga clic en **Examinar** y, a continuación, haga clic en **Grupos de recursos**. 
2.	En la lista de grupos de recursos, haga clic en el nombre del grupo de recursos de su granja de SharePoint.
3.	En el panel del grupo de recursos de su granja de SharePoint, haga clic en **Historial de implementaciones**. 
4.	En el panel **Historial de implementaciones**, haga clic en **Microsoft.SharePointFarm**.
5.	En el panel **Microsoft.SharePointFarm**, seleccione la URL del campo SHAREPOINTCENTRALADMINURL y cópiela. 
6.	Desde su explorador de Internet, pegue la URL en el campo de direcciones.
7.	Cuando el sistema lo solicite, escriba las credenciales de cuenta de usuario que especificó cuando creó la granja.


Notas:

- El portal de Azure en vista previa crea estas máquinas virtuales en su suscripción.
- El portal de Azure en vista previa crea los dos tipos de granja en una red virtual solo en la nube con presencia web orientada a Internet. No hay una conexión VPN de sitio a sitio con la red de su organización. 
- Puede administrar estos servidores a través de conexiones de Escritorio remoto.


##Administrador de recursos de Azure##

Granja de servidores SharePoint utiliza el Administrador de recursos de Azure y scripts para crear automáticamente la infraestructura y las configuraciones de servidor para estas granjas de SharePoint. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

##Recursos adicionales

[SharePoint en los servicios de infraestructura de Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Detalles de configuración de una granja de servidores de SharePoint](../virtual-machines-sharepoint-farm-config-azure-preview/)

[Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](http://azure.microsoft.com/documentation/articles/virtual-networks-setup-sharepoint-hybrid-cloud-testing/)

<!--HONumber=47-->
