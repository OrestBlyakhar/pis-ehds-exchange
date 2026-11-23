# ПІС — обмін медичними даними

Прототип платформи керованого обміну медичними даними між закладами (за мотивами EHDS).

Первинне використання: лікувальний заклад передає або отримує дані пацієнта для лікування. Вторинне використання: дослідник отримує лише знеособлений знімок, якщо є дозвіл, чинна згода на досліджувані категорії і в групі не менше ніж 5 різних пацієнтів. Згода, перевірка доступу (RBAC/ABAC) і журнал аудиту входять у сам обмін.

Репозиторій: https://github.com/OrestBlyakhar/pis-ehds-exchange

## Каркас тек

PIS-10 просить структуру `docs/` з розділу 6 документа `docs/01-requirements/stage-1-and-requirements.md`. Цього файлу у віддаленому репозиторії немає: його має покласти PIS-12, а коментар до PIS-12 каже, що файл лишився в локальній копії, бо віддаленого репозиторію тоді ще не було. Тому дерево нижче зібране не з тексту розділу 6, а зі шляхів, які описи і коментарі Jira вже називають явно:

- `docs/00-organization/meetings/` — PIS-11
- `docs/00-organization/jira-board.md`, `docs/00-organization/backlog-and-assignees.md` — PIS-7, PIS-8
- `docs/01-requirements/` — PIS-12, PIS-13, PIS-15, PIS-16, PIS-18
- `docs/02-swot/` — PIS-21, PIS-22
- `docs/03-architecture/uml/` — PIS-14, PIS-20, PIS-23
- `docs/05-testing/` — PIS-43, PIS-44
- `docs/06-report/` — PIS-46, PIS-49

```
docs/
  00-organization/
    meetings/
  01-requirements/
  02-swot/
  03-architecture/
    uml/
  05-testing/
  06-report/
```

Теки `docs/04-…` у цих шляхах немає. Між `docs/03-architecture` і `docs/05-testing` номер вільний; окрему теку не створював.

Порожню теку тримає `.gitkeep` з рядком, що вміст додає інша задача. Файли чужих задач цей каркас не підставляє.

## Документи цього спринту

- [НФВ і стек](docs/01-requirements/nfr-and-stack.md) — PIS-17
- [Контур компонентів](docs/01-requirements/component-contour.md) — PIS-19
