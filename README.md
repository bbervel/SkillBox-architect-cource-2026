# architechCource2026
Дипломная работа по курсу Архитектор программного обеспечения SkillBox в 2026 году
# Спортивное социальное приложение

## Описание проекта
Репозиторий содержит артефакты архитектурного проекта глобального спортивного приложения, разработанного в рамках курса «Архитектор программного обеспечения». Документация структурирована по этапам согласования с архитектурным комитетом компании.

## Структура репозитория

```
├── README.md # Общее описание и навигация по этапам
├── stage1/ # Этап 1: Утверждение концепта
│ ├── 01_business_goals.md # Бизнес-цели
│ ├── 02_functional_requirements.md # Функциональные требования
│ ├── 03_stakeholders.md # Анализ стейкхолдеров
│ ├── 04_conceptual_architecture.md # Концептуальная архитектура
│ ├── 05_initial_risks.md # Начальные риски
│ ├── 06_development_roadmap.md # План поэтапной разработки
│ ├── 07_critical_scenarios.md # Критические бизнес-сценарии
│ ├── 08_quality_attributes.md # Атрибуты качества
│ ├── 09_non_functional_requirements.md # Нефункциональные требования
│ └── 10_architectural_options.md # Архитектурные опции и обоснование
│
├── stage2/ # Этап 2: Защита плана реализации
│ ├── 11_adr/ # Архитектурные решения (ADR)
│ │ ├── 001-choice-of-microservices.md
│ │ ├── 002-polyglot-persistence.md
│ │ ├── 003-async-communication.md
│ │ ├── 004-multi-cloud-k8s.md
│ │ ├── 005-integration-service.md
│ │ ├── 006-identity-provider.md
│ │ ├── 007-api-rest-grpc.md
│ │ └── 008-redis-caching.md
│ ├── 12_use_cases.md # Сценарии использования (Use Cases)
│ ├── 13_base_architecture.md # Базовая архитектура с учётом ограничений
│ └── 14_views/ # Основные представления
│ ├── functional.md # Функциональное представление
│ ├── information.md # Информационное представление
│ ├── concurrency.md # Многозадачность (concurrency)
│ ├── infrastructure.md # Инфраструктурное представление
│ └── security.md # Безопасность
│
├── stage3/ # Этап 3: Анализ рисков и стоимости
│ ├── 15_final_risk_analysis.md # Анализ рисков и компромиссы
│ └── 16_tco.md # Стоимость владения (TCO) в рублях
│
└── presentation/ # Материалы для финальной защиты
├── final_presentation.pptx # Презентация в PowerPoint
└── final_presentation.pdf # Презентация в PDF (для ознакомления)
```
## Навигация по этапам

### Этап 1: Утверждение концепта
*Артефакты, необходимые для прохождения первого этапа согласования.*

- [Бизнес-цели](stage1/01_business_goals.md)
- [Функциональные требования](stage1/02_functional_requirements.md)
- [Анализ стейкхолдеров](stage1/03_stakeholders.md)
- [Концептуальная архитектура](stage1/04_conceptual_architecture.md)
- [Начальные риски](stage1/05_initial_risks.md)
- [План поэтапной разработки](stage1/06_development_roadmap.md)
- [Критические бизнес-сценарии](stage1/07_critical_scenarios.md)
- [Атрибуты качества](stage1/08_quality_attributes.md)
- [Нефункциональные требования](stage1/09_non_functional_requirements.md)
- [Архитектурные опции и обоснование](stage1/10_architectural_options.md)

### Этап 2: Защита плана реализации
*Детальная проработка архитектуры и принятые решения.*

- [ADR (Architecture Decision Records)](stage2/11_adr/)
- [Сценарии использования](stage2/12_use_cases.md)
- [Базовая архитектура](stage2/13_base_architecture.md)
- **Представления:**
  - [Функциональное](stage2/14_views/functional.md)
  - [Информационное](stage2/14_views/information.md)
  - [Многозадачность](stage2/14_views/concurrency.md)
  - [Инфраструктурное](stage2/14_views/infrastructure.md)
  - [Безопасность](stage2/14_views/security.md)

### Этап 3: Анализ рисков и стоимости
*Оценка рисков, компромиссов и затрат.*

- [Анализ рисков созданной архитектуры](stage3/15_final_risk_analysis.md)
- [Стоимость владения (TCO) в рублях](stage3/16_tco.md)

### Презентация для финальной защиты
- [Презентация PowerPoint](presentation/Sportivnoe-socialnoe-prilozhenie-dlya-globalnogo-soobshestva.pptx)

## Условные обозначения
- `*.md` — файлы в формате Markdown.
- `*.pptx` / `*.pdf` — файлы презентации.
- Папка `11_adr` содержит отдельные файлы для каждого архитектурного решения.

---
*Репозиторий создан в рамках финальной работы курса «Архитектор программного обеспечения».*