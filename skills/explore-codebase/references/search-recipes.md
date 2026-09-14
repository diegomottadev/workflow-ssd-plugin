# Codebase Search Recipes by Artifact Type

Reference for the `explore-codebase` skill. These recipes outline search patterns for locating components quickly without false negatives.

---

## 1. Routes and Endpoints
- Search URL fragments in route definitions (`routes/`, controllers, `@Controller`, `router.ts`, etc.):
  - Search path segments (e.g. `/api/orders`, `/checkout`).
  - Search action or handler method names.
- Beware of route prefixes grouped at the class or module level.

## 2. Environment Variables and Settings
- Search where defined: `.env`, `.env.example`, `config/`, `application.properties`.
- Search where consumed in application code.
- Check whether the framework requires accessing variables through a config service or directly from `process.env` / `os.environ`.

## 3. Controllers, Handlers, or Services
- Search by domain entity name.
- Identify injected constructor dependencies to map the call hierarchy.

## 4. UI Views and Frontend Components
- Search by visible text labels, data-test attributes, or component names.
- Check whether the app uses server-rendered templates or an SPA framework.

## 5. Data Models and Entities
- Search in entity/model directories (`models/`, `entities/`, `domain/`).
- Identify table names, primary keys, and foreign key relationships.

## 6. Permissions, Roles, and Auth Policies
- Search for route guards, annotations, or auth middlewares (e.g. `@RolesAllowed`, `can:`, `hasPermission`).
- Check where permissions or roles are cataloged.

## 7. Magic IDs and Status Constants
- Search if state numbers or strings are defined in enums/constants or hardcoded in logic.
- Trace back to the source table or schema definition.

## 8. Call Graph: Who Calls This?
- Search the method or function name across the project.
- If the name is very generic (e.g. `get()`, `save()`), search combined with the service or class variable (e.g. `orderService.save(`, `OrderRepository.save`).
