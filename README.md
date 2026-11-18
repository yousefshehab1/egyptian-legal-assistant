# Egyptian Legal Assistant

### Arabic legal-text retrieval with FAISS and language models

A notebook-based RAG prototype that retrieves passages from Egyptian legal texts and uses a language model to draft an Arabic answer. It includes a right-to-left Gradio interface.

> Educational prototype, not legal advice. Check every answer and citation against current legislation and a qualified lawyer when needed.

## How it works

```text
Legal source pages -> text chunks -> Arabic embeddings -> FAISS index
Question -> top 5 chunks -> Arabic prompt -> Groq model -> Gradio answer
```

1. Load two legal-text pages from Manshurat.
2. Split the documents into 1,000-character chunks with a 200-character overlap.
3. Encode chunks with `sayed0am/arabic-english-bge-m3`, using normalized embeddings.
4. Store embeddings in FAISS and retrieve five chunks for each question.
5. Pass the question and retrieved context to a Groq-hosted language model through LangChain.
6. Display the answer in an Arabic Gradio interface.

## Technology

| Part | Implementation |
| --- | --- |
| Embeddings | `sayed0am/arabic-english-bge-m3` via Hugging Face |
| Retrieval | FAISS, five chunks per query |
| Orchestration | LangChain |
| Generation | Groq API, configurable model |
| Interface | Gradio, right-to-left layout |
| Intended environment | Google Colab, GPU recommended |

## Getting started

1. Open `egyptian_legal_assistant.ipynb` in Google Colab.
2. Select a GPU runtime if available. CPU fallback is supported by the code but may be slow.
3. In Colab Secrets, add `GROQ_API_KEY` and `GROQ_MODEL` and enable notebook access. Choose a model currently available in your Groq account. Environment variables with the same names also work.
4. Optionally add `HUGGINGFACEHUB_API_TOKEN` if authenticated model access is needed.
5. Run the notebook cells in order. The sample API query is commented out; uncomment it only when ready to make a request. The final cell launches the demo.

The `.env.example` file lists variable names only. The notebook does not automatically load `.env` files. Network access is required to install packages, download embeddings, load source pages and call Groq. Provider usage may incur charges.

### Model compatibility

The original notebook configured `deepseek-r1-distill-llama-70b` and `qwen-qwq-32b`; its answer chain used Qwen. Both IDs have since been shut down, according to [Groq's deprecation notice](https://console.groq.com/docs/deprecations).

This cleaned edition requires an explicit `GROQ_MODEL` instead of silently choosing a replacement. No replacement model has been evaluated for this project. Dependencies are unpinned because no tested environment lockfile was supplied. The cleaned notebook has passed static structure and syntax checks, but has not been run end to end.

## Sources and scope

The notebook loads these pages at runtime:

- [Egyptian Constitution, amended 2019](https://manshurat.org/node/14675)
- [Penal Code, Law No. 58 of 1937](https://manshurat.org/node/14677)

These are third-party legal-text pages, not a verified live legislative database. The Penal Code page labels its last amendment as August 15, 2021. This project does not establish that either page includes every current amendment. Source text is not bundled in this repository.

## Limitations

- The notebook covers two source pages, not all Egyptian law. Some demo questions may be outside the retrieved material.
- Whole-page loading can include navigation and other non-article text.
- Generated answers and article references can be wrong, incomplete or unsupported, even when the prompt asks for citations.
- The repository contains no reproducible embedding comparison, labeled evaluation set or accuracy metrics. It does not claim one model outperformed seven alternatives.
- No production deployment, authentication or case-data privacy controls are included.

## Security and privacy

- Keep API keys in Colab Secrets or environment variables, never in source code or outputs.
- Questions and retrieved text are sent to Groq for generation. Do not enter confidential client or case information.
- Gradio sharing is off by default. Enabling it creates an externally accessible temporary demo link.
- Clear notebook outputs before committing. The FAISS index and local secret files are excluded by `.gitignore`.

## Repository files

```text
README.md
egyptian_legal_assistant.ipynb
requirements.txt
.env.example
.gitignore
```

## Cleanup notes

This edition removes embedded credentials, saved outputs, execution metadata and unused code; updates LangChain imports; adds configuration checks and CPU fallback; and disables automatic shared demo links. The original retrieval settings, legal source URLs, Arabic prompt and demo structure are retained. No benchmark or legal-accuracy result is implied.
