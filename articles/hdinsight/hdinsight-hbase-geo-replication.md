---
title: "Configuración de la replicación de HBase entre dos centros de datos | Microsoft Docs"
description: "Aprenda a configurar la replicación de HBase entre dos centros de datos y sobre los casos de uso para la replicación de clúster."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7590d02b-f745-450a-9daf-dd942db2d38d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 112e64a740354feb55692212e964d33be8f9c9b6


---
# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configuración de la replicación geográfica de HBase en HDInsight
> [!div class="op_single_selector"]
> * [Configuración de la conectividad VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
> * [Configuración de DNS](hdinsight-hbase-geo-replication-configure-dns.md)
> * [Configuración de la replicación de HBase](hdinsight-hbase-geo-replication.md) 
> 
> 

Aprenda a configurar la replicación de HBase entre dos centros de datos. Algunos casos de uso de replicación de clúster incluyen:

* Copia de seguridad y recuperación ante desastres
* Agregación de datos
* Distribución de datos geográficos
* Ingesta de datos en línea combinados con análisis de datos sin conexión

La replicación de clúster usa una metodología de inserción de origen. Un clúster de HBase puede ser un origen, un destino, o puede cumplir ambos roles a la vez. La replicación es asincrónica y el objetivo de la replicación es la coherencia eventual. Cuando el origen recibe una edición en una familia de columna con la replicación habilitada, esa edición se propaga a todos los clústeres de destino. Cuando se replican datos de un clúster a otro, se realiza un seguimiento del clúster de origen y todos los clústeres que ya han consumido los datos para evitar bucles de replicación. Para obtener más información, en este tutorial, configurará una replicación de origen y destino.  Para otras topologías de clúster, consulte [Guía de referencia de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Se trata de la tercera parte de la serie:

* [Configuración de la conectividad VPN entre dos redes virtuales][hdinsight-hbase-replication-vnet]
* [Configuración de DNS entre dos redes virtuales][hdinsight-hbase-replication-dns]
* Configuración de la replicación geográfica de HBase (este tutorial)

El siguiente diagrama ilustra las dos redes virtuales y la conectividad de red creada en [Configuración de la conectividad VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet] and [Configuración de DNS entre dos redes virtuales][hdinsight-hbase-replication-dns]: 

![Diagrama de red virtual de replicación de HBase para HDInsight][img-vnet-diagram]

## <a name="a-idprerequisitesaprerequisites"></a><a id="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Una estación de trabajo con Azure PowerShell**.
  
    Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte Uso del cmdlet Set-ExecutionPolicy.
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* **Red virtual dos de Azure con conectividad VPN y con DNS configurado**.  Para obtener instrucciones, consulte [Configuración de una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-replication-vnet], y [Configuración de DNS entre dos redes virtuales de Azure][hdinsight-hbase-replication-dns].

    Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Add-AzureAccount

    Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Aprovisionamiento de clústeres de HBase en HDInsight
En [Configuración de una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-replication-vnet], ha creado una red virtual en un centro de datos de Europa y una red virtual en un centro de datos de Estados Unidos. La red virtual dos está conectada a través de VPN. En esta sesión, se realizará el aprovisionamiento de un clúster de HBase en cada una de las redes virtuales. Más adelante en este tutorial, hará que uno de los clústeres de HBase replique el otro clúster de HBase.

El portal de Azure clásico no es compatible con el aprovisionamiento de clústeres de HDInsight con opciones de configuración personalizadas. Por ejemplo, establezca *hbase.replication* en *true*. Si establece el valor en el archivo de configuración después de realizar el aprovisionamiento en el clúster, perderá la configuración después de restablecer la imagen inicial del clúster. Para más información, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight][hdinsight-provision]. Una de las opciones para aprovisionar el clúster de HDInsight con opciones personalizadas es utilizar PowerShell de Azure.

**Para realizar el aprovisionamiento de un clúster de HBase en Contoso-VNet-EU** 

1. En la estación de trabajo, abra Windows PowerShell ISE.
2. Establezca las variables al principio del script y, a continuación, ejecútelo.
   
        # create hbase cluster with replication enabled
   
        $azureSubscriptionName = "[AzureSubscriptionName]"
   
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
   
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
   
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
   
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
   
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
   
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
   
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
   
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
   
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
   
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
   
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
   
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential

**Para realizar el aprovisionamiento de un clúster de HBase en Contoso-VNet-US** 

* Utilice el mismo script con los siguientes valores:
  
        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'    
  
    Dado que ya se ha conectado a su cuenta de Azure, no necesitará ejecutar los comlets siguientes:
  
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName

## <a name="configure-dns-conditional-forwarder"></a>Configuración del reenviador condicional de DNS
En [Configuración de DNS entre dos redes virtuales][hdinsight-hbase-replication-dns], ha configurado servidores DNS para las dos redes. Los clústeres de HBase tienen sufijos de dominio diferentes. Por lo tanto, tendrá que configurar reenviadores condicionales DNS adicionales.

Para configurar el reenviador condicional, necesitará saber los sufijos de dominio de los dos clústeres de HBase. 

**Para buscar los sufijos de dominio de los dos clústeres de HBase**

1. RDP en **Contoso-HBase-EU**.  Para obtener instrucciones, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure clásico][hdinsight-manage-portal]. Es en realidad headnode0 del clúster.
2. Abra una consola de Windows PowerShell o un símbolo del sistema.
3. Ejecute **ipconfig** y anote el **sufijo DNS específico de la conexión**.
4. No cierre la sesión de RDP.  La necesitará más adelante para probar la resolución de nombres de dominio.
5. Repita los mismos pasos para averiguar el **sufijo DNS específico de la conexión** de **Contoso-HBase-US**.

**Para configurar reenviadores DNS**

1. RDP en **Contoso-DNS-EU**. 
2. Haga clic en la tecla Windows en la esquina inferior izquierda.
3. Haga clic en **Herramientas administrativas**.
4. Haga clic en **DNS**.
5. En el panel izquierdo, expanda **DSN**, **Contoso-DNS-EU**.
6. Haga clic en **Reenviadores condicionales** y, a continuación, haga clic en **Nuevo reenviador condicional**. 
7. Escriba la siguiente información:
   * **Dominio DNS**: escriba el sufijo DNS de Contoso-HBase-US. Por ejemplo: Contoso-HBase-US.f5.internal.cloudapp.net.
   * **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-US. Compruebe la dirección IP. El servidor DNS puede tener una dirección IP diferente.
8. Presione **ENTRAR** y luego haga clic en **Aceptar**.  Ahora podrá resolver la dirección IP de Contoso-DNS-US de Contoso-DNS-EU.
9. Repita los pasos para agregar un reenviador condicional de DNS al servicio DNS en la máquina virtual de Contoso-DNS-US con los siguientes valores:
   * **Dominio DNS**: escriba el sufijo DNS de Contoso-HBase-EU. 
   * **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-EU.

**Para probar la resolución de nombres de dominio**

1. Cambie a la ventana de Contoso-HBase-EU RDP.
2. Abra el símbolo del sistema.
3. Ejecute el comando ping:
   
        ping headnode0.[DNS suffix of Contoso-HBase-US]
   
    El protocolo ICM está activado en los nodos de trabajo de los clústeres de HBase
4. No cierre la sesión de RDP. La necesitará más adelante en el tutorial.
5. Repita los mismos pasos para hacer ping en headnode0 en Contoso-HBase-EU desde Contoso-HBase-US.

> [!IMPORTANT]
> DNS debe funcionar para poder continuar con los pasos siguientes.
> 
> 

## <a name="enable-replication-between-hbase-tables"></a>Habilitación de la replicación entre las tablas de HBase
Ahora, puede crear una tabla de HBase de ejemplo, habilitar la replicación y, a continuación, realizar la prueba con algunos datos. La tabla de ejemplo que se va a utilizar tiene dos familias de columnas: Personal y Office. 

En este tutorial, hará que el clúster de HBase de Europa sea el clúster de origen y el clúster de HBase de Estados Unidos el clúster de destino.

Cree tablas de HBase con los mismos nombres y familias de columna en los clústeres de origen y de destino, de manera que el clúster de destino sepa dónde almacenar los datos que va a recibir. Para más información sobre el uso del shell de HBase, consulte [Introducción a Apache HBase en HDInsight][hdinsight-hbase-get-started].

**Para crear una tabla de HBase en Contoso-HBase-EU**

1. Cambie a la ventana de RPD de **Contoso-HBase-EU** .
2. En el escritorio, haga clic en **Línea de comandos Hadoop**.
3. Cambie la carpeta al directorio principal de HBase:
   
        cd %HBASE_HOME%\bin
4. Abra del shell de HBase:
   
        hbase shell
5. Cree una tabla de HBase:
   
        create 'Contacts', 'Personal', 'Office'
6. No cierre la sesión de RDP ni en la ventana de línea de comandos de Hadoop. Los necesitará más adelante en el tutorial.

**Para crear una tabla de HBase en Contoso-HBase-US**

* Repita los mismos pasos para crear la misma tabla en Contoso-HBase-US.

**Para agregar Contoso-HBase-US como un punto de replicación**

1. Cambie a la ventana de RPD de **Contso-HBase_EU**.
2. En la ventana de shell de HBase, agregue el clúster de destino (Contoso-HBase-US) como un punto, por ejemplo:
   
        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'
   
    En el ejemplo, el sufijo de dominio es *contoso-hbase-us.d4.internal.cloudapp.net*. Deberá actualizarlo para que coincida con el sufijo de dominio del clúster de HBase de Estados Unidos. Asegúrese de que no hay ningún espacio entre los nombres de host.

**Para configurar cada familia de columna para la replicación en el clúster de origen**

1. En la ventana de shell de HBase de la sesión de RDP de **Contso-HBase-EU**, configure cada familia de columnas que se vaya a replicar:
   
        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Para cargar datos en la tabla de HBase de forma masiva**

Se ha cargado un archivo de datos de ejemplo en un contenedor de blobs de Azure público con la dirección URL siguiente:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

El contenido del archivo:

        8396    Calvin Raji    230-555-0191    5415 San Gabriel Dr.
        16600    Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891    Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller    397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272    Julia Lee    870-555-0110    3148 Rose Street
        4868    Jose Hayes    599-555-0171    793 Crawford Street
        4761    Caleb Alexander    670-555-0141    4775 Kentucky Dr.
        16443    Terry Chander    998-555-0171    771 Northridge Drive

Puede cargar el mismo archivo de datos en el clúster de HBase e importar los datos desde ahí.

1. Cambie a la ventana de RPD de **Contoso-HBase-EU** .
2. En el escritorio, haga clic en **Línea de comandos Hadoop**.
3. Cambie la carpeta al directorio principal de HBase:
   
        cd %HBASE_HOME%\bin
4. Para cargar los datos:
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## <a name="verify-that-data-replication-is-taking-place"></a>Compruebe que la replicación de datos se está llevando a cabo.
Puede comprobar que la replicación se está llevando a cabo mediante la exploración de las tablas desde ambos clústeres con los siguientes comandos de shell de HBase:

        Scan 'Contacts'


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo configurar la replicación de HBase entre dos centros de datos. Para obtener más información acerca de HDInsight y HBase, consulte:

* [Página de servicio de HDInsight](https://azure.microsoft.com/services/hdinsight/)
* [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Introducción a Apache HBase en HDInsight][hdinsight-hbase-get-started]
* [Información general de HBase para HDInsight][hdinsight-hbase-overview]
* [Aprovisionamiento de clústeres de HBase en Azure Virtual Network][hdinsight-hbase-provision-vnet]
* [Realizar análisis de opinión en Twitter en tiempo real con HBase][hdinsight-hbase-twitter-sentiment]
* [Análisis de datos de sensores con Storm y HBase en HDInsight (Hadoop) ][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md



<!--HONumber=Nov16_HO3-->


