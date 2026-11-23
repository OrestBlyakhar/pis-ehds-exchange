# ПІС — обмін медичними даними

Прототип платформи керованого обміну медичними даними між закладами (за мотивами EHDS).

Первинне використання: лікувальний заклад передає або отримує дані пацієнта для лікування. Вторинне використання: дослідник отримує лише знеособлений знімок, якщо є дозвіл, чинна згода на досліджувані категорії і в групі не менше ніж 5 різних пацієнтів. Згода, перевірка доступу (RBAC/ABAC) і журнал аудиту входять у сам обмін.

Репозиторій: https://github.com/OrestBlyakhar/pis-ehds-exchange

Стек етапу 1 (рішення D8): Python (FastAPI), PostgreSQL, React (Vite).

## Каркас тек

Дерево нижче — розділ 6 документа `docs/01-requirements/stage-1-and-requirements.md`. Сам документ у `main` ще не злитий: його переносить PIS-12. Каркас повторює назване там дерево і не заповнює чужі документи.

```text
pis-ehds-exchange/
  README.md
  .gitignore
  .env.example
  docs/
    00-organization/
      roles.md
      methodology.md
      meetings/
      retros/
    01-requirements/
      actors-and-use-cases.md
      nfr.md
      data-governance.md
      data-dictionary.md
    02-swot/
    03-architecture/
      uml/
      zachman.md
      adr/
    04-prototype/
    05-testing/
    06-report/
  backend/
  frontend/
  tests/
```

Порожню теку тримає `.gitkeep`. Файл без вмісту задачі має один рядок, що вміст додає окрема задача. Діаграми в Git пишуться Mermaid або PlantUML.

## Документи цього спринту

- [НФВ і стек](docs/01-requirements/nfr.md) — PIS-17
- [Контур компонентів](docs/01-requirements/component-contour.md) — PIS-19
