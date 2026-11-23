# Data dictionary FHIR-підмножини

Задача: PIS-16. Профіль: підмножина FHIR R4 для прототипу обміну, не повний FHIR.

У словнику п’ять ресурсів: Patient, AllergyIntolerance, Condition, MedicationStatement, Observation. Згода, журнал і дослідницький знімок є окремими класами матриці PIS-15 і в цю клінічну підмножину не входять.

Позначки приватності:

- **ID** — прямий ідентифікатор. У дослідницький CSV не копіюється.
- **QID** — квазіідентифікатор. У знімок потрапляє лише після узагальнення з політики PIS-18.
- **CLN** — клінічний атрибут. У знімок потрапляє, якщо на цю категорію є дослідницька згода.
- **SYS** — службове поле системи. Користувач його не редагує.

Посилання `patient` у клінічних ресурсах всередині лікувального контуру веде на Patient.id. У дослідницькому знімку це посилання замінюється псевдонімом.

## Patient

Категорія згоди не ставиться: ресурс керує ідентичністю. Лікар читає його разом із будь-якою дозволеною лікувальною категорією. Дослідник замість нього бачить лише псевдонім, стать і вікову групу.

| Поле | Тип | Обов’язковість | Приватність | Правило |
|---|---|---|---|---|
| id | string | 1 | SYS | Логічний id. Вхід HMAC, назовні досліднику не віддається |
| identifier | Identifier | 1 | ID | Медичний номер у закладі. У знімок не входить |
| active | boolean | 1 | SYS | Неактивний пацієнт не потрапляє в нові запити |
| name.family | string | 1 | ID | Прізвище. Лише лікувальний і аварійний контур |
| name.given | string | 1 | ID | Ім’я |
| gender | code | 1 | QID | `male`, `female`, `other`, `unknown`. У знімок копіюється як є |
| birthDate | date | 1 | ID | Повна дата. У знімок не копіюється; з неї рахується вікова група |
| address.city | string | 0..1 | ID | Місто. У знімок не копіюється |
| managingOrganization | Reference | 1 | QID | Один із двох закладів або лабораторія-джерело спостереження. У знімок входить код організації |

## AllergyIntolerance

Категорія згоди: алергії.

| Поле | Тип | Обов’язковість | Приватність | Правило |
|---|---|---|---|---|
| id | string | 1 | SYS | Ідентифікатор запису |
| patient | Reference(Patient) | 1 | SYS | У знімку замінюється на псевдонім |
| clinicalStatus | code | 1 | CLN | `active`, `inactive`, `resolved` |
| verificationStatus | code | 1 | CLN | `unconfirmed`, `confirmed`, `refuted` |
| category | code | 1 | CLN | `food`, `medication`, `environment`, `biologic` |
| criticality | code | 1 | CLN | `low`, `high`, `unable-to-assess` |
| code | CodeableConcept | 1 | CLN | Речовина або клас алергену. Текст і код |
| reaction.manifestation | CodeableConcept | 0..1 | CLN | Прояв, якщо він відомий |
| recordedDate | dateTime | 0..1 | CLN | Дата запису. У знімок входить місяць і рік |

## Condition

Категорія згоди: стани.

| Поле | Тип | Обов’язковість | Приватність | Правило |
|---|---|---|---|---|
| id | string | 1 | SYS | Ідентифікатор запису |
| patient | Reference(Patient) | 1 | SYS | У знімку замінюється на псевдонім |
| clinicalStatus | code | 1 | CLN | `active`, `recurrence`, `inactive`, `remission`, `resolved` |
| verificationStatus | code | 1 | CLN | `unconfirmed`, `confirmed`, `refuted` |
| code | CodeableConcept | 1 | CLN | Діагноз або стан. Текст і код |
| onsetDateTime | dateTime | 0..1 | CLN | Початок, якщо відомий. У знімок входить місяць і рік |
| recordedDate | dateTime | 1 | CLN | Дата фіксації. У знімок входить місяць і рік |

## MedicationStatement

Категорія згоди: ліки.

| Поле | Тип | Обов’язковість | Приватність | Правило |
|---|---|---|---|---|
| id | string | 1 | SYS | Ідентифікатор запису |
| patient | Reference(Patient) | 1 | SYS | У знімку замінюється на псевдонім |
| status | code | 1 | CLN | `active`, `completed`, `stopped`, `unknown` |
| medication | CodeableConcept | 1 | CLN | Препарат. Текст і код |
| effectivePeriod.start | dateTime | 0..1 | CLN | Початок приймання. У знімок входить місяць і рік |
| effectivePeriod.end | dateTime | 0..1 | CLN | Кінець, якщо є. У знімок входить місяць і рік |
| dosage.text | string | 0..1 | CLN | Короткий текст дози, без вільних приміток з ім’ям пацієнта |

## Observation

Категорія згоди: спостереження. Сюди входять вимірювання закладу і результати лабораторії.

| Поле | Тип | Обов’язковість | Приватність | Правило |
|---|---|---|---|---|
| id | string | 1 | SYS | Ідентифікатор запису |
| patient | Reference(Patient) | 1 | SYS | У знімку замінюється на псевдонім |
| status | code | 1 | CLN | `final`, `amended`, `cancelled`. Скасовані у знімок не входять |
| category | code | 1 | CLN | `vital-signs` або `laboratory` |
| code | CodeableConcept | 1 | CLN | Що виміряно. Текст і код |
| effectiveDateTime | dateTime | 1 | CLN | Коли виміряно. У знімок входить місяць і рік |
| valueQuantity.value | decimal | 0..1 | CLN | Числовий результат. Порожньо, якщо результат текстовий |
| valueQuantity.unit | string | 0..1 | CLN | Одиниця. Обов’язкова, коли є value |
| valueString | string | 0..1 | CLN | Текстовий результат. Рівно одне з valueQuantity або valueString |

## Що не входить у підмножину

Не використовуються Encounter, Procedure, DiagnosticReport, DocumentReference, Specimen і повні HumanName, Address, ContactPoint. Вільний текст поза переліченими полями заборонений: туди легко потрапляє ім’я, телефон або точна адреса.

Лікувальна відповідь лікаря збирається з оригінальних ресурсів. Дослідницький CSV збирається лише з полів CLN і QID після правил PIS-18.
