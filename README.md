Reference ikea new reference: AS-P6203354 

Input from facebook
-------------------
First attempt at the 70-532 yesterday. Scored 675. I was so close! There were about 60-70% coding related questions on Service Fabric, Tuning Cosmos DB, ARM templates parameters, Active Directory B2B and B2C, Mobile App Services. There were also questions on the Open source Paas offering such as Pivotal Cloud Foundry, Open Shift etc. I have already scheduled my 2nd attempt in the next two weeks. Here is my plan to study:

1) Go deeper in the Service Fabric coding, Deploy and debug stateful services using reliable dictionary, stateless services, and Actor services
2) Deploy the evaluation offering of Pivotal Cloud Foundry and Open Shift as these offering consume a 30-40 VMS
3) Go deeper in the ARM template deployment and understand the structure
4) Deploy application using COSMOS DB and understand what's involved in tuning when there are performance issue
5) Study the query language for Application Insight analysis tool

I welcome any other suggestion. Thanks.

VM disks 
---------
By default a VM contain an OS disk and data disks can be added after. OS disks have caching enabled by default. 
Caching is not an optimal solution when I/O requests are random.
Caching is an optimal solution when I/O requests are seuquential.

Scale a VM
-----------

Just pick your Virtual Machine go to size and you can it up or scale it down


VM Scale sets
--------------
- You can alocate a bunch of virtual machines
- You can pick the number of instances you want, a domain name and a IP adress
- You can pick na autoscale sale strategy based on cpu perecentage threshold usage, and between to say 3 to 6 VMs

Notes in the test
------------------

Action element prop types
https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference


SSD VMS
-------
- You can add a second data disk when you create a VM and define the caching (Read, Read/Write, None)

Azure storage replication
-------------------------
https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy


Monitor ARM VMs
---------------
- There are some metrics you can check
- You can create an alert when a metric is greater than...
- Notify owners/contributors and readers or add the emails manually
- Or trigger a webhook or run a "Runbook" - an automation task for this alert  

Design and Implement a Storage and Data Strategy (25-30%)
----------------------------------------------------------
**Implement Azure Storage blobs and Azure Files**  
- Storage account Premium is with SSD drive which is faster but more expensive
- Check the access keys to get access to the blob storage

- FetchAttributes downloads the attributes of the container

Access blods securely:
- With shared access signature you can limit the access that you Access keys will have
- If you have an intermediate service that access the blob so that you don't access the blob directly from the application can give you more control if you want to switch blobs.

Implement async blob copy:
```csharp
static void CopyBlob(CloudBlobContainer container)
{
  CloudBlockBlob blockBlob = container.GetBlockBlobReference("examobjectives");
  CloudBlockBlob copyToBlockBlob = container.GetBlockBlobReference("examobjectives-copy");
  copyToBlockBlob.StartCopyAsync(new Uri(blockBlob.Uri.AbsoluteUri));
}
```

Configure Content Delivery Network  (CDN)
- We can create a new CDN to serve files, which is a resource, and add an endpoint to it. 
- We pick a name for our endpoint (a unique url - something..azureedge.net), an origin type (at this point we'll choose "Storage"). We choose an origin hostname, an origin path (should be the name of the container)  
<img src="./images/cdn.png" width="200px">
- So now we can hit directly the url publicfiles.azureedge.net/nameofourfile.pdf and download the file

Design Blob Hierarchies (create directories - subdirectories)

```csharp
static void UploadBlobSubdirectory(CloudBlobContainer container)
{
  CloudBlobDirectory directory = container.GetDirectoryReference("parent-directory");
  CloudBlockBlob blockBlob = directory.GetBlockBlobReference("newexamobjectives");
  CloudBlockBlob blockBlob = subdirectory.GetBlockBlobReference("newexamobjectives");

  using (var fileStream = System.IO.File.OpenRead(@"LOCAL_PATH"))
  {
    blockBlob.UploadFromStream(fileStream);
  }
}
```

#Configure Custom Domains

- You can customize the domain through CNAME record and the setting is in Azure Portal.

#Scale blob storage and implement blob tiering

- There are limits for the storage account (500 TB for the whole account)
- You can create a second storage account and move half of the files alphabetically to the first one and half of the other files to the second one
- We can: 
1) implement an Azure CDN to enable geo-caching to keep blobs close to the users. 
2) We can implement read access geo-redundant storage and offload some of the reads to another geographic location (thus creating a mini-CDN that will be slower, but cheaper).

#Azure Files

Azure file storage provides a way for applications to share storage accessible via SMB 2.1 protocol. It is particularly useful for VMs and cloud services as a mounted share, and applications can use the File Storage API to access file storage.

#Create connections to files from on-premises or cloud-based Windows or Linux machines

Should use table storage for fast no-sql storage

#2.2
  #Tables
  demo name (Case insensitive): awesomeservicelogs
  #Queues
  demo name (Lowercase): awesomeservice-messages-001

  Scale tables and queues
  -----------------------
  Tables
  -------
  Azure table service should be the first thought when it comes to store structured data that needs to be high performing and highly availabe
  Limits on tables
  500tb maximum storage account size
  1mb data in an entity

  Azure Groups together by Partition key (i.e. "Car") all kept together on one server

  - One value for all entities
    Small amount of data is ok
    Large amount of data could have scaling issues (as they are served by a single server)

  - Multiple values ("bmw, ford etc")
    Uneven distribution of data (e.g. less maseratis than fords)
    Batch restriction restricted to one PartitionKey

  - Unique value
    Azure can handle the data evenly, create range partition as it wishes
    Range queries might access more than one server 
    Batches transactions are not possible



Activity Logs and Diagnostics
------------------------------
If you got to the Activity log menu item then you can see many stats, these are handled through the Diagnostics tab which exists a little at the bottom.
  

Create an SQL Database
----------------------
Elastic vs Single Database: 

Redis Caching
----------------
- Fastest way to retrieve and save data, it stores everything in memory.



# Notes from the Pluralsight test 

## Storage

- Scaling a queue  
*It is NOT feasible to increase the queue client requests from 32 messages in a single batch to 64 messages in a single batch. The queue client can request up to 32 messages in a single batch and then process them sequentially or in parallel.*

- Submitting operations in a batch  
*It is NOT correct to state that operations in the batch can be in different partitions. All operations have to be in the same partition.*

- You are the Azure administrator for Nutex. You have several applications that run mission-critical applications, and must be available all the time. Because even Azure can experience hardware failures, you want to take advantage of Azure's 99.95% service-level agreement (SLA) for your VMs.  
*You will need create at least two virtual machines, group them into an availability set, and place them in different fault domains. An availability set will offer redundancy in case of downtime due to failure or maintenance. Because machines in the same fault domain share a common power source and network switch, you would not want the machines to be in the same fault domain.*

- Your application uses Azure Storage. You decided to scale your application by enabling Content Delivery Network (CDN) on the Azure Storage account.  
  - It is NOT correct to state that for a public container named lessons, the Azure CDN URL will be http://<identifier>.lessons.azureedge.net. The URL is incorrect. For a container named lessons, the Azure CDN URL should be in the following format:  
http://<identifier>.azureedge.net/lessons/  
  - It is correct to state that blobs in public containers that are available for anonymous access are cached by the CDN. The blobs must be in public containers for the blob to be available for anonymous read access.  
  - It is correct to state that for a storage account named studentaccount and public container named lessons, the Azure Blob service URL will be http://studentaccount.blob.core.windows.net/lessons/.   
  - It is correct to state that only publicly available blobs can be cached with the Azure CDN. The blob needs to be publicly available in order to be cached.


- You manage your company's databases. You need to migrate the Parts database from a SQL Server 2008 instance to a SQL Server 2016 instance on an Azure VM. The Parts database has 45 tables and takes up 3 TB of storage.  
*You cannot perform a backup to URL and restore into the Azure virtual machine from the URL. This is possible because the source database is larger than 1 TB, and the source SQL instance is not running SQL Server 2012 SP1 or higher. You can only perform a backup to URL and restore into the Azure virtual machine from the URL if your source database and destination database is on a SQL Server instance that runs SQL Server 2012 SP1 CU2 or higher. Also, if the source database is on a SQL Server instance that is running any version prior to SQL Server 2016, then you are limited to 1 TB for your source database. SQL Server 2016 allows source database sizes up to 12.8 TB. Azure blob storage is supported using the following URL format:*


- Your application uses Azure SQL Database. While making changes in the database, you inadvertently ran a script that dropped a table and deleted records in other tables. You need to restore the database from a backup.  
*Restore-AzureRmSqlDatabase*

- You are designing an application that uses Azure Table storage. You get together with your team and you start discussing the Table storage implementation. A few ideas are mentioned about the partition keys.  
*You must always consider how partition keys will affect future updates to your data using batch transactions. Batches can only have operations that exist in the same partition, with the same partition key. In fact, if the PartitionKey contains unique values for each entity, then batch operations will not be possible.  
Partition keys are not required to be unique. Although they can be unique in rare cases, they should not necessarily be unique. A primary key for an Azure entity would consist of a combination of the PartitionKey properties and the RowKey properties, forming a single clustered index within the table.  
The same partition key can be used for all records if the entity set is small, but not when the entity set is large. For example, suppose you have a set of records for a large running event. You could partition the data based on the different events. The PartitionKey may contain three different values compromised of the event name and distance, such as 2015_IronHorseRace_15K, 2015_IronHorseRace_10K, and 2015_IronHorseRace_5K. You can use the PartitionKey to partition the entries of the race by the distance that they ran and store those records on different servers.  
Partition keys should not be split randomly between partition servers. By doing so, the application will not have a predictable performance because one partition server can end up unevenly loaded with more entities than the others.*

- You are the administrator of the Nutex Corporation. Your application, NutexApp, has to perform various cache operations using the StackExchange.Redis.NET client to improve data access. The cache size never exceeds 15 GB, and you do not want to share data across multiple Redis nodes. You can accept a few minutes of data loss in case of disasters.  
Which Redis data persistence solution should you use?  
*You should use RDB because RDB persistence performs point-in-time snapshots of your database at intervals. The intervals can be set in minutes (15/30/60) or hours (6/12/24). You can simply use RDB alone if you can tolerate a few minutes of data loss in case of disasters.  
You do not have to choose AOF because AOF files are typically larger than RDB files for the same dataset. RDB can be faster than AOF depending on the exact fsync policy. Also, Nutex can accept a few minutes of data loss in case of disasters (minimum 15 minutes).  
You do not have to choose RDB and AOF because Nutex can accept a few minutes of data loss.  
You do not have to choose a Redis cluster. You should use Redis clustering, which is available in the Premium tier, if you want to create caches larger than 53 GB or want to share data across multiple Redis nodes. In this case you do not need caches larger than 53 GB, and you do not want to share data across multiple Redis nodes.*


- Which operation would you use to retrieve the approximate length of the queue?
*Get Queue Metadata: returns stats of the queue along with the length as property*

- You are the administrator of the Nutex Corporation. You have created a Redis Cache named nutexredis.redis.cache.windows.net. You want to add clustering to your cache solution, and you want to optimize your Redis Cache cluster performance. Keys with a hash tag are being used. It has to be the responsibility of the application to ensure best cluster performance.  
*You should create a new Premium Redis Cache and add clustering because you can only enable clustering when you create a cache. The cluster size can be changed after the cache is created, but you cannot add clustering to a Premium cache or remove clustering from a Premium cache after the cache is created.
You should use keys with hash tags because the key space is split into 16384 slots. Each of these slots has a key that is hashed assigned to it, and the slots are distributed across the nodes of the cluster. To ensure that multiple keys are located in the same share using hash tags, you can configure which part of the key is hashed. You should distribute the keys to ensure best performance. If you are using keys with a hash tag, the application has responsibility to ensure the keys are distributed evenly.*

- Your application uses Blob storage. You need to access a blob for a movie to display on the browser. The movie is in a container named movies, and the storage account is named teaching.  
*http://teaching.blob.core.windows.net/movies/INTROTOAZURE.AVI*  
*The URL to the blob content has the following format:*  
`http://<account name>.blob.core.windows.net/<container name>/<path and filename>`

- To Collect the Metadata of a container the following code should be used  
```
{
    container.FetchAttributes();
    Console.WriteLine("Container metadata:\n");
    foreach (var item in container.Metadata)
    {
        Console.WriteLine("Key: {0}", item.Key);
        Console.WriteLine("Value: {0}", item.Value);
    }
}
```
- You created a web application that uses blob storage. You need to copy the contents and the details of one blob to another with minimum impact on performance.  
*You should use BeginStartCopy(). This method will start an asynchronous operation for copying a blob's contents, properties, and metadata to the current blob.*

- Scaling a database involves finding out what to do when the load becomes too large for the database to handle. You should select sharding. Sharding is the practice of spreading records across multiple databases so you can decrease the load on any particular database. You can use sharding to scale the application horizontally, which avoids limitations imposed by pricing tiers.  
*Scaling a database involves finding out what to do when the load becomes too large for the database to handle. You should select sharding. Sharding is the practice of spreading records across multiple databases so you can decrease the load on any particular database. You can use sharding to scale the application horizontally, which avoids limitations imposed by pricing tiers.*