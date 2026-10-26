# Правила за работа в paptools (Antigravity Agent Rules)

## 📌 Основни Правила и Стандарти

1. **Автоматично Пушване на Кода (`git push`)**:
   - При всеки комит на промени (`git commit`), **винаги** изпълнявай `git push origin master` веднага след това.

2. **Непрекъснато Обучение и Автоматично Обновяване на Плъгина (Continuous Learning & Knowledge Sync)**:
   - **ЗАДЪЛЖИТЕЛНО ПРАВИЛО**: При всяко научаване на нова информация, промяна в архитектурата, нова функционалност, промяна в конфигурации (.env), добавяне/модификация на MongoDB колекции или откриване на важен детайл за екосистемата:
     - **НИКОГА** не оставяй новото знание само в текущия чат контекст!
     - **ВЕДНАГА актуализирай съответния скил** в [`skills/`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/) (напр. `pm-data-ingestion-*`, `pm-investments`, `pm-ai-analytics`, `pm-architecture` и др.).
     - **Актуализирай централната база знания**: [`KNOWLEDGEBASE.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/KNOWLEDGEBASE.md) и [`README.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/README.md).
     - **Веднага комитвай и пушвай**: След обновяване на файловете в плъгина, веднага направи `git add`, `git commit` и `git push origin master` в репозиторията на `paptools`, за да се персистира новото знание за всички бъдещи сесии.

3. **Синхронизация на Базата Знания**:
   - Пази централната база знания (`KNOWLEDGEBASE.md`) и скила [`pm-knowledgebase`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-knowledgebase/SKILL.md) 100% актуални при всяка архитектурна или схемена промяна.

4. **Задължителна Регистрация на Нови Страници в Breadcrumbs (`_mapUrl`)**:
   - При добавяне на нов маршрут (`@page "/segment"` или `@page "/category/segment"`) в Blazor (`Web.ManagerV2`), **винаги** регистрирай съответния URL сегмент с неговия български превод/наименование в речника `_mapUrl` в `Web.ManagerV2/Shared/MainLayout.razor`.
   - Никога не оставяй сегменти да се рендират по подразбиране със суровия английски URL път в навигационните хлябни трохи (Breadcrumbs).
