# ESG RAG Analysis Pipeline

This repository contains an n8n workflow exported as JSON for automating ESG and sustainability analysis from PDF email attachments.

## What it does

- listens for incoming Gmail messages containing PDF attachments
- downloads the attached PDF files
- loads and splits the PDF text for ingestion
- stores document embeddings in Pinecone using the sender email as the namespace
- uses a LangChain information extractor to parse ESG metrics from the document
- generates a structured ESG report as HTML/PDF
- emails the generated report back to the sender

## Workflow components

1. **When Email with PDF Arrives**
   - Trigger: `gmailTrigger`
   - Filters for emails with PDF attachments
   - Downloads attachments and prefixes them with `attachment_`

2. **Store in Pinecone**
   - Inserts document data into a Pinecone vector store
   - Uses the sender email as namespace via `={{ $json.from }}`

3. **Load PDF**
   - Loads attachment binary data using `pdfLoader`
   - Splits by pages

4. **Split Text**
   - Recursively splits the document text with 200-character overlap

5. **Extract ESG Data**
   - Uses `informationExtractor` to extract structured ESG fields from text
   - Expected output fields:
     - `esg_score`
     - `environmental_score`
     - `social_score`
     - `governance_score`
     - `carbon_footprint_tons`
     - `carbon_intensity`
     - `sustainability_summary`
     - `key_risks`
     - `recommendations`

6. **Llama Model**
   - Language model node configured for Ollama
   - `model` currently contains a placeholder and must be updated before use

7. **Generate PDF Report**
   - Custom code node builds an HTML report and converts it to PDF

8. **Email Report to Sender**
   - Sends the generated report back through Gmail
   - Uses dynamic subject, recipient, and attachment fields

9. **Embeddings Cohere**
   - Adds an embeddings node for the Pinecone ingestion step

## Setup

1. Install and run n8n.
2. Import the workflow from `esg-rag-analysis-pipeline.json`.
3. Configure credentials:
   - Gmail OAuth2
   - Pinecone index and environment
   - Ollama model endpoint or equivalent LLM
4. Update the `Llama Model` node `model` field with a valid model name.
5. Adjust the Pinecone index settings if needed.
6. Activate the workflow in n8n.

## Notes

- The workflow is currently inactive in the exported JSON.
- Keep credentials and sensitive keys out of version control.
- The workflow assumes the sender email is available in `$json.from` for namespace partitioning.

## File rename

The workflow JSON has been renamed to `esg-rag-analysis-pipeline.json` for a cleaner filename and easier import.
