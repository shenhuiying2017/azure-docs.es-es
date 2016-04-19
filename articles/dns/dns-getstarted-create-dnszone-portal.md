<properties
   pageTitle="Creación y administración de una zona DNS en el Portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear zonas DNS para DNS de Azure. Esta es una guía paso a paso para crear y administrar su primer DNS y comenzar a hospedar el dominio DNS con el Portal de Azure."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Creación y administración de una zona DNS en el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI de Azure](dns-getstarted-create-dnszone-cli.md)



En este artículo encontrará una guía de los pasos necesarios para crear una zona DNS con el Portal de Azure. También puede crear una zona DNS con PowerShell o la CLI.

El dominio "contoso.com" puede contener una serie de registros DNS como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para comenzar a hospedar el dominio, deberá crear primero una zona DNS. Todos los registros DNS creados para un dominio concreto se ubicarán dentro de una zona DNS del dominio.

### <a name="names"></a>Acerca de los nombres de la zona DNS
 
- El nombre de la zona debe ser único en el grupo de recursos y la zona no debe existir aún. De lo contrario, la operación presentará un error.

- El mismo nombre de zona podrá reutilizarse en un grupo de recursos distinto o en una suscripción a Azure diferente. Cuando varias zonas comparten el mismo nombre, a cada instancia se le asignarán distintas direcciones de servidores de nombres, y solo se podrá delegar una instancia desde el dominio primario. Consulte [Delegación de un dominio a DNS de Azure](#delegate) para obtener más información.

### Acerca de las etiquetas para DNS de Azure


Las etiquetas son una lista de pares nombre-valor que Azure Resource Manager usa para etiquetar los recursos con fines de facturación o agrupación. Si desea más información sobre las etiquetas, consulte el artículo [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

Puede agregar etiquetas en el Portal de Azure en la hoja **Configuración** de la zona DNS.


## Creación de una zona DNS

1. Inicie sesión en el Portal de Azure.

2. En el menú Concentrador, haga clic en **Nuevo > Redes >** y, luego, en **Zona DNS** para abrir la hoja de la zona DNS.
 
	![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. En la hoja **Zona DNS**, haga clic en **Crear** en la parte inferior. Se abrirá la hoja **Crear zona DNS**.

	![Crear zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. En la hoja **Crear zona DNS**, asígnele un nombre a la zona DNS. Por ejemplo, *contoso.com*. [Consulte Acerca de los nombres de la zona DNS](#names) en la sección anterior.

5. A continuación, especifique el grupo de recursos que desea usar. Puede crear un grupo de recursos o seleccionar uno ya existente.

6. En el menú desplegable **Ubicación**, especifique la ubicación del grupo de recursos. Observe que esta configuración se refiere a la ubicación del grupo de recursos y no a la ubicación de la zona DNS. El recurso de la zona DNS real pasa automáticamente a ser global y no es algo que se pueda especificar en el portal (ni tampoco es necesario hacerlo).

7. Puede dejar activada la casilla **Anclar al panel** si desea ubicar fácilmente la zona nueva en el panel. A continuación, haga clic en **Crear**.

	![Anclar al panel](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. Una vez que hace clic en Crear, verá la nueva zona configurada en el panel.

	![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. Una vez que se crea la zona nueva, en el panel se abrirá la hoja correspondiente a esta zona nueva.


## Ver registros de la zona DNS

Cuando se crea una zona DNS, también se crean los siguientes registros:

- El registro "Inicio de autoridad" (SOA). El SOA se encuentra en la raíz de cada zona DNS.
- Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para más información.

Puede consultar los registros en el Portal de Azure.

1. En la hoja **Zona DNS**, haga clic en **Toda la configuración** para abrir la hoja **Configuración** correspondiente a la zona DNS. 

	![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. En la parte inferior del panel de elementos esenciales, puede consultar los conjuntos de registros correspondientes a la zona DNS.


	![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)



## Eliminar una zona DNS

Puede eliminar la zona DNS directamente desde el portal. Antes de eliminar una zona DNS de DNS de Azure, deberá eliminar todos los conjuntos de registros, salvo los registros NS y SOA de la raíz de la zona que se crearon automáticamente cuando se creó la zona.

1. Ubique la hoja **Zona DNS** correspondiente a la zona que desea eliminar y, luego, haga clic en **Eliminar** en la parte superior de la hoja.
 
2. Aparecerá un mensaje que indica que debe eliminar todos los conjuntos de registros, excepto los registros NS y SOA que se crearon automáticamente. Si eliminó los conjuntos de registros, haga clic en **Sí**. Tenga en cuenta que cuando elimina una zona DNS del portal, no se eliminará el grupo de recursos con que está asociada la zona DNS.


## Pasos siguientes

Después de crear la zona DNS, consulte [Introducción a la creación de registros y conjuntos de registros](dns-getstarted-create-recordset-portal.md), [Administración de zonas DNS](dns-operations-dnszones.md) y [Administración de registros DNS](dns-operations-recordsets-portal.md).

<!---HONumber=AcomDC_0406_2016-->