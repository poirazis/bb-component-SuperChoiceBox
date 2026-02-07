# Super Choice Box

A flexible choice selection component for Budibase that supports multiple layouts and data sources.

## Features

- **Multiple Layouts**: Vertical, horizontal, and grid orientations
- **Data Sources**: Custom options, data providers, or static arrays
- **Grid Columns**: Configurable number of columns for grid layout
- **Ellipsis Trimming**: Long option labels are trimmed with ellipses
- **Reactive Updates**: Automatically selects first option when data loads

## How to build

### Development Build

For development with automatic rebuilds:

```bash
bun run watch
```

### Production Build

```bash
bun run build
```

Output will be in `dist/`:

- `plugin.min.js` - Your minified component
- `schema.json` - Component metadata and settings
- `bb-component-YourName-1.0.0.tar.gz` - Ready to deploy to Budibase

## Resources

- [Budibase Documentation](https://github.com/Budibase/budibase)
- [Svelte 5 Docs](https://svelte.dev)
- [Custom Components Guide](https://docs.budibase.com)
- [Schema Configuration](https://github.com/Budibase/budibase/tree/master/packages/backend-core)
