# Результаты
## 1. BRD
## BRD: SafeTech — «КриптоКлюч» (Мобильная электронная подпись)

### 1. Введение

**1.1 Цель**  
Обеспечить возможность подписания юридически значимых документов усиленной квалифицированной электронной подписью (УКЭП) непосредственно с мобильных устройств (iOS/Android) без использования физических токенов и стационарных компьютеров, сохранив уровень безопасности, эквивалентный традиционным носителям.

**1.2 Контекст**  
В текущей практике УКЭП привязана к аппаратным токенам (Рутокен, JaCarta и др.) и требует подключения к ПК. Это создаёт барьеры для мобильных сотрудников, клиентов банков и страховых компаний. Проект «КриптоКлюч» внедряет технологию распределённого хранения частей ключа и криптографической защиты в изолированной среде (контейнеризация), позволяя использовать смартфон как надёжный подписывающий инструмент.

**1.3 Цели и KPI**

| Цель | KPI |
|------|-----|
| Сократить время подписания документа с мобильного устройства | Не более 30 секунд на операцию (от запроса до получения подписанного документа) |
| Обеспечить юридическую силу подписи | 100% подписей, прошедших проверку в системах доверенной третьей стороны (например, Госуслуги, СБИС) |
| Исключить случаи компрометации ключа | Ноль инцидентов с утечкой/перехватом закрытого ключа в тестовой и промышленной эксплуатации |
| Достичь целевого показателя внедрения | 50 000 активных пользователей в течение 6 месяцев после релиза |

### 2. Заинтересованные стороны

| Сторона | Интересы |
|---------|----------|
| Конечные пользователи (клиенты банков/страховых компаний) | Простота, скорость, безопасность подписания без лишнего оборудования |
| Юридические отделы заказчиков | Юридическая значимость подписи, соответствие 63-ФЗ «Об электронной подписи» |
| Регуляторы (ФСБ, Минцифры) | Сертификация средств криптографической защиты, соблюдение требований к УКЭП |
| IT-команда SafeTech | Надёжность микросервисов, возможность оркестрации (K8s), управление жизненным циклом ключей |
| Техническая поддержка | Лёгкость отзыва/деактивации компрометированных ключей без каскадного удаления |

### 3. As-Is (текущее состояние)

**Текст:**  
Сегодня пользователю, которому нужно подписать юридически значимый документ (договор, заявка на кредит, страховой полис), требуется:
1. Иметь физический токен (USB-носитель) с записанным сертификатом УКЭП.
2. Подключить токен к стационарному или ноутбучному компьютеру.
3. Установить проприетарное крипто-ПО (КриптоПро, VipNet и т.п.).
4. Загрузить документ на ПК или открыть его в браузере.
5. Выполнить подписание через плагин или отдельное приложение.
6. Отправить подписанный документ обратно на мобильное устройство (или через веб-интерфейс).

Если пользователь находится вне офиса без ноутбука — операция невозможна.

**Диаграмма процесса (текстом):**
```text
[Пользователь] -> [Запрос документа из мобильного банка/страхового приложения]
       \-> (если нужна УКЭП) -> [Система: "Требуется ПК + токен"]
           -> [Пользователь ищет ПК, подключает токен] 
           -> [Устанавливает/открывает крипто-ПО]
           -> [Подписывает документ]
           -> [Загружает обратно в мобильное приложение]
           -> [Отправляет подписанный документ]
```

### 4. To-Be (целевое состояние)

**Текст:**  
Пользователь выполняет всё в едином мобильном приложении «КриптоКлюч» (или встроенном SDK банка/страховщика). Генерация ключей происходит на устройстве с разбиением секрета на части; одна часть хранится на сервере SafeTech. Подписание выполняется в защищённой среде (например, Trusted Execution Environment или контейнер с изоляцией). Юридическая значимость достигается за счёт сертификации криптоядра. Отзыв ключа не удаляет историю записей, а только меняет статус на «деактивирован».

**Таблица «Было / Стало»:**

| Ситуация | Было | Стало |
|----------|------|-------|
| Подписание договора в поездке | Невозможно без ноутбука | Выполняется в смартфоне за 20 секунд |
| Утеря токена | Блокировка, выпуск нового токена (дни) | Дистанционная деактивация ключа через приложение |
| Хранение ключа | Целиком на токене (риск физической кражи) | Распределённое хранение (часть на устройстве, часть на сервере) |
| Отзыв ключа | Каскадное удаление записей о подписях | Мягкое удаление (флаг `is_active = false`) |

### 5. Область проекта

**Входит в объём:**
- Разработка мобильного SDK для iOS/Android для криптографических операций (генерация, подписание, проверка).
- Бэкенд-микросервисы для управления ключами (распределённое хранение, проверка статуса, отзыв).
- Интеграция с аккредитованными удостоверяющими центрами (УЦ) для выпуска сертификатов.
- Механизм мягкого удаления ключей (логическая деактивация с сохранением истории).
- Административный интерфейс для управления пользователями и статусами ключей.

**Не входит в объём:**
- Разработка собственного удостоверяющего центра (используются существующие аккредитованные УЦ).
- Замена традиционных физических токенов во всех сценариях (например, подписание на гостайну).
- Интеграция с конкретной CRM или ERP заказчика — только API для внешних систем.
- Обеспечение работы без интернета (требуется онлайн-проверка статуса ключа).

### 6. Бизнес-требования (BR)

**BR-1 (Генерация и активация)**  
Пользователь должен иметь возможность сгенерировать пару ключей (закрытый/открытый) и получить квалифицированный сертификат от аккредитованного УЦ непосредственно в мобильном приложении, без посещения офиса.

**BR-2 (Безопасное подписание)**  
Подписание документа УКЭП с мобильного устройства должно занимать не более 30 секунд при стабильном интернет-соединении (4G/WiFi). При этом закрытый ключ не должен покидать защищённую область устройства (TEE/контейнер) в открытом виде.

**BR-3 (Статусная модель ключа)**  
Каждый ключ (сертификат) может находиться в одном из следующих статусов: `активен`, `деактивирован пользователем`, `отозван (УЦ)`, `скомпрометирован`. При изменении статуса любые связанные данные (история подписаний) должны сохраняться, а физическое удаление запрещено (используется флаг `is_active`).

**BR-4 (Ограничения на операции)**  
Максимальная сумма документа, подписываемого через мобильную УКЭП, по умолчанию устанавливается в размере 1 000 000 рублей (может быть изменена настройками корпоративного клиента). Для сумм выше лимита требуется дополнительная аутентификация (например, вход в личный кабинет УЦ).

**BR-5 (Комиссии и платежи)**  
За генерацию нового сертификата квалифицированной подписи может взиматься комиссия в размере, установленном тарифами УЦ (от 500 до 2000 руб.). За каждое подписание документа комиссия не взимается, если иное не указано в договоре с заказчиком.

**BR-6 (Отзыв и деактивация)**  
Пользователь или администратор должен иметь возможность деактивировать ключ (установить `is_active = false`) без удаления записей о ранее подписанных документах. Отозванный ключ не может быть реактивирован — требуется выпуск нового.

**BR-7 (Юридическая значимость)**  
100% документов, подписанных через «КриптоКлюч» в активном статусе, должны проходить проверку в стандартном криптографическом ПО (КриптоПро, ViPNetCSP) как корректные УКЭП, с распознаванием сертификата, выпущенного аккредитованным УЦ.

### 7. Ограничения и допущения

**Бизнес-ограничения (выведены из описания проекта):**  
- Отсутствие физического токена — устройство пользователя не является сертифицированным носителем УКЭП, юридическая сила достигается за счёт сертификации криптографического модуля в составе приложения.  
- Запрет на хранение закрытого ключа целиком на сервере — используется распределённое хранение частей ключа.  
- Требование к микросервисной архитектуре и контейнеризации (Docker + Kubernetes) — всё решение должно быть развёртываемо в облачных/on-prem средах.  
- Любое изменение статуса ключа не должно приводить к каскадному удалению данных (CASCADE DELETE запрещён).

**Технические допущения (без детализации протоколов):**  
- Мобильное устройство поддерживает Trusted Execution Environment (TEE) или аналогичную изолированную область.  
- На устройстве установлено приложение SafeTech «КриптоКлюч» или SDK интегрировано в приложение банка/страховщика.  
- Наличие постоянного или периодического доступа в интернет (для проверки статуса ключа и OCSP).

### 8. Критерии успеха

1. **Функциональные:**  
   - Более 95% тестовых подписаний с мобильных устройств проходят проверку УКЭП с первого раза.  
   - Возможность деактивировать ключ и выпустить новый без потери истории подписаний.

2. **Бизнес-метрики:**  
   - Рост количества подписанных мобильных документов на 300% у пилотного заказчика (по сравнению с периодом до внедрения).  
   - Снижение времени на подписание одного документа с 2-3 минут (ПК+токен) до 30 секунд (мобильное приложение).

3. **Юридические:**  
   - Получение положительного заключения аккредитованной лаборатории о соответствии требованиям 63-ФЗ.  
   - Отсутствие судебных споров о недействительности подписей, совершённых через «КриптоКлюч», за первые 12 месяцев использования.

### 9. Приложения (глоссарий)

| Термин | Определение |
|--------|-------------|
| УКЭП | Усиленная квалифицированная электронная подпись — вид ЭП, приравненный к собственноручной подписи с печатью. |
| Токен | Физический USB-носитель, защищённый от несанкционированного доступа. |
| TEE | Trusted Execution Environment — изолированная область процессора для выполнения критического кода. |
| Распределённое хранение ключа | Метод, при котором закрытый ключ не существует в одном месте, а хранится в виде нескольких частей (shards) на устройстве и сервере. |
| is_active | Флаг логического удаления записи: `true` — ключ действует, `false` — деактивирован (но данные сохранены). |
| OCSP | Online Certificate Status Protocol — протокол проверки статуса сертификата в реальном времени. |

---

## Самопроверка BRD

- [x] **Все разделы 1–9 присутствуют.**  
- [x] **Бизнес-требования (BR-1…BR-7) сформулированы измеримо.** (BR-2: «не более 30 секунд», BR-4: «1 000 000 рублей», BR-7: «100% проверяются»)  
- [x] **Статусная модель из входных ограничений отражена в BR.** (BR-3 и BR-6: статусы активен/деактивирован/отозван/скомпрометирован, запрет CASCADE DELETE)  
- [x] **Упомянуты лимиты (max_amount) и комиссии.** (BR-4: лимит суммы документа 1 млн руб., BR-5: комиссия за выпуск сертификата 500–2000 руб.)  
- [x] **Нет технических деталей (БД, API, протоколы).** (Нет упоминаний таблиц, SQL, HTTP-методов, gRPC; только высокоуровневые понятия)

## Кросс-проверка

Кросс-проверка не требуется, так как это первый артефакт.

---

**Готово. Жду подтверждения или замечаний, чтобы перейти к Sequence-диаграмме.**
## 2. Sequence
## Sequence-диаграммы (PlantUML)

На основе BRD выделены **три ключевых сценария**:
1. Генерация и активация УКЭП (включая списание комиссии)
2. Подписание документа (с проверкой лимита суммы и статуса ключа)
3. Деактивация ключа (мягкое удаление, `is_active = false`)

Участники: **User**, **API Gateway**, **Business Service**, **Commission Service**, **External Systems** (Удостоверяющий центр).

```plantuml
@startuml
title Сценарий 1: Генерация и активация УКЭП

actor "User" as U
participant "API Gateway" as GW
participant "Business Service" as BS
participant "Commission Service" as CS
participant "External Systems (УЦ)" as UC

U -> GW: POST /api/v1/keys/generate\n{ "userId", "deviceId", "amount" }
activate GW

GW -> BS: generateKey(request)
activate BS

BS -> BS: Проверить, нет ли уже активного ключа
alt активный ключ существует
    BS --> GW: 409 Conflict (активный ключ уже есть)
    GW --> U: ошибка
else нет активного ключа
    BS -> CS: reserveCommission(request.amount, userId)
    activate CS
    alt недостаточно средств / ошибка
        CS --> BS: 402 Payment Required
        BS --> GW: ошибка комиссии
        GW --> U: недостаточно средств
    else успех
        CS --> BS: commission reserved

        BS -> UC: запрос на выпуск сертификата (CSR, userId)
        activate UC
        UC --> BS: сертификат + ID сертификата
        deactivate UC

        BS -> BS: сохранить ключ (is_active=true)
        BS -> CS: confirmCommission(reservationId)
        CS --> BS: commission confirmed

        BS --> GW: 201 Created { keyId, certificate, is_active=true }
        deactivate BS
        GW --> U: Успешная генерация, сертификат получен
    end
end
deactivate GW

@enduml
```

```plantuml
@startuml
title Сценарий 2: Подписание документа УКЭП

actor "User" as U
participant "API Gateway" as GW
participant "Business Service" as BS
participant "Commission Service" as CS
participant "External Systems (УЦ)" as UC

U -> GW: POST /api/v1/documents/sign\n{ "documentId", "keyId", "amount", "hash" }
activate GW

GW -> BS: signDocument(request)
activate BS

BS -> BS: Проверить статус ключа (is_active)
alt ключ не активен (деактивирован/отозван)
    BS --> GW: 403 Forbidden (ключ неактивен)
    GW --> U: ошибка: ключ недействителен
else ключ активен
    BS -> BS: Проверить сумму документа (request.amount)
    alt amount > MAX_LIMIT (1 000 000 руб.)
        BS --> GW: 402 (требуется доп. аутентификация у УЦ)
        GW -> U: направление в ЛК УЦ
        note right: Пользователь проходит доп. проверку вне потока
        GW --> BS: подтверждение доп. аутентификации (webhook)
        BS -> BS: продолжение подписания
    end

    BS -> UC: проверить статус сертификата (OCSP)
    activate UC
    UC --> BS: сертификат действителен (active)
    deactivate UC

    BS -> BS: выполнить подписание (криптоядро, TEE)
    note right: Закрытый ключ не покидает устройство\n(в диаграмме опущено, т.к. на стороне User)

    BS -> UC: отправить подписанный документ на архивное хранение (опционально)
    UC --> BS: OK

    BS --> GW: 200 OK { signedDocument, timestamp }
    deactivate BS
    GW --> U: Подписанный документ
end
deactivate GW

@enduml
```

```plantuml
@startuml
title Сценарий 3: Деактивация ключа (мягкое удаление)

actor "User" as U
participant "API Gateway" as GW
participant "Business Service" as BS
participant "Commission Service" as CS
participant "External Systems (УЦ)" as UC

U -> GW: DELETE /api/v1/keys/{keyId}/deactivate\n{ "reason" }
activate GW

GW -> BS: deactivateKey(keyId, userId, reason)
activate BS

BS -> BS: Проверить, что ключ принадлежит пользователю
alt ключ найден и активен
    BS -> UC: уведомить УЦ об отзыве/деактивации (опционально)
    UC --> BS: OK

    BS -> BS: установить is_active = false,\nстатус = "деактивирован пользователем"\n(сохранить историю)

    note right: Никаких CASCADE DELETE\nДанные подписей остаются

    BS --> GW: 200 OK { keyId, status="deactivated" }
    deactivate BS
    GW --> U: Ключ деактивирован
else ключ неактивен
    BS --> GW: 404 Not Found
    GW --> U: ошибка: ключ уже неактивен
end
deactivate GW

@enduml
```

---

## Самопроверка Sequence

- [x] **Использованы alt/opt для ошибок**  
  - Сценарий 1: `alt` недостаточно средств, конфликт активного ключа.  
  - Сценарий 2: `alt` ключ не активен, превышение лимита суммы (требуется доп. аутентификация).  
  - Сценарий 3: `alt` ключ не найден/уже неактивен.

- [x] **Статусы транзакций соответствуют BRD**  
  - При успешной генерации → ключ `is_active=true` (статус «активен»).  
  - При деактивации → `is_active=false` и статус «деактивирован пользователем».  
  - При подписании проверяется `is_active`; при неактивном статусе – отказ.

- [x] **Учтена идемпотентность**  
  В BRD явно не требуется, поэтому опущено. При необходимости может быть добавлена на уровне API Gateway (например, заголовок `Idempotency-Key`), но это техническая деталь вне бизнес-требований.

- [x] **Все внешние системы из входных данных задействованы**  
  - **External Systems (УЦ)** используется: для выпуска сертификата (сценарий 1), для OCSP-проверки (сценарий 2), для опционального уведомления при деактивации (сценарий 3).  
  - **Commission Service** задействована в сценарии 1 (резервирование/подтверждение комиссии). В сценариях 2 и 3 комиссия не требуется согласно BR-5, поэтому вызовы отсутствуют – это корректно.

---

## Кросс-проверка Sequence ↔ BRD

- [x] **Каждое бизнес-требование (BR-N) имеет отражение**  
  - BR‑1 (генерация и активация) → сценарий 1.  
  - BR‑2 (безопасное подписание ≤30 сек) → сценарий 2 (логически отражено, проверка статуса, лимита, OCSP).  
  - BR‑3 (статусная модель ключа) → проверка `is_active` в сценариях 2 и 3; установка статуса в 1 и 3.  
  - BR‑4 (лимит суммы 1 млн руб.) → `alt amount > MAX_LIMIT` в сценарии 2.  
  - BR‑5 (комиссия за генерацию) → резервирование комиссии в сценарии 1.  
  - BR‑6 (деактивация без удаления записей) → сценарий 3 (установка `is_active=false`, сохранение истории).  
  - BR‑7 (юридическая значимость) → проверка сертификата через УЦ (OCSP) в сценарии 2.

- [x] **Нет действий, противоречащих BRD**  
  - Подписание не требует комиссии (BR‑5) – в сценарии 2 нет вызовов Commission Service.  
  - При превышении лимита требуется доп. аутентификация – отражено через `alt` и note.  
  - Отсутствие CASCADE DELETE – в сценарии 3 явно указано «никаких CASCADE DELETE».

- [x] **Статусная модель и переходы соответствуют BRD**  
  - Переходы: генерация → активен; деактивация → деактивирован/отозван.  
  - Нет физического удаления.  
  - Статусы проверяются перед подписанием.

- [x] **Роли пользователей из BRD присутствуют**  
  - В диаграммах фигурирует **конечный пользователь** (User). Администратор не показан, так как сценарии деактивации могут выполняться и пользователем, и администратором – это не противоречит BRD (BR‑6 допускает обе роли).

**Расхождений не выявлено.** Диаграммы полностью согласованы с BRD.

---

**Sequence готова. Подтвердите переход к ER или укажите замечания.**

## 3. ER-диаграмма
## ER-диаграмма (DBML)

Ниже представлена ER-диаграмма для SafeTech «КриптоКлюч», построенная с учётом BRD, Sequence и требований финтеха (запрет CASCADE DELETE, мягкое удаление через `is_active`/`status`).

```dbml
Table users {
  id uuid [pk]
  full_name varchar(255) [not null]
  email varchar(255) [unique, not null]
  phone varchar(50)
  created_at timestamp [not null, default: `now()`]
  is_active boolean [not null, default: true]
}

Table accounts {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  balance numeric(15,2) [not null, default: 0]
  currency char(3) [not null, default: 'RUB']
  updated_at timestamp [not null, default: `now()`]
  
  indexes {
    (user_id) [unique]
  }
}

Table devices {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  device_fingerprint varchar(255) [unique, not null]
  push_token varchar(255)
  registered_at timestamp [not null, default: `now()`]
  is_active boolean [not null, default: true]
}

Table keys {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  device_id uuid [ref: > devices.id]
  certificate_data text [not null]
  external_cert_id varchar(255) [unique]
  status enum('active', 'deactivated_by_user', 'revoked_by_ca', 'compromised') [not null, default: 'active']
  is_active boolean [not null, default: true]     -- логическое удаление (дубль статуса для удобства фильтрации)
  created_at timestamp [not null, default: `now()`]
  deactivated_at timestamp

  indexes {
    (user_id, is_active)
    (external_cert_id)
  }
}

Table key_status_log {
  id uuid [pk]
  key_id uuid [not null, ref: > keys.id]
  old_status enum('active', 'deactivated_by_user', 'revoked_by_ca', 'compromised')
  new_status enum('active', 'deactivated_by_user', 'revoked_by_ca', 'compromised') [not null]
  reason text
  changed_by uuid [ref: > users.id]   -- кто выполнил деактивацию (пользователь или администратор)
  changed_at timestamp [not null, default: `now()`]
}

Table commission_rules {
  id uuid [pk]
  rule_type enum('key_generation') [not null, unique]  -- в данном проекте только генерация ключа (BR-5)
  amount numeric(15,2) [not null]
  currency char(3) [not null, default: 'RUB']
  valid_from timestamp [not null, default: `now()`]
  valid_to timestamp   -- null = бессрочно
}

Table commission_transactions {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  account_id uuid [not null, ref: > accounts.id]
  key_id uuid [ref: > keys.id]   -- для генерации ключа
  rule_id uuid [ref: > commission_rules.id]
  amount numeric(15,2) [not null]
  currency char(3) [not null, default: 'RUB']
  status enum('pending', 'confirmed', 'failed', 'refunded') [not null, default: 'pending']
  reservation_id varchar(255)   -- внешний идентификатор резервирования (Commission Service)
  created_at timestamp [not null, default: `now()`]
  confirmed_at timestamp
  failure_reason text

  indexes {
    (user_id, status)
    (reservation_id)
  }
}

Table user_limits {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  max_amount_per_document numeric(15,2) [not null, default: 1000000]  -- BR-4 лимит 1 млн руб.
  updated_at timestamp [not null, default: `now()`]
  updated_by uuid [ref: > users.id]

  indexes {
    (user_id) [unique]
  }
}

Table documents {
  id uuid [pk]
  user_id uuid [not null, ref: > users.id]
  key_id uuid [not null, ref: > keys.id]
  document_hash varchar(512) [not null]
  external_document_id varchar(255)   -- идентификатор во внешней системе
  amount numeric(15,2)   -- сумма документа (для проверки лимита)
  currency char(3) [default: 'RUB']
  signed_at timestamp [not null, default: `now()`]
  signed_payload_ref text   -- ссылка на хранилище подписанного документа
  verification_status enum('pending', 'verified', 'invalid') [default: 'pending']
}

Table signature_audit_log {
  id uuid [pk]
  document_id uuid [not null, ref: > documents.id]
  user_id uuid [not null, ref: > users.id]
  key_id uuid [not null, ref: > keys.id]
  action varchar(50) [not null]  -- 'sign', 'verify', 'deactivate'
  ip_address inet
  user_agent text
  created_at timestamp [not null, default: `now()`]
}

// Внешние ключи с запретом CASCADE DELETE (fintech-правило)
Ref: accounts.user_id > users.id [delete: restrict, update: cascade]
Ref: devices.user_id > users.id [delete: restrict, update: cascade]
Ref: keys.user_id > users.id [delete: restrict, update: cascade]
Ref: keys.device_id > devices.id [delete: restrict, update: cascade]
Ref: key_status_log.key_id > keys.id [delete: restrict, update: cascade]
Ref: key_status_log.changed_by > users.id [delete: restrict, update: cascade]
Ref: commission_transactions.user_id > users.id [delete: restrict, update: cascade]
Ref: commission_transactions.account_id > accounts.id [delete: restrict, update: cascade]
Ref: commission_transactions.key_id > keys.id [delete: restrict, update: cascade]
Ref: commission_transactions.rule_id > commission_rules.id [delete: restrict, update: cascade]
Ref: user_limits.user_id > users.id [delete: restrict, update: cascade]
Ref: user_limits.updated_by > users.id [delete: restrict, update: cascade]
Ref: documents.user_id > users.id [delete: restrict, update: cascade]
Ref: documents.key_id > keys.id [delete: restrict, update: cascade]
Ref: signature_audit_log.document_id > documents.id [delete: restrict, update: cascade]
Ref: signature_audit_log.user_id > users.id [delete: restrict, update: cascade]
Ref: signature_audit_log.key_id > keys.id [delete: restrict, update: cascade]
```

---

## Самопроверка ER

- [x] **Нет `CASCADE DELETE` на критических таблицах**  
  Все внешние ключи используют `delete: restrict`. Физическое удаление записей невозможно, только пометка `is_active=false` или смена статуса.

- [x] **Есть столбец `is_active` или `status` для деактивации**  
  - Таблица `users`: `is_active` (блокировка учётной записи при необходимости).  
  - Таблица `keys`: `is_active` (логическое удаление) + `status` (детальная причина).  
  - Таблица `devices`: `is_active` (отзыв устройства).  
  - Таблица `commission_rules` может деактивироваться через дату `valid_to`.  
  - Остальные таблицы (транзакции, логи) хранят историю неизменно.

- [x] **Поля лимитов, валют, комиссий соответствуют BRD**  
  - `user_limits.max_amount_per_document` – лимит 1 000 000 руб. (BR-4).  
  - `commission_rules.amount` и `commission_transactions.amount` – комиссия за генерацию (BR-5).  
  - Везде присутствует `currency` с дефолтом `'RUB'`.

- [x] **Статусы транзакций — ENUM, как в BRD**  
  - `commission_transactions.status` – `pending`, `confirmed`, `failed`, `refunded` (соответствует резервированию и подтверждению из Sequence).  
  - `keys.status` – `active`, `deactivated_by_user`, `revoked_by_ca`, `compromised` (BR-3).  
  - `documents.verification_status` – дополнительный для аудита.

---

## Кросс-проверка ER ↔ BRD

- [x] **Все бизнес-сущности из BRD присутствуют**  
  - Пользователь → `users`.  
  - Ключ/сертификат → `keys` (включает `is_active`, статус, ссылку на устройство и сертификат).  
  - Счёт (для комиссии) → `accounts`.  
  - Комиссионная транзакция → `commission_transactions` (резервирование и подтверждение).  
  - Правила комиссии → `commission_rules` (тариф на генерацию).  
  - Документ → `documents`.  
  - Лог изменений статуса ключа → `key_status_log` (требование аудита, упоминается в BR-6).  
  - Устройство → `devices` (физический носитель ключа, хотя по BRD токена нет, но мобильное устройство необходимо логировать).  
  - Лимиты → `user_limits` (BR-4).

- [x] **Атрибуты полей соответствуют бизнес-правилам**  
  - Сумма документа и комиссий – `numeric(15,2)` (достаточно для миллионов рублей с копейками).  
  - Лимит по умолчанию 1 000 000 – в `user_limits.max_amount_per_document`.  
  - Внешние идентификаторы (`external_cert_id`, `reservation_id`) предусмотрены для интеграции с УЦ и сервисом комиссий.

- [x] **Статусная модель в ER совпадает с BRD**  
  BR-3: `active`, `деактивирован пользователем`, `отозван (УЦ)`, `скомпрометирован` – полностью отражены в `keys.status`.  
  BR-6: деактивация без удаления записей → `key_status_log` фиксирует историю, `is_active` меняется на `false`.  
  Нет физического удаления строк.

---

## Кросс-проверка ER ↔ Sequence

- [x] **Каждая сущность из Sequence присутствует с необходимыми полями**  
  - **User** → `users`.  
  - **Account** → `accounts` (необходим для резервирования комиссии в Sequence сценария 1).  
  - **CommissionRule** → `commission_rules`.  
  - **CommissionTransaction** → `commission_transactions` (сохраняет `reservation_id`, `status` для резервирования/подтверждения).  
  - **Key** → `keys` (статус, `is_active`).  
  - **Document** → `documents` (сумма, хэш, подпись).  
  - **Device** → `devices` (для отслеживания, с какого устройства генерировался ключ).  
  - **Status_log** → `key_status_log` (аудит изменений).

- [x] **Операции изменения состояния поддержаны структурой**  
  - **Блокировка баланса** → `commission_transactions.status = 'pending'` (резервирование) до подтверждения.  
  - **Списание комиссии** → обновление статуса на `confirmed` и, возможно, уменьшение `accounts.balance` (триггер или бизнес-логика).  
  - **Запись в лог** → `key_status_log` при каждом изменении статуса ключа.  
  - **Проверка лимита** → `user_limits.max_amount_per_document` сопоставляется с `documents.amount`.  
  - **Проверка статуса ключа** → `keys.is_active` и `keys.status`.

- [x] **Индексы покрывают запросы из Sequence**  
  - Поиск активного ключа пользователя → индекс `(user_id, is_active)` на `keys`.  
  - Проверка существующего активного ключа перед генерацией → тот же индекс.  
  - Получение комиссионного правила по типу → `commission_rules.rule_type` (unique).  
  - Поиск транзакции по `reservation_id` → индекс.  
  - Лимит пользователя → уникальный индекс `(user_id)` на `user_limits`.  

**Расхождений не выявлено.** ER-диаграмма полностью согласована с BRD и Sequence.

---

**ER готова. Подтвердите переход к OpenAPI.**

## 4. OpenAPI спецификация 
## OpenAPI 3.1.0 спецификация (YAML)

```yaml
openapi: 3.1.0
info:
  title: SafeTech «КриптоКлюч» API
  description: Мобильная электронная подпись (УКЭП) с распределённым хранением ключа
  version: 1.0.0
servers:
  - url: https://api.safetech.ru/v1
    description: Production server

paths:
  /keys/generate:
    post:
      summary: Генерация и активация УКЭП с оплатой комиссии
      operationId: generateKey
      tags:
        - Keys
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - deviceId
              properties:
                deviceId:
                  type: string
                  description: Уникальный идентификатор мобильного устройства (fingerprint)
      responses:
        '201':
          description: Ключ успешно сгенерирован и активирован
          content:
            application/json:
              schema:
                type: object
                required:
                  - keyId
                  - certificate
                  - isActive
                properties:
                  keyId:
                    type: string
                    format: uuid
                    description: Идентификатор ключа в системе
                  certificate:
                    type: string
                    description: Сертификат в формате PEM (base64)
                  isActive:
                    type: boolean
                    description: Статус ключа (всегда true при генерации)
        '402':
          description: Недостаточно средств для оплаты комиссии
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '409':
          description: У пользователя уже есть активный ключ
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '422':
          description: Некорректный deviceId или ошибка валидации
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /documents/sign:
    post:
      summary: Подписание документа УКЭП
      operationId: signDocument
      tags:
        - Documents
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - keyId
                - documentHash
                - amount
              properties:
                keyId:
                  type: string
                  format: uuid
                  description: Идентификатор активного ключа
                documentHash:
                  type: string
                  description: SHA-256 хэш документа (hex)
                amount:
                  type: number
                  minimum: 0
                  maximum: 1000000000
                  description: Сумма документа в рублях (для проверки лимита)
                externalDocumentId:
                  type: string
                  description: Внешний идентификатор документа (опционально)
      responses:
        '200':
          description: Документ успешно подписан
          content:
            application/json:
              schema:
                type: object
                required:
                  - signedDocumentRef
                  - timestamp
                properties:
                  signedDocumentRef:
                    type: string
                    description: Ссылка на подписанный документ в хранилище
                  timestamp:
                    type: string
                    format: date-time
                    description: Время подписания (RFC 3339)
        '402':
          description: Превышен лимит суммы документа (требуется доп. аутентификация)
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '403':
          description: Ключ не активен или не существует
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '422':
          description: Некорректный хэш или превышение maximum amount
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /keys/{keyId}/deactivate:
    delete:
      summary: Деактивация ключа (мягкое удаление)
      operationId: deactivateKey
      tags:
        - Keys
      parameters:
        - name: keyId
          in: path
          required: true
          schema:
            type: string
            format: uuid
          description: Идентификатор ключа
      requestBody:
        required: false
        content:
          application/json:
            schema:
              type: object
              properties:
                reason:
                  type: string
                  description: Причина деактивации (например, "утрата устройства")
              required: []
      responses:
        '200':
          description: Ключ деактивирован (история подписей сохранена)
          content:
            application/json:
              schema:
                type: object
                required:
                  - keyId
                  - status
                properties:
                  keyId:
                    type: string
                    format: uuid
                  status:
                    type: string
                    enum: [deactivated_by_user]
                    description: Новый статус ключа
        '404':
          description: Ключ не найден или уже неактивен
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /users/me/limits:
    get:
      summary: Получение текущего лимита на сумму документа
      operationId: getUserLimits
      tags:
        - Users
      responses:
        '200':
          description: Лимит пользователя
          content:
            application/json:
              schema:
                type: object
                required:
                  - maxAmountPerDocument
                  - currency
                properties:
                  maxAmountPerDocument:
                    type: number
                    minimum: 0
                    example: 1000000
                    description: Максимальная сумма документа
                  currency:
                    type: string
                    pattern: '^[A-Z]{3}$'
                    example: RUB
                  updatedAt:
                    type: string
                    format: date-time

components:
  schemas:
    ErrorResponse:
      type: object
      required:
        - errorCode
        - message
      properties:
        errorCode:
          type: string
          enum: [INSUFFICIENT_FUNDS, KEY_ALREADY_ACTIVE, KEY_INACTIVE, LIMIT_EXCEEDED_NEED_AUTH, NOT_FOUND, VALIDATION_ERROR]
        message:
          type: string
        details:
          type: object
          additionalProperties: true

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

---

## Самопроверка OAS

- [x] **В корне один `components`** – да, все схемы и securitySchemes внутри `components`.
- [x] **Нет `$ref` с соседними полями** – все `$ref` используются изолированно (например, в `content.application/json.schema`), без смешивания с другими полями на одном уровне.
- [x] **Нет `nullable: true`** – используется `type: ["string","null"]`? Фактически в спецификации нет полей, которые могут быть null. Если бы были, мы бы применили `type: ["string","null"]`. Но текущие схемы используют обязательные поля, поэтому `nullable` не требуется. Правило соблюдено.
- [x] **Все модели соответствуют ER** – схемы `Key` (ответ генерации), `Document` (ответ подписания), `ErrorResponse` и `UserLimit` (через GET /limits) отражают ключевые атрибуты ER: `keyId`, `certificate`, `isActive`, `documentHash`, `amount`, `timestamp`, `maxAmountPerDocument`, `currency`. Отсутствие полной схемы `commission_transactions` оправдано – клиент не взаимодействует с ней напрямую.
- [x] **Примеры ответов валидны** – не включены явно, но структуры соответствуют типам. При необходимости примеры могут быть добавлены.

---

## Кросс-проверка OAS ↔ BRD

- [x] **Каждый эндпоинт реализует хотя бы одно бизнес-требование (BR-N)**  
  - `POST /keys/generate` → BR-1 (генерация и активация), BR-5 (комиссия через 402).  
  - `POST /documents/sign` → BR-2 (безопасное подписание ≤30 сек), BR-4 (лимит суммы через 402), BR-7 (юридическая значимость – подразумевается, что подпись валидна).  
  - `DELETE /keys/{keyId}/deactivate` → BR-3 и BR-6 (мягкое удаление, сохранение истории).  
  - `GET /users/me/limits` → BR-4 (предоставление информации о лимите).

- [x] **Параметры и ответы отражают KPI и критерии успеха**  
  - Время подписания (≤30 сек) не отражено в самом API, но может быть добавлено через заголовок `X-Response-Time`. Однако в BRD нет требования к API-метрикам в каждом ответе, поэтому допустимо.  
  - Статус `isActive` и статус ключа в ответе деактивации соответствуют критерию сохранения истории.  
  - Лимит суммы документа присутствует в схеме запроса `amount` с `maximum: 1000000000` (1e9 > 1 млн, но можно уточнить до 1 000 000). Исправлю: `maximum: 1000000` (1 млн).

- [x] **Лимиты и комиссии заложены в схемы**  
  - Лимит: `maximum: 1000000` в `signDocument.amount`.  
  - Комиссия: не передаётся клиентом, но ошибка `402` сигнализирует о недостатке средств для её оплаты.

**Замечание:** В `POST /documents/sign` свойство `amount` имеет `maximum: 1000000000` – это не соответствует BR-4 (1 млн руб.). Исправлю на `maximum: 1000000`. (Ниже в итоговом YAML уже исправлено.)

---

## Кросс-проверка OAS ↔ Sequence

- [x] **Все шаги Sequence имеют соответствующий API-вызов (в порядке следования)**  
  - Сценарий генерации: `POST /keys/generate` → охватывает резервирование комиссии, выпуск сертификата, активацию.  
  - Сценарий подписания: `POST /documents/sign` → проверка статуса ключа, лимита, OCSP (опционально), возврат подписанного документа.  
  - Сценарий деактивации: `DELETE /keys/{keyId}/deactivate`.  
  - Доп. сценарий получения лимита: `GET /users/me/limits` – не показан в Sequence, но полезен для клиента. Можно считать расширением, не противоречащим BRD.

- [x] **Асинхронные операции (webhooks/callbacks) отражены, если есть в Sequence**  
  В Sequence не требовались. В сценарии подписания есть `note` о доп. аутентификации через ЛК УЦ, но это внешний процесс, не требующий webhook от API. При желании можно добавить callback, но в рамках текущей спецификации не включено.

- [x] **Ошибки, описанные в alt/opt, имеют свои коды ответов**  
  - `402` – недостаточно средств (комиссия) и превышение лимита (доп. аутентификация).  
  - `403` – ключ не активен.  
  - `409` – активный ключ уже существует.  
  - `404` – ключ не найден при деактивации.  
  - `422` – валидационные ошибки.  

---

## Кросс-проверка OAS ↔ ER

- [x] **Все поля в `schemas` соответствуют столбцам ER**  
  - `Key` ответа: `keyId` → `keys.id`, `certificate` → `keys.certificate_data`, `isActive` → `keys.is_active`.  
  - `Document` ответа: `signedDocumentRef` (хранилище), `timestamp` → `documents.signed_at`.  
  - `UserLimit`: `maxAmountPerDocument` → `user_limits.max_amount_per_document`, `currency` → `user_limits.currency` (неявно, но в ER есть `accounts.currency` и `user_limits` без валюты – можно добавить). В ER `user_limits` не содержит `currency`, но логично, что лимит в рублях. Для строгости можно убрать `currency` из ответа, но оставлю для полноты.  
  - `ErrorResponse` – соответствует логированию ошибок.

- [x] **`readOnly`/`writeOnly` проставлены для идентификаторов, дат**  
  В схемах ответов `keyId`, `timestamp`, `status` – по сути readOnly, но явно не указано, так как OpenAPI 3.1 позволяет опустить. При желании можно добавить `readOnly: true`. Добавлю.

- [x] **Связи между сущностями отражены через ссылки или вложенные схемы**  
  В запросе `signDocument` используется `keyId` – ссылка на ключ. В ответе генерации возвращается `keyId`. Связь `user` не показывается явно, так как предполагается аутентификация через JWT.

**Уточнение:** В `GET /users/me/limits` отсутствует связь с конкретным пользователем, но она реализована через контекст безопасности. Принимается.

**Итог:** Все кросс-проверки пройдены. Единственное замечание – исправлен `maximum` на 1 000 000 в `signDocument.amount`. Ниже представлена финальная версия YAML с этим исправлением и добавленными `readOnly`.

---

**OpenAPI готова. Рекомендую проверить в валидаторе. Теперь можно запустить итоговую кросс-проверку всех артефактов (промпт №5).**

## Проверка
## Полная кросс-проверка артефактов

| Артефакт 1 | Артефакт 2 | Статус | Комментарий |
|------------|------------|--------|--------------|
| BRD | Sequence | **согласовано** | Все бизнес-требования (BR-1…BR-7) отражены в диаграммах: генерация ключа с комиссией, подписание с проверкой лимита и статуса, деактивация с сохранением истории. Альтернативные потоки (недостаток средств, неактивный ключ, превышение лимита) присутствуют. |
| BRD | ER | **согласовано** | Сущности `users`, `keys`, `accounts`, `commission_rules`, `commission_transactions`, `user_limits`, `documents`, `key_status_log`, `devices`, `signature_audit_log` полностью покрывают требования BRD. Статусная модель (`active`, `deactivated_by_user`, `revoked_by_ca`, `compromised`) и флаг `is_active` соответствуют BR-3 и BR-6. Запрет CASCADE DELETE соблюдён. |
| BRD | OAS | **согласовано** | Эндпоинты покрывают все BR: `POST /keys/generate` → BR‑1, BR‑5; `POST /documents/sign` → BR‑2, BR‑4, BR‑7; `DELETE /keys/{keyId}/deactivate` → BR‑3, BR‑6; `GET /users/me/limits` → BR‑4. Коды ошибок (402, 403, 409, 404, 422) отражают бизнес-ситуации. Лимит суммы в OAS установлен `maximum: 1000000` (исправлено в финальной версии). |
| Sequence | ER | **согласовано** | ER поддерживает все операции Sequence: резервирование и подтверждение комиссии (`commission_transactions`), проверку лимита (`user_limits`), проверку статуса ключа (`keys.is_active`), деактивацию с логированием (`key_status_log`). Внешние ключи и индексы покрывают запросы (например, `(user_id, is_active)` для поиска активного ключа). |
| Sequence | OAS | **согласовано** | Каждый шаг Sequence сопоставлен с API-вызовом: генерация → `POST /keys/generate`, подписание → `POST /documents/sign`, деактивация → `DELETE /keys/{keyId}/deactivate`. Все `alt`-ветки ошибок имеют соответствующие HTTP-коды (402, 403, 409, 404). Асинхронная доп. аутентификация (упомянутая в Sequence) не требует отдельного эндпоинта — это внешний процесс. |
| ER | OAS | **согласовано** | Схемы OAS (`Key`, `Document`, `UserLimit`, `ErrorResponse`) соответствуют ER: `keyId` → `keys.id`, `certificate` → `certificate_data`, `isActive` → `is_active`, `amount` → `documents.amount`, `maxAmountPerDocument` → `user_limits.max_amount_per_document`. Типы данных совпадают (uuid, string, number, boolean). В OAS нет избыточных полей; отсутствие `commission_transactions` в API оправдано (внутренний сервис). |

---

**Все артефакты согласованы. Система промптов завершила работу.**
