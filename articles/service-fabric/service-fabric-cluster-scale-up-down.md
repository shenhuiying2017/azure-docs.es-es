<properties
   pageTitle="Escalado o reducción vertical de Service Fabric | Microsoft Azure"
   description="Escalar o reducir verticalmente Service Fabric para satisfacer la demanda agregando o quitando máquinas virtuales"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Escalado o reducción vertical de un clúster de Service Fabric agregando o quitando máquinas virtuales (VM) de él.

Puede escalar o reducir verticalmente clústeres de Service Fabric para satisfacer a la demanda agregando o quitando máquinas virtuales.

>[AZURE.NOTE]Se supone que su suscripción tiene suficientes núcleos para agregar las nuevas máquinas virtuales que conformarán este clúster.


## Escalado manual de un clúster de Service Fabric

### Elección del tipo de nodo que se escalará

Si el clúster tiene varios tipos de nodo, ahora deberá agregar o quitar máquinas virtuales de determinados tipos de nodo.

1. Inicie sesión en el Portal de Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Navegue a los clústeres de Service Fabric ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource].

3. Seleccione el clúster que quiere escalar o reducir verticalmente.

4. Navegue hasta la hoja de configuración del panel del clúster. Si no ve la hoja de configuración, haga clic en Toda la configuración en la parte esencial del panel del clúster.

5. Haga clic en NodeTypes, que abrirán la hoja de lista NodeTypes.

7. Haga clic en el tipo de nodo que quiere escalar o reducir verticalmente, y que luego abrirá la hoja de detalles del tipo de nodo.

### Escalado vertical agregando nodos

Ajuste el número de máquinas virtuales hasta el que quiera y guárdelo. Los nodos o las máquinas virtuales se agregarán ahora una vez que se complete la implementación.

### Reducción vertical eliminando nodos

La eliminación de nodos es un proceso de dos pasos:

1. Ajuste el número de máquinas virtuales quiera y guárdelo. El punto de conexión inferior del control deslizante indica el requisito mínimos de la máquina virtual de ese NodeType.

  >[AZURE.NOTE]Debe mantener un mínimo de 5 máquinas virtuales para el tipo de nodo principal.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. Vuelva al panel del clúster y haga clic en el grupo de recursos. Abrirá la hoja del grupo de recursos.

3. Busque en Resumen o abra la lista de recursos haciendo clic en "...".

4. Haga clic en el nombre de la máquina virtual que el sistema había indicado que se puede eliminar.

5. Haga clic en el icono Eliminar para eliminar la máquina virtual.

## Escalado manual del clúster de Service Fabric

En este momento, los clústeres de Service Fabric no admiten el escalado automático. En un futuro cercano, los clústeres se integrarán en conjuntos de escala de máquina virtual (VMSS) y en ese momento el escalado automático será posible y se comportará de manera similar al comportamiento de escala automática disponible en los Servicios en la nube.

## Escalado mediante PowerShell/CLI

En este artículo se trata el escalado de clústeres mediante el portal. Sin embargo, puede realizar las mismas acciones desde la línea de comandos con comandos ARM en el recurso de clúster. La respuesta GET de ClusterResource ofrecerá la lista de nodos que se han deshabilitado.

## Pasos siguientes

- [Obtener información sobre actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Obtener información sobre los servicios con estado de creación de particiones para una escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->