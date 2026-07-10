## CST8917 - Serverless Applications | Summer 2026

# Lab 2: Smart Image Analyzer 

Azure Durable Functions app that analyzes uploaded images using the **Fan-Out/Fan-In** pattern. When an image is uploaded to Blob Storage, four analyses run in parallel, results are combined into a report, and stored in Azure Table Storage.


**Student Name**: IDRIS JOVIAL SOP NWABO

**Student ID**: 041199877

**Semester**: Summer 2026

---

## Demo Video

🎥 [Watch Demo Video](https://www.youtube.com/watch?v=3iMvC1Jw7lM)

---


## Prerequisites

- Python 3.11 or 3.12
- [Azure Functions Core Tools](https://learn.microsoft.com/azure/azure-functions/functions-run-local) v4
- [Azurite](https://learn.microsoft.com/azure/storage/common/storage-use-azurite) (VS Code extension or standalone)
- Azure Storage Explorer (optional, for uploading images)

## Setup

1. Create and activate a virtual environment:

   ```bash
   python3.12 -m venv .venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   ```

2. Install dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

3. Copy local settings:

   ```bash
   cp local.settings.example.json local.settings.json
   ```

## Run Locally

1. **Start Azurite** — Command Palette: `Azurite: Start`, or:

   ```bash
   azurite --silent --location .azurite --debug .azurite/debug.log
   ```

2. **Create the `images` container** (first time only):

   ```bash
   curl -X PUT "http://127.0.0.1:10000/devstoreaccount1/images?restype=container" \
     -H "x-ms-version: 2020-10-02"
   ```

3. **Start the function app** — press `F5` in VS Code, or from the terminal:

   ```bash
   source .venv/bin/activate
   languageWorkers__python__defaultExecutablePath="$(pwd)/.venv/bin/python" func start
   ```

   > If `func start` fails with `No module named 'azure.durable_functions'`, ensure the venv is active or use the `languageWorkers__python__defaultExecutablePath` command above.

4. **Upload a test image** to the `images` container using Azure Storage Explorer, or:

   ```bash
   curl -X PUT "http://127.0.0.1:10000/devstoreaccount1/images/test-image.jpg" \
     -H "x-ms-version: 2020-10-02" \
     -H "x-ms-blob-type: BlockBlob" \
     -H "Content-Type: image/jpeg" \
     --data-binary @/path/to/your/image.jpg
   ```

5. **Retrieve results**:

   - All results: `http://localhost:7071/api/results`
   - With limit: `http://localhost:7071/api/results?limit=5`
   - By ID: `http://localhost:7071/api/results/{id}`

   You can also use `test-function.http` with the REST Client extension.

## Functions

| Function | Type | Description |
| -------- | ---- | ----------- |
| `blob_trigger` | Blob trigger | Starts orchestration on image upload |
| `image_analyzer_orchestrator` | Orchestrator | Fan-out/fan-in + report chaining |
| `analyze_colors` | Activity | Dominant color extraction (Pillow) |
| `analyze_objects` | Activity | Mock object detection |
| `analyze_text` | Activity | Mock OCR |
| `analyze_metadata` | Activity | Real image metadata (Pillow) |
| `generate_report` | Activity | Combines all analyses |
| `store_results` | Activity | Saves report to Table Storage |
| `get_results` | HTTP GET | Retrieves stored results |

## Deploy to Azure

1. Create a Function App (Python 3.12, Consumption plan) with a Storage Account.
2. Add `ImageStorageConnection` in Function App **Configuration** (use the storage account connection string).
3. Create an `images` container in the storage account.
4. Deploy: Command Palette → `Azure Functions: Deploy to Function App`.
5. Upload an image to the `images` container, then visit `https://<your-app>.azurewebsites.net/api/results`.


## Author

CST8917 — Serverless Applications, Lab 2

## Technical Explanations


---

## Challenges and Learnings (Optional)


---

## Acknowledgments

[Optional: Credit any resources, documentation, or people who helped you]
