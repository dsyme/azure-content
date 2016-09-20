
The core experience of getting started with Azure and F# is not very positive, because all existing documentation only covers C#.  This is usually done in a very C#/VisualStudio-centric way, catering for a particular wizard-first C# developer.  The documentation is not at all suitable for code-first F# developers.

Sometime in October/November we will take a crack at preparing comprehensive docs for F# for some core Azure topics, where there is 
value in having thse distinct from the existing .NET/C# docs.

Below is a list of the pages which may  need an "F#" tab or might benefit from F# content. We may encourage Azure to rename ".NET" to "C#" or "C#/VB" in these cases.


Here is the current [fsharp-content](https://github.com/Azure/azure-content/compare/master...dsyme:fsharp-content) with some F# editions of the articles, linked below




## Storage

- [ ] [Blobs - F# version](https://github.com/dsyme/azure-content/blob/fsharp-content/articles/storage/storage-fsharp-how-to-use-blobs.md) [C# version](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/) [Node version](https://azure.microsoft.com/en-us/documentation/articles/storage-nodejs-how-to-use-blob-storage/)
- [ ] [Files](https://github.com/Azure/azure-content/blob/master/articles/storage/storage-dotnet-how-to-use-files.md)
- [ ] [Queues](https://github.com/Azure/azure-content/blob/master/articles/storage/storage-dotnet-how-to-use-queues.md)
- [ ] [Tables](https://github.com/Azure/azure-content/blob/master/articles/storage/storage-dotnet-how-to-use-tables.md)
- [ ] [Shared Access Signature Part 1](https://github.com/Azure/azure-content/blob/master/articles/storage/storage-dotnet-shared-access-signature-part-1.md) Note these can likely be covered in the primary Blob storage docs
- [ ] [Shared Access Signature Part 2](https://github.com/Azure/azure-content/blob/master/articles/storage/storage-dotnet-shared-access-signature-part-2.md) Note these can likely be covered in the primary Blob storage docs

## WebApps and AppService

- [ ] [AppService get started](https://github.com/Azure-Samples/app-service-web-dotnet-get-started)
- [ ] [Add link here](https://azure.microsoft.com/en-us/documentation/articles/app-service-web-get-started/)

Other AppService topics:
* https://github.com/Azure/azure-content/blob/master/articles/app-service-api/app-service-api-dotnet-get-started.md
* https://github.com/Azure/azure-content/blob/master/articles/app-service-api/app-service-api-dotnet-service-principal-auth.md
* https://github.com/Azure/azure-content/blob/master/articles/app-service-api/app-service-api-dotnet-swashbuckle-customize.md
* https://github.com/Azure/azure-content/blob/master/articles/app-service-api/app-service-api-dotnet-triggers.md
* https://github.com/Azure/azure-content/blob/master/articles/app-service-api/app-service-api-dotnet-user-principal-auth.md

## WebSites

- [ ] [WebSites get started](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-get-started/)

## WebJobs

- [ ] [WebJobs get started](https://azure.microsoft.com/en-us/documentation/articles/websites-dotnet-webjobs-sdk-get-started/)

## MobileApps

- [ ] [Xamarin.Android get started](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started/)
- [ ] [Xamarin.Forms get started](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started/)

## SQL Database

* F# version of this? https://azure.microsoft.com/en-us/documentation/articles/sql-database-develop-dotnet-simple/
*	Actually this https://msdn.microsoft.com/library/mt718320.aspx
*	And this? https://msdn.microsoft.com/library/mt703195.aspx

## Azure Redis Cache

*	And this: https://azure.microsoft.com/en-us/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/

## Azure Batch

- [ ] [Azure Batch get started](https://github.com/Azure/azure-content/blob/master/articles/batch/batch-dotnet-get-started.md)
- [ ] [Azure Batch Management from .NET](https://github.com/Azure/azure-content/blob/master/articles/batch/batch-management-dotnet.md)

## Azure Cache - Retired
	
## Azure CDN

- [ ] [Azure CDN App Dev](https://github.com/Azure/azure-content/blob/master/articles/cdn/cdn-app-dev-net.md)

## Other Articles

* https://github.com/dsyme/azure-content/blob/master/articles/dotnet-sdk.md
* https://github.com/dsyme/azure-content/blob/master/articles/dotnet-develop-multitenant-applications.md
* https://github.com/dsyme/azure-content/blob/master/articles/partner-twilio-cloud-services-dotnet-phone-call-web-role.md
* https://github.com/dsyme/azure-content/blob/master/articles/store-new-relic-cloud-services-dotnet-application-performance-management.md
* https://github.com/dsyme/azure-content/blob/master/articles/twilio-dotnet-how-to-use-for-voice-sms.md
* https://github.com/dsyme/azure-content/blob/master/articles/virtual-machines-dotnet-diagnostics.md
* https://github.com/Azure/azure-content/blob/master/articles/active-directory/active-directory-devquickstarts-dotnet.md
