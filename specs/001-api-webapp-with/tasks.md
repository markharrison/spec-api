# Tasks: API WebApp with Color Management

**Input**: Design documents from `/workspaces/.devcontainer/specs/001-api-webapp-with/`
**Prerequisites**: plan.md (required), research.md, data-model.md, contracts/

## Phase 3.1: Setup
- [X] T001 Create project structure per plan.md (src/ColorApi, src/ColorApi.Tests, docs/)
- [X] T002 Initialize .NET 9.0 solution and projects (ColorApi, ColorApi.Tests)
- [X] T003 [P] Configure linting and formatting tools (EditorConfig, StyleCop, SonarAnalyzer)
- [X] T004 [P] Add Swashbuckle.AspNetCore for Swagger UI

## Phase 3.2: Tests First (TDD) ⚠️ MUST COMPLETE BEFORE 3.3
- [X] T005 [P] Contract test GET /api/colors in src/ColorApi.Tests/Contract/GetAllColorsTests.cs
- [X] T006 [P] Contract test GET /api/colors/{id} in src/ColorApi.Tests/Contract/GetColorByIdTests.cs
- [X] T007 [P] Contract test PUT /api/colors/{id} in src/ColorApi.Tests/Contract/SetColorTests.cs
- [X] T008 [P] Contract test GET /api/colors/random in src/ColorApi.Tests/Contract/GetRandomColorTests.cs
- [X] T009 [P] Integration test: retrieve all colors (empty, populated) in src/ColorApi.Tests/Integration/GetAllColorsIntegrationTests.cs
- [X] T010 [P] Integration test: retrieve color by id (found, not found) in src/ColorApi.Tests/Integration/GetColorByIdIntegrationTests.cs
- [X] T011 [P] Integration test: add/update color in src/ColorApi.Tests/Integration/SetColorIntegrationTests.cs
- [X] T012 [P] Integration test: get random color (empty, populated) in src/ColorApi.Tests/Integration/GetRandomColorIntegrationTests.cs
- [X] T013 [P] Integration test: error scenarios (invalid hex, invalid id, malformed request) in src/ColorApi.Tests/Integration/ErrorScenariosIntegrationTests.cs

## Phase 3.3: Core Implementation (ONLY after tests are failing)
- [ ] T014 [P] Implement Color entity in src/ColorApi/Models/Color.cs
- [ ] T015 [P] Implement Color repository (in-memory, thread-safe) in src/ColorApi/Repositories/ColorRepository.cs
- [ ] T016 [P] Implement Color service (business logic) in src/ColorApi/Services/ColorService.cs
- [ ] T017 Implement Color controller with endpoints in src/ColorApi/Controllers/ColorController.cs
- [ ] T018 Implement health check endpoint in src/ColorApi/Controllers/HealthController.cs
- [ ] T019 Implement error handling middleware in src/ColorApi/Middleware/ErrorHandlingMiddleware.cs
- [ ] T020 Implement Swagger UI and OpenAPI docs in src/ColorApi/Startup.cs

## Phase 3.4: Integration
- [ ] T021 Integrate structured logging (ILogger) in src/ColorApi/Program.cs and Startup.cs
- [ ] T022 Integrate Application Insights or equivalent APM (optional)
- [ ] T023 Configure CORS and security headers in src/ColorApi/Startup.cs

## Phase 3.5: Polish
- [ ] T024 [P] Unit tests for ColorRepository in src/ColorApi.Tests/Unit/ColorRepositoryTests.cs
- [ ] T025 [P] Unit tests for ColorService in src/ColorApi.Tests/Unit/ColorServiceTests.cs
- [ ] T026 [P] Unit tests for ColorController in src/ColorApi.Tests/Unit/ColorControllerTests.cs
- [ ] T027 [P] Performance tests for endpoints in src/ColorApi.Tests/Performance/PerformanceTests.cs
- [ ] T028 [P] Update docs/api/ and docs/setup/ with usage and setup instructions
- [ ] T029 [P] Review and update OpenAPI spec in contracts/colors-api.yaml
- [ ] T030 [P] Manual test using quickstart.md scenarios

## Parallel Execution Example
```
# Launch T005-T013 together (contract/integration tests):
Task: "Contract test GET /api/colors in src/ColorApi.Tests/Contract/GetAllColorsTests.cs"
Task: "Integration test: retrieve all colors in src/ColorApi.Tests/Integration/GetAllColorsIntegrationTests.cs"
# Launch T014-T016 together (models/services):
Task: "Implement Color entity in src/ColorApi/Models/Color.cs"
Task: "Implement Color repository in src/ColorApi/Repositories/ColorRepository.cs"
Task: "Implement Color service in src/ColorApi/Services/ColorService.cs"
```

## Dependencies
- Setup (T001-T004) before all
- Tests (T005-T013) before implementation (T014-T020)
- Models (T014) before repository/service (T015-T016)
- Repository/service before controller/endpoints (T017)
- Implementation before integration (T021-T023) and polish (T024-T030)
- [P] tasks = different files, no dependencies

## Validation Checklist
- [ ] All contracts have corresponding tests
- [ ] All entities have model tasks
- [ ] All tests come before implementation
- [ ] Parallel tasks truly independent
- [ ] Each task specifies exact file path
- [ ] No task modifies same file as another [P] task
