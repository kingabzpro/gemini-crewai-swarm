# Build an Agent Swarm with CrewAI and Gemini 3.5 Flash

A beginner-friendly, end-to-end Jupyter notebook that walks you through building a small **multi-agent swarm** with [CrewAI](https://docs.crewai.com/) powered by [Google Gemini 3.5 Flash](https://deepmind.google/models/gemini/flash/).

Four cooperating agents — a **Researcher**, a **Writer**, a **Reviewer**, and a **Coordinator** — take a single topic, do research on it, draft a blog post, polish it, and hand you a final article.

---

## What's in this repo

| File | Purpose |
| --- | --- |
| `Build_Agent_Swarm_with_CrewAI_and_Gemini_Flash.ipynb` | The complete guided tutorial. Open in Jupyter / VS Code / Colab. |
| `requirements.txt` | Pinned Python dependencies (`crewai`, `google-genai`, `litellm`, `python-dotenv`, `ipykernel`). |
| `.env.example` | Template for your `.env` file — copy this and fill in your Gemini API key. |
| `.gitignore` | Standard Python / Jupyter ignores (your `.env` is already excluded). |
| `LICENSE` | MIT license. |

---

## Quick start

### 1. Clone and set up a virtual environment

```bash
git clone https://github.com/<your-username>/crewai-gemini-agent-swarm.git
cd crewai-gemini-agent-swarm

python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```bash
pip install -U -r requirements.txt
```

> CrewAI also supports the [uv](https://docs.astral.sh/uv/) package manager — if you have it installed, you can run `uv pip install -r requirements.txt` instead.

### 3. Get a Gemini API key (free)

1. Go to <https://aistudio.google.com/app/apikey>.
2. Click **Create API key** and copy the value.
3. Copy the env template and paste your key in:

   ```bash
   cp .env.example .env       # macOS / Linux
   copy .env.example .env     # Windows
   ```

   Then edit `.env` so it reads:

   ```dotenv
   GEMINI_API_KEY=your-real-key-here
   ```

   > Either `GEMINI_API_KEY` *or* `GOOGLE_API_KEY` works — CrewAI reads both. See the [LLMs docs](https://docs.crewai.com/en/concepts/llms#setting-up-your-llm).

### 4. Run the notebook

```bash
jupyter notebook Build_Agent_Swarm_with_CrewAI_and_Gemini_Flash.ipynb
```

Or open it directly in VS Code with the Jupyter extension. Every cell runs top-to-bottom with no manual edits required.

#### Alternative: Google Colab

You can also upload the notebook to [Google Colab](https://colab.research.google.com/). The install cell uses `%pip`, which Colab supports natively; the only extra step is to set the `GEMINI_API_KEY` via `os.environ` or Colab's "Secrets" panel (the notebook will prompt you to set it).

---

## What the notebook covers

1. Title and short intro
2. What we are building
3. Architecture overview (with a Mermaid diagram of the agent swarm)
4. Installing the dependencies
5. Setting up API keys securely with `python-dotenv`
6. Configuring **Gemini 3.5 Flash** with CrewAI
7. Creating the four agents (Researcher, Writer, Reviewer, Manager)
8. Defining a `Task` for each agent with clear `expected_output` contracts
9. Building the `Crew` workflow
10. Running the swarm on the example topic **"The benefits of local-first AI agents"**
11. Displaying and walking through the final output
12. Error-handling tips and a robust `run_crew_safely()` wrapper
13. Optional improvements: save to file, add tools, enable memory, switch to `Process.hierarchical`, mix-and-match models
14. Conclusion and next steps

---

## A note on the model name

The title of this guide says **"Gemini 3.5 Flash"** because that is the current Flash-tier name on Google's official [DeepMind Gemini Flash page](https://deepmind.google/models/gemini/flash/) and the official [Gemini API × CrewAI example](https://ai.google.dev/gemini-api/docs/crewai-example).

The exact model string we pass to CrewAI is:

```python
"gemini/gemini-3.5-flash"
```

The leading `gemini/` prefix is required by [LiteLLM](https://docs.litellm.ai/docs/providers/gemini), the routing layer CrewAI uses to talk to Google AI Studio. If Google rotates the Flash model name in the future, change **only this one string** — for example:

- `gemini/gemini-3.5-flash` — current default
- `gemini/gemini-2.5-flash` — previous stable Flash
- `gemini/gemini-2.0-flash` — older, paid tier only

The Google team also recommends a **`temperature=1.0`** for Gemini 3 family models; the notebook uses that value.

---

## Project structure

```
crewai-gemini-agent-swarm/
├── Build_Agent_Swarm_with_CrewAI_and_Gemini_Flash.ipynb   ← start here
├── README.md                                              ← you are here
├── requirements.txt
├── .env.example
├── .gitignore
└── LICENSE
```

---

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `RuntimeError: No Gemini API key found` | Make sure `.env` exists in the notebook's CWD (or one level above) and the value is your **real** key, not the placeholder. |
| `litellm.AuthenticationError` | Your key is invalid, revoked, or restricted. Create a new one at <https://aistudio.google.com/app/apikey>. |
| `404 models/gemini-3.5-flash not found` | Google renamed the model. Try `gemini/gemini-2.5-flash` instead, or check the [Gemini models page](https://ai.google.dev/gemini-api/docs/models). |
| `litellm.BadRequestError ... models/...` | You have **two** `gemini/` prefixes in the model name. Use exactly `gemini/gemini-3.5-flash`. See [crewAI#2645](https://github.com/crewAIInc/crewAI/issues/2645). |
| `ModuleNotFoundError: google.generativeai` | You installed the legacy SDK. Run `pip install -U google-genai` — the legacy package was retired on **30 Nov 2025**. |
| Agents produce empty / very short answers | Lower `temperature` to `0.3-0.5` and make the `expected_output` strings more specific. |
| Want to see what's going over the wire | Add `os.environ["LITELLM_LOG"] = "DEBUG"` (and optionally `litellm._turn_on_debug()`) before `crew.kickoff()`. |

---

## Docs checked

The code and instructions in this repo were verified against the following official sources.

### CrewAI

- [CrewAI docs home](https://docs.crewai.com/) — overview, concepts, and CLI.
- [CrewAI quickstart](https://docs.crewai.com/en/quickstart) — installation and first project.
- [LLMs / Google Gemini integration](https://docs.crewai.com/en/concepts/llms) — the `gemini/...` model string, `LLM(model=...)` API, and env-var requirements.
- [Agents concept guide](https://docs.crewai.com/en/concepts/agents) — `role`, `goal`, `backstory`, `llm`, `allow_delegation`.
- [Tasks & Processes](https://docs.crewai.com/en/concepts/tasks) and [Processes](https://docs.crewai.com/en/concepts/processes) — `Task` contract, `context=[...]`, `Process.sequential` vs `Process.hierarchical`, `manager_llm`.
- [CrewAI tools catalogue](https://docs.crewai.com/en/concepts/tools) — `SerperDevTool`, `FileReadTool`, etc.
- [GitHub: crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) and the related [bug #2645](https://github.com/crewAIInc/crewAI/issues/2645) on the `models/` double-prefix gotcha.

### Google AI / Gemini

- [Gemini API + CrewAI example (Google)](https://ai.google.dev/gemini-api/docs/crewai-example) — the official end-to-end sample this guide is patterned on.
- [Gemini API models](https://ai.google.dev/gemini-api/docs/models) — the canonical list of model strings.
- [DeepMind Gemini Flash](https://deepmind.google/models/gemini/flash/) — confirms the current **Gemini 3.5 Flash** naming.
- [Get a Gemini API key](https://aistudio.google.com/app/apikey) — where to generate the key used in `.env`.
- [Google Gen AI Python SDK on GitHub](https://github.com/googleapis/python-genai) — the modern, maintained SDK (`pip install google-genai`).
- [Google Gen AI SDK on PyPI](https://pypi.org/project/google-genai/) — install instructions.
- [Gemini API libraries overview](https://ai.google.dev/gemini-api/docs/libraries) — confirms the legacy `google-generativeai` package was retired on **30 Nov 2025** in favour of `google-genai`.

### LiteLLM (the routing layer used by CrewAI)

- [LiteLLM — Gemini provider](https://docs.litellm.ai/docs/providers/gemini) — model strings, env vars, advanced options.
- [LiteLLM — Vertex AI](https://docs.litellm.ai/docs/providers/vertex) — alternative Vertex-AI-backed setup.

---

## Extending the swarm

The notebook ends with five plug-and-play upgrades:

1. **Save the final post** to a markdown file in `output/`.
2. **Add a tool** like `SerperDevTool` to give the researcher live web access.
3. **Enable shared memory** (`memory=True` on the `Crew`).
4. **Switch to hierarchical mode** (`Process.hierarchical` + `manager_llm`).
5. **Mix models per agent** (e.g. Gemini 2.5 Pro for research, Gemini 3.5 Flash for the rest).

Each one is a small code change — the rest of the swarm keeps working as-is.

---

## License

MIT. See [`LICENSE`](./LICENSE).
