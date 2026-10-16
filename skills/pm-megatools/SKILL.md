---
name: pm-megatools
description: >-
  Ръководство, архитектура и стандарти за клиентския финансов и инвестиционен пакет MegaTools (.NET 10 Blazor WebAssembly, PWA, Radzen, криптиран LocalStorage сейф, борсови данни в реално време през Finnhub и интеграция с Gemini AI).
---

# MegaTools - Financial & Investment Suite Guide

## 1. Общ Преглед

**MegaTools** е самостоятелен клиентски Progressive Web App (PWA) пакет от екосистемата на **PersonalManager**, предназначен за бързи интерактивни финансови изчисления, дългосрочни симулации на инвестиционни портфейли, дивиденти, кредити и следене на реални борсови котировки.

### Ключови характеристики:
- **100% Client-Side WebAssembly**: Изчисленията се извършват директно в браузъра на потребителя – бързо, автономно и с висока производителност.
- **PWA (Progressive Web App)**: Инсталира се като нативно приложение на телефон (Android, iOS) и компютър (Windows, Mac) с пълноекранен изглед и офлайн поддръжка.
- **Криптиран Сейф (Local Vault)**: API ключовете (Finnhub, Gemini AI) се криптират локално преди запис в `localStorage`.
- **Реални Борсови Данни**: Директна връзка към Finnhub API за котировки на живо за над 25 000 акции и ETF-и, допълнен с вграден каталог от 60+ популярни актива.
- **Абстрактни Стойности**: Числата и финансовите изчисления са универсални и не са твърдо обвързани с конкретна валута.

---

## 2. Технологичен Стек

```text
MegaTools (.NET 10 Blazor WASM)
├── Microsoft.AspNetCore.Components.WebAssembly (10.0.13)
├── Radzen.Blazor (6.1.2)                  # UI компоненти, карти, DataGrid, Charts
├── Blazored.LocalStorage (4.5.0)          # Клиентско съхранение на настройки & история
├── PWA Service Worker                     # Офлайн кеширане и инсталируемост
└── Azure Static Web Apps                  # Автоматичен CI/CD хостинг през GitHub Actions
```

---

## 3. Архитектура на Проекта

```text
MegaTools/
├── Components/
│   ├── Calculators/                       # Специализирани UI калкулатори
│   │   ├── LongStockInvestment.razor      # Дългосрочно инвестиране в акции (DCA)
│   │   ├── DividendCalculator.razor       # Дивиденти, реинвестиране и модел на Гордън
│   │   ├── CompoundInterestCalculator.razor # Сложна лихва
│   │   ├── DepositCalculator.razor        # Банкови депозити и срочно спестяване
│   │   ├── LoanCalculator.razor           # Кредитен калкулатор & погасителен план
│   │   ├── SalaryCalculator.razor         # Заплати (Бруто / Нето за България)
│   │   ├── FuelCalculator.razor           # Разход на гориво и споделено пътуване
│   │   ├── PercentageCalculator.razor     # Процентни изчисления
│   │   ├── BmiCalorieCalculator.razor     # BMI индекс и калориен баланс (TDEE)
│   │   └── StockSearchPicker.razor        # Live търсачка на акции с autocomplete & чипове
│   ├── Settings/
│   │   └── CloudSyncCard.razor            # Supabase Cloud Vault UI с E2EE синхронизация
│   └── Common/
│       └── PwaInstallButton.razor         # PWA бутон за инсталация с модал за Android/iOS
├── Layout/
│   ├── MainLayout.razor                   # Главен лейаут с top-bar и бранд лента
│   ├── MainLayout.razor.css               # Стилизиране на работната площ
│   ├── NavMenu.razor                      # Странично навигационно меню с акордеони
│   └── NavMenu.razor.css                  # Sticky навигация и фиксиран долен панел
├── MegaFuncs/                             # Чиста математическа и финансова логика
│   └── Calculators/
│       ├── Investment/
│       │   ├── LongStockCalculatorFunc.cs # DCA симулатор с DRIP и динамичен ръст
│       │   ├── DividendCalculatorFunc.cs  # Гордън модел и дивидентен напредък
│       │   ├── DepositCalculatorFunc.cs   # Депозитна доходност
│       │   ├── LiveStockService.cs        # Finnhub API клиент за котировки на живо
│       │   └── StockPresetsCatalog.cs     # 60+ преконфигурирани борсови актива
│       ├── Financial/
│       │   ├── CompoundInterestCalculatorFunc.cs
│       │   ├── LoanCalculatorFunc.cs      # Анюитетни вноски и разбивка лихва/главница
│       │   └── SalaryCalculatorFunc.cs    # ДОО, ДЗПО, ЗО, ДОД данъчни ставки
│       ├── Auto/
│       │   └── FuelCalculatorFunc.cs
│       ├── Health/
│       │   └── BmiCalorieCalculatorFunc.cs
│       └── Maths/
│           └── PercentageCalculatorFunc.cs
├── Models/
│   ├── Calculators/Input/                 # Входни модели с DataAnnotations валидация
│   ├── Calculators/Result/                # Изходни резултатни модели
│   ├── Calculators/Investment/            # StockPresetModel за акции и ETF-и
│   └── Settings/
│       ├── AppSettingsModel.cs            # Модел за настройки, API ключове и предпочитания
│       └── SupabaseSyncModels.cs          # Схеми за Supabase Auth, Vault Record и Payload
├── Pages/
│   ├── Home.razor                         # Начално табло с бързи карти към калкулаторите
│   ├── Calculators.razor                  # Каталог на всички калкулатори с филтриране
│   └── Settings.razor                     # Настройки, криптирани API ключове и управление на кеша
├── Services/
│   ├── SecureStorageService.cs            # Браузърно защитен шифър (SHA-256 CTR keystream)
│   ├── AppSettingsService.cs              # Управление на настройки, ping тестове и миграция
│   └── SupabaseSyncService.cs            # Zero-Knowledge E2EE клиент за Supabase PostgREST
└── wwwroot/
    ├── manifest.webmanifest               # PWA манифест с App Shortcuts и метаданни
    ├── service-worker.js                  # PWA Service Worker (Dev среда)
    ├── service-worker.published.js        # PWA Service Worker с офлайн кеш (Prod среда)
    ├── pwa-helper.js                      # JSInterop за инсталация и принудително обновяване
    ├── icon-192.png / icon-512.png        # Стандартни PWA икони
    ├── icon-maskable-512.png              # Адаптивна икона за Android теми
    └── apple-touch-icon.png               # Retina икона за iOS Safari
```

---

## 4. Справочник по Калкулатори

### 4.1 Дългосрочно инвестиране в акции (`LongStockInvestment`)
- **DCA (Dollar-Cost Averaging)**: Симулира редовно месечно довнасяне в портфейла.
- **DRIP (Dividend Reinvestment Plan)**: Опция за автоматично реинвестиране на изплатения дивидент за придобиване на нови дробни акции.
- **Двупосочен растеж**: Отчита отделно очаквания годишен ръст на цената на акцията и темпа на нарастване на дивидента.
- **Подробна 12-месечна динамика**: Разгъване на всеки ред в `RadzenDataGrid` показва месечен отчет на цената, броя акции, довнесените средства и изплатените дивиденти.

### 4.2 Дивиденти & Ръст (`DividendCalculator`)
- **Модел на Гордън (Gordon Growth Model)**: Изчислява справедливата фундаментална цена на акцията при зададена желана годишна норма на доходност:
  $$P_0 = \frac{D_1}{r - g}$$
  *(където $D_1$ е очакваният дивидент, $r$ е желаната доходност, а $g$ е растежът на дивидента).*
- **Данъчно облагане**: Прецизно отчитане на данъка върху дивидентите (по подразбиране 5% за България, 10% за САЩ с W-8BEN).

### 4.3 Сложна лихва & Депозити (`CompoundInterestCalculator`, `DepositCalculator`)
- Капитализация: месечна, тримесечна, шестмесечна или годишна.
- Нагледно съпоставяне на внесената главница спрямо генерираната чиста лихва.

### 4.4 Кредити & Погасителен План (`LoanCalculator`)
- Пресмятане на месечна анюитетна вноска, общо изплатени средства и надвнесена лихва:
  $$M = P \cdot \frac{r(1+r)^n}{(1+r)^n - 1}$$
- Годишен амортизационен план с разпределение между погасена главница и платена лихва.

### 4.5 Заплати в България (`SalaryCalculator`)
- Преизчисляване от брутно към нетно възнаграждение и обратно.
- Пълна законова разбивка на осигуровките за сметка на служителя и за сметка на работодателя (ДОО, ДЗПО, ЗО, ДОД).

---

## 5. Борсови Данни на Живо & Каталог

### Интелигентно извличане (`LiveStockService.cs`):
1. **Локален Каталог (`StockPresetsCatalog.cs`)**: Съдържа над 60 утвърдени позиции (O, KO, SCHD, AAPL, MSFT, NVDA, VOO, MAIN, PEP, PG, JNJ и др.) с предварително дефинирани типични дивиденти, периодичност и препоръчителен хоризонт.
2. **Finnhub Real-Time API**: При въведен безплатен ключ от [finnhub.io](https://finnhub.io), търсачката автоматично извлича актуалната борсова котировка в реално време през endpoint `quote?symbol={ticker}` и името на компанията през `stock/profile2`.
3. **Гъвкав Fallback**: Ако тикерът е изцяло нов, системата създава динамична позиция с текущата котировка и позволява моментално изчисление в калкулатора.

---

## 6. Сигурност: Клиентски Криптиран Сейф (`SecureStorageService.cs`)

### Защо стандартният `Aes.Create()` не е приложим в браузъра:
В .NET Blazor WebAssembly стандартният десктопен `Aes.Create()` зависи от OS-специфични библиотеки (CNG / OpenSSL), които не са налични в браузърния рантайм, което води до платформени предупреждения `CA1416` или грешки `PlatformNotSupportedException`.

### Архитектура на `SecureStorageService`:
- Използва **чист managed симетричен Counter-Mode (CTR) keystream шифър**, базиран на `SHA256.HashData`.
- За всяко криптиране се генерира уникален произволен 16-байтов вектор (IV):
  $$\text{KeystreamBlock}_i = \text{SHA256}(\text{MasterKey} \parallel \text{IV} \parallel \text{BlockIndex})$$
- Шифрованият текст се комбинира с вектора `[IV (16B) + Ciphertext]` и се записва като Base64 стринг с префикс `sec_` в `localStorage`.
- **Резултат**: 0 предупреждения в компилатора, пълна сигурност срещу локално подслушване през Browser DevTools и чист текст никога не се записва на диска.
- **Автоматична миграция**: При зареждане, всякакви открити стари некриптирани ключове автоматично се криптират и се изтриват от некриптираната памет.

---

## 7. Облачна Синхронизация: Supabase Cloud Vault (Zero-Knowledge E2EE)

MegaTools предлага крос-платформена синхронизация на настройките, API ключовете и предпочитанията между всички ваши устройства (десктоп, лаптоп, Android, iOS) без компрометиране на сигурността.

### Архитектура на Zero-Knowledge E2EE:
1. **Деривация на ключ**: От въведената от потребителя парола и имейл адрес се генерира 256-битов ключ чрез **PBKDF2 с SHA-256 (50 000 итерации)**:
   $$\text{Key} = \text{PBKDF2}(\text{Password}, \text{Salt}_{\text{email}}, 50000, 32)$$
2. **Локално шифроване**: Цялото състояние (`AppSettingsModel`) се сериализира в JSON и се криптира локално в браузъра чрез управляван симетричен Counter-Mode (CTR) keystream с 16-байтов случаен вектор (IV).
3. **Supabase PostgREST & Auth**:
   - Автентикацията се извършва директно през Supabase Auth API (`POST /auth/v1/token` или `/auth/v1/signup`) с връщане на JWT Bearer Token.
   - Криптираният шифър се качва в PostgreSQL таблица `user_settings_vault` чрез `POST /rest/v1/user_settings_vault` (с `Prefer: resolution=merge-duplicates` за автоматичен upsert).
4. **Row Level Security (RLS)**: В Supabase всеки потребител може да чете и записва единствено собствения си ред (`auth.uid() = user_id`).
5. **Нулев достъп до сурови данни**: Дори администраторът на базата данни в Supabase вижда само криптирания шифър `encrypted_payload`. Паролата и ключовете никога не напускат браузъра.

### 1-минутна настройка на Supabase:
В безплатен проект на [supabase.com](https://supabase.com) в SQL Editor се изпълнява:
```sql
create table if not exists public.user_settings_vault (
  user_id uuid references auth.users not null primary key,
  encrypted_payload text not null,
  iv text not null,
  updated_at timestamp with time zone default timezone('utc'::text, now()) not null
);
alter table public.user_settings_vault enable row level security;
create policy "Users can access and modify own vault"
  on public.user_settings_vault
  for all
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);
```

### Автоматична фонова синхронизация (Background Auto-Sync):
1. **Авто-пул при стартиране (`AutoPullOnStartup`)**:
   - В `MainLayout.razor` методът `OnInitializedAsync` задейства асинхронен неблокиращ таск `TryAutoPullOnStartupAsync(SettingsService)` (0 ms забавяне за потребителя).
   - Сравнява се `vault.UpdatedAt > config.LastSyncedAt`. При откриване на по-нов сейф в облака (напр. променен на компютъра), настройките се изтеглят, декриптират и прилагат незабавно.
   - Потребителят получава дискретно Toast известие: *"Облачна Синхронизация: Настройките бяха автоматично обновени от последната ви сесия."*
2. **Запомняне на ключа на доверени устройства (`RememberKeyOnDevice`)**:
   - Деривираният 256-битов ключ се запазва в локалния криптиран трезор `SecureStorageService` с ключ `sec_supabase_derived_key`.
   - Това позволява на PWA на мобилния телефон да извършва фонов декрипт без да кара потребителя да въвежда мастър паролата при всяко отваряне.
3. **Авто-качване при запис (`AutoSyncOnSave`)**:
   - При всяка промяна в страницата `Settings.razor` и клик върху *"Запази промените"*, настройките се криптират и автоматично се изпращат към Supabase.

---

## 8. Мобилно Приложение (PWA) & Респонсивни Графики

### PWA Инсталация:
- **Android / Windows / macOS**: Докосване на бутона **„Инсталирай като Апп“** задейства нативното събитие `beforeinstallprompt` и инсталира приложението директно на работния плот или начален екран.
- **iPhone / iPad (iOS Safari)**: Вграденият модал дава ясни визуални стъпки: бутон „Споделяне“ ($\uparrow$) $\rightarrow$ „Към началния екран“ (*Add to Home Screen*).
- **Service Worker & Кеш**: `service-worker.published.js` кешира всички статични dll, wasm и css ресурси, позволявайки офлайн работа.
- **Принудително обновяване (Force Refresh)**: В страницата `/settings` е наличен бутон, който изчиства кеша на Service Worker-а и форсира презареждане при пускане на нова версия.

### Мобилна Респонсивност на Графиките (RadzenChart Standard):
> [!IMPORTANT]
> **Златно правило за RadzenChart на мобилни екрани:**
> По подразбиране RadzenChart поставя легендата вдясно (`LegendPosition.Right`). При по-дълги заглавия на сериите, това изяжда над 70% от ширината на екрана на смартфон и сбива графиката в тясна лента.
> 
> **Задължителни настройки за всяка графика в MegaTools:**
> 1. Винаги задавайте легендата отдолу:
>    ```razor
>    <RadzenLegend Position="LegendPosition.Bottom" />
>    ```
> 2. Задавайте пълна ширина в контейнера:
>    ```razor
>    <RadzenChart Style="width: 100%; height: 300px;">
>    ```
> 3. Премахвайте паразитни заглавия на оста $X$ (`<RadzenAxisTitle Text="Година" />`), които излишно избутват легендата надолу.

---

## 9. Разработка и Деплой (Azure Static Web Apps)

### Локално стартиране:
За да тествате както от компютъра, така и от телефон в същата WiFi мрежа:
```bash
dotnet run --launch-profile http
```
- Компютър: `http://localhost:5266`
- Телефон през локална мрежа: `http://192.168.X.X:5266` *(конфигурирано в `Properties/launchSettings.json` с `0.0.0.0:5266`)*.

### CI/CD Пайплайн към Azure:
Файлът `.github/workflows/azure-static-web-apps-victorious-meadow-0c24a6803.yml` автоматично компилира .NET 10 при пуш в клон `main`:
```yaml
- name: Setup .NET SDK
  uses: actions/setup-dotnet@v4
  with:
    dotnet-version: '10.0.x'

- name: Publish Blazor App
  run: dotnet publish MegaTools.csproj -c Release -o release

- name: Build And Deploy
  uses: Azure/static-web-apps-deploy@v1
  with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_VICTORIOUS_MEADOW_0C24A6803 }}
    action: "upload"
    app_location: "release/wwwroot"
    output_location: ""
    skip_app_build: true
```

### Опростена Git Архитектура:
- **Единствен активен клон (`main`)**: Тъй като проектът се поддържа от самостоятелен разработчик, отпада необходимостта от паралелни клонове като `Development`.
- Всеки `git commit` и `git push origin main` директно задейства GitHub Actions пайплайна и публикува новата версия в Azure Static Web Apps без излишни мърджове.

---

## 10. Стандарти за Разработка на Нови Калкулатори

При добавяне на нов финансов инструмент или калкулатор в MegaTools се спазват следните задължителни правила:

1. **Разделение на отговорностите (Separation of Concerns)**:
   - Цялата чиста математическа и финансова логика се изолира в статичен C# клас в папка `MegaFuncs/Calculators/{Category}/`.
   - UI компонентът в `Components/Calculators/` се грижи единствено за представянето, извикването на функцията и интерактивността.
2. **Радзен Графика (RadzenChart Standard)**:
   - Винаги задавайте `<RadzenLegend Position="LegendPosition.Bottom" />`.
   - Винаги задавайте респонсивна ширина в контейнера `Style="width: 100%; height: 320px;"`.
   - Премахвайте паразитни заглавия на хоризонталната ос, които сбиват графиката.
3. **Регистриране в каталога**:
   - Добавете карта за новия калкулатор в [`Pages/Calculators.razor`](file:///d:/Developments/MegaTools/Pages/Calculators.razor) с подходяща категория (`Investment`, `Financial`, `Auto`, `Health`, `Maths`) и бранд икона от Bootstrap Icons (`bi-...`).
4. **Конфигурация на Настройки**:
   - Ако калкулаторът има параметри по подразбиране (данъци, хоризонти, стъпки), те се регистрират в [`AppSettingsModel.cs`](file:///d:/Developments/MegaTools/Models/Settings/AppSettingsModel.cs), за да бъдат обхванати от локалния криптиран сейф и от Supabase облачната синхронизация.
