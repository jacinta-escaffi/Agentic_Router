# Agentic_Router
Agentic RAG router that sends each question to the right source (OpenAI docs, SEC 10-K filings, or live web search). Splits multi-part questions into sub-queries, compares a GPT router against TypeSafe's Jev on accuracy, safety, speed and cost, and adds a role-based semantic cache that can't leak answers across permissions.
