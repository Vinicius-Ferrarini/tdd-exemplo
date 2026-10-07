---
title: "Constitution — Regras Persistentes do Projeto"
type: knowledge
status: done
area: resources
resource: talks
tags:
  - kind/knowledge
  - area/resources
  - resource/talks
  - status/done
created: 2026-10-07
updated: 2026-10-07
---
# Constitution — Regras persistentes do projeto

Ler os arquivos nesta ordem: constitution, spec, plan, tests, tasks. Depois seguir o tasks.md até o fim.

1. Código e identificadores em **inglês**; documentação em português. Os nomes dos campos JSON são exatamente os do spec.md (inclusive `telefone`).
2. API REST: recursos no plural (`/courts`, `/clients`, `/bookings`), JSON em camelCase, datas ISO 8601, IDs inteiros sequenciais começando em 1.
3. Framework: **Python 3.11 + FastAPI**; persistência em memória (dict) — sem banco de dados.
4. Dependências: somente `fastapi`, `uvicorn`, `pytest` e `httpx` (usado pelo TestClient).
5. Erros: 400 para dado inválido ou regra violada, 404 para não encontrado, 409 para conflito de horário. Nunca devolver 422.
6. Não criar rotas, campos ou regras que não estejam no spec.md. Em caso de dúvida entre arquivos, vale o spec.md.
7. Testes com **pytest**; cada cenário do tests.md vira uma função de teste. Não alterar teste para fazer passar.
8. Todo o código fica na pasta `app/`, seguindo a PEP 8. O servidor sobe em `0.0.0.0`, porta 8000.
