# Activity Diagram UC-06. Отримати дослідницький знімок

Задача PIS-20. Потік дослідника від відкриття дозволу до журналу. Ідентифікованих даних у відповіді немає.

```mermaid
flowchart TD
  startNode([Дослідник відкриває свій дозвіл]) --> role{Роль дослідник і сеанс активний?}
  role -->|ні| deny[deny]
  deny --> auditDeny[Аудит deny]
  auditDeny --> stopDeny([Кінець])
  role -->|так| permit{Дозвіл approved, у вікні дат, дослідник названий?}
  permit -->|ні| deny
  permit -->|так| same{Знімок за цим дозволом уже побудовано?}
  same -->|так| repeat[Віддати той самий файл]
  repeat --> auditRepeat[Аудит повторного завантаження]
  auditRepeat --> showSame([JSON і CSV без нового знімка])
  same -->|ні| cohort[Відібрати пацієнтів із чинною дослідницькою згодою на категорії дозволу]
  cohort --> filter[Застосувати фільтр когорти]
  filter --> small{Пацієнтів менше ніж 5?}
  small -->|так| smallDeny[denied_small_cell, файл не створюється]
  smallDeny --> auditSmall[Аудит denied_small_cell]
  auditSmall --> stopSmall([Кінець])
  small -->|ні| pseudo[Псевдоніми HMAC, прибрати прямі ідентифікатори, вікова група, зсув дат]
  pseudo --> vault[Відповідність лишається лише у vault]
  vault --> auditOk{Аудит успішно в тій самій транзакції?}
  auditOk -->|ні| noFile[Файл досліднику не віддається]
  noFile --> stopNo([Кінець])
  auditOk -->|так| showOk([Дослідник бачить FHIR JSON і похідний CSV])
  showOk --> vaultTry{Дослідник просить таблицю відповідності?}
  vaultTry -->|так| vaultDeny[Відмова і окремий запис журналу]
  vaultTry -->|ні| endNode([Кінець])
```

CSV не є другим джерелом правди. Повторний знімок потребує нового дозволу. Строк дозволу не довший за 90 діб.