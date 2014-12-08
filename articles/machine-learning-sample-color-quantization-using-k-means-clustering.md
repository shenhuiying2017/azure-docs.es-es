<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Ejemplo de aprendizaje automático: cuantificación del color usando la agrupación en clúster de K-Means | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Ejemplo de Aprendizaje automático de Azure: cuantificación del color usando la agrupación en clúster de K-Means

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset


##Descripción del problema

[Cuantificación del color](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") es el proceso de reducir el número de colores distintos de una imagen comprimiéndolos. Normalmente, el intento de preservar la apariencia del color de la imagen lo más posible y reducir al mismo tiempo el número de colores, ya sea por limitaciones de memoria o por compresión. 

##Datos

En este experimento de ejemplo, asumimos que cualquier imagen RGB dada de 24 bits tiene 256 x 256 x 256 colores posibles. Y seguro que podemos crear histogramas de colores estándar basados en estos valores de intensidad. Pero otro enfoque es cuantificar explícitamente la imagen y *reducir* el número de colores a, pongamos, 16 o 64. De esta forma se crea un espacio considerablemente más pequeño e (idealmente) menos ruido y varianza. Para ello, pasamos los datos de píxeles (cada píxel como una fila del conjunto de datos) a nuestro módulo de agrupación en clúster de K-Means. 

##Modelo

El modelo se crea como se muestra en la siguiente imagen:

![Model][image1]

Ejecutamos la agrupación en clústeres de K-Means con K=10 hasta 500 en 5 ramas diferentes. Primero calculamos los clústeres y, a continuación, agregamos la agrupación en clústeres a la media de los colores de píxeles (mediante un script R). 
Finalmente, asociamos a cada píxel el color de clúster agregado y enviamos la nueva imagen en formato CSV. Mientras tanto, calculamos también la diferencia de la raíz cuadrada de los nuevos colores de píxeles con la imagen original y los mostramos en un gráfico R (mediante la opción para ejecutar el script R). 

##Resultados

Probamos los resultados en diferentes números de clústeres (colores) como se muestra en el siguiente modelo de experimento. Como puede ver a continuación, cuanto más agrupación en clústeres haya, mayor será la calidad de las imágenes y menor será la compresión:

||
------------ | ---------
**Original** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


También hemos medido la exactitud usando la diferencia de raíz cuadrada con los colores de la imagen original que se pueden ver desde el segundo puerto de salida del módulo de ejecución del script R:

![Output of Execute R Script module][image3]

Como se puede ver, cuantos más clústeres de colores, más colores coinciden con las imágenes originales (mejor calidad). 

##Código para convertir las imágenes en CSV y a la inversa

Para insertar las imágenes en Estudio de aprendizaje automático, escribimos un sencillo código de conversión que puede convertir los archivos de imágenes a un formato csv que se puede usar en Estudio de aprendizaje automático, y también uno que los convierte de nuevo en una imagen. Utilice el siguiente código libremente. En el futuro, estamos planeando agregar también un módulo para leer imágenes. 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimento de ejemplo - Compresión de imágenes basada en colores mediante agrupaciones en clústeres de K-Means - Desarrollo**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Imagen RGB de Bill Gates
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

