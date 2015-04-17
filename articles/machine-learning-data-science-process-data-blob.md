<properties 
	pageTitle="Procesar datos en  Blob de Azure" 
	description="Procesar datos en Blob de Azure" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="sunliangms,fashah,msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,fashah,msolhab,garye" /> 

#<a name="heading"></a>Procesar datos de Blob de Azure en su entorno de ciencia de datos

En este documento se trata la exploración de datos y generación de características a partir de los datos almacenados en un Blob de Azure. Para ello, se deben descargar los datos desde el origen de blob en un archivo local que se pueda cargar en una trama de datos de Pandas para su exploración y manipulación. Estos son los pasos que se deben seguir:

1. Descargue los datos del blob de Azure con el siguiente código de Python de ejemplo mediante el servicio BLOB. Reemplace la variable en el código siguiente por sus valores específicos: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Lea los datos en una trama de datos de Pandas desde el archivo descargado.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

Ya puede explorar los datos y generar características en este conjunto de datos.

####<a name="blob-dataexploration"></a>Exploración de datos

A continuación, se muestran algunos ejemplos de formas de explorar datos mediante Pandas:

1. Inspeccionar el número de filas y columnas 

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Inspeccionar las primeras o las últimas filas del conjunto de datos, como se indica a continuación:

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. Comprobar el tipo de datos como el que se importó cada columna mediante el siguiente código de ejemplo
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Comprobar las estadísticas básicas de las columnas del conjunto de datos de la siguiente forma
 
		dataframe_blobdata.describe()
	
5. Observar el número de entradas de cada valor de columna, como se indica a continuación

		dataframe_blobdata['<column_name>'].value_counts()

6. Contar los valores que faltan frente al número real de entradas de cada columna, mediante el siguiente código de ejemplo

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	Si hay valores que faltan para una columna determinada en los datos, puede quitarlos como se indica:

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	Otra forma de reemplazar los valores que faltan es a través de la función de modo:
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. Crear un gráfico de histograma con un número variable de discretizaciones para trazar la distribución de una variable	
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. Examinar las correlaciones entre las variables mediante un gráfico de dispersión o con la función de correlación integrada

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()
	
	
####<a name="blob-featuregen"></a>Generación de características
	
Es posible generar características con Python de la siguiente manera:

#####<a name="blob-countfeature"></a>Generación de características basada en el valor de indicador

Las características de categorías se pueden crear como sigue:

1. Inspeccione la distribución de la columna de categorías:
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Genere valores de indicador para cada uno de los valores de columna

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Combine la columna de indicador con la trama de datos original 
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Quite la propia variable original:

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
#####<a name="blob-binningfeature"></a>Generación de características de discretización

Para generar características discretizadas, se procede de la siguiente manera:

1. Agregue una secuencia de columnas para discretizar una columna numérica
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Convierta la discretización en una secuencia de variables booleanas

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Por último, combine las variables de prueba con la trama de datos original

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

####<a name="sql-featuregen"></a>Volver a escribir datos en un blob de Azure y consumirlos en Aprendizaje automático de Azure

Cuando haya explorado los datos y haya creado las características necesarias, puede cargar los datos (muestreados o con características) en un blob de Azure y consumirlos en Aprendizaje automático de Azure, mediante los siguientes pasos:
Tenga en cuenta que se pueden crear características adicionales en el Estudio de aprendizaje automático de Azure. 
1. Escriba la trama de datos en el archivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargue los datos en el blob de Azure como se indica a continuación:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Ahora se pueden leer los datos del blob mediante  *Reader Module* de Aprendizaje automático de Azure, como se muestra en la pantalla siguiente:
 
![reader blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png

<!--HONumber=49-->