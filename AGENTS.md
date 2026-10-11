# Правила за работа в paptools (Antigravity Agent Rules)

## 📌 Основни Правила и Стандарти

1. **Автоматично Пушване на Кода (`git push`)**:
   - При всеки комит на промени (`git commit`), **винаги** изпълнявай `git push origin master` веднага след това.

2. **Актуализация на Документацията**:
   - При добавяне, промяна или премахване на функционалност, скил, модул или таблица/колекция, **винаги** ъпдейтвай:
     - [`README.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/README.md)
     - [`KNOWLEDGEBASE.md`](file:///C:/Users/papun/.gemini/config/plugins/paptools/KNOWLEDGEBASE.md)
     - Съответните скилове в [`skills/`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/) и техните `SKILL.md` файлове.

3. **Синхронизация на Базата Знания**:
   - Пази централната база знания (`KNOWLEDGEBASE.md`) и скила [`pm-knowledgebase`](file:///C:/Users/papun/.gemini/config/plugins/paptools/skills/pm-knowledgebase/SKILL.md) 100% актуални при всяка архитектурна или схемена промяна.

4. **Задължителна Регистрация на Нови Страници в Breadcrumbs (`_mapUrl`)**:
   - При добавяне на нов маршрут (`@page "/segment"` или `@page "/category/segment"`) в Blazor (`Web.ManagerV2`), **винаги** регистрирай съответния URL сегмент с неговия български превод/наименование в речника `_mapUrl` в [`Web.ManagerV2/Shared/MainLayout.razor`](file:///c:/Users/papun/RiderProjects/PersonalManager/PersonalManager/Web.ManagerV2/Shared/MainLayout.razor).
   - Никога не оставяй сегменти да се рендират по подразбиране със суровия английски URL път в навигационните хлябни трохи (Breadcrumbs).

