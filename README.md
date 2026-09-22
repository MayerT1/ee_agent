# Earth Engine ADK Biomass Estimation Agent

An ADK-based agent that answers ecological/carbon questions about a GeoJSON
polygon using Google Earth Engine + Vertex AI (Gemini). Sourced from the
[earthengine-community `biomass_estimation_agent` example](https://github.com/google/earthengine-community/tree/master/examples/adk_agents/biomass_estimation_agent).

## Prerequisites

- A GCP Cloud Project with a **billing account** attached.
  **This consumes billable resources (Vertex AI / Gemini calls). Shut it
  down when not in use — see "Shutting down" below.**
- APIs enabled on that project:
  - [Earth Engine API](https://console.cloud.google.com/apis/api/earthengine.googleapis.com/)
  - [Vertex AI API](https://console.cloud.google.com/apis/api/aiplatform.googleapis.com/)
  - [Service Usage API](https://console.cloud.google.com/apis/api/serviceusage.googleapis.com/)
  - [Cloud Resource Manager API](https://console.cloud.google.com/apis/api/cloudresourcemanager.googleapis.com/)
- The project **registered with Earth Engine** (separate from enabling the
  API — do this at https://console.cloud.google.com/earth-engine/configuration?project=YOUR-PROJECT-ID).

⚠️ **Gotcha:** `GOOGLE_CLOUD_PROJECT` in `.env` needs the project **ID**
(the short string, e.g. `gedi-vit`), not the numeric project number
(e.g. `712257425884`). The numeric ID gets far enough to hit the Earth
Engine API, then fails with "Project not registered" because EE resolves
that to the ID string internally. Use the ID string from the start.

## Setup

```powershell
pip3 install -U google-adk earthengine-api
```

Create `.env` in this folder (not committed — gitignored) with:
```
GOOGLE_CLOUD_PROJECT="your-project-id"
GOOGLE_GENAI_USE_VERTEXAI=TRUE
```

## Authenticate

```powershell
gcloud auth application-default login
```
or, without `gcloud`:
```powershell
earthengine authenticate --scopes https://www.googleapis.com/auth/earthengine,https://www.googleapis.com/auth/cloud-platform
```

## Run

From the **parent** directory of `ee_agent/`:
```powershell
cd ..
adk web
```
Open `http://127.0.0.1:8000/dev-ui/`, select `ee_agent`, and ask:
```
What can you do?
```
Then try a polygon:
```
Please tell me about {"type":"Polygon","coordinates":[[[-122.29065062984834,37.24640452631664],[-122.30163695797334,37.2361551304299],[-122.28052260860811,37.2306882160823],[-122.26610305294405,37.24408145207234]]],"evenOdd":true}
```

## Shutting down (avoid ongoing cost)

1. `Ctrl+C` the `adk web` terminal — stops all local compute/billing triggers.
2. Disable the billable APIs on the project until next use:
   - https://console.cloud.google.com/apis/api/earthengine.googleapis.com/overview?project=YOUR-PROJECT-ID
   - https://console.cloud.google.com/apis/api/aiplatform.googleapis.com/overview?project=YOUR-PROJECT-ID

## Spinning back up

1. Re-enable the two APIs above.
2. Recreate `.env` (see Setup) if it's not still on disk.
3. `gcloud auth application-default login` if credentials expired.
4. `adk web` from the parent directory.

## Files

- `agent.py` — agent definition, EE/Vertex AI initialization
- `prompts.py` — system prompt / instructions
- `tools.py` — Earth Engine data tools (biomass, land cover, change detection, etc.)
- `__init__.py` — package entry point
