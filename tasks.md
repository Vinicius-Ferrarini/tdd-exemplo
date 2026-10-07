---
title: "Tasks — Decomposição"
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
# Tasks — Decomposição

Fazer na ordem, um arquivo por vez. Ao fim de cada etapa, rodar `cd app && python -m pytest`. Se a resposta for cortada, continuar de onde parou.

- [ ] 1. `app/requirements.txt` (fastapi, uvicorn, pytest, httpx) e instalar
- [ ] 2. Repositório e modelos (`store.py`, `models.py`)
- [ ] 3. `main.py` com app, handler 400 e `reset_state()`; `test_app.py` com os 26 testes do tests.md
- [ ] 4. UC1: quadras — testes T1–T5
- [ ] 5. UC2: clientes — testes T6–T8
- [ ] 6. UC3: validações e preço da reserva — testes T9–T17
- [ ] 7. UC3: sobreposição — testes T18–T20
- [ ] 8. UC4: consulta e cancelamento — testes T21–T22
- [ ] 9. UC5: listagens — testes T23–T26
- [ ] 10. `Dockerfile` (igual ao plan.md) e `README.md` com como rodar local, com Docker, com Podman e os testes
- [ ] 11. Rodar `python -m pytest -v` e corrigir o código até os 26 passarem, sem alterar os testes
