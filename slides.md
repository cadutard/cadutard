% Avaliação de LLMs — Pacote Completo
% Autor: cadutard (compilado por assistente)
% 2025-09-07

# Slide 1 — Título
Avaliação de Modelos de Linguagem (LLMs)
- Resumo: metodologia, dataset, resultados (simulados), scripts para rodar evals reais, integração com OpenAI Evals / Freeplay / W&B, estimativa de custo.
- Entregáveis: CSVs, scripts, workflow CI que gera PDF automaticamente.

# Slide 2 — Sumário Executivo
- Objetivo: comparar modelos em QA factual, summarization e instruction-following, com métricas automáticas e humanas.
- Conclusão rápida (simulação): Modelo A (gpt-like) > Modelo C (Mistral7B) > Modelo B (Llama2-13B) em acurácia e menor hallucination rate.
- Próximo passo: rodar evals reais (1k–10k) e calibrar LLM-judge com amostra humana.

# Slide 3 — Metodologia
- Tarefas: QA factual, Summaries, Instruction-following.
- Dataset amostral: 30 prompts (10 por tarefa), seed=42.
- Anotação: 3 avaliadores humanos por item; majority vote / média (1–5).
- Métricas: Accuracy (QA), ROUGE-L (summ), Relevância (humana 1–5), Hallucination rate (%), Kappa (agreement LLM-judge vs humanos).

# Slide 4 — Dataset (exemplos)
- QA: "Quem foi o primeiro presidente do Brasil?" → referência: Deodoro da Fonseca.
- SUM: parágrafo sobre renováveis → referência resumo.
- INS: instruções em 3 passos (ex.: configurar 2FA).
(Arquivo completo: dataset_sample.csv)

# Slide 5 — Anotações humanas (exemplo)
- Estrutura: id, task, rater1, rater2, rater3, majority_label, avg_rating
- Exemplo: QA-001 rater1=1 rater2=1 rater3=1 → majority=1
(Arquivo completo: human_annotations.csv)

# Slide 6 — Resultados Agregados (simulados)
| Modelo | QA Accuracy | Avg Relevância | Hallucination % | ROUGE-L |
|--------|-------------:|---------------:|---------------:|--------:|
| Modelo A (gpt-like) | 0.86 | 4.45 | 6%  | 0.72 |
| Modelo B (Llama2-13B) | 0.72 | 3.90 | 14% | 0.61 |
| Modelo C (Mistral7B)  | 0.77 | 4.05 | 10% | 0.65 |

# Slide 7 — Insights por Task
- QA factual: erros frequentes em datas e números; solução: RAG + verificação factual.
- Summaries: omissões e facts inventados; combinação ROUGE + avaliação humana.
- Instruction-following: perda de ordem/etapas; usar checklists e prompts estruturados.

# Slide 8 — LLM-as-judge vs Humanos
- Kappa observado (simulado): A=0.78, B=0.58, C=0.64.
- Recomendação: calibrar LLM-judge com 5–10% de amostras humanas antes de confiar 100%.

# Slide 9 — Estimativa de Custos (resumo)
- Suposições: ~187 tokens/execution média; exemplo gpt-4o-mini pricing aproximado.
- Estimativas (média hipotética gpt-4o-mini):
  - 10k execs ≈ $0.53
  - 100k execs ≈ $5.30
  - 1M execs ≈ $53.25
- Notas: modelos maiores e LLM-as-judge adicional aumentam custo.

# Slide 10 — Arquitetura de Execução
- Opções:
  - Hosted (OpenAI Evals / Freeplay / W&B) — menos infra, mais custo variável.
  - Self-hosted (Llama/Mistral) — controle e custos de GPU.
- Observability: logar traces em Phoenix / W&B; salvar artefatos CSV para auditoria.

# Slide 11 — Pipeline proposto (CI)
1. PR trigger or schedule → run eval script (OpenAI/Local)  
2. Save per-run logs, metrics → upload to W&B/Phoenix  
3. Generate aggregated report CSV + graphs  
4. Build PDF (pandoc) and upload artifact

# Slide 12 — Instruções para Repetir (local)
1. Instalar deps: pandoc, LaTeX (TeXLive), Python deps (requirements.txt)
2. Rodar: bash generate_pdf.sh
3. Output: presentation.pdf

# Slide 13 — Próximos Passos
- Opção A: Rodar POC real (1k execs) com sua API key — gera resultados reais e PDF.
- Opção B: Gerar dataset ampliado (1k ou 10k prompts) e estimativa de custo detalhada.
- Opção C: Integrar Freeplay + W&B + GitHub Actions para runs periódicos.

# Slide 14 — Arquivos anexos no repositório
- dataset_sample.csv
- human_annotations.csv
- eval_results.csv (simulados)
- run_evals_openai.py
- run_freeplay_stub.py
- wandb_integration.py
- slides.md
- generate_pdf.sh
- .github/workflows/build-pdf.yml

# Slide 15 — Contato / Observações finais
- Observação: dados apresentados são simulação. Para dados reais preciso que você autorize o uso de chaves (ou rode localmente).