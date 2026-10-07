---
title: "Tests — Cenários de Teste (TDD)"
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
# Tests — Cenários de teste (TDD)

Cada item abaixo vira um teste em `app/test_app.py` (`def test_t1_...` até `def test_t26_...`). Os casos de borda são obrigatórios.

Preparação: `TestClient(app)`; fixture `autouse` que chama `reset_state()`; reservas sempre num dia futuro (hoje + 30 dias), salvo T15; quadra de teste com `pricePerHour` 100.

| # | Cenário | Tipo |
| --- | --- | --- |
| T1 | `POST /courts` válida retorna 201 + `id` inteiro | feliz |
| T2 | `POST /courts` com `pricePerHour` = 0 retorna 400 | borda |
| T3 | `POST /courts` sem `pricePerHour` retorna 400 (não 422) | borda |
| T4 | `POST /courts` sem `type` retorna 201 com `type` = `futebol` | borda |
| T5 | `POST /courts` com `type` = `tênis` retorna 400 | borda |
| T6 | `POST /clients` com `telefone` retorna 201; `GET /clients` mostra o telefone | feliz |
| T7 | `POST /clients` com `phone` retorna 201 com `telefone` e `phone` iguais | borda |
| T8 | `POST /clients` sem telefone retorna 400 | borda |
| T9 | Reserva de 2h (10:00–12:00) retorna 201, `price` = 200.0 e `status` = `active` | feliz |
| T10 | Reserva com `clientId` = `"cliente-1"` (cliente não cadastrado) retorna 201 | borda |
| T11 | Reserva de exatamente 1h retorna 201 com `price` = 100.0 | borda |
| T12 | Reserva de exatamente 3h retorna 201 com `price` = 300.0 | borda |
| T13 | Reservas de 59min e de 3h01 retornam 400 | borda |
| T14 | Reserva com `end` igual a `start` retorna 400 | borda |
| T15 | Reserva com `start` ontem retorna 400 | borda |
| T16 | Reserva em quadra inexistente (`courtId` 999) retorna 404 | borda |
| T17 | Reservas de 1h01 e de 1h30 cobram 200.0 (arredonda para cima) | borda |
| T18 | Reserva 10:59–12:00 sobre outra 10:00–11:00 na mesma quadra retorna 409 | borda |
| T19 | Reserva 11:00–12:00 encostada em 10:00–11:00 retorna 201 | borda |
| T20 | Mesmo horário em quadra diferente retorna 201 | borda |
| T21 | Cancelar retorna 204; depois `GET` e novo `DELETE` retornam 404 | feliz |
| T22 | Depois de cancelar, o mesmo horário pode ser reservado (201) | borda |
| T23 | `GET /bookings?courtId=` traz só reservas daquela quadra, ordenadas por `start` | feliz |
| T24 | Reserva cancelada não aparece em `?date=` nem em `?courtId=` | borda |
| T25 | Reserva 23:00–01:00 aparece em `?date=` dos dois dias | borda |
| T26 | `GET /bookings?date=05/11/2026` retorna 400 | borda |
