---
name: pm-data-ingestion-modules
description: >-
  Подробно ръководство за модулите в PersonalManager-Data-Ingestion (BNB експорт, Market Exporter, Stock AI, GCP Document OCR, Daily Automation).
---

# Data Ingestion Modules Guide

## 1. BNB Export (`modules/export_bnb.py`)
- **Задача**: Извлича валутни курсове от БНБ спрямо EUR/BGN.
- **Резервен механизъм (Fallback)**: В почивни дни или официални празници, когато БНБ няма публикувани нови данни, модулът автоматично копира и записва последната налична стойност за текущата дата, осигурявайки непрекъснатост на данните за PersonalManager.

---

## 2. Market Exporter (`modules/market_exporter.py`)
- **Задача**: Извлича фундаментални данни за борсови и извънборсови акции/ETF от `StockAnalysis.com`.
- **Извлечени метрики**: P/E Ratio, Market Cap, Revenue, EPS, Dividend Yield, Beta, Profit Margin и др.
- **Запис в базата**: Обновява колекция `StockFundaments` в MongoDB.

---

## 3. AI Data Processing (`modules/ai_data.py` & `modules/stock_ai_data.py`)
- **Автомобили & Валути (`ai_data.py`)**:
  - Изчислява среден пробег и очаквани разходи по автомобили въз основа на `car_ai_prompt.txt`.
  - Анализира тенденциите на валутните курсове през последните N дни.
- **Инвестиционни профили (`stock_ai_data.py`)**:
  - Генерира AI анализи и досиета за компаниите на базата на `stock_ai_prompt.txt`.

---

## 4. Daily Automation (`modules/daily_automation.py`)
- Изпълнява ежедневно автоматично генериране на повтарящи се разходи (`RecurringExpense`).
- Изчислява месечни баланси и синхронизира състоянието на сметките и заплатите.

---

## 5. GCP Processor (`modules/gcp_processor.py`)
- Използва Google Cloud Vision / Document AI за OCR сканиране на касови бележки и извличане на търговци, суми и дати.
