# Infonetica – Software Engineer Intern Take-Home Exercise
## Configurable Workflow Engine (State-Machine API)

A minimal backend service implementing a configurable workflow engine using .NET 8.

## Quick Start

### Prerequisites
- .NET 8 SDK

### Run the Application
```bash
cd WorkflowEngine
dotnet restore
dotnet run
```

The API will be available at `http://localhost:5000`

### Health Check
```bash
curl http://localhost:5000/health
```

## API Endpoints

### Workflow Definitions
- `POST /api/workflow-definitions` - Create workflow definition
- `GET /api/workflow-definitions/{id}` - Get specific definition
- `GET /api/workflow-definitions` - List all definitions

### Workflow Instances
- `POST /api/workflow-instances?definitionId={id}` - Start new instance
- `GET /api/workflow-instances/{id}` - Get specific instance
- `GET /api/workflow-instances` - List all instances
- `POST /api/workflow-instances/{id}/execute` - Execute action on instance

## Example Usage

### 1. Create a Workflow Definition

```bash
curl -X POST "http://localhost:5000/api/workflow-definitions" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "approval-process",
    "name": "Document Approval",
    "states": [
      {
        "id": "draft",
        "name": "Draft",
        "isInitial": true,
        "isFinal": false,
        "enabled": true
      },
      {
        "id": "approved",
        "name": "Approved",
        "isInitial": false,
        "isFinal": true,
        "enabled": true
      }
    ],
    "actions": [
      {
        "id": "approve",
        "name": "Approve Document",
        "enabled": true,
        "fromStates": ["draft"],
        "toState": "approved"
      }
    ]
  }'
```

### 2. Start a Workflow Instance

```bash
curl -X POST "http://localhost:5000/api/workflow-instances?definitionId=approval-process"
```

### 3. Execute an Action

```bash
curl -X POST "http://localhost:5000/api/workflow-instances/{instance-id}/execute" \
  -H "Content-Type: application/json" \
  -d '{"actionId": "approve"}'
```

## Core Concepts

- **State**: A point in the workflow with id, name, isInitial, isFinal, enabled flags
- **Action**: A transition between states with fromStates and toState
- **Workflow Definition**: Collection of states and actions (must have exactly one initial state)
- **Workflow Instance**: Running instance tracking current state and action history

## Validation Rules

- Workflow definitions must have exactly one initial state
- No duplicate state or action IDs within a workflow
- Actions can only reference existing states
- Instances cannot execute actions from final states
- Actions must be enabled and valid from current state

## Assumptions & Limitations

- **In-memory persistence**: Data is lost when application restarts
- **Single-threaded access**: No concurrent modification protection for same workflow instance
- **Simple validation**: Focus on structural validation rather than complex business rules
- **No authentication**: Concentrate on core workflow functionality

## Project Structure

```
WorkflowEngine/
├── Models/           # Domain entities (State, WorkflowAction, WorkflowDefinition, WorkflowInstance)
├── DTOs/            # Data transfer objects for API
├── Services/        # Business logic (IWorkflowService, WorkflowService)
├── Storage/         # Data persistence (IDataStore, InMemoryDataStore)
└── Program.cs       # API endpoints and configuration
```

## TODO Items (with more time)

- [ ] Database persistence (SQLite/SQL Server)
- [ ] Unit tests with xUnit
- [ ] Concurrent access protection
- [ ] More comprehensive error handling
- [ ] API versioning
- [ ] Performance optimizations
