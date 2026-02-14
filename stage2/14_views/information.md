# Информационное представление

## Логическая модель данных (основные сущности)

### User Service (PostgreSQL)
- **users**: id, email, password_hash, name, birth_date, gender, country, city, registration_date, last_login, status (active/deleted), consent_flags (jsonb), settings (jsonb)
- **friendships**: user_id, friend_id, status (pending/approved), created_at
- **consent_logs**: user_id, consent_type, granted_at, revoked_at, ip_address

### Workout Service (PostgreSQL + TimescaleDB)
- **workouts**: id, user_id, activity_type, start_time, end_time, duration, distance, avg_heart_rate, calories, notes, gpx_file_url, created_at
- **workout_tracks**: id, workout_id, time, latitude, longitude, altitude, heart_rate (TimescaleDB hypertable)
- **personal_records**: user_id, activity_type, distance, time, date

### Inventory Service (PostgreSQL)
- **inventory_items**: id, user_id, type (shoe, apparel, equipment), brand, model, purchase_date, start_usage_date, initial_mileage, current_mileage, wear_limit, status (active/archived)
- **workout_inventory**: workout_id, inventory_item_id (связь многие-ко-многим)

### Social Service
- **MongoDB**:
  - groups: _id, name, description, sport_type, location, created_by, created_at, is_private, members (array), moderators (array)
  - posts: _id, group_id, user_id, content, media_urls, likes (array), comments (array), created_at
  - messages: _id, group_id, user_id, text, timestamp
- **Neo4j**:
  - узлы: User, Group
  - связи: FRIENDS_WITH, MEMBER_OF, FOLLOWS
- **Elasticsearch**:
  - индексы: users, groups, posts для полнотекстового поиска

### Gamification Service (PostgreSQL + Redis)
- **levels**: id, level_number, xp_required, rewards (json)
- **user_xp**: user_id, total_xp, current_level
- **achievements**: id, name, description, icon, criteria (json)
- **user_achievements**: user_id, achievement_id, earned_at
- **Redis**:
  - лидерборды: sorted sets по типу (friends, group, global) с ключом `leaderboard:{type}:{period}`

### Promo Service (PostgreSQL + MongoDB)
- **PostgreSQL**:
  - promos: id, name, description, discount_type, discount_value, start_date, end_date, target_criteria (json), region, status
  - user_promo_interactions: user_id, promo_id, viewed_at, clicked_at, converted_at
- **MongoDB**:
  - news_articles: _id, title, content, sport_type, source, published_at, tags
  - user_news_preferences: user_id, sport_types (array)

## Потоки данных

### Основные потоки событий (Kafka)

| Топик | Продюсеры | Консьюмеры | Назначение |
|-------|-----------|------------|------------|
| `user-events` | User Service | Analytics, Notification (приветственные), другие сервисы | Уведомления об изменении профиля, удалении аккаунта |
| `workout-events` | Workout Service | Inventory, Gamification, Analytics, Notification (личные рекорды) | Обновление инвентаря, начисление опыта, сбор данных |
| `social-events` | Social Service | Analytics, Notification (упоминания) | Отслеживание активности в группах |
| `inventory-events` | Inventory Service | Promo, Analytics | Срабатывание порогов износа для промо |
| `gamification-events` | Gamification Service | Notification, Analytics | Уведомления о достижениях, новых уровнях |
| `promo-events` | Promo Service | Analytics | Отслеживание эффективности промо |
| `notification-requests` | Все сервисы | Notification Service | Запросы на отправку уведомлений |

### Потоки данных при записи тренировки

1. Клиент отправляет тренировку → API Gateway → Workout Service.
2. Workout Service сохраняет данные в Workout DB и Object Storage (GPX).
3. Workout Service публикует событие в `workout-events` с полной информацией о тренировке.
4. Inventory Service получает событие, обновляет `current_mileage` для связанного инвентаря.
5. Gamification Service получает событие, вычисляет полученный опыт, обновляет `user_xp`, проверяет достижения, обновляет лидерборды в Redis.
6. Analytics Service получает событие и сохраняет в ClickHouse для отчётов.
7. Если достигнут личный рекорд, Gamification Service публикует событие в `gamification-events`, Notification Service отправляет уведомление.