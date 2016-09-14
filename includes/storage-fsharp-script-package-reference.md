### Use Nuget or Paket to obtain the package

If using [Paket](https://fsprojects.github.io/Paket/) as your dependency manager, install the `paket.exe` tool and resolve the `WindowsAzure.Storage` dependency. For example, `paket.dependencies` may contain:

    frameworks: net45
    source https://nuget.org/api/v2

    nuget WindowsAzure.Storage

Now resolve and install these dependencies:

    > paket install

    Resolving packages for group Main...

Now generate include scripts for the packages:

    > paket generate-include-scripts 

    generating scripts for framework net45

From your F# script you can now reference all necessary packages as follows:

    #load @"paket-files/include-scripts/net45/include.windowsazure.storage.fsx"

If using `nuget.exe` as your dependency manager, install and reference the `WindowsAzure.Storage` package, then add references
to all necessary DLLs.
