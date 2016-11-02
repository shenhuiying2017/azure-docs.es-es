<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS con el Portal de Azure | Microsoft Azure"
   description="Creación de registros de host para DNS de Azure y generación de registros y conjuntos de registros con el Portal de Azure"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>



# Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)


Este artículo le guiará a través de la creación de registros y conjuntos de registros usando el Portal de Azure. Después de crear la zona DNS, agregue los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[AZURE.INCLUDE [incluir-DNS-acerca-de-los-registros](../../includes/dns-about-records-include.md)]


## Creación de un nuevo conjunto de registros y de un registro

En el ejemplo siguiente, se le guiará a través del proceso de creación de un conjunto de registros y un registro mediante el portal de Azure. Utilizaremos el tipo de registro DNS "A".

1. Inicie sesión en el portal.

2. Vaya a la hoja **Zona DNS** en la que quiera crear un conjunto de registros.

3. En la parte superior de la hoja **Zona DNS**, seleccione **Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

	![Nuevo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. En la hoja **Agregar conjunto de registros**, especifique un nombre para el conjunto de registros. Por ejemplo, podría llamar "**www**" a su conjunto de registros.

	![Agregar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Seleccione el tipo de registro que quiere crear. Por ejemplo, seleccione **A**.

6. Establezca el **TTL**. El período de vida predeterminado en el portal es una hora.

7. Agregue las direcciones IP; una por línea. Cuando use el nombre sugerido del conjunto de registros y el tipo de registro descrito anteriormente, se agregarán direcciones IP de tipo IPv4 al registro **A** para el conjunto de registros www.

8. Cuando termine de agregar direcciones IP, seleccione **Aceptar** en la parte inferior de la hoja. Se creará el conjunto de registros de DNS.


## Pasos siguientes

Para administrar el conjunto de registros y los registros, consulte [Administración de registros y conjuntos de registros DNS mediante el Portal de Azure](dns-operations-recordsets-portal.md).

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).

<!---HONumber=AcomDC_0817_2016-->