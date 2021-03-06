---
title: Muestreo de datos en SQL Server en Azure | Microsoft Docs
description: Muestreo de datos en SQL Server en Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 94b109fdc2c902f1452c143a84b20356e9d58df0


---
# <a name="a-nameheadingasample-data-in-sql-server-on-azure"></a><a name="heading"></a>Muestreo de datos en SQL Server en Azure
En este documento se trata cómo realizar una muestra de datos almacenados en SQL Server en Azure con SQL o con el lenguaje de programación Python. También se muestra cómo mover los datos de muestra a Aprendizaje automático de Azure guardándolos en un archivo, cargándolos en un blob de Azure y leyéndolos Estudio de aprendizaje automático de Azure.

El muestreo de Python usa la biblioteca ODBC [pyodbc](https://code.google.com/p/pyodbc/) para conectarse a SQL Server en Azure y la biblioteca [Pandas](http://pandas.pydata.org/) para realizar el muestreo.

> [!NOTE]
> En el código SQL de ejemplo en este documento se supone que los datos están en un servidor SQL Server en Azure. Si no es así, consulte el tema [Mover datos a un servidor SQL Server en una máquina virtual de Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) para obtener instrucciones sobre cómo mover los datos a SQL Server en Azure.
> 
> 

**¿Por qué realizar un muestreo de los datos?**
 Si el conjunto de datos que pretende analizar es grande, suele ser una buena idea reducirlo a un tamaño más pequeño, pero representativo, que sea más manejable. Esto facilita la comprensión y exploración de los datos, y el diseño de características. Su rol en el [proceso de ciencia de datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) es permitir la rápida creación de prototipos de las funciones de procesamiento de datos y de los modelos de aprendizaje automático.

El **menú** siguiente vincula a temas que describen cómo realizar un muestreo de datos desde varios entornos de almacenamiento. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarea de muestreo es un paso en el [proceso de ciencia de datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="a-namesqlausing-sql"></a><a name="SQL"></a>Uso de SQL
En esta sección se describen varios métodos con SQL para realizar un muestreo aleatorio simple con los datos de la base de datos. Elija un método basado en el tamaño de los datos y su distribución.

Los dos elementos siguientes muestran cómo utilizar newid en SQL Server para realizar el muestreo. El método que elija depende de lo aleatoria que desee que sea la muestra (se supone que pk_id del código de muestreo siguiente es una clave principal generada automáticamente).

1. Muestra aleatoria menos estricta
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Muestra más aleatoria 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample se puede usar para el muestreo, como se muestra a continuación. Podría ser un enfoque preferible si el tamaño de los datos es grande (suponiendo que los datos de las distintas páginas no están correlacionados) y para que la consulta se complete en un tiempo razonable.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Puede explorar y generar características de los datos muestreados almacenándolos en una nueva tabla
> 
> 

### <a name="a-namesql-amlaconnecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Conexión con Aprendizaje automático de Azure
Puede usar directamente las consultas de ejemplo anteriores en el módulo [Importar datos][import-data] de Azure Machine Learning para reducir los datos sobre la marcha y usarlos en un experimento de Azure Machine Learning. A continuación se muestra una captura de pantalla con el uso del módulo del lector para leer los datos de muestreo:

![lector sql][1]

## <a name="a-namepythonausing-the-python-programming-language"></a><a name="python"></a>Uso del lenguaje de programación Python
En esta sección se muestra cómo usar la [biblioteca pyodbc](https://code.google.com/p/pyodbc/) para establecer una conexión de ODBC a una base de datos de SQL Server en Python. La cadena de conexión de la base de datos se muestra a continuación: (reemplace servername, dbname, username y password por su configuración):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La biblioteca [Pandas](http://pandas.pydata.org/) en Python ofrece un amplio conjunto de herramientas de análisis de datos y estructuras de datos para la manipulación de datos para la programación en Python. El código siguiente lee una muestra de 0,1 % de los datos de una tabla de Base de datos SQL de Azure en datos de Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Ahora puede trabajar con los datos de muestreo en la trama de datos de Pandas. 

### <a name="a-namepython-amlaconnecting-to-azure-machine-learning"></a><a name="python-aml"></a>Conexión con Aprendizaje automático de Azure
Puede usar el siguiente código de ejemplo para guardar los datos muestreados reducidos en un archivo y cargarlos en un blob de Azure. Los datos del blob pueden leerse directamente en un experimento de Azure Machine Learning mediante el módulo [Importar datos][import-data]. Los pasos son los siguientes: 

1. Escribir la trama de datos de Pandas en un archivo local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Cargar el archivo local en un blob de Azure
   
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
3. Lea datos de Azure Blob mediante el módulo [Importar datos][import-data] de Azure Machine Learning, como se muestra en la captura de pantalla siguiente:

![lector de blobs][2]

## <a name="the-team-data-science-process-in-action-example"></a>Proceso de ciencia de datos en equipos en acción: ejemplo
Para ver un ejemplo de tutorial completo del proceso de ciencia de datos en equipos mediante un conjunto de datos público, consulte [Proceso de ciencia de datos en equipos en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



<!--HONumber=Nov16_HO3-->


