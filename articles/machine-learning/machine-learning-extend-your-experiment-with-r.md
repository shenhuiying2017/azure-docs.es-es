<properties
	pageTitle="Extender el experimento con R | Microsoft Azure"
	description="Cómo extender la funcionalidad de Estudio de aprendizaje automático de Azure mediante el lenguaje R con el módulo Ejecutar script de R."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="garye" />


#Extender el experimento con R

Puede extender la funcionalidad de Estudio de aprendizaje automático mediante el lenguaje R con el módulo [Ejecutar script de R][execute-r-script].

Este módulo acepta varios conjuntos de datos de entrada y genera un solo conjunto de datos como salida. Puede escribir un script de R en el parámetro **Script de R** del módulo [Ejecutar script de R][execute-r-script].

Para tener acceso a cada puerto de entrada del módulo, se usa un código similar al siguiente:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Enumeración de todos los paquetes actualmente instalados

La lista de paquetes instalados puede cambiar. Para obtener la lista completa y actual de los paquetes instalados, incluida la descripción de cada paquete, escriba el código siguiente en el módulo [Ejecutar script R][execute-r-script]\:

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

Esto acción envía la lista de paquetes al puerto de salida del módulo [Ejecutar script R][execute-r-script]. Para ver la lista de paquetes, conecte un módulo de conversión, como [Convertir a CSV][convert-to-csv] con la salida izquierda del módulo [Ejecutar script R][execute-r-script], ejecute el experimento y luego haga clic en la salida del módulo de conversión y seleccione **Descargar**.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##Importación de paquetes

También puede importar paquetes que todavía no están instalados desde un repositorio de Estudio de aprendizaje automático de ensayo si usa los siguientes comandos en el módulo [Ejecutar script de R][execute-r-script] y el archivo de paquetes comprimido:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

donde el archivo `my_favorite_package.zip` contiene el archivo ZIP del paquete.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0824_2016-->