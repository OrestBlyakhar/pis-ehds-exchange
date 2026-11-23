# Activity Diagram UC-04. Запросити дані для лікування

Задача PIS-14. Потік від входу лікаря до запису аудиту. Джерело кроків — специфікація UC-04.

```mermaid
flowchart TD
  startNode([Лікар відкриває запит]) --> auth{Сеанс і роль лікаря активні?}
  auth -->|ні| denyRole[Результат deny без клінічних даних]
  denyRole --> auditDeny[Запис аудиту deny]
  auditDeny --> stopDeny([Кінець])
  auth -->|так| input[Ідентифікатор пацієнта, мета treatment, категорії]
  input --> episode{Заклад лікаря і прапорець поточного епізоду?}
  episode -->|ні| denyRole
  episode -->|так| consent{Чинна згода покриває категорії?}
  consent -->|немає згоди| glass{Лікар підтверджує аварійний доступ і вводить причину?}
  glass -->|ні| denyConsent[Результат deny]
  denyConsent --> auditDeny
  glass -->|так| ehrGlass[EHR віддає алергії, стани і медикаменти]
  ehrGlass --> noLab[Лабораторний архів у цей шлях не входить]
  noLab --> auditGlass[Аудит break_glass в тій самій транзакції]
  auditGlass --> showGlass([Лікар бачить обмежений набір і correlation id])
  consent -->|частина категорій| ehrPart[EHR віддає покриті категорії]
  ehrPart --> qualityPart[Перевірка якості]
  qualityPart --> auditPart[Аудит partial]
  auditPart --> showPart([Лікар бачить покриті і назви відхилених])
  consent -->|усі категорії| ehrFull[EHR віддає FHIR Bundle]
  ehrFull --> quality{Ідентифікатор пацієнта, відомий код, дата не в майбутньому, Bundle не порожній?}
  quality -->|ні| rejectQ[rejected_quality, набір не зберігається]
  rejectQ --> auditQ[Аудит rejected_quality]
  auditQ --> stopQ([Кінець без клінічних даних])
  quality -->|так| stale[Дані старші за 365 діб лишаються з quality=stale]
  stale --> auditOk{Запис аудиту allow в тій самій транзакції?}
  auditOk -->|ні| noData[Відповідь із даними не відправляється]
  noData --> stopFail([Кінець])
  auditOk -->|так| showOk([Лікар бачить Bundle і correlation id, пакет 30 діб])
```

Порядок перевірки збігається зі спільним правилом: збій автентифікації, ролі, згоди, якості або журналу зупиняє видачу. Окремого тихого шляху немає.

Часткова видача і аварійний доступ проходять ту саму перевірку якості і той самий запис аудиту в одній транзакції, що й повний Bundle. На схемі гілку `rejected_quality` розгорнуто на повному шляху, щоб не малювати три однакові відмови. Лабораторний архів в аварійний шлях не входить: це правило UC-04 AC2. Розбіжність із PIS-15 A-07 винесена в аркуш вичитки, а не знята цією діаграмою.