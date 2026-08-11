---
name: pm-finance-core
description: >-
  Правила и модели за управление на транзакции, заплати, планирани и периодични разходи и сканиране на касови бележки в PersonalManager.
---

# PersonalManager Finance Core

## 1. Транзакции и Категории (`Transaction.cs`, `Category.cs`)

Транзакциите са основните финансови записи в системата:

```csharp
public class Transaction
{
    public ObjectId Id { get; set; }
    public ObjectId UserId { get; set; }
    public ObjectId SalaryId { get; set; } = ObjectId.Empty;
    public ObjectId CategoryId { get; set; }
    public string CategoryName { get; set; }
    public decimal Sum { get; set; }
    public string Comment { get; set; }
    public bool Expense { get; set; } // true = Разход, false = Приход
    public bool Marked { get; set; }  // Маркирана за специален отчет
    public DateTime AddedOn { get; set; }
}
```

### Важни правила при транзакции:
- Полето `Expense` определя дали сумата е приход (`false`) или разход (`true`).
- Полето `SalaryId` свързва транзакцията с конкретен месечен бюджет/заплата (`Salary`).

---

## 2. Заплати и Месечни Бюджети (`Salary.cs`, `SumSalary.cs`)

Системата групира разходите по месечни периоди (заплати):
- **`Salary`**: Представлява период на заплата с начална дата, крайна дата и очакван приход.
- **`SumSalary`**: Изчислява общия сумарен баланс, спестявания и балансови съотношения за даден период.

---

## 3. Планирани и Периодични Разходи

- **`PlannedExpense`**: Планиран еднократен разход за даден месец с приоритет и статут на изпълнение.
- **`RecurringExpense`**: Автоматично повтарящ се разход (напр. наем, абонамент), който се генерира от `DailyCronJob`.

---

## 4. Сканиране и Мапинг на Касови Бележки (`Receipt.cs`, `ReceiptMap.cs`, `ReceiptExpression.cs`)

Системата поддържа мапинг на касови бележки чрез регулярни изрази (`Regex`):
- `Receipt`: Документ за сканирана бележка (QR код или текст).
- `ReceiptMap`: Мапинг между текст от бележка и категория в системата.
- `ReceiptExpression`: Шаблон (Regex) за парсване на специфични вериги магазини.
