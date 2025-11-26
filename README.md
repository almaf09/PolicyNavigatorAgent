# PolicyNavigatorAgent

## What the agents do
- **RAG Agent**: indexes the CCPA regulations PDF and scraped enforcement case examples, then routes questions to the right index for grounded answers.
- **Legal Research Agent**: always performs Tavily web search before responding, ideal for fresh policy/case updates.
- **Executive Orders Agent**: runs SQL over the executive orders dataset to find relevant EOs, then (optionally) opens linked HTML/PDF for richer summaries.
-**Google Docs Agent**: Creates a new Google Docs document for any topic or regulation
- **Team Agent**: acts as an orchestrator/dispatcher; it decides which specialized agent to invoke based on the user’s intent (e.g., EO questions → Executive Orders Agent; web-sourced legal research → Legal Research Agent; Creating doc with specific topic → Google Docs Agent ; CCPA-specific → RAG Agent) and aggregates the response.

## Project layout
- `Policy_Navigator.ipynb` — main notebook containing all four agents.
- `data/dataset/ccpa_regulations.pdf` — base CCPA regulations PDF.
- `data/dataset/executiveOrders.csv` / `executiveOrders.db` — executive orders dataset.
- `data/dataset/ccpa_enforcement_case_examples.txt` — generated at runtime by the scraper in `Policy_Navigator.ipynb`.

## Prerequisites
- Python 3.10+ and Jupyter.
- aiXplain account/key. Set `TEAM_API_KEY` in your environment (recommended) before running the notebooks.
- Tavily search tool ID (`TAVILY_SEARCH_TOOL` in `Policy_Navigator.ipynb`) must be valid for your account.
- The web scraper model ID used is `66f423426eb563fa213a3531`; swap it if your workspace differs.

## Quick start
1) Create/activate a virtual environment (optional but recommended).
2) Launch Jupyter Lab/Notebook in the repo root.
3) Open `Policy_Navigator.ipynb` and run cells top-to-bottom. The first install cell runs `pip install aixplain`.
4) Verify datasets exist under `data/dataset/`; the notebook asserts the PDF path and uses the CSV directly.

## Dataset/source links
- CCPA regulations PDF: `data/dataset/ccpa_regulations.pdf` from 'https://cppa.ca.gov/regulations/pdf/cppa_regs.pdf'
- CCPA enforcement cases: scraped from `https://oag.ca.gov/privacy/ccpa/enforcement` into `data/dataset/ccpa_enforcement_case_examples.txt`.
- Executive Orders dataset: `data/dataset/executiveOrders.csv` (plus `executiveOrders.db`) from 'https://catalog.data.gov/dataset/executive-orders-from-1994-present' 

## Tool integration steps
- aiXplain: set `TEAM_API_KEY` in your environment; the notebooks instantiate tools/agents via `ToolFactory`, `ModelFactory`, `IndexFactory`, and `AgentFactory`.
- Tavily: set `TAVILY_SEARCH_TOOL` to your Tavily tool ID; the Legal Research and Executive Orders agents call it for grounded web context.
- Web scraper: uses model ID `66f423426eb563fa213a3531` to scrape URLs (RAG Agent setup + User Input Agent URL mode). Replace with your own model ID if needed.
- Insert your Google Docs Tool ID at 'GOOGLE_DOCS_TOOL_ID'


## Example Input/Ouput
-"Create a Google Doc that summarizes the key points of the CCPA and give me its url"

    "The Google Doc titled 'CCPA Summary' has been created successfully. You can access it [here]. The document includes the following key points:
    1. **Right to Know**: Consumers have the right to know what personal information is being collected about them and how it is used and shared.
    2. **Right to Delete**: Consumers can request the deletion of their personal information held by businesses.
    3. **Right to Opt-Out**: Consumers can opt-out of the sale of their personal information to third parties.
    4. **Non-Discrimination**: Businesses cannot discriminate against consumers who exercise their rights under the CCPA.
    5. **Notice Requirements**: Businesses must provide clear notices to consumers about their data collection practices and their rights under the CCPA.
    6. **Enforcement**: The California Attorney General's Office enforces the CCPA, and businesses have a 30-day period to cure any alleged noncompliance after     being notified.
    7. **Global Privacy Control**: Businesses must honor consumer requests made through user-enabled global privacy controls.
    8. **Financial Incentives**: If businesses offer financial incentives for the collection of personal information, they must provide clear disclosures and obtain opt-in consent from consumers."


-"How many Executive Orders were signed in 2017"

   "There were 60 Executive Orders signed in 2017." 


-"Has Section 230 ever been challenged in court? What was the outcome?"

    "In recent court cases challenging Section 230, two notable outcomes are as follows:
    1. **Gonzalez v. Google LLC**: The U.S. Supreme Court upheld Section 230 protections, ruling that the plaintiffs' claims did not sufficiently state a viable legal claim. The Court emphasized that the allegations did not meet the necessary criteria for liability under the relevant statutes, effectively maintaining the immunity granted to platforms under Section 230.
    2. **Anderson v. TikTok**: The Third Circuit Court of Appeals reversed a lower court's dismissal of a lawsuit against TikTok, which had been based on Section 230. The appellate court found that the case had merit and that the lower court's application of Section 230 was not appropriate in this instance, allowing the case to proceed.
    These cases illustrate the ongoing legal debates surrounding Section 230 and its implications for online platforms."


-"Draft a one-page brief explaining Executive Order 14067."

    "The document has been created successfully. You can view it [here]. It contains the following sections:
    - **Introduction**: Overview of Executive Order 14067, its signing date, and purpose.
    - **Key Points**: Highlights including public input, regulatory framework, ban on CBDCs, and revocation.
    - **Conclusion**: Summary of the significance of the order and its revocation."


## Future Improvements
-**Frontend/UI**: Build a simple web UI to ask questions, view sources, and inspect retrieval scoring; add upload drop-zone with preview and progress; include light/dark themes and responsive layout.
-**Multilingual agent/translator**: Add language detection and translation layer (e.g., source → English for retrieval, English → user language for answers); expand embeddings to multilingual models; localize UI strings.
-**Follow-up questions**: Track conversation state, let the agent summarize context and propose follow-up queries; add a “clarify” prompt path for ambiguous questions; surface suggested next questions in the UI.
