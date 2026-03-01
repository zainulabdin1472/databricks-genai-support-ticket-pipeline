Databricks GenAI Support Ticket Pipeline

Real-world flavored Databricks pipeline that blends classic data engineering with generative AI inference at scale.

Core flow:
- Generates synthetic, multi-tenant, multi-lingual (en/ur/ar) support tickets across channels (email/chat/phone/portal)
- Bronze layer: raw ingestion into partitioned Delta tables (event_date bucketing)
- Silver layer: lightweight cleansing, text normalization, deduplication
- LLM layer (Silver_LLM): incremental batch enrichment using gpt-4o-mini — structured JSON output for:
  • concise 1–2 sentence summaries
  • zero-shot sentiment (pos/neu/neg)
  • priority scoring (P1–P4)
  • topic keyword extraction
- Gold layer: time-series aggregates per tenant (daily ticket volume, neg sentiment ratio, P1/P2 volume)
- Consumption: two lightweight SQL views ready for Databricks SQL dashboards, Power BI, or quick ad-hoc queries

Key patterns used:
- Medallion architecture (Bronze → Silver → Gold)
- Delta Lake ACID + partitioning for efficient incremental writes
- Resumable LLM processing (left-anti join to skip already enriched records)
- Rate-limit friendly batching with exponential-backoff-friendly sleep
- Structured output prompting + JSON parsing fallback

Main artifact: de_genai.ipynb (self-contained notebook)

Tech stack highlights:
- Databricks Runtime (PySpark 3.5+, Delta Lake 3.x)
- OpenAI API (gpt-4o-mini with function calling / structured outputs)
- Spark SQL for final KPI materialization + view definitions

Quick start:
1. Drop the notebook into a Databricks workspace
2. Inject OpenAI key (dbutils.secrets.get or os.environ)
3. Execute sequentially — enrichment is chunked so you can resume anytime

Future vectors:
- Embed summaries → vector index (Databricks Vector Search / Chroma)
- Delta Live Tables for near-real-time ingestion
- LLM observability (token usage, latency, cost tracking)
- Prompt caching / fine-tuning for cost reduction
