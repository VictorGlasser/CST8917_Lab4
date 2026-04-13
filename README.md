# CST8917 Lab 4

Video Demo: https://youtu.be/5ovLSVULxk4

## Setup instructions
(Sections of these instructions were taken directly from the following file: https://github.com/ramymohamed10/26W_CST8917_Lab4/blob/main/README.md?plain=1)
### Infrastructure
#### Resource Group
- Create a resource group 
- Create a storage account 
- Ensure that **Allow Blob anonymous access** is set it to **Enabled**
- Create blob containers named `image-uploads` and `image results`
- Configure CORS for the storage account with the following rule:

| Field | Value |
|-------|-------|
| **Allowed origins** | `*` |
| **Allowed methods** | `GET, PUT, OPTIONS, HEAD` |
| **Allowed headers** | `*` |
| **Exposed headers** | `*` |
| **Max age** | `3600` |

- Create an event grid system topic
- Create the first subscription in your Event Grid System Topic:

| Field | Value |
|-------|-------|
| **Name** | `process-image-sub` |
| **Event Schema** | `Event Grid Schema` |
| **Filter to Event Types** | Select only `Blob Created` |
| **Endpoint Type** | `Azure Function` |
| **Endpoint** | Click **Select an endpoint** -> choose your Function App -> select the `process-image` function |

Check **enable subject filtering**

| Field | Value |
|-------|-------|
| **Subject Begins With** | `/blobServices/default/containers/image-uploads` |
| **Subject Ends With** | `.jpg` |

| Field | Value |
|-------|-------|
| **Key** | `subject` |
| **Operator** | `String ends with` |
| **Value(s)** | `.jpg` and `.png` (add both values) |

- Create the second subscription in your Event Grid System Topic:

| Field | Value |
|-------|-------|
| **Name** | `audit-log-sub` |
| **Event Schema** | `Event Grid Schema` |
| **Filter to Event Types** | Select `Blob Created` |
| **Endpoint Type** | `Azure Function` |
| **Endpoint** | Click **Select an endpoint** -> choose your Function App -> select the `audit-log` function |

Check **enable subject filtering**

| Field | Value |
|-------|-------|
| **Subject Begins With** | `/blobServices/default/containers/image-uploads` |
| **Subject Ends With** | (leave empty) |

- Generate a SAS Token for your storage account

### Function App Deployment

- Install packages
```bash
source .venv/bin/activate
python -m pip install -r requirements.txt
```

- Deploy Function App to Azure

| Prompt | Your Action |
|--------|-------------|
| **Enter a globally unique name** | e.g., `yourname-photopipe-func` |
| **Select a runtime stack** | `Python 3.12` |
| **Select an OS** | `Linux` |
| **Select a resource group** | `rg-serverless-lab4` |
| **Select a location** | Same region as your storage account |
| **Select a hosting plan** | `Consumption` |
| **Select a storage account** | Create new (e.g., `stphotopipefunc`) |
| **Select an Application Insights** | `Skip for now` |

### Connecting Components on Azure
- Add the `STORAGE_CONNECTION_STRING` to your function app using the connection string of your storage account.

- In the CORS of your function app, add `*` to the allowed origins list

### Running the Application
- Open `client.html` and fill out the required fields, including your SAS Token, function app URL, and storage account name