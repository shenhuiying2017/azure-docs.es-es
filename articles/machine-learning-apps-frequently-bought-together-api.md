<properties title="Machine Learning example app: Frequently Bought Together" pageTitle="Aplicación de ejemplo de aprendizaje automático: artículos que con frecuencia se compran juntos | Azure" description="A Machine Learning web service that performs online shopping cart analysis to produce product recommendations of items frequently bought together from historical transactions provided by the user." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="coromt" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="coromt" /> 

# Aplicación de ejemplo de aprendizaje automático: artículos que con frecuencia se compran juntos
 
Este [servicio web]( https://datamarket.azure.com/dataset/amla/mba) de aprendizaje automático realiza análisis en línea del carro de la compra para producir recomendaciones de artículos que con frecuencia se compran juntos a partir de las transacciones históricas proporcionadas por el usuario. Las recomendaciones de artículos que con frecuencia se compran juntos ayudan a los compradores a identificar los productos de un catálogo que son más relevantes cuando se compra un artículo concreto. La presentación de estas recomendaciones de forma visible ha demostrado ser efectiva para que los distribuidores en línea aumenten las ventas. 
  
##Primeros pasos 
Después de haberse suscrito al [servicio web]( https://datamarket.azure.com/dataset/amla/mba) puede usar la [aplicación web de ejemplo de Artículos que con frecuencia se compran juntos](https://marketbasket.cloudapp.net/) para cargar fácilmente sus datos en un modelo y detectar los grupos de productos que con frecuencia se compran juntos.  Para usar la aplicación o la API, primero necesita su clave de API que puede obtener en la [página de la cuenta del mercado de datos de Azure](https://datamarket.azure.com/account)

##Uso del servicio web 

Este servicio contiene API para administrar y crear modelos para Artículos que con frecuencia se compran juntos, cargar las transacciones del historial y recuperar los conjuntos de artículos que con frecuencia se compran juntos mejor clasificados para un producto dado.  Se pueden encontrar ejemplos que demuestran cómo usar estas API en el [repositorio de Azure-MachineLearning-DataScience en GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether).

