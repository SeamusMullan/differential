# Development Guide ☉

## Getting Started

### Prerequisites

Ensure you have the following installed:

- **Bun** >= 1.0 - [Installation Guide](https://bun.sh/docs/installation)
- **Rust** >= 1.70 - [Installation Guide](https://www.rust-lang.org/tools/install)
- **Node.js** >= 18 - [Installation Guide](https://nodejs.org/)

#### Platform-Specific Requirements

**macOS:**

```bash
xcode-select --install
```

**Windows:**

- Install [Microsoft C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
- Install [WebView2](https://developer.microsoft.com/en-us/microsoft-edge/webview2/)

**Linux (Ubuntu/Debian):**

```bash
sudo apt update
sudo apt install libwebkit2gtk-4.0-dev \
  build-essential \
  curl \
  wget \
  libssl-dev \
  libgtk-3-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev
```

### Initial Setup

1. **Clone and install dependencies:**

```bash
git clone https://github.com/seamusmullan/differential.git
cd differential
bun install
```

2. **Verify installation:**

```bash
# Check Rust
rustc --version
cargo --version

# Check Bun
bun --version

# Check Tauri CLI
bun run tauri --version
```

## Development Workflow

### Running the App

```bash
# Start development server with hot reload
bun run tauri dev
```

This will:

- Start Vite dev server on port 1420
- Compile Rust code in debug mode
- Launch the Tauri app with dev tools
- Enable hot module replacement (HMR)

### Development Mode Features

- **Hot Reload**: Frontend changes reload instantly
- **Rust Recompile**: Rust changes rebuild automatically
- **DevTools**: Browser developer tools are available
- **Console Output**: See both frontend and backend logs

### Making Changes

#### Frontend Development (TypeScript/React)

1. **File Location**: `src/`
2. **Hot Reload**: Changes apply immediately
3. **Type Checking**: VS Code shows errors in real-time

```typescript
// Example: Adding a new component
// src/components/FileSelector.tsx

import React from 'react';

export const FileSelector: React.FC = () => {
  return <div>File Selector</div>;
};
```

#### Backend Development (Rust)

1. **File Location**: `src-tauri/src/`
2. **Rebuild**: Automatic on save
3. **Commands**: Add new Tauri commands in `lib.rs`

```rust
// Example: Adding a new command
// src-tauri/src/lib.rs

#[tauri::command]
fn my_custom_command(input: String) -> Result<String, String> {
    Ok(format!("Processed: {}", input))
}

// Don't forget to register in the handler:
// .invoke_handler(tauri::generate_handler![my_custom_command])
```

### Building for Production

```bash
# Build optimized release
bun run tauri build
```

Output locations:

- **macOS**: `src-tauri/target/release/bundle/dmg/`
- **Windows**: `src-tauri/target/release/bundle/msi/`
- **Linux**: `src-tauri/target/release/bundle/deb/` (and other formats)

## Project Structure Deep Dive

```text
differential/
├── src/                          # Frontend source
│   ├── components/               # React components
│   │   ├── DiffViewer.tsx       # Main diff display
│   │   ├── FileSelector.tsx     # File picker UI
│   │   └── MonacoEditor.tsx     # Monaco wrapper
│   ├── hooks/                    # Custom React hooks
│   │   ├── useDiff.ts           # Diff computation hook
│   │   └── useFileLoader.ts     # File loading hook
│   ├── types/                    # TypeScript types
│   │   ├── diff.ts              # Diff result types
│   │   └── file.ts              # File metadata types
│   ├── utils/                    # Helper functions
│   │   └── formatting.ts        # Text formatting utils
│   ├── App.tsx                   # Root component
│   ├── main.tsx                  # React entry point
│   └── App.css                   # Global styles
│
├── src-tauri/                    # Backend source
│   ├── src/
│   │   ├── commands/            # Tauri command handlers
│   │   │   ├── diff.rs          # Diff commands
│   │   │   └── file.rs          # File commands
│   │   ├── diff/                # Diff engine
│   │   │   ├── mod.rs           # Module entry
│   │   │   ├── algorithm.rs     # Myers algorithm
│   │   │   └── types.rs         # Rust types
│   │   ├── lib.rs               # Library entry
│   │   └── main.rs              # App entry
│   ├── Cargo.toml               # Rust dependencies
│   ├── tauri.conf.json          # Tauri configuration
│   └── build.rs                 # Build script
│
├── public/                       # Static assets
│   └── tauri.svg
├── docs/                         # Documentation
├── .github/                      # GitHub config
│   └── workflows/               # CI/CD pipelines
├── .vscode/                      # VS Code settings
├── package.json                  # Node dependencies
├── tsconfig.json                 # TypeScript config
└── vite.config.ts               # Vite config
```

## Key Configuration Files

### `package.json`

Defines frontend dependencies and scripts:

```json
{
  "scripts": {
    "dev": "vite",              // Frontend dev server
    "build": "tsc && vite build", // Build frontend
    "tauri": "tauri"            // Tauri CLI
  }
}
```

### `src-tauri/Cargo.toml`

Defines Rust dependencies:

```toml
[dependencies]
tauri = { version = "2", features = [] }
serde = { version = "1", features = ["derive"] }
similar = "2.3"  # Diff algorithm library
```

### `src-tauri/tauri.conf.json`

Tauri app configuration:

```json
{
  "build": {
    "beforeDevCommand": "bun run dev",
    "beforeBuildCommand": "bun run build",
    "devUrl": "http://localhost:1420",
    "frontendDist": "../dist"
  },
  "app": {
    "windows": [{
      "title": "differential",
      "width": 1200,
      "height": 800
    }]
  }
}
```

## Common Development Tasks

### Adding a New Frontend Component

1. Create component file in `src/components/`
2. Import and use in `App.tsx`
3. Add types in `src/types/` if needed

### Adding a New Rust Command

1. Define command function in `src-tauri/src/lib.rs` or separate module
2. Add `#[tauri::command]` attribute
3. Register in `invoke_handler!` macro
4. Call from frontend using `invoke('command_name')`

### Adding Dependencies

**Frontend:**

```bash
bun add package-name
bun add -d package-name  # Dev dependency
```

**Backend:**

```bash
cd src-tauri
cargo add package-name
```

### Running Tests

**Rust tests:**

```bash
cd src-tauri
cargo test
```

**TypeScript tests (future):**

```bash
bun test
```

### Linting and Formatting

**TypeScript:**

```bash
bun run tsc --noEmit  # Type check
```

**Rust:**

```bash
cd src-tauri
cargo clippy  # Lint
cargo fmt     # Format
```

## Debugging

### Frontend Debugging

1. Open DevTools (Cmd+Option+I on macOS)
2. Set breakpoints in browser
3. Use `console.log()` for quick debugging
4. Check Network tab for IPC calls

### Backend Debugging

1. Add `println!` statements in Rust code
2. View output in terminal running `tauri dev`
3. Use VS Code debugger (see `.vscode/launch.json`)
4. Add `#[cfg(debug_assertions)]` for debug-only code

```rust
#[cfg(debug_assertions)]
println!("Debug: Processing diff for {} lines", lines.len());
```

### Common Issues

**Issue: Port 1420 already in use**

```bash
# Kill the process using the port
lsof -ti:1420 | xargs kill -9
```

**Issue: Rust compilation errors**

```bash
# Clean and rebuild
cd src-tauri
cargo clean
cargo build
```

**Issue: Frontend not connecting to backend**

- Check Tauri dev server is running
- Verify `tauri.conf.json` has correct `devUrl`
- Look for CORS or CSP errors in console

## Performance Profiling

### Frontend Performance

```typescript
// Use React DevTools Profiler
import { Profiler } from 'react';

<Profiler id="DiffViewer" onRender={callback}>
  <DiffViewer />
</Profiler>
```

### Backend Performance

```rust
// Use criterion for benchmarks
use std::time::Instant;

let start = Instant::now();
compute_diff(&left, &right);
println!("Diff took: {:?}", start.elapsed());
```

## Continuous Integration

CI runs on every push and PR via GitHub Actions:

- TypeScript type checking
- Rust compilation and tests
- Linting checks
- Build verification

See `.github/workflows/` for configuration.

## Release Process

1. Update version numbers:
   - `package.json`
   - `src-tauri/Cargo.toml`
   - `src-tauri/tauri.conf.json`

2. Update CHANGELOG.md

3. Create git tag:

```bash
git tag v1.0.0
git push origin v1.0.0
```

4. Build release:

```bash
bun run tauri build
```

5. Upload artifacts to GitHub Releases

## Tips and Best Practices

1. **Keep components small** - Single responsibility principle
2. **Type everything** - Use TypeScript strict mode
3. **Handle errors** - Never use `.unwrap()` in production Rust code
4. **Test edge cases** - Empty files, huge files, special characters
5. **Document public APIs** - Add JSDoc and Rust doc comments
6. **Use meaningful names** - `leftFileContent` not `data1`
7. **Optimize later** - Make it work, then make it fast

## Getting Help

- Check [Architecture docs](./architecture.md)
- Read [API Reference](./api.md)
- Search existing issues
- Ask in discussions

Happy coding!
