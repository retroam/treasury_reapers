# Hermes-style skill packaging (reference)

This repository uses modular **skills**: each skill is a folder containing `SKILL.md` with operational contracts (when to use, inputs, procedure, outputs, failure signals, fallbacks).

At inference time, agents load only the skills relevant to the current task. The canonical on-disk layout mirrors common agent conventions:

```
skills/
  bulletin-retriever/SKILL.md
  plaintext-table-parser/SKILL.md
  arithmetic-verifier/SKILL.md
  cross-doc-aggregator/SKILL.md
```

See `notebooks/pipeline_single_instance.ipynb` for a **single LLM-driven instance** of the Skill-Centric Grounded Reasoning Pipeline. It retrieves documents from the demo corpus under `corpus/`, injects skill contracts into the system prompt, and sends the full context to **Qwen** via OpenRouter for end-to-end reasoning. In competition containers, corpus paths map to `/app/corpus/`.
