# Практическое занятие № 4. Сравнение REST и GraphQL: разработка одного и того же функционала двумя способами
# Нам А. В., ЭФМО-01-25

## Цель работы
Освоить практическое сравнение REST и GraphQL на примере одного и того же прикладного сценария, научиться реализовывать одинаковый функционал двумя подходами, анализировать различия в структуре запросов и ответов, а также делать обоснованный вывод о целесообразности использования каждого из подходов в backend-разработке.

## Структура проекта
```
pz12/
├── deploy
│   ├── db
│   │   ├── docker-compose.yml
│   │   └── init.sql
│   ├── lb
│   │   └── docker-compose.yml
│   ├── monitoring
│   │   ├── docker-compose.yml
│   │   └── prometheus.yml
│   ├── redis
│   │   └── docker-compose.yml
│   └── tls
│       ├── cert.pem
│       ├── docker-compose.yml
│       ├── key.pem
│       ├── nginx-lb.conf
│       └── nginx.conf
├── go.mod
├── go.sum
├── pkg
│   ├── auth_grpc.pb.go
│   └── auth.pb.go
├── proto
│   └── auth.proto
├── services
│   ├── auth
│   │   ├── cmd
│   │   │   └── auth
│   │   │       └── main.go
│   │   ├── Dockerfile
│   │   ├── go.mod
│   │   ├── go.sum
│   │   └── internal
│   │       ├── grpc
│   │       │   └── server.go
│   │       ├── handler
│   │       │   └── auth_handler.go
│   │       └── service
│   │           └── auth_servise.go
│   ├── graphql
│   │   ├── cmd
│   │   │   └── graphql
│   │   │       └── main.go
│   │   ├── Dockerfile
│   │   ├── go.mod
│   │   ├── go.sum
│   │   ├── gqlgen.yml
│   │   ├── graph
│   │   │   ├── generated.go
│   │   │   ├── model
│   │   │   │   └── models_gen.go
│   │   │   ├── resolver.go
│   │   │   ├── schema.graphqls
│   │   │   └── schema.resolvers.go
│   │   └── internal
│   │       ├── repository
│   │       │   └── task_repository.go
│   │       └── service
│   │           └── task_service.go
│   └── tasks
│       ├── cmd
│       │   └── tasks
│       │       └── main.go
│       ├── Dockerfile
│       ├── go.mod
│       ├── go.sum
│       └── internal
│           ├── cache
│           │   └── redis.go
│           ├── client
│           │   └── auth_client.go
│           ├── csrf
│           │   └── middleware.go
│           ├── handler
│           │   └── task_handler.go
│           ├── metrics
│           │   └── metrics.go
│           ├── repository
│           │   ├── postgres.go
│           │   └── task_repository.go
│           └── service
│               └── task_service.go
└── shared
    ├── httpx
    │   └── client.go
    ├── logger
    │   └── logger.go
    ├── middleware
    │   ├── accesslog.go
    │   ├── metrics.go
    │   └── requestid.go
    └── models
        ├── models.go
        └── task.go
```

## Результаты выполнения (скриншоты)

### Подготовить примеры REST-запросов для отчёта

<img width="694" height="394" alt="Снимок экрана 2026-06-07 в 07 30 00" src="https://github.com/user-attachments/assets/c05fb54b-b294-4480-8a04-6e40643e9797" />

<img width="608" height="381" alt="Снимок экрана 2026-06-07 в 07 30 15" src="https://github.com/user-attachments/assets/09a1b240-5c84-4a6a-954a-998f5ff9a6a2" />

<img width="609" height="319" alt="Снимок экрана 2026-06-07 в 07 30 31" src="https://github.com/user-attachments/assets/3d1cbe32-139e-4655-9253-9fe84a9056c7" />

### Реализовать GraphQL-вариант

<img width="702" height="451" alt="Снимок экрана 2026-06-07 в 07 32 29" src="https://github.com/user-attachments/assets/b1e61107-2bc0-473c-91db-8d3e1f675c2a" />

<img width="672" height="429" alt="Снимок экрана 2026-06-07 в 07 32 38" src="https://github.com/user-attachments/assets/cd7bc465-cb88-4a7f-84db-386d2514651c" />

<img width="692" height="326" alt="Снимок экрана 2026-06-07 в 07 32 55" src="https://github.com/user-attachments/assets/c8af4b94-44c1-44e9-a462-27fb950de8c1" />

<img width="708" height="389" alt="Снимок экрана 2026-06-07 в 07 33 08" src="https://github.com/user-attachments/assets/1124b691-e8d9-4bff-b63c-8158e69746a6" />



## Сравнительная таблица

| Аспект | REST | graphql |
 |-------|----------|----------|
 |Структура API|Несколько endpoint|Один endpoint|
|Выбор полей|Определяет сервер|Определяет клиент|
|Over-fetching|Возможен|Отсутствует, тк можно выбирать поля|
|Количество запросов|Одинаково|Одинаково|
|Обработка ошибок|HTTP-статусы|Поле errors|
|Кэширование|Просто, тк HTTP-кэш работает по URL|Сложное|
|Сложность внедрения|Выше|Ниже|
|Гибкость для клиента|Ниже|Выше за счет интерфейса, выбора нобора полей|


### Контрольные вопросы

1. В чём принципиальное отличие REST и GraphQL?

REST - набор endpoint с фиксированными ответами, GraphQL - один endpoint, клиент сам выбирает поля

2. Что такое over-fetching и under-fetching?

Over-fetching - получение лишних данных. Under-fetching - нужно делать несколько запросов.

3. Почему GraphQL позволяет клиенту точнее выбирать поля ответа?

Клиент явно указывает нужные поля в запросе

4. Почему REST проще кэшировать стандартными средствами HTTP?

Каждый endpoint имеет свой URL, HTTP-кэш работает по URL

5. Чем отличается обработка ошибок в REST и GraphQL?

REST — HTTP-статусы. GraphQL — HTTP 200 + поле errors.

6. В каких случаях REST оказывается более практичным решением?

Для простых CRUD-сервисов, внутренних API, когда мало клиентов

7. В каких случаях GraphQL может дать преимущества?

Разные группы клиентов с разными наборами полей.

8. Почему корректное сравнение нужно проводить на одном сценарии?

Чтобы выводы были объективными, а не зависели от разных условий

9. Какие сложности возникают при сопровождении GraphQL API?

Сложнее кэширование, мониторинг, защита от сложных запросов

10. Почему для учебных CRUD-сервисов REST часто оказывается проще?

Меньше boilerplate кода, проще отладка, стандартные инструменты
