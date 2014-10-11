<properties linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Microsoft .NET Library for Serialization with Avro" pageTitle="Serialize data with the Microsoft .NET Library for Avro | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize data with the Microsoft .NET Library for Avro " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Serialización de datos con la biblioteca de Microsoft .NET para Avro

## Información general

En este tema se explica el uso de la biblioteca de Microsoft .NET para Avro para serializar objetos y otras estructuras de datos en secuencias con el fin de guardarlos en memoria, en una base de datos o en un archivo, y cómo deserializarlos para recuperar los objetos originales.

### Apache Avro

La biblioteca de Microsoft .NET para Avro implementa el sistema de serialización de datos Apache Avro para el entorno Microsoft.NET. Apache Avro proporciona un formato compacto de intercambio de datos binarios para la serialización. Utiliza [JSON][] para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes. Los datos serializados en un lenguaje se pueden leer en otro. Actualmente, se admiten C, C++, C#, Java, PHP, Python y Ruby. Encontrará información detallada sobre el formato en las [Especificaciones de Apache Avro][]. Tenga en cuenta que la versión actual de la biblioteca de Microsoft .NET para Avro no admite la parte de las llamadas a procedimiento remoto (RPC) de estas especificaciones.

La representación serializada de un objeto en el sistema Avro consta de dos partes: esquema y valor real. El esquema Avro describe el modelo de datos independiente del lenguaje de los datos serializados con JSON. Está presente en paralelo con una representación binaria de los datos. Tener el esquema aparte de la representación binaria permite escribir cada objeto sin sobrecargas por valor, lo que agiliza la serialización y reduce la representación.

### Escenario de Hadoop

El formato de serialización de Apache Avro se utiliza de forma generalizada en HDIngisht de Azure y otros entornos Apache Hadoop. Avro proporciona una forma práctica de representar estructuras de datos complejas dentro de un trabajo MapReduce de Hadoop. El formato de los archivos de Avro está diseñado para admitir el modelo de programación distribuida de MapReduce. La característica clave que permite la distribución es que los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto.

### Serialización en la biblioteca de Microsoft .NET para Avro

La biblioteca de .NET para Avro admite dos formas de serialización de objetos:

-   **Reflexión**: el esquema JSON para los tipos se compila automáticamente a partir de los atributos del contrato de datos de los tipos .NET que se van a serializar.
-   **Registro genérico**: el esquema JSON se especifica de forma explícita en un registro representado por la clase [**AvroRecord**][] cuando no hay tipos .NET para describir el esquema de los datos que se van a serializar.

Cuando tanto el escritor como el lector de la secuencia conocen el esquema de los datos, se pueden enviar los datos sin el esquema. Pero si este no es el caso, debe compartirse el esquema con un archivo contenedor de Avro. Se pueden especificar otros parámetros como el códec utilizado para la compresión de datos. Estos escenarios se explican e ilustran con más detalle en los ejemplos de código siguientes.

### Requisitos previos de la biblioteca de Microsoft .NET para Avro

-   [Microsoft .NET Framework 4.0][]
-   [Newtonsoft Json.NET][] (v5.0.5 o posterior)

Tenga en cuenta que la dependencia de Newtonsoft.Json.dll se descarga automáticamente con la instalación de la biblioteca de Microsoft .NET para Avro, procedimiento para el que se proporciona en la sección siguiente.

### Instalación de la biblioteca de Microsoft .NET para Avro

La biblioteca de Microsoft .NET para Avro se distribuye como un paquete de NuGet que se puede instalar desde Visual Studio usando el siguiente procedimiento:

-   Seleccione la pestaña **Proyecto** -\> **Administrar paquetes de NuGet**.
-   Busque "Microsoft.Hadoop.Avro" en el cuadro **Búsqueda en línea**.
-   Haga clic en el botón **Instalar** junto a **Microsoft .NET Library for Avro**.

Tenga en cuenta que la dependencia de Newtonsoft.Json.dll (\>= .5.0.5) se descarga también automáticamente con la biblioteca de Microsoft .NET para Avro.

## Guía para los ejemplos

En este tema se proporcionan cinco ejemplos que ilustran escenarios diferentes compatibles con la biblioteca de Microsoft .NET para Avro.

Los dos primeros muestran cómo serializar y deserializar datos en búferes de secuencias de memoria usando reflexión y registros genéricos. En estos dos casos se supone que el esquema está compartido entre lectores y escritores fuera de banda, de forma que no es necesario serializarlo con los datos en un archivo contenedor de Avro.

Los ejemplos tercero y cuarto muestran cómo serializar y deserializar datos en búferes de secuencias de memoria usando reflexión y un registro genérico con archivos contenedores de objetos de Avro. Cuando los datos se almacenan en un archivo contenedor de Avro, el esquema se almacena siempre con ellos porque debe compartirse para la deserialización.

La muestra que contiene los cuatro ejemplos primeros se puede descargar del sitio de [ejemplos de código de Azure][].

El quinto y último ejemplo muestra cómo usar un códec de compresión personalizado para archivos contenedores de objetos. Puede descargar una muestra que contiene el código para este ejemplo en el sitio de [ejemplos de código de Azure][1].

La biblioteca de Microsoft .NET para Avro está diseñada para funcionar con cualquier secuencia. En estos ejemplo, los datos se manipulan usando secuencias de memoria en lugar de secuencias de archivos o bases de datos por simplicidad y coherencia. El método que se elija en un entorno de producción dependerá de los requisitos exactos del escenario, el origen de datos y el volumen, restricciones de rendimiento y otros factores.

-   [**Serialización mediante el uso de reflexión**][]: El esquema JSON para los tipos que se van a serializar se compila automáticamente a partir de los atributos del contrato de datos.
-   [**Serialización mediante el uso de un registro genérico**][]: El esquema JSON se especifica de forma explícita en un registro cuando no hay ningún tipo .NET disponible para reflexión.
-   [**Serialización mediante el uso de archivos contenedores de objetos con reflexión**][]: El esquema JSON se serializa de forma implícita con los datos y se comparte con un archivo contenedor de Avro.
-   [**Serialización mediante el uso de archivos contenedores de objetos con un registro genérico**][]: El esquema JSON se serializa de forma explícita con los datos y se comparte con un archivo contenedor de Avro.
-   [**Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado**][]: El esquema JSON se serializa con los datos y se comparte en un archivo contenedor de Avro con una implementación personalizada para .NET del códec de compresión de datos Deflate.

## <a name="Scenario1"></a>Serialización mediante el uso de reflexión

El esquema JSON para los tipos se puede compilar automáticamente con la biblioteca de Microsoft .NET para Avro usando reflexión a partir de los atributos del contrato de datos de los objetos de C# que se van a serializar. La biblioteca de Microsoft .NET para Avro crea un objeto [**IAvroSeralizer<t>**][] para identificar los campos que se van a serializar.

En este ejemplo, los objetos (una clase **SensorData** con un miembro struct **Location**) se serializan en una secuencia de memoria que, a su vez, se deserializa. Después se compara el resultado con la instancia inicial para confirmar que el objeto **SensorData** recuperado es idéntico al original.

En este ejemplo, se supone que el esquema está compartido entre los lectores y escritores, por lo que no es necesario el formato de contenedor de objetos de Avro. Para ver un ejemplo de cómo serializar y deserializar datos en búferes de memoria usando reflexión con el formato de contenedor de objetos cuando debe serializarse el esquema con los datos, consulte [Serialización mediante el uso de archivos contenedores de objetos con reflexión.][**Serialización mediante el uso de archivos contenedores de objetos con reflexión**]

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft .NET Library for Avro
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using Reflection
            //No explicit schema definition is required - schema of serialized objects is automatically built
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set using sample Class and struct 
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using Reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="Scenario2"></a>Serialización mediante el uso de un registro genérico

Cuando no se puede usar reflexión porque los datos no se pueden representar mediante clases .NET con un contrato de datos, se puede especificar el esquema JSON de forma explícita en un registro genérico. En general, este método es más lento que el uso de reflexión y serializa para una clase de C# específica. En estos casos, el esquema de los datos puede ser dinámico también porque no se conoce hasta el momento de la compilación. Los datos representados como archivos de valores separados por comas (CSV) cuyo esquema se desconoce hasta que se transforman con el formato de Avro en tiempo de ejecución son un ejemplo de este tipo de escenario dinámico.

En este ejemplo se muestra cómo crear y usar un objeto [**AvroRecord**][] para especificar un esquema JSON de forma explícita, cómo rellenarlo con los datos y cómo serializarlo y deserializarlo. Después se compara el resultado con la instancia inicial para confirmar que el registro recuperado es idéntico al original.

En este ejemplo, se supone que el esquema está compartido entre los lectores y escritores, por lo que no es necesario el formato de contenedor de objetos de Avro. Para ver un ejemplo de cómo serializar y deserializar datos en búferes de memoria usando un registro genérico con el formato de contenedor de objetos cuando debe incluirse el esquema con los datos serializados, consulte [Serialización mediante el uso de archivos contenedores de objetos con un registro genérico][**Serialización mediante el uso de archivos contenedores de objetos con un registro genérico**].

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //This class contains all methods demonstrating
    //the usage of Microsoft .NET Library for Avro
    public class AvroSample
    {

        //Serialize and deserialize sample data set using Generic Record.
        //Generic Record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of Generic Record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a Memory Stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample Class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using Generic Record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="Scenario3"></a>Serialización mediante el uso de archivos contenedores de objetos y serialización con reflexión

Este ejemplo es similar al escenario del [primer ejemplo][**Serialización mediante el uso de reflexión**], donde el esquema se especifica de forma implícita con reflexión, excepto que aquí no se supone que el lector que lo deserializa lo conoce. Los objetos **SensorData** que se van a serializar y el esquema especificado de forma implícita se almacenan en un archivo contenedor de objetos representado por la clase [**AvroContainer**][].

En este ejemplo, los datos se serializan con [**SequentialWriter<sensordata>**][] y deserializan con [**SequentialReader<sensordata>**][**SequentialWriter<sensordata>**]. Después se compara el resultado con las instancias iniciales para asegurarse de la identidad.

Los datos del archivo contenedor de objetos se comprimen usando el códec de compresión predeterminado [**Deflate**][] de .NET Framework 4.0. Consulte el [último ejemplo][**Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado**] de este tema para ver cómo utilizar una versión más reciente del códec de compresión [**Deflate**][2] disponible en .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft .NET Library for Avro
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with Deflate codec
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will be compressed using Deflate codec
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="Scenario4"></a>Serialización mediante el uso de archivos contenedores de objetos y serialización con registro genérico

Este ejemplo es similar al escenario del [segundo ejemplo][**Serialización mediante el uso de un registro genérico**], donde el esquema se especifica de forma explícita con JSON, excepto que aquí no se supone que el lector que lo deserializa lo conoce.

El conjunto de datos de prueba se recopila en una lista de objetos [**AvroRecord**][] usando un esquema JSON definido de forma explícita y se almacenan en un archivo contenedor de objetos representado por la clase [**AvroContainer**][]. Este archivo contenedor crea un escritor que se usa para serializar los datos, descomprimidos, en una secuencia de memoria que se guarda después en un archivo. El parámetro [**Codex.Null**][] que se usa cuando se crea el lector es el que especifica que estos datos no se van a comprimir.

Los datos se leen después en el archivo y se deserializan en una colección de objetos. Esta colección se compara con la lista inicial de registros de Avro para confirmar que son idénticos.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;

        //This class contains all methods demonstrating
        //the usage of Microsoft .NET Library for Avro
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
            //Serialized data is not compressed
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file
                //Create a MemoryStream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will not be compressed (Null compression codec)
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializng the data
                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Generic Record to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="Scenario5"></a>Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado

El ejemplo siguiente muestra cómo usar un códec de compresión personalizado para archivos contenedores de objetos de Avro. Las [Especificaciones de Avro][] permiten el uso de un códec de compresión opcional (además de los predeterminados **Null** y **Deflate**). En este ejemplo no se implementa el códec totalmente nuevo Snappy (mencionado como códec opcional admitido en las [Especificaciones de Avro][3]). Se muestra cómo usar la implementación de .NET Framework 4.5 del códec [**Deflate**][2], que proporciona un algoritmo de compresión basado en la biblioteca de compresión [zlib][] mejor que el predeterminado de la versión .NET Framework 4.0.

    // 
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        #region Defining objects for serialization
        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET Codec class contains two methods 
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //which are the key ones for data compression.
        //To enable a custom codec one needs to implement these methods for the required codec

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //can not be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from Stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features 
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //Codec class uses classes for comressed and decompressed streams defined above
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified Codec Factory to be used in Reader
        //It will catch the attempt to use "deflate" and provide Custom Codec 
        //For all other cases it will rely on the base class (CodecFactory)
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft .NET Library for Avro
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
            //
            //This sample uses Memory Stream for all operations related to serialization, deserialization and
            //Object Container manipulation, though File Stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
                    //when Codec Factory is explicitly specified
                    //You may comment the line below if you want to use built-in Deflate (see next comment)
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    //Here the custom Codec Factory is introduced.
                    //For convenience the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File using Custom Codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

  [JSON]: http://www.json.org
  [Especificaciones de Apache Avro]: http://avro.apache.org/docs/current/spec.html
  [**AvroRecord**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.avrorecord.aspx
  [Microsoft .NET Framework 4.0]: http://www.microsoft.com/en-us/download/details.aspx?id=17851
  [Newtonsoft Json.NET]: http://james.newtonking.com/json
  [ejemplos de código de Azure]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923
  [1]: http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111
  [**Serialización mediante el uso de reflexión**]: #Scenario1
  [**Serialización mediante el uso de un registro genérico**]: #Scenario2
  [**Serialización mediante el uso de archivos contenedores de objetos con reflexión**]: #Scenario3
  [**Serialización mediante el uso de archivos contenedores de objetos con un registro genérico**]: #Scenario4
  [**Serialización mediante el uso de archivos contenedores de objetos con un códec de compresión personalizado**]: #Scenario5
  [**IAvroSeralizer<t>**]: http://msdn.microsoft.com/en-us/library/dn627341.aspx
  [**AvroContainer**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.container.avrocontainer.aspx
  [**SequentialWriter<sensordata>**]: http://msdn.microsoft.com/en-us/library/dn627340.aspx
  [**Deflate**]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.100).aspx
  [2]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.110).aspx
  [**Codex.Null**]: http://msdn.microsoft.com/en-us/library/microsoft.hadoop.avro.container.codec.null.aspx
  [Especificaciones de Avro]: http://avro.apache.org/docs/current/spec.html#Required+Codecs
  [3]: http://avro.apache.org/docs/current/spec.html#snappy
  [zlib]: http://zlib.net/
