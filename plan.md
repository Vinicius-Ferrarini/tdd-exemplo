---
title: "Plan — Arquitetura e Decisões"
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
# Plan — Arquitetura e decisões

## Stack

- **Python 3.11 + FastAPI + uvicorn**, pydantic v2 (justificativa: pouco código e TestClient pronto para testar o contrato REST).
- Persistência **em memória**, um dict por recurso.

## Estrutura de arquivos a gerar (pasta `app/`)

```
main.py           # app FastAPI, rotas e handler de erro 400
models.py         # dataclasses Court, Client, Booking + conversão para JSON camelCase
service.py        # regras de negócio (duração, passado, sobreposição, preço)
store.py          # repositório em memória, IDs sequenciais, reset()
test_app.py       # testes pytest (refletem tests.md)
requirements.txt  # fastapi, uvicorn, pytest, httpx
Dockerfile        # imagem do app
README.md         # como rodar local, com Docker e com Podman, e como testar
```

## Decisões

1. Sobreposição detectada por comparação de intervalos: `start < existing.end and end > existing.start`, só contra reservas ativas da mesma quadra.
2. Duração em minutos: aceita de 60 a 180, inclusive.
3. Preço: `round(pricePerHour * math.ceil(minutes / 60), 2)`.
4. O FastAPI devolve 422 em corpo inválido; registrar `@app.exception_handler(RequestValidationError)` que responde 400.
5. Regras de valor (preço > 0, tipo da quadra, telefone obrigatório) ficam no `service.py` e lançam um erro com o status; o modelo pydantic só define os campos (opcionais com `None`).
6. Datas: `dt.astimezone().replace(tzinfo=None) if dt.tzinfo else dt`.
7. Cancelar muda `status` para `"cancelled"`; a reserva não é apagada, mas é tratada como inexistente.
8. O parâmetro `date` conflita com o tipo `date` do Python: declarar `day: date | None = Query(None, alias="date")`. Declarar `GET /bookings` antes de `GET /bookings/{id}`.
9. `main.py` expõe `reset_state()` (limpa os repositórios) para a fixture dos testes; não é rota.

## Dockerfile

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```
