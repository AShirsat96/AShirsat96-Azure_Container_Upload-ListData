# Azure Blob Storage Operations

A Python implementation for managing Azure Blob Storage operations, including uploading blobs, streaming data, and listing container contents.

## Prerequisites

- Python 3.11.7
- Azure Storage Account
- Azure Storage Connection String
- Azure Storage Blob libraries

## Installation

Install the required Azure libraries:

```bash
pip install azure-storage-blob
pip install azure-mgmt-storage
```

## Required Libraries
```python
import os
import uuid
import io
from azure.storage.blob import BlobClient, BlobServiceClient
from azure.core.exceptions import ResourceExistsError
from azure.core import exceptions
```

## Configuration

Set up your Azure connection:

```python
connection_string = "your_connection_string"
blob_service_client = BlobServiceClient.from_connection_string(connection_string)
container_name = "your_container_name"
```

## Features

### 1. Basic Blob Upload
Upload a text file as a blob:

```python
# Create local directory and file
local_path = "./Blobdata1"
os.mkdir(local_path)
local_file_name = str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Create and upload file
with open(upload_file_path, 'w') as file:
    file.write("Hello, World!")

blob_client = blob_service_client.get_blob_client(
    container=container_name, 
    blob=local_file_name
)

with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### 2. Stream Upload
Upload data directly from a stream:

```python
def upload_blob_stream(blob_service_client: BlobServiceClient, container_name: str):
    """
    Upload random bytes as a block blob using stream.
    
    Args:
        blob_service_client: Azure blob service client
        container_name: Target container name
    """
    blob_client = blob_service_client.get_blob_client(
        container=container_name, 
        blob="sample-blob3.txt"
    )
    input_stream = io.BytesIO(os.urandom(15))
    blob_client.upload_blob(input_stream, blob_type="BlockBlob")
```

### 3. List Blobs
List all blobs in a container:

```python
def list_blobs(blob_service_client, container_name):
    """
    List all blobs in the specified container.
    """
    blob_list = blob_service_client.get_container_client(container_name).list_blobs()
    for blob in blob_list:
        print(f"\t{blob.name}")
```


## Usage Examples

1. Initialize Client:
```python
blob_service_client = BlobServiceClient.from_connection_string(connection_string)
```

2. Upload File:
```python
# Create blob client
blob_client = blob_service_client.get_blob_client(
    container=container_name, 
    blob=local_file_name
)

# Upload file
with open(file_path, "rb") as data:
    blob_client.upload_blob(data)
```

3. Stream Upload:
```python
upload_blob_stream(blob_service_client, container_name)
```

4. List Container Contents:
```python
blob_list = blob_service_client.get_container_client(container_name).list_blobs()
for blob in blob_list:
    print(blob.name)
```

## Error Handling

The implementation includes handling for:
- Resource existence errors
- Connection issues
- File operation errors
- Azure service exceptions
