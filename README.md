# Metamodel Studio — automatic instance generation

## Install
Import `node_red_metamodel_with_generated_instances.json` in Node-RED, replacing/disabling the older metamodel flow to avoid duplicate HTTP routes. Deploy, then visit `http://127.0.0.1:1880/metamodel-ui` (do not open the HTML as a file). Configure `OPENAI_API_KEY`, optionally `OPENAI_MODEL` (default `gpt-5.5`) and `OPENAI_BASE_URL`.

## Workflow
1. Generate or edit the PlantUML class metamodel.
2. Under **Instance model editor**, enter a natural-language description and click **Generate instances from text (LLM)**. The generated objects and links populate editable JSON.
3. The API automatically runs a deterministic structural conformance check and displays results. Fix any failures and run **Validate instance model** to check instance constraints.
4. Alternatively import `.json` containing `{ "objects": [], "links": [] }` and validate it.

## REST API
`POST http://127.0.0.1:1880/api/metamodel/instances/generate`

JSON request:
```json
{"plantuml":"@startuml\nclass Building {\n id: String\n}\nclass Zone\nBuilding "1" -- "1..*" Zone\n@enduml","description":"Create Building B-001 and one Zone."}
```

JSON response:
```json
{"instances":{"objects":[],"links":[]},"validation":{"status":"PASS|FAIL|INCOMPLETE","summary":{"passed":0,"failed":0,"notEvaluated":0},"results":[]},"generation":"LLM proposal","validationEngine":"deterministic JavaScript"}
```

`POST /api/metamodel/instances/validate` remains available with `plantuml`, `instances`, and `constraints` or `rules`. Submit `rules: []` for conformance-only validation. Natural-language constraint mapping is LLM-assisted; satisfaction checks are deterministic. The implementation is an OCL-inspired restricted rule evaluator, **not full OCL**. Unsupported PlantUML constructs may be missed; review generated instances and model interpretation before formal use.

All webpage API calls target `http://127.0.0.1:1880`.
