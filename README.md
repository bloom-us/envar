# Type-Safe Environment Variables Manager

A TypeScript library for managing environment variables with strong typing, runtime validation, and flexible transformations.

## Features

- 🔒 **Type-safe**: Full TypeScript support with proper type inference
- ✨ **Runtime validation**: Ensures required environment variables are present
- 🔄 **Flexible transformations**: Built-in parsers for common types (number, boolean, JSON, etc.)
- 🎯 **Default values**: Support for fallback values when variables are not set
- 🚨 **Required flags**: Mark critical environment variables that must be present
- 🎭 **Custom parsers**: Define your own transformation functions

## Installation

```bash
npm install your-package-name
# or
yarn add your-package-name
```

## Usage

### Basic Example

```typescript
import { env } from "your-package-name";

const config = env([
  { k: "PORT", f: parseEnvInt, d: 3000 },
  { k: "DATABASE_URL", required: true },
  { k: "DEBUG", f: parseEnvBoolean, d: false },
]);

// Type-safe access to environment variables
console.log(config.PORT); // number
console.log(config.DATABASE_URL); // string
console.log(config.DEBUG); // boolean
```

### Built-in Formatters

The package includes several built-in formatters for common types:

```typescript
import {
  parseEnvInt,
  parseEnvFloat,
  parseEnvBoolean,
  parseEnvJSON,
  parseEnvString,
  parseEnvEnum,
} from "your-package-name";

const config = env([
  { k: "PORT", f: parseEnvInt },
  { k: "PI", f: parseEnvFloat },
  { k: "ENABLE_FEATURE", f: parseEnvBoolean },
  { k: "API_CONFIG", f: parseEnvJSON },
  { k: "USERNAME", f: parseEnvString },
  { k: "LOG_LEVEL", f: parseEnvEnum(["debug", "info", "warn", "error"]) },
]);
```

### Required Variables

Mark variables as required to ensure they're present:

```typescript
const config = env([
  { k: "API_KEY", required: true }, // Will throw if not set
  { k: "API_URL", required: true, f: parseEnvString },
]);
```

### Default Values

Provide fallback values for optional variables:

```typescript
const config = env([
  { k: "PORT", f: parseEnvInt, d: 3000 },
  { k: "HOST", d: "localhost" },
  { k: "CACHE_TTL", f: parseEnvInt, d: 3600 },
]);
```

### Custom Transformers

Create your own transformation functions:

```typescript
const parseArrayOfInts = (v: string | undefined): number[] | undefined => {
  if (!v) return undefined;
  return v.split(",").map(Number);
};

const config = env([{ k: "ALLOWED_PORTS", f: parseArrayOfInts, d: [80, 443] }]);
```

## API Reference

### `env(keys)`

The main function for configuring environment variables.

Parameters:

- `keys`: An array of environment variable configurations

Each configuration object can include:

- `k`: The environment variable key (required)
- `f`: A transformation function (optional)
- `d`: A default value (optional)
- `required`: Whether the variable is required (optional)

### Built-in Formatters

- `parseEnvInt`: Parse as integer
- `parseEnvFloat`: Parse as float
- `parseEnvBoolean`: Parse as boolean ("true" → true)
- `parseEnvJSON`: Parse as JSON
- `parseEnvString`: Parse as trimmed string
- `parseEnvEnum`: Create an enum validator

## Error Handling

The library throws errors in these cases:

- Required variable is missing
- JSON parsing fails with `parseEnvJSON`
- Enum value is invalid with `parseEnvEnum`

## TypeScript Support

The library is written in TypeScript and provides full type inference:

```typescript
const config = env([{ k: "PORT", f: parseEnvInt, d: 3000 }]);

config.PORT; // TypeScript knows this is a number
```

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
