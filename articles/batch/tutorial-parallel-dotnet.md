---
title: "Ausführen einer parallelen Workload – Azure Batch .NET"
description: "Tutorial – Paralleles Transcodieren von Mediendateien mit ffmpeg in Azure Batch per Batch .NET-Clientbibliothek"
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: 
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 1100f8fddcd2f802b5f38e0b9789bc9ec359e03a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Tutorial: Ausführen einer parallelen Workload mit Azure Batch über die .NET-API

Mithilfe von Azure Batch können Sie umfangreiche auf Parallelverarbeitung ausgelegte HPC-Batchaufträge (High Performance Computing) effizient in Azure ausführen. In diesem Tutorial wird Schritt für Schritt ein C#-Beispiel für die Ausführung einer parallelen Workload per Batch beschrieben. Sie erfahren, wie Sie einen gängigen Batch-Anwendungsworkflow durchführen und programmgesteuert mit Batch- und Storage-Ressourcen interagieren. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Hinzufügen eines Anwendungspakets zu Ihrem Batch-Konto
> * Authentifizieren mit Batch- und Storage-Konten
> * Hochladen von Eingabedateien in Storage
> * Erstellen eines Pools mit Computeknoten für die Ausführung einer Anwendung
> * Erstellen eines Auftrags und von Aufgaben zum Verarbeiten von Eingabedateien
> * Überwachen der Aufgabenausführung
> * Abrufen von Ausgabedateien

In diesem Tutorial konvertieren Sie MP4-Mediendateien parallel in das MP3-Format, indem Sie das Open-Source-Tool [ffmpeg](http://ffmpeg.org/) verwenden. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio-IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 oder höher). 

* Ein Batch-Konto und ein verknüpftes allgemeines Speicherkonto. Informationen zur Erstellung dieser Konten finden Sie in den Batch-Schnellstartanleitungen zum [Azure-Portal](quick-create-portal.md) und zur [Azure CLI](quick-create-cli.md).

* [Windows-Version (64 Bit) von ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (.zip). Laden Sie die ZIP-Datei auf Ihren lokalen Computer herunter. Für dieses Tutorial benötigen Sie nur die ZIP-Datei. Es ist nicht erforderlich, die Datei zu entzippen oder sie lokal zu installieren. 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.


## <a name="add-an-application-package"></a>Hinzufügen eines Anwendungspakets

Verwenden Sie das Azure-Portal, um ffmpeg Ihrem Batch-Konto als [Anwendungspaket](batch-application-packages.md) hinzuzufügen. Mit Anwendungspaketen können Sie Aufgabenanwendungen und die dazugehörige Bereitstellung auf den Computeknoten in Ihrem Pool verwalten. 

1. Klicken Sie im Azure-Portal auf **Weitere Dienste** > **Batch-Konten** und dann auf den Namen Ihres Batch-Kontos.
3. Klicken Sie auf **Anwendungen** > **Hinzufügen**.
4. Geben Sie für **Anwendungs-ID** das Tool *ffmpeg* und als Paketversion *3.4* ein. Wählen Sie die ffmpeg-ZIP-Datei aus, die Sie heruntergeladen haben, und klicken Sie auf **OK**. Das ffmpeg-Anwendungspaket wird Ihrem Batch-Konto hinzugefügt.

![Hinzufügen eines Anwendungspakets](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Herunterladen und Ausführen des Beispiels

### <a name="download-the-sample"></a>Herunterladen des Beispiels

[Laden Sie die Beispiel-App von GitHub herunter, oder klonen Sie sie](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Verwenden Sie den folgenden Befehl, um das Beispiel-App-Repository mit einem Git-Client zu klonen:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Navigieren Sie zu dem Verzeichnis, in dem die Visual Studio-Projektmappendatei `BatchDotNetFfmpegTutorial.sln` enthalten ist.

Öffnen Sie die Projektmappendatei in Visual Studio, und aktualisieren Sie die Zeichenfolgen mit den Anmeldeinformationen in `program.cs` mit den Werten für Ihre Konten. Beispiel: 

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

Stellen Sie außerdem sicher, dass der Verweis auf das ffmpeg-Anwendungspaket in der Lösung mit der ID und Version des ffmpeg-Pakets übereinstimmt, das Sie in Ihr Batch-Konto hochgeladen haben.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>Erstellen und Ausführen des Beispielprojekts

* Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die Projektmappe, und klicken Sie dann auf **Projektmappe erstellen**. 

* Bestätigen Sie die Wiederherstellung von NuGet-Paketen, wenn Sie hierzu aufgefordert werden. Stellen Sie beim Herunterladen von fehlenden Paketen sicher, dass der [NuGet-Paket-Manager](https://docs.nuget.org/consume/installing-nuget) installiert ist.

Führen Sie ihn anschließend aus. Beim Ausführen der Beispielanwendung sieht die Konsolenausgabe in etwa wie folgt aus: Bei der Ausführung kommt es bei `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` zu einer Pause, während die Computeknoten des Pools gestartet werden. 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto, um den Pool, die Computeknoten, den Auftrag und die Aufgaben zu überwachen. Klicken Sie beispielsweise auf **Pools** > *WinFFmpegPool*, um ein Wärmebild mit den Computeknoten Ihres Pools anzuzeigen.

Bei der Ausführung von Aufgaben sieht das Wärmebild in etwa wie folgt aus:

![Wärmebild für Pool](./media/tutorial-parallel-dotnet/pool.png)


Die normale Ausführungsdauer beträgt ca. **zehn Minuten**, wenn die Anwendung in der Standardkonfiguration ausgeführt wird. Die meiste Zeit wird für die Poolerstellung benötigt.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In den folgenden Abschnitten ist die Beispielanwendung in die Schritte unterteilt, die ausgeführt werden, um eine Workload im Batch-Dienst zu verarbeiten. Ziehen Sie beim Lesen des restlichen Teils dieses Artikels die in Visual Studio geöffnete Projektmappe zurate, da nicht jede Codezeile im Beispiel beschrieben wird.

### <a name="authenticate-blob-and-batch-clients"></a>Authentifizieren des Blobs und der Batch-Clients

Zum Interagieren mit dem verknüpften Speicherkonto verwendet die App die Azure Storage-Clientbibliothek für .NET. Sie erstellt mit [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) einen Verweis auf das Konto, und es wird die Authentifizierung mit gemeinsam verwendetem Schlüssel genutzt. Anschließend wird ein [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient)-Element erstellt.

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Die App erstellt ein [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)-Objekt zum Erstellen und Verwalten von Pools, Aufträgen und Aufgaben im Batch-Dienst. Für den Batch-Client im Beispiel wird die Authentifizierung mit gemeinsam verwendetem Schlüssel genutzt. Batch unterstützt auch die Authentifizierung per [Azure Active Directory](batch-aad-auth.md), um einzelne Benutzer oder eine unbeaufsichtigte Anwendung zu authentifizieren.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Hochladen von Eingabedateien

Die App übergibt das `blobClient`-Objekt an die `CreateContainerIfNotExist`-Methode, um einen Speichercontainer für die Eingabedateien (MP4-Format) und einen Container für die Aufgabeausgabe zu erstellen.

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

Anschließend werden Dateien aus dem lokalen Ordner `InputFiles` in den Eingabecontainer hochgeladen. Die Dateien im Speicher werden als Batch-[ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile)-Objekte definiert, die von Batch später auf Computeknoten heruntergeladen werden können. 

Am Upload der Dateien sind in `Program.cs` zwei Methoden beteiligt:

* `UploadResourceFilesToContainer`: Gibt eine Sammlung mit ResourceFile-Objekten zurück und ruft intern `UploadResourceFileToContainer` auf, um die einzelnen Dateien hochzuladen, die im Parameter `filePaths` übergeben werden.
* `UploadResourceFileToContainer`: Lädt jede Datei als Blob in den Eingabecontainer hoch. Nach dem Hochladen der Datei wird eine Shared Access Signature (SAS) für das Blob abgerufen, und es wird ein ResourceFile-Objekt zurückgegeben, um das Blob darzustellen. 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Ausführliche Informationen zum Hochladen von Dateien als Blobs in ein Speicherkonto per .NET finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="create-a-pool-of-compute-nodes"></a>Erstellen eines Pools mit Computeknoten

Als Nächstes erstellt das Beispiel im Batch-Konto durch Aufrufen von `CreatePoolIfNotExist` einen Pool mit Computeknoten. In dieser definierten Methode wird die [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)-Methode verwendet, um die Anzahl von Knoten, die VM-Größe und eine Poolkonfiguration festzulegen. Hier gibt ein [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration)-Objekt einen [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference)-Verweis auf ein Windows Server-Image an, das im Azure Marketplace veröffentlicht wurde. Batch unterstützt viele verschiedene VM-Images im Azure Marketplace und auch benutzerdefinierte VM-Images.

Die Anzahl von Knoten und die VM-Größe werden mit definierten Konstanten festgelegt. Batch unterstützt dedizierte Knoten und [Knoten mit niedriger Priorität](batch-low-pri-vms.md), und Sie können eine oder beide Arten von Knoten in Ihren Pools verwenden. Dedizierte Knoten sind für Ihren Pool reserviert. Knoten mit niedriger Priorität werden zu einem reduzierten Preis basierend auf überschüssiger VM-Kapazität in Azure angeboten. Knoten mit niedriger Priorität sind nicht mehr verfügbar, wenn in Azure nicht genügend Kapazität vorhanden ist. Im Beispiel wird standardmäßig ein Pool mit nur fünf Knoten mit niedriger Priorität und der Größe *Standard_A1_v2* erstellt. 

Die Anwendung ffmpeg wird auf den Computeknoten bereitgestellt, indem der Poolkonfiguration ein [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)-Element hinzugefügt wird. 

Mit der [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit)-Methode wird der Pool an den Batch-Dienst übermittelt.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>Erstellen eines Auftrags

Für einen Batch-Auftrag werden ein Pool zum Ausführen von Aufgaben und optionale Einstellungen wie eine Priorität und ein Zeitplan für die Arbeitsschritte angegeben. Im Beispiel wird ein Auftrag mit einem Aufruf von `CreateJobIfNotExist` erstellt. In dieser definierten Methode wird die [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob)-Methode zum Erstellen eines Auftrags in Ihrem Pool verwendet. 

Mit der [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit)-Methode wird der Auftrag an den Batch-Dienst übermittelt. Der Auftrag enthält ursprünglich keine Aufgaben.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>Erstellen von Aufgaben

Im Beispiel werden Aufgaben im Auftrag durch einen Aufruf der `AddTasks`-Methode erstellt, mit der eine Liste mit [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)-Objekten erstellt wird. Für jede `CloudTask` wird ffmpeg ausgeführt, um ein `ResourceFile`-Eingabeobjekt per [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline)-Eigenschaft zu verarbeiten. ffmpeg wurde zuvor bei der Erstellung des Pools auf jedem Knoten installiert. Hier wird in der Befehlszeile ffmpeg ausgeführt, um jede MP4-Eingabedatei (Video) in eine MP3-Datei (Audio) zu konvertieren.

Im Beispiel wird nach der Ausführung über die Befehlszeile ein [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile)-Objekt für die MP3-Datei erstellt. Die Ausgabedateien (in diesem Fall eine Datei) jeder Aufgabe werden in einen Container im verknüpften Speicherkonto hochgeladen, indem die [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles)-Eigenschaft der Aufgabe verwendet wird.

Anschließend werden dem Auftrag im Beispiel mit der [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask)-Methode Aufgaben hinzugefügt und für die Ausführung auf den Computeknoten in die Warteschlange eingereiht. 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>Überwachen von Aufgaben

Wenn Batch einem Auftrag Aufgaben hinzufügt, werden sie vom Dienst automatisch in die Warteschlange eingereiht und für die Ausführung auf Computeknoten im zugeordneten Pool eingeplant. Basierend auf den Einstellungen, die Sie angeben, führt Batch das Einreihen, Planen und erneute Ausführen sowie andere Schritte der Aufgabenverwaltung aus. 

Es gibt viele Ansätze für die Überwachung der Aufgabenausführung. In diesem Beispiel wird eine `MonitorTasks`-Methode definiert, mit der für Aufgaben nur der Abschluss und die fehlerhafte bzw. erfolgreiche Durchführung gemeldet wird. Im `MonitorTasks`-Code ist ein [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel)-Element angegeben, um eine effiziente Auswahl von minimal erforderlichen Informationen zu den Aufgaben zu ermöglichen. Anschließend wird ein [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor)-Element erstellt, mit dem Hilfsprogramme zum Überwachen von Aufgabenstatus bereitgestellt werden. Bei `MonitorTasks` wird im Beispiel gewartet, bis alle Aufgaben innerhalb eines bestimmten Zeitlimits den Status `TaskState.Completed` erreichen. Anschließend wird der Auftrag beendet, und es werden alle Aufgaben gemeldet, die zwar abgeschlossen wurden, aber für die ggf. ein Fehler wie „Exitcode ungleich null“ aufgetreten ist.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach dem Ausführen der Aufgaben löscht die App den erstellten Eingabespeichercontainer automatisch und ermöglicht Ihnen das Löschen des Batch-Pools und -Auftrags. [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) und [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) von BatchClient verfügen jeweils über entsprechende Löschmethoden, die aufgerufen werden, wenn Sie das Löschen bestätigen. Für die Aufträge und Aufgaben fallen zwar keine Kosten an, für Computeknoten dagegen schon. Daher empfehlen wir Ihnen, Pools nur bei Bedarf zuzuordnen. Beim Löschen des Pools werden alle Aufgabenausgaben auf den Knoten gelöscht. Die Eingabe- und Ausgabedateien verbleiben aber im Speicherkonto.

Löschen Sie die Ressourcengruppe, das Batch-Konto und das Speicherkonto, wenn diese Elemente nicht mehr benötigt werden. Wählen Sie hierzu im Azure-Portal die Ressourcengruppe für das Batch-Konto aus, und klicken Sie auf **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen eines Anwendungspakets zu Ihrem Batch-Konto
> * Authentifizieren mit Batch- und Storage-Konten
> * Hochladen von Eingabedateien in Storage
> * Erstellen eines Pools mit Computeknoten für die Ausführung einer Anwendung
> * Erstellen eines Auftrags und von Aufgaben zum Verarbeiten von Eingabedateien
> * Überwachen der Aufgabenausführung
> * Abrufen von Ausgabedateien

Weitere Beispiele zur Verwendung der .NET-API zum Planen und Verarbeiten von Batch-Workloads finden Sie in den Beispielen auf GitHub.

> [!div class="nextstepaction"]
> [C#-Beispiele für Batch](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
