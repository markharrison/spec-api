# Data Model: Color Management API

## Core Entities

### Color Entity
**Purpose**: Represents a color entry with unique identifier, display name, and color value.

**Attributes**:
- **Id** (string, required): Unique identifier for the color (e.g., "red-primary", "blue-500")
  - Validation: Non-empty, alphanumeric with hyphens, max 50 characters
  - Primary key for storage operations
- **Name** (string, required): Human-readable display name (e.g., "Primary Red", "Ocean Blue")
  - Validation: Non-empty, max 100 characters, printable characters only
- **HexValue** (string, required): Hexadecimal color representation (e.g., "#FF0000", "#3498DB")
  - Validation: Valid hex format (#RRGGBB or #RGB), case-insensitive
- **CreatedAt** (DateTime, auto-generated): Timestamp when color was added
- **UpdatedAt** (DateTime, auto-updated): Timestamp of last modification

**Validation Rules**:
- Id must be unique across the collection
- HexValue must be valid hexadecimal color format
- All required fields must be present and non-empty
- Id cannot be changed after creation (immutable identifier)

**Example**:
```json
{
  "id": "primary-red",
  "name": "Primary Red",
  "hexValue": "#FF0000",
  "createdAt": "2025-09-29T10:30:00Z",
  "updatedAt": "2025-09-29T10:30:00Z"
}
```

### Color Collection
**Purpose**: Container for managing the complete set of available colors.

**Properties**:
- **Colors**: Dictionary/Map of Color entities keyed by Id
- **Count**: Total number of colors in the collection
- **LastModified**: Timestamp of most recent color addition/modification

**Operations**:
- **GetAll()**: Retrieve all colors as list/array
- **GetById(id)**: Retrieve specific color by identifier
- **GetRandom()**: Return randomly selected color from collection
- **AddOrUpdate(color)**: Store color (create if new, update if exists)
- **Remove(id)**: Delete color by identifier (future enhancement)
- **Exists(id)**: Check if color with given ID exists

**Business Rules**:
- Empty collection is valid state
- Random selection from empty collection returns error
- Color IDs are case-sensitive for exact matching
- Updates preserve original creation timestamp
- Collection operations are thread-safe for concurrent access

## Data Transfer Objects (DTOs)

### ColorRequest DTO
Used for SetColor API endpoint input:
```json
{
  "id": "string (required)",
  "name": "string (required)", 
  "hexValue": "string (required)"
}
```

### ColorResponse DTO
Used for API endpoint responses:
```json
{
  "id": "string",
  "name": "string",
  "hexValue": "string",
  "createdAt": "string (ISO 8601)",
  "updatedAt": "string (ISO 8601)"
}
```

### ErrorResponse DTO
Standard error format for all endpoints:
```json
{
  "error": "string (error type)",
  "message": "string (human-readable description)",
  "correlationId": "string (request tracking)"
}
```

## Storage Implementation

### In-Memory Repository
**Storage Mechanism**: ConcurrentDictionary<string, Color>
- Thread-safe for concurrent read/write operations
- O(1) lookup performance by color ID
- Memory-efficient for typical color collection sizes

**Key Design Decisions**:
- String keys (color IDs) for fast lookups
- Value type storage for minimal memory overhead
- No external dependencies or configuration required
- Data persists only during application lifetime

**Capacity Considerations**:
- Designed for hundreds to low thousands of colors
- Memory usage: ~100-200 bytes per color entry
- No artificial limits imposed (constrained by available memory)

## Data Flow Patterns

### Read Operations (GetAllColors, GetColor, GetRandomColor)
```
HTTP Request → Controller → Service → Repository → In-Memory Storage → Response
```

### Write Operations (SetColor)
```
HTTP Request → Validation → Controller → Service → Repository → In-Memory Storage → Response
```

### Error Scenarios
```
Invalid Input → Validation Error → Error Response
Missing Color → NotFound Error → 404 Response
Storage Error → Server Error → 500 Response
```

## Validation Strategy

### Input Validation Layers
1. **Model Binding Validation**: Automatic validation of required fields and basic format
2. **Custom Validation Attributes**: Hex color format validation, ID format rules
3. **Business Logic Validation**: Uniqueness checks, business rule enforcement
4. **Sanitization**: Input cleaning and normalization (trim whitespace, normalize hex format)

### Error Response Consistency
- All validation errors return 400 Bad Request
- Missing resources return 404 Not Found
- Server errors return 500 Internal Server Error
- Consistent error message format across all endpoints

## Performance Characteristics

### Expected Operations/Second
- **Read Operations**: 10,000+ ops/sec (in-memory lookup)
- **Write Operations**: 5,000+ ops/sec (concurrent dictionary updates)
- **Random Selection**: 1,000+ ops/sec (array iteration required)

### Memory Usage Patterns
- **Base Memory**: ~1-2 MB for empty application
- **Per Color**: ~100-200 bytes (object overhead + string storage)
- **1000 Colors**: ~200 KB additional memory usage
- **Growth Pattern**: Linear with number of colors stored

### Response Time Targets
- **Single Color Lookup**: <5ms average
- **All Colors Retrieval**: <10ms for collections under 1000 colors
- **Random Color Selection**: <15ms average
- **Color Creation/Update**: <10ms average
- **Total HTTP Response**: <200ms (95th percentile) including network overhead