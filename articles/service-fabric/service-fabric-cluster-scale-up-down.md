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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# Escalado o reducción vertical de un clúster de Service Fabric agregando o quitando máquinas virtuales (VM) de él.

Puede escalar o reducir verticalmente clústeres de Service Fabric para satisfacer a la demanda agregando o quitando máquinas virtuales.

>[AZURE.NOTE]Se supone que su suscripción tiene suficientes núcleos para agregar las nuevas máquinas virtuales que conformarán este clúster.


## Escalado manual de un clúster de Service Fabric

Si el clúster tiene varios tipos de nodo, tendrá que agregar o quitar máquinas virtuales de determinados tipos de nodo. En la misma implementación puede agregar máquinas virtuales a un tipo de un nodo y quitarlas de otro.

### Actualización de un clúster que había implementado mediante el portal

Se trata de un proceso complejo, por lo que hemos cargado un módulo de PowerShell en un repositorio Git para que se encargue de ello.

**Paso 1**: Copie esa carpeta en la máquina desde este [repositorio Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Paso 2**: Asegúrese de que Azure SDK 1.0+ está instalado en su equipo.

**Paso 3**: Abra una ventana de PowerShell e importe el certificado ServiceFabricRPHelpers.psm

```
Remove-Module ServiceFabricRPHelpers
```

Importe el módulo de PowerShell que acaba de copiar. Simplemente, puede copiar el siguiente cmd y cambiar la ruta de acceso a .psm1 para que sea la de su equipo.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **Paso 4**: Inicie sesión en su cuenta de Azure

```
Login-AzureRmAccount
```

Ejecute el comando Invoke-ServiceFabricRPClusterScaleUpgrade, asegúrese de que el nombre del grupo de recursos y la suscripción son correctos.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

Este es un ejemplo ya modificado del mismo comando PS

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **Paso 5**: El comando recuperará ahora la información de clúster y le guiará a través de todos los tipos de nodo, indicándole primero el número actual de máquinas virtuales/nodos para ese tipo de nodo y, a continuación, pidiéndole que proporcione la información sobre el nuevo número de máquinas virtuales/nodos que debe haber.

 **Para escalar verticalmente este tipo de nodo**, especifique un número mayor al recuento actual de máquinas virtuales.

**Para reducir verticalmente este tipo de nodo**, especifique un número menor al recuento actual de máquinas virtuales.

Estos mensaje irán enlazando para todos los tipos de nodo. Si el clúster tiene solo un tipo de nodo, se le pedirán datos solo una vez.
 
  **Paso 6**: Si va a agregar nuevas máquinas virtuales, ahora le aparecerá un mensaje para que proporcione el identificador de usuario de escritorio remoto y la contraseña para las máquinas virtuales que vaya a agregar.
 
**Paso 7**: En la ventana de resultados, irá viendo mensajes que le informan del progreso de la implementación. Una vez completada la implementación, el clúster debe tener el número de nodos que especificó en el Paso 5.


![ScaleupDownPSOut][ScaleupDownPSOut]


**Paso 8**: Si se trataba de una solicitud de reducción vertical, le queda un paso más, el de la eliminación de las máquinas virtuales. El script desactiva todas las máquinas virtuales para las que solicitó la retirada, es decir, en estos nodos/máquinas virtuales no existen réplicas de aplicación o sistema. Así que ahora es seguro eliminar esas máquinas virtuales.

**NOTA**: Aunque el clúster SF ya no usa los nodos desactivados, debe eliminar las máquinas virtuales desactivadas para que no se las cobremos.

[Paso 8.1](http://aka.ms/servicefabricportal): Inicie sesión en el Portal de Azure **http://aka.ms/servicefabricportal**.

**Paso 8.2**: Busque el recurso de clúster en el que estaba realizando la reducción vertical y haga clic en "Todas las configuraciones" en el elemento web Essentials.

**Paso 8.3**: Haga clic en Tipos de nodo, verá una lista de nodos que están desactivados. En esta imagen, chackodnt15, chackodnt24, chackodnt25 y chackodnt26 son las máquinas virtuales que hay que eliminar.

![DeactivatedNodeList][DeactivatedNodeList]

**Paso 8.4**: Elimine esas máquinas virtuales a través de PS o del Portal. Una vez que se eliminan, ahora ha terminado con la reducción vertical del clúster.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
Este es un ejemplo ya modificado de ese comando

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### Actualización de un clúster que había implementado mediante la CLI o ARM con PowerShell

Si implementó el clúster mediante una plantilla ARM inicialmente, tiene que modificar el número de las máquinas virtuales para un tipo de nodo determinado y todos los recursos que admiten la máquina virtual. El número mínimo de máquinas virtuales que se permite para el tipo de nodo principal es 5 (excepto para los clústeres de prueba), para los clústeres de prueba el mínimo es 3.

Una vez que tenga la nueva plantilla, puede implementarla a través de ARM con el mismo grupo de recursos que el clúster que va a actualizar.


## Escalado manual del clúster de Service Fabric

En este momento, los clústeres de Service Fabric no admiten el escalado automático. En un futuro cercano, los clústeres se integrarán en conjuntos de escala de máquina virtual (VMSS) y en ese momento el escalado automático será posible y se comportará de manera similar al comportamiento de escala automática disponible en los Servicios en la nube.


## Pasos siguientes

- [Obtener información sobre actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Obtener información sobre los servicios con estado de creación de particiones para una escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->