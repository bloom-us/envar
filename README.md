# Type-Safe Environment Variables

A TypeScript library for managing environment variables with strong typing, runtime validation, and flexible parsing.

## Features

- Full TypeScript support with precise type inference
- Runtime validation for required variables
- Built-in parsers for common types (numbers, booleans, JSON, enums)
- Custom parser support
- Default values
- Zero dependencies

## Installation

```bash
npm install type-safe-env
```

## Basic Usage

```typescript
import { env } from "type-safe-env";

const config = env([
  ["PORT", { parser: parseEnvInt, default: 3000 }],
  ["NODE_ENV", { required: true }],
  ["API_KEY", { required: true }],
  ["DEBUG", { parser: parseEnvBoolean, default: false }],
]);

// TypeScript knows the exact types:
// config.PORT: number
// config.NODE_ENV: string
// config.API_KEY: string
// config.DEBUG: boolean
```

## Built-in Parsers

The library includes several built-in parsers for common types:

```typescript
import {
  parseEnvInt,
  parseEnvFloat,
  parseEnvBoolean,
  parseEnvJSON,
  parseEnvString,
  parseEnvEnum,
} from "type-safe-env";

const config = env([
  ["PORT", { parser: parseEnvInt }],
  ["RATE_LIMIT", { parser: parseEnvFloat }],
  ["ENABLE_CACHE", { parser: parseEnvBoolean }],
  ["API_CONFIG", { parser: parseEnvJSON }],
  ["NAME", { parser: parseEnvString }],
  ["LOG_LEVEL", { parser: parseEnvEnum(["debug", "info", "warn", "error"]) }],
]);
```

### Parser Behaviors

- `parseEnvInt`: Converts strings to integers using `parseInt`
- `parseEnvFloat`: Converts strings to floating-point numbers using `parseFloat`
- `parseEnvBoolean`: Converts "true" (case-insensitive) to `true`, otherwise `undefined`
- `parseEnvJSON`: Parses JSON strings into objects, throws on invalid JSON
- `parseEnvString`: Trims strings and returns `undefined` if empty
- `parseEnvEnum`: Creates a parser that validates against a set of allowed values

## Advanced Usage

### Custom Parsers

You can create custom parsers for specific needs:

```typescript
const parsePort = (v: string | undefined) => {
  const port = parseEnvInt(v);
  if (port && (port < 1 || port > 65535)) {
    throw new Error("Port must be between 1 and 65535");
  }
  return port;
};

const config = env([["PORT", { parser: parsePort, required: true }]]);
```

### Required Variables

When a variable is marked as required:

- It must be present in the environment
- Its parsed value cannot be undefined
- TypeScript will remove `undefined` from the type

```typescript
const config = env([
  ["API_KEY", { required: true }], // Will throw if API_KEY is not set
]);
```

### Default Values

Default values are used when:

- The environment variable is not set
- The parser returns undefined

```typescript
const config = env([
  ["PORT", { parser: parseEnvInt, default: 3000 }],
  ["API_URL", { default: "https://api.example.com" }],
]);
```

### Type Inference

The library automatically infers precise types based on your configuration:

```typescript
const config = env([
  ["PORT", { parser: parseEnvInt, required: true }],
  ["API_KEY", { required: true }],
  ["DEBUG", { parser: parseEnvBoolean, default: false }],
]);

// TypeScript infers:
// {
//   readonly PORT: number;
//   readonly API_KEY: string;
//   readonly DEBUG: boolean;
// }
```

## Error Handling

The library throws errors in these cases:

- A required variable is missing or undefined
- A parser throws an error (e.g., invalid JSON)
- An enum value doesn't match allowed options

Example error messages:

```
Missing required environment variable: API_KEY
Invalid JSON value: {invalid-json}
Expected one of: debug, info, warn, error
```

## Best Practices

1. Define all environment variables in one place
2. Use TypeScript to catch configuration errors early
3. Add validation in custom parsers when needed
4. Provide sensible defaults for optional values
5. Mark security-critical variables as required

## License

MIT
