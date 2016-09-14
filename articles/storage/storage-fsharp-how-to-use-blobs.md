<properties
	pageTitle="Get started with Azure Blob storage (object storage) using F# | Microsoft Azure"
	description="Store unstructured data in the cloud with Azure Blob storage (object storage)."
	services="storage"
	documentationCenter=".net"
	authors="dsyme"
	manager="cbishop"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/22/2016"
	ms.author="tamram"/>


# Get started with Azure Blob storage using F# 

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Overview

Azure Blob storage is a service that stores unstructured data in the cloud as objects/blobs. Blob storage can store any type of text or binary data, such as a document, media file, or application installer. Blob storage is also referred to as object storage.

### About this tutorial

This article shows you how to perform common scenarios using Blob storage. The samples are written using F# using the Azure Storage Client Library for .NET. The scenarios covered include how to upload, list, download, and delete blobs.

### Conceptual overview

For a conceptual overview of blob storage, see [the .NET guide for blob storage](storage-dotnet-how-to-use-blobs.md#conceptual-overview)

### Create an Azure storage account

To use this guide, you must first [create an Azure storage account](storage-create-storage-account.md#create-a-storage-account).
You will also later need your storage access key for this account.

## Create an F# Script and Start F# Interactive

The samples in this article can be used in either an F# application or an F# script.
To create an F# script, create a file `blobs.fsx` in your F# development environment.

[AZURE.INCLUDE [storage-fsharp-script-package-reference](../../includes/storage-fsharp-script-package-reference.md)]

### Add namespace declarations

Add the following `open` statements to the top of the `blobs.fsx` file:

    open Microsoft.Azure // Namespace for CloudConfigurationManager
    open Microsoft.WindowsAzure.Storage // Namespace for CloudStorageAccount
    open Microsoft.WindowsAzure.Storage.Blob // Namespace for Blob storage types

### Get your connection string

For the purposes of this tutorial, you will enter your storage connection string directly into a script.
For more information about connection strings, see [Configure a Connection String to Azure Storage](storage-configure-connection-string.md).

    let storageConnString = "..." // fill this in from your storage account

> [AZURE.NOTE] Your storage account key is similar to the root password for your storage account. Always be careful to protect your storage account key. Avoid distributing it to other users, hard-coding it, or saving it in a plain-text file that is accessible to others. Regenerate your key using the Azure Portal if you believe it may have been compromised.

For applications, the best way to maintain your storage connection string is in a configuration file.
To fetch the connection string from a configuration file, use:

    // Parse the connection string and return a reference to the storage account.
    let storageConnString = CloudConfigurationManager.GetSetting("StorageConnectionString")

Using Azure Configuration Manager is optional. You can also use an API such as the .NET Framework's **ConfigurationManager** type.

### Parse the connection string

To parse the connection string, use:

    // Parse the connection string and return a reference to the storage account.
    let storageAccount = CloudStorageAccount.Parse(storageConnString)

### Create the Blob service client

The **CloudBlobClient** type enables you to retrieve containers and blobs stored in Blob storage. Here's one way to create the service client:

    let blobClient = storageAccount.CreateCloudBlobClient()

Now you are ready to write code that reads data from and writes data to Blob storage.

## Create a container

This example shows how to create a container if it does not already exist:

    // Retrieve a reference to a container.
    let container = blobClient.GetContainerReference("mycontainer")

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists()

By default, the new container is private, meaning that you must specify your storage access key to download blobs from this container. If you want to make the files within the container available to everyone, you can set the container to be public using the following code:

    let permissions = BlobContainerPermissions(PublicAccess = BlobContainerPublicAccessType.Blob)

    container.SetPermissions(permissions)

Anyone on the Internet can see blobs in a public container, but you can modify or delete them only if you have the appropriate account access key or a shared access signature.

## Upload a blob into a container

Azure Blob Storage supports block blobs and page blobs.  In the majority of cases, block blob is the recommended type to use.

To upload a file to a block blob, get a container reference and use it to get
a block blob reference. Once you have a blob reference, you can upload any
stream of data to it by calling the **UploadFromStream** method. This operation will create the blob if it didn't previously exist,
or overwrite it if it does exist.

The following example shows how to upload a blob into a container and assumes that the container was already created.

    // Retrieve reference to a blob named "myblob".
    let blockBlob = container.GetBlockBlobReference("myblob")

    // Create or overwrite the "myblob" blob with contents from a local file.
    let _ = 
        use fileStream = System.IO.File.OpenRead(@"path\myfile")
        blockBlob.UploadFromStream(fileStream)

## List the blobs in a container

To list the blobs in a container, first get a container reference. You
can then use the container's **ListBlobs** method to retrieve the blobs and/or directories
within it. To access the rich set of properties and methods for a
returned **IListBlobItem**, you must cast it to a **CloudBlockBlob**,
**CloudPageBlob**, or **CloudBlobDirectory** object.  If the type is unknown, you can use a
type check to determine which to cast it to.  The following code
demonstrates how to retrieve and output the URI of each item in
the `photos` container:

    // Retrieve reference to a previously created container.
    let photosContainer = blobClient.GetContainerReference("photos")

    // Loop over items within the container and output the length and URI.
    for item in photosContainer.ListBlobs(null, false) do
        match item with 
        | :? CloudBlockBlob as blob -> 
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri)

        | :? CloudPageBlob as pageBlob ->
            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri)

        | :? CloudBlobDirectory as directory ->
            Console.WriteLine("Directory: {0}", directory.Uri);

As shown above, you can name blobs with path information in their names. This creates a virtual directory structure that you can organize and traverse as you would a traditional file system. Note that the directory structure is virtual only - the only resources available in Blob storage are containers and blobs. However, the storage client library offers a **CloudBlobDirectory** object to refer to a virtual directory and simplify the process of working with blobs that are organized in this way.

For example, consider the following set of block blobs in a container named `photos`:

	photo1.jpg
	2015/architecture/description.txt
	2015/architecture/photo3.jpg
	2015/architecture/photo4.jpg
	2016/architecture/photo5.jpg
	2016/architecture/photo6.jpg
	2016/architecture/description.txt
	2016/photo7.jpg

When you call **ListBlobs** on the 'photos' container (as in the above sample), a hierarchical listing is returned. It contains both **CloudBlobDirectory** and **CloudBlockBlob** objects, representing the directories and blobs in the container, respectively. The resulting output looks like:

	Directory: https://<accountname>.blob.core.windows.net/photos/2015/
	Directory: https://<accountname>.blob.core.windows.net/photos/2016/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optionally, you can set the **UseFlatBlobListing** parameter of of the **ListBlobs** method to
**true**. In this case, every blob in the container is returned as a **CloudBlockBlob** object. The call to **ListBlobs** to return a flat listing looks like this:

    // Loop over items within the container and output the length and URI.
    for item in container.ListBlobs(null, true) do
        ...

and the results look like this:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2015/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2015/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2015/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2016/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2016/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2016/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2016/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Download blobs

To download blobs, first retrieve a blob reference and then call the **DownloadToStream** method. The following
example uses the **DownloadToStream** method to transfer the blob
contents to a stream object that you can then persist to a local file.

    // Retrieve reference to a blob named "photo1.jpg".
    let blockBlob = container.GetBlockBlobReference("photo1.jpg")

    // Save blob contents to a file.
    let _ = 
        use fileStream = System.IO.File.OpenWrite(@"path\myfile")
        blockBlob.DownloadToStream(fileStream);

You can also use the **DownloadToStream** method to download the contents of a blob as a text string.

    // Retrieve reference to a blob named "myblob.txt"
    let blockBlob2 = container.GetBlockBlobReference("myblob.txt")

    let text =
        use memoryStream = new MemoryStream()
        blockBlob2.DownloadToStream(memoryStream)
        System.Text.Encoding.UTF8.GetString(memoryStream.ToArray())

## Delete blobs

To delete a blob, first get a blob reference and then call the
**Delete** method on it.

    // Retrieve reference to a previously created container.
    let container = blobClient.GetContainerReference("mycontainer")

    // Retrieve reference to a blob named "myblob.txt".
    let blockBlob = container.GetBlockBlobReference("myblob.txt")

    // Delete the blob.
    blockBlob.Delete()

## List blobs in pages asynchronously

If you are listing a large number of blobs, or you want to control the number of results you return in one listing operation, you can list blobs in pages of results. This example shows how to return results in pages asynchronously, so that execution is not blocked while waiting to return a large set of results.

This example shows a flat blob listing, but you can also perform a hierarchical listing, by setting the `useFlatBlobListing` parameter of the **ListBlobsSegmentedAsync** method to `false`.

Because the sample method calls an asynchronous method, it must be prefaced with the `async` keyword, and it must return a **Task** object. The await keyword specified for the **ListBlobsSegmentedAsync** method suspends execution of the sample method until the listing task completes.

    let ListBlobsSegmentedInFlatListing(container:CloudBlobContainer) =
      async {

        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:")

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.

        let rec loop continuationToken i = 
          async {
            // This overload allows control of the page size. You can return all remaining results
            // by passing null for the maxResults parameter, or by calling a different overload.
            let! ct = Async.CancellationToken
            let! resultSegment = 
                container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null, ct) 
                |> Async.AwaitTask

            if (resultSegment.Results.Count<IListBlobItem>() > 0) then
                Console.WriteLine("Page {0}:", i)

            for blobItem in resultSegment.Results do
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri)

            Console.WriteLine()

            //Get the continuation token.
            let continuationToken = resultSegment.ContinuationToken
            if (continuationToken <> null) then
                do! loop continuationToken (i+1)
          }
        do! loop null 1
      }

## Writing to an append blob

An append blob is a new type of blob, introduced with version 5.x of the Azure storage client library for .NET. An append blob is optimized for append operations, such as logging. Like a block blob, an append blob is comprised of blocks, but when you add a new block to an append blob, it is always appended to the end of the blob. You cannot update or delete an existing block in an append blob. The block IDs for an append blob are not exposed as they are for a block blob.

Each block in an append blob can be a different size, up to a maximum of 4 MB, and an append blob can include a maximum of 50,000 blocks. The maximum size of an append blob is therefore slightly more than 195 GB (4 MB X 50,000 blocks).

The example below creates a new append blob and appends some data to it, simulating a simple logging operation.

    //Get a reference to a container.
    let appendContainer = blobClient.GetContainerReference("my-append-blobs")

    //Create the container if it does not already exist.
    appendContainer.CreateIfNotExists()

    //Get a reference to an append blob.
    let appendBlob = appendContainer.GetAppendBlobReference("append-blob.log")

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace()

    let numBlocks = 10

    //Generate an array of random bytes.
    let rnd = new Random()
    let bytes = Array.zeroCreate<byte>(numBlocks)
    rnd.NextBytes(bytes)

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for i in 0 .. numBlocks-1 do
        let msg = String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",DateTime.UtcNow, bytes[i], Environment.NewLine)
        appendBlob.AppendText(msg)

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText())

See [Understanding Block Blobs, Page Blobs, and Append Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) for more information about the differences between the three types of blobs.

## Concurrent access

To support concurrent access to a blob from multiple clients or multiple process instances, you can use **ETags** or **leases**.

* **Etag** - provides a way to detect that the blob or container has been modified by another process

* **Lease** - provides a way to obtain exclusive, renewable, write or delete access to a blob for a period of time

For further information see [Managing Concurrency in Microsoft Azure Storage](https://azure.microsoft.com/en-us/blog/managing-concurrency-in-microsoft-azure-storage-2/).

## Naming containers

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

## Managing security for blobs

By default, Azure Storage keeps your data secure by limiting access to the account owner, who is in possession of the account access keys. When you need to share blob data in your storage account, it is important to do so without compromising the security of your account access keys. Additionally, you can encrypt blob data to ensure that it is secure going over the wire and in Azure Storage.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### Controlling access to blob data

By default, the blob data in your storage account is accessible only to storage account owner. Authenticating requests against Blob storage requires the account access key by default. However, you may wish to make certain blob data available to other users. 

For details on how to control access to blob storage, see [the .NET guide for blob storage](storage-dotnet-how-to-use-blobs.md#controlling-access-to-blob-data).


### Encrypting blob data

Azure Storage supports encrypting blob data both at the client and on the server.

For details on encrypting blob data, see [the .NET guide for blob storage](storage-dotnet-how-to-use-blobs.md#encrypting-blob-data).

## Next steps

Now that you've learned the basics of Blob storage, follow these links
to learn more.

### Microsoft Azure Storage Explorer
- [Microsoft Azure Storage Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, OS X, and Linux.

### Blob storage samples

- [Getting Started with Azure Blob Storage in C#](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### Blob storage reference

- [Storage Client Library for .NET reference](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST API reference](http://msdn.microsoft.com/library/azure/dd179355)

### Conceptual guides

- [Transfer data with the AzCopy command-line utility](storage-use-azcopy.md)
- [Get started with File storage for .NET](storage-fsharp-how-to-use-files.md)
- [How to use Azure blob storage with the WebJobs SDK](../app-service-web/websites-fsharp-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
