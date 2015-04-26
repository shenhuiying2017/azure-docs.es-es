<properties 
	pageTitle="Creación y administración de conexiones híbridas | Azure" 
	description="Obtenga información acerca de cómo crear una conexión híbrida, administrar la conexión e instalar el administrador de conexiones híbridas. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="mandia"/>


#Creación y administración de conexiones híbridas

En este tema se muestran los pasos para crear y administrar conexiones híbridas de Azure. 

Para conectar con un recurso local, siga estos pasos:

1. [Cree una conexión híbrida](#CreateHybridConnection) especificando el nombre de host o la dirección IP del recurso no local de la red privada.

2.	[Vincule el sitio web o el servicio móvil de Azure](#LinkWebSite) a la conexión híbrida.

3. [Instale el Administrador de conexiones híbridas](#InstallHCM) en el recurso local y conéctese a la conexión híbrida específica. El portal de Azure ofrece una experiencia con un solo clic para la instalación y la conexión.

4. [Administre las conexiones híbridas](#ManageHybridConnection) y sus claves de conexión.


##<a name="CreateHybridConnection"></a>Creación de una conexión híbrida

Una conexión híbrida se puede crear en el Portal de administración de Azure mediante sitios web **o** servicios BizTalk. 

**Para crear conexiones híbridas mediante Sitios web**, consulte [Conexión de un sitio web de Azure a un recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

**Para crear conexiones híbridas en los servicios de BizTalk, siga estos pasos**:

1. Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
<br/>Si no tiene un servicio de BizTalk existente, puede [crear un servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870).
3. Seleccione la pestaña Conexiones híbridas:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. Seleccione **Crear una conexión híbrida** o seleccione el botón **AGREGAR** en la barra de tareas. Escriba lo siguiente:

	<table border="1">
    <tr>
       <td><strong>Nombre</strong></td>
        <td>El nombre de la conexión híbrida debe ser único y no puede ser el mismo que el servicio de BizTalk. Puede escribir cualquier nombre pero sea concreto con su finalidad. Algunos ejemplos son:<br/><br/>
		Payroll<em>SQLServer</em><br/>
		SupplyList<em>SharepointServer</em><br/>
		Customers<em>OracleServer</em>
        </td>
    </tr>
    <tr>
        <td><strong>Nombre de host</strong></td>
        <td>Escriba el nombre de host completo, solo el nombre de host o la dirección IPv4 del recurso local. Algunos ejemplos son:
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domain</em>.corp.<em>yourCompany</em>.com
<br/>
<em>myHTTPSharePointServer</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>yourCompany</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Port</strong></td>
        <td>Escriba el número de puerto del recurso local. Por ejemplo, si va a usar un sitio web, escriba el puerto 80 o el puerto 443. Si va a usar SQL Server, escriba el puerto 1433.</td>
	</tr>
	</table>


5. Seleccione la marca de verificación. 

####Información adicional

- Se pueden crear varias conexiones híbridas. Consulte [Servicios de BizTalk: Gráfico de las ediciones](http://go.microsoft.com/fwlink/p/?LinkID=302279) para ver el número de conexiones permitidas. 
- Cada conexión híbrida se crea con un par de cadenas de conexión: las claves de aplicación que ENVÍAN y las claves locales que ESCUCHAN. Cada par tiene una clave principal y una secundaria. 


##<a name="LinkWebSite"></a>Vinculación de un sitio web o un servicio móvil de Azure

Para vincular el sitio web de Azure a una conexión híbrida existente, seleccione **usar una conexión híbrida existente** en la hoja Conexiones híbridas. Consulte [Conexión de un sitio web de Azure a un recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Para vincular el servicio móvil de Azure a una conexión híbrida existente, seleccione **agregar una conexión híbrida** al cambiar o crear un servicio móvil. Consulte [Servicios móviles de Azure y conexiones híbridas](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).


##<a name="InstallHCM"></a>Instalación del Administrador de conexiones híbridas en el entorno local

Después de que se crea una conexión híbrida, instale el Administrador de conexiones híbridas en el recurso local. Este se puede descargar desde el sitio web de Azure o desde el servicio de BizTalk. Pasos para los servicios de BizTalk: 

1. Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. En la barra de tareas, seleccione **Configuración local**:
<br/>
![On-Premises Setup][HCOnPremSetup]
5. Seleccione **Instalar y configurar** para ejecutar o descargar el Administrador de conexiones híbridas en el sistema local. 
6. Seleccione la marca de verificación para iniciar la instalación. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### Información adicional
- Las conexiones híbridas admiten recursos locales instalados en los siguientes sistemas operativos:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Después de instalar el Administrador de conexiones híbridas, tiene lugar lo siguiente: 

	- La conexión híbrida hospedada en Azure se configura automáticamente para usar la cadena de conexión de la aplicación principal. 
	- El recurso local se configura automáticamente para usar la cadena de conexión local principal.

- El Administrador de conexiones híbridas debe usar una cadena de conexión local válida para la autorización. El sitio web o el servicio móvil de Azure deben usar una cadena de conexión de aplicación válida para la autorización.


##<a name="ManageHybridConnection"></a>Administración de conexiones híbridas
Para administrar las conexiones híbridas, puede:

- Usar el portal de Azure para ir al servicio de BizTalk. 
- Use [API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

####Copia y regeneración de las cadenas de conexión híbridas

1. Inicie sesión en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Seleccione la conexión híbrida. En la barra de tareas, seleccione **Administrar conexión**:
<br/>
![Manage Options][HCManageConnection]
<br/>
**Administrar conexión** muestra las cadenas de conexión de aplicación y local. Puede copiar las cadenas de conexión o regenerar la clave de acceso usada en la cadena de conexión. 
<br/>
<br/>
**Si selecciona Regenerar**, cambia la clave de acceso compartido dentro de la cadena de conexión. Haga lo siguiente:
- En el Portal de administración de Azure, seleccione **Sincronizar claves** en la aplicación de Azure.
- Vuelva a ejecutar la **Configuración local**. Al volver a ejecutar la configuración local, el recurso local se configura automáticamente para usar la cadena de conexión principal actualizada.


####Uso de la directiva de grupo para controlar los recursos locales utilizados por una conexión híbrida

1. Descargue las plantillas administrativas del Administrador de conexiones híbridas.
2. Extraiga los archivos.
3. En el equipo que modifica la directiva de grupo, haga lo siguiente: 
- Copie los archivos .ADMX en la carpeta *%WINROOT%\PolicyDefinitions*.
- Copie los archivos .ADML en la carpeta *%WINROOT%\PolicyDefinitions\es-es*.

Una vez copiados, puede usar el Editor de directivas de grupo para cambiar la directiva.




## Pasos siguientes

- [Conexión de un sitio web de Azure a un recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Conexiones híbridas paso a paso: Conexión a SQL Server local desde un sitio web de Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Servicios móviles de Azure y conexiones híbridas](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [Introducción a las conexiones híbridas](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview)


## Otras referencias

- [API REST para administrar los servicios de BizTalk en Windows Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [Servicios de BizTalk: Gráfico de ediciones](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Creación un servicio de BizTalk mediante el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 

<!--HONumber=46--> 
