---
name: pm-db-migrations
description: >-
  Скриптове, миграции на данни, поддръжка и рефакториране на MongoDB схеми за PersonalManager (от PersonalManager-Supported-Scripts).
  Използвайте този скил при преименуване, триене или одит на полета в MongoDB, или при корекция на исторически данни за дивиденти и БНБ курсове.
---

# PersonalManager DB Migrations & Maintenance Scripts

## Общ преглед

Репозиторито `PersonalManager-Supported-Scripts` съдържа Python помощни скриптове за схема миграции, одит на свойства и корекция на исторически данни в MongoDB базата данни на PersonalManager.

```text
PersonalManager-Supported-Scripts/
├── check_property.py        # Проверка и одит на съществуването на дадено поле в колекциите
├── remove_property.py       # Изтриване на остарели/неизползвани полета от колекция
├── rename_property.py       # Преименуване на полета в MongoDB документи
├── fix-bnb-dates.py         # Корекция и уеднаквяване на дати в БНБ валутните курсове
├── update_t212_dividends.py # Преизчисление на брутни дивиденти за Trading 212 с --dry-run
└── commands.sh              # Изпълними команди и runbook за миграции
```

---

## 1. Инструменти за Миграция на Полета (Schema Refactoring)

### А) Одит на полета (`check_property.py`)
Проверява в кои колекции присъства дадено поле:
```bash
python check_property.py -p SumInBGN -x ConvertedSalaries ConvertedTransactions -d
```

### Б) Преименуване на полета (`rename_property.py`)
Променя името на поле в избрана колекция:
```bash
python rename_property.py -c BrokerFees -o CurrencyRateToBGN -n CurrencyRateToEUR
python rename_property.py -c Dividends -o CurrencyRateToBGN -n CurrencyRateToEUR
```

### В) Премахване на полета (`remove_property.py`)
Безопасно изтрива остарели полета от документи:
```bash
python remove_property.py -c Funds -p SumInBGN
python remove_property.py -c FundOrders -p CurrentPriceInBGN
```

---

## 2. Специализирани Скриптове за Данни

### А) Корекция на Trading 212 дивиденти (`update_t212_dividends.py`)
Преизчислява брутната сума на дивидентите (`SumInCurrency = SumInCurrency + TaxInCurrency`):
- **DRY-RUN (Симулация)**:
  ```bash
  python update_t212_dividends.py --dry-run
  ```
- **COMMIT (Запис)**:
  ```bash
  python update_t212_dividends.py --commit
  ```

### Б) Поправка на БНБ Дати (`fix-bnb-dates.py`)
Валидира и коригира невалидни или текстови форматни дати в колекцията с валутни курсове от БНБ.

---

## Правила за Безопасност при Миграции

1. **Винаги тествайте с Dry-Run**: При възможност стартирайте скриптовете първо в симулационен режим.
2. **Архивиране (Backup)**: Преди стартиране на `rename_property` или `remove_property` над продукционна база данни, направете `mongodump`.
3. **UTF-8 поддръжка за Windows**: Всички скриптове поддържат UTF-8 преконфигурация за правилен изход в Windows PowerShell / CMD.
