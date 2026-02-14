# Функциональное представление системы

## Компоненты и их функции

| Компонент | Основные функции | Взаимодействие с другими компонентами |
|-----------|------------------|----------------------------------------|
| **User Service** | Управление учётными записями, профилями, настройками приватности, согласиями, аутентификация (совместно с Keycloak). | API Gateway, User DB, Redis (кэш), Keycloak, генерирует события `user.updated`, `user.deleted` в Kafka. |
| **Workout Service** | Запись тренировок (GPS, ручной ввод), хранение истории, статистика, сравнение с прошлыми тренировками, личные рекорды, работа с GPX-файлами. | API Gateway, Workout DB (PostgreSQL+TimescaleDB), Object Storage (S3), генерирует события `workout.created`, `workout.updated` в Kafka. |
| **Social Service** | Управление группами, чатами, лентой активности, друзьями, поиск людей и групп, модерация. | API Gateway, Social DB (MongoDB, Neo4j, Elasticsearch), генерирует события `group.created`, `post.created`, `friendship.established` в Kafka. |
| **Inventory Service** | Добавление инвентаря, отслеживание износа, уведомления о замене, привязка к тренировкам. | API Gateway, Inventory DB (PostgreSQL), получает события `workout.created` для обновления пробега, генерирует события `inventory.wear_threshold_reached`. |
| **Gamification Service** | Начисление опыта, уровни, значки, челленджи, лидерборды. | API Gateway, Gamification DB (PostgreSQL, Redis), получает события `workout.created`, `workout.personal_best`, генерирует события `achievement.earned`, `level.up`. |
| **Promo Service** | Управление промоакциями, персонализированные предложения, новостная лента спорта, интеграция с e-commerce. | API Gateway, Promo DB (PostgreSQL, MongoDB), получает события `inventory.wear_threshold_reached`, `workout.completed`, запрашивает каталог товаров у Integration Service. |
| **Notification Service** | Отправка push-уведомлений, email, SMS. | Получает события из Kafka, вызывает внешние провайдеры (FCM, APNS, Twilio, SendGrid). |
| **Integration Service** | Адаптеры для внешних систем: носимые устройства, фитнес-платформы, e-commerce, другие приложения компании. | Получает запросы от других сервисов (синхронно через API Gateway или асинхронно через Kafka), вызывает внешние API. |
| **Analytics Service** | Сбор событий, построение Data Lake, ETL, подготовка отчётов для бизнеса. | Подписан на все события Kafka, сохраняет их в ClickHouse, запускает периодические ETL-задачи (Airflow). |
| **API Gateway** | Единая точка входа, маршрутизация, аутентификация, rate limiting, логирование. | Принимает запросы от клиентов, направляет к сервисам, проверяет JWT, взаимодействует с Keycloak. |
| **Keycloak** | Identity Provider: аутентификация, SSO, управление пользователями, OAuth2. | Интегрирован с API Gateway и User Service. |

## Диаграмма компонентов

(См. диаграмму в разделе 13.2, она полностью отражает функциональную структуру.)

## Описание ключевых взаимодействий

- **Сохранение тренировки**: Клиент → API Gateway → Workout Service → Workout DB. Workout Service публикует событие `workout.created` в Kafka. Inventory Service (обновление пробега), Gamification Service (начисление опыта, проверка достижений), Analytics Service (сбор данных) подписываются на это событие.
- **Отправка уведомления**: Любой сервис публикует событие `notification.required` в Kafka. Notification Service потребляет событие и отправляет push/email.
- **Персонализированное промо**: Inventory Service публикует событие `inventory.wear_threshold_reached`. Promo Service получает его, запрашивает через Integration Service каталог товаров, формирует предложение и отправляет уведомление через Notification Service.
- **Поиск людей**: Клиент → API Gateway → Social Service → Elasticsearch → возврат результатов.