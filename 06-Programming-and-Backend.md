# Programming and Backend Concepts

## API and Protocol Concepts

- CRUD: Create, Read, Update, Delete.
- REST style example: `GET /accounts/123`.
- SOAP: usually XML-based and strongly structured.

## Backend Best Practices

- Use dedicated request/response models.
- Environment variables are typically read from the runtime process context.

## Databases and ORM

- PostgreSQL: relational data storage engine.
- SQLAlchemy: ORM model layer.
- Alembic: database migration management.

ORM note:
- Model changes do not automatically update DB schema; migrations are required.

## SQLAlchemy Notes

- `db.flush()` pushes pending changes to DB transaction context, useful for generated IDs before commit.

## Async and Yield

- `return` sends one final value from a function.
- `yield` produces values over time from a generator-like flow.
- Async helps handle concurrency for I/O-heavy workloads.

## Infrastructure as Code and Network as Code

- IaC: define and manage infrastructure through version-controlled code (example: Terraform).
- NaC: apply code-driven management principles to network devices and policy.

## Application-Centric Infrastructure

- ACI ties infrastructure behavior to application requirements (for example, voice, streaming, IoT profiles).

## Monitoring and Metrics

- Service exporters expose runtime metrics (CPU, memory, etc.) for observability systems.
- Batch requests can send multiple payloads in one HTTP request where supported.
