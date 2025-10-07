# API Reference ☉

## Tauri Commands (Rust → TypeScript)

### `compute_diff`

Computes the difference between two text strings using the Myers diff algorithm.

**Rust Signature:**

```rust
#[tauri::command]
fn compute_diff(left: String, right: String) -> Result<DiffResult, String>
```

**TypeScript Usage:**

```typescript
import { invoke } from '@tauri-apps/api/core';

const result = await invoke<DiffResult>('compute_diff', {
  left: 'Hello world',
  right: 'Hello Rust world'
});
```

**Parameters:**

- `left` (string): Original text content
- `right` (string): Modified text content

**Returns:**

```typescript
interface DiffResult {
  changes: DiffChange[];
  stats: DiffStats;
}

interface DiffChange {
  type: 'insert' | 'delete' | 'equal';
  value: string;
  oldLine?: number;
  newLine?: number;
}

interface DiffStats {
  additions: number;
  deletions: number;
  unchanged: number;
}
```

**Errors:**

- Returns error string if diff computation fails

**Example:**

```typescript
try {
  const diff = await invoke<DiffResult>('compute_diff', {
    left: fileA,
    right: fileB
  });
  console.log(`Changes: +${diff.stats.additions} -${diff.stats.deletions}`);
} catch (error) {
  console.error('Diff failed:', error);
}
```

---

### `read_file`

Reads the contents of a file from the file system.

**Rust Signature:**

```rust
#[tauri::command]
fn read_file(path: String) -> Result<String, String>
```

**TypeScript Usage:**

```typescript
const content = await invoke<string>('read_file', {
  path: '/path/to/file.txt'
});
```

**Parameters:**

- `path` (string): Absolute path to the file

**Returns:**

- File contents as UTF-8 string

**Errors:**

- File not found
- Permission denied
- Invalid UTF-8 encoding
- IO errors

**Example:**

```typescript
try {
  const content = await invoke<string>('read_file', {
    path: selectedPath
  });
  setFileContent(content);
} catch (error) {
  alert(`Failed to read file: ${error}`);
}
```

---

### `select_file`

Opens a native file picker dialog for the user to select a file.

**Rust Signature:**

```rust
#[tauri::command]
fn select_file() -> Result<Option<String>, String>
```

**TypeScript Usage:**

```typescript
const path = await invoke<string | null>('select_file');
```

**Parameters:**

- None

**Returns:**

- File path as string, or `null` if user cancelled

**Errors:**

- Dialog failed to open
- Platform-specific errors

**Example:**

```typescript
const path = await invoke<string | null>('select_file');
if (path) {
  const content = await invoke<string>('read_file', { path });
  setLeftFile({ path, content });
}
```

---

## React Hooks

### `useDiff`

Custom hook for computing diffs between two text strings.

**Signature:**

```typescript
function useDiff(
  left: string,
  right: string
): {
  result: DiffResult | null;
  loading: boolean;
  error: string | null;
}
```

**Parameters:**

- `left` (string): Original text
- `right` (string): Modified text

**Returns:**

- `result`: Diff computation result
- `loading`: True while computing
- `error`: Error message if computation failed

**Example:**

```typescript
function DiffViewer({ leftText, rightText }: Props) {
  const { result, loading, error } = useDiff(leftText, rightText);

  if (loading) return <div>Computing diff...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!result) return null;

  return <DiffDisplay result={result} />;
}
```

---

### `useFileLoader`

Custom hook for loading file contents with file picker.

**Signature:**

```typescript
function useFileLoader(): {
  loadFile: () => Promise<FileData | null>;
  loading: boolean;
  error: string | null;
}
```

**Returns:**

- `loadFile`: Function to open file picker and load file
- `loading`: True while loading file
- `error`: Error message if loading failed

**Example:**

```typescript
function FileSelector() {
  const { loadFile, loading, error } = useFileLoader();

  const handleClick = async () => {
    const file = await loadFile();
    if (file) {
      console.log('Loaded:', file.path);
    }
  };

  return (
    <button onClick={handleClick} disabled={loading}>
      {loading ? 'Loading...' : 'Open File'}
    </button>
  );
}
```

---

## TypeScript Types

### Core Types

```typescript
// File metadata and content
interface FileData {
  path: string;
  content: string;
  name: string;
  size: number;
}

// Diff computation result
interface DiffResult {
  changes: DiffChange[];
  stats: DiffStats;
}

// Individual diff change
interface DiffChange {
  type: 'insert' | 'delete' | 'equal';
  value: string;
  oldLine?: number;
  newLine?: number;
}

// Statistics about changes
interface DiffStats {
  additions: number;
  deletions: number;
  unchanged: number;
  totalLines: number;
}

// Monaco editor configuration
interface EditorConfig {
  theme: 'vs-dark' | 'vs-light';
  fontSize: number;
  lineNumbers: 'on' | 'off';
  minimap: boolean;
  renderSideBySide: boolean;
}
```

---

## Component Props

### `<DiffViewer>`

Main diff display component.

```typescript
interface DiffViewerProps {
  leftFile: FileData | null;
  rightFile: FileData | null;
  onFileChange?: (side: 'left' | 'right', file: FileData) => void;
}

function DiffViewer(props: DiffViewerProps): JSX.Element;
```

---

### `<FileSelector>`

File picker button component.

```typescript
interface FileSelectorProps {
  onFileSelected: (file: FileData) => void;
  label?: string;
  disabled?: boolean;
}

function FileSelector(props: FileSelectorProps): JSX.Element;
```

---

### `<MonacoEditor>`

Monaco editor wrapper component.

```typescript
interface MonacoEditorProps {
  original: string;
  modified: string;
  language?: string;
  theme?: 'vs-dark' | 'vs-light';
  onMount?: (editor: monaco.editor.IStandaloneDiffEditor) => void;
}

function MonacoEditor(props: MonacoEditorProps): JSX.Element;
```

---

## Rust Types

### Core Types

```rust
// Result of diff computation
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct DiffResult {
    pub changes: Vec<DiffChange>,
    pub stats: DiffStats,
}

// Individual change in diff
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct DiffChange {
    #[serde(rename = "type")]
    pub change_type: ChangeType,
    pub value: String,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub old_line: Option<usize>,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub new_line: Option<usize>,
}

// Type of change
#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(rename_all = "lowercase")]
pub enum ChangeType {
    Insert,
    Delete,
    Equal,
}

// Statistics about diff
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct DiffStats {
    pub additions: usize,
    pub deletions: usize,
    pub unchanged: usize,
    pub total_lines: usize,
}
```

---

## Error Handling

### Frontend Errors

```typescript
// Error wrapper type
type Result<T> = { ok: true; value: T } | { ok: false; error: string };

// Usage
async function safeInvoke<T>(
  command: string,
  args?: Record<string, unknown>
): Promise<Result<T>> {
  try {
    const value = await invoke<T>(command, args);
    return { ok: true, value };
  } catch (error) {
    return { ok: false, error: String(error) };
  }
}
```

### Backend Errors

```rust
// Custom error type
#[derive(Debug, thiserror::Error)]
pub enum DiffError {
    #[error("Failed to read file: {0}")]
    IoError(#[from] std::io::Error),
    
    #[error("Invalid UTF-8 encoding")]
    Utf8Error(#[from] std::string::FromUtf8Error),
    
    #[error("Diff computation failed: {0}")]
    ComputationError(String),
}

// Convert to string for Tauri
impl From<DiffError> for String {
    fn from(err: DiffError) -> String {
        err.to_string()
    }
}
```

---

## Configuration

### Tauri Window Configuration

```json
{
  "app": {
    "windows": [{
      "title": "Differential",
      "width": 1200,
      "height": 800,
      "minWidth": 800,
      "minHeight": 600,
      "resizable": true,
      "fullscreen": false,
      "decorations": true,
      "alwaysOnTop": false
    }]
  }
}
```

### Monaco Editor Configuration

```typescript
const editorOptions: monaco.editor.IStandaloneDiffEditorConstructionOptions = {
  enableSplitViewResizing: true,
  renderSideBySide: true,
  originalEditable: false,
  readOnly: true,
  minimap: { enabled: true },
  scrollBeyondLastLine: false,
  fontSize: 14,
  lineNumbers: 'on',
  glyphMargin: false,
  folding: false,
};
```

---

## Examples

### Complete File Comparison Flow

```typescript
import { invoke } from '@tauri-apps/api/core';

async function compareFiles() {
  // Select first file
  const leftPath = await invoke<string | null>('select_file');
  if (!leftPath) return;

  // Select second file
  const rightPath = await invoke<string | null>('select_file');
  if (!rightPath) return;

  // Read both files
  const [leftContent, rightContent] = await Promise.all([
    invoke<string>('read_file', { path: leftPath }),
    invoke<string>('read_file', { path: rightPath })
  ]);

  // Compute diff
  const result = await invoke<DiffResult>('compute_diff', {
    left: leftContent,
    right: rightContent
  });

  // Display results
  console.log(`Found ${result.changes.length} changes`);
  console.log(`+${result.stats.additions} -${result.stats.deletions}`);
}
```

---

## Performance Guidelines

- **Large Files**: Use streaming for files >10MB
- **Debouncing**: Debounce diff computation during live editing
- **Memoization**: Cache diff results for unchanged inputs
- **Lazy Loading**: Only render visible diff regions

---

For more examples, see the [Development Guide](./development.md).
