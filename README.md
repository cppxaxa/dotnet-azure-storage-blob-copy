# dotnet-azure-storage-blob-copy
Copy blob between two azure storage using SAS URL

# Code
```
        /// <summary>
        /// Copy storage account blob from source SAS URL to destination SAS URL.
        /// </summary>
        /// <param name="sourceUri">The source SAS URL.</param>
        /// <param name="destinationUri">The desitination SAS URL.</param>
        /// <param name="sourceContainerName">The source container name.</param>
        /// <param name="destinationContainerName">The destination container name.</param>
        /// <param name="blobName">The blob name.</param>
        /// <returns>True if success, false otherwise.</returns>
        private async Task<bool> CopyBlobAsync(
            string sourceUri,
            string destinationUri,
            string sourceContainerName,
            string destinationContainerName,
            string blobName)
        {
            var sourceClient = new BlobServiceClient(new Uri(sourceUri));
            var destinationClient = new BlobServiceClient(new Uri(destinationUri));

            var sourceContainerClient = sourceClient.GetBlobContainerClient(
                sourceContainerName);
            var destinationContainerClient = destinationClient.GetBlobContainerClient(
                destinationContainerName);

            var sourceBlobClient = sourceContainerClient.GetBlobClient(blobName);
            var destinationBlobClient = destinationContainerClient.GetBlobClient(blobName);

            var copyInfo = await destinationBlobClient.StartCopyFromUriAsync(sourceBlobClient.Uri);

            int timeout = 600;
            int millisTimeout = timeout * 60;

            using var cancellationTokenSource = new CancellationTokenSource(millisTimeout);
            var cancellationToken = cancellationTokenSource.Token;

            await copyInfo.WaitForCompletionAsync(cancellationToken);

            return copyInfo.HasCompleted;
        }
```
