# API Reference Documentation

## Core APIs

### Editor API

```typescript
interface IEditorAPI {
  // File Operations
  openFile(path: string): Promise<void>;
  saveFile(): Promise<void>;
  closeFile(): void;

  // Content Management
  getValue(): string;
  setValue(content: string): void;
  getSelection(): Selection;
  
  // Editor State
  getLanguage(): string;
  setLanguage(languageId: string): void;
  getPosition(): Position;
}
```

### Workspace API

```typescript
interface IWorkspaceAPI {
  // Workspace Management
  getCurrentWorkspace(): string;
  openWorkspace(path: string): Promise<void>;
  closeWorkspace(): void;

  // File System Operations
  listFiles(): Promise<FileInfo[]>;
  createFile(path: string): Promise<void>;
  deleteFile(path: string): Promise<void>;
}
```

### Extension API

```typescript
interface IExtensionAPI {
  // Extension Lifecycle
  activate(): Promise<void>;
  deactivate(): Promise<void>;
  
  // Extension Capabilities
  registerCommand(command: string, callback: Function): void;
  registerProvider(type: ProviderType, provider: any): void;
}
```

## IPC Communication

### Main Process APIs

```typescript
// Available in Main Process
interface IMainProcessAPI {
  // Window Management
  createWindow(options: WindowOptions): BrowserWindow;
  closeWindow(windowId: number): void;
  
  // Application Lifecycle
  quit(): void;
  restart(): void;
  
  // System Integration
  showOpenDialog(options: OpenDialogOptions): Promise<string[]>;
  showSaveDialog(options: SaveDialogOptions): Promise<string>;
}
```

### Renderer Process APIs

```typescript
// Available in Renderer Process
interface IRendererAPI {
  // IPC Communication
  invoke(channel: string, ...args: any[]): Promise<any>;
  send(channel: string, ...args: any[]): void;
  
  // Window Controls
  minimize(): void;
  maximize(): void;
  close(): void;
}
```

## Events

### Editor Events

```typescript
interface EditorEvents {
  // Content Changes
  onDidChangeContent: Event<TextDocumentChangeEvent>;
  onDidChangeCursorPosition: Event<CursorPositionChangeEvent>;
  
  // File Events
  onDidSave: Event<TextDocument>;
  onDidClose: Event<TextDocument>;
}
```

### Workspace Events

```typescript
interface WorkspaceEvents {
  // File System Events
  onDidCreateFile: Event<FileCreateEvent>;
  onDidDeleteFile: Event<FileDeleteEvent>;
  onDidChangeFile: Event<FileChangeEvent>;
  
  // Workspace Events
  onDidChangeWorkspace: Event<WorkspaceChangeEvent>;
}
```

## Configuration

### Settings API

```typescript
interface ISettingsAPI {
  // Settings Management
  get<T>(key: string): T;
  set<T>(key: string, value: T): Promise<void>;
  observe<T>(key: string, callback: (value: T) => void): Disposable;
}
```

## Extension Development

### Extension Manifest

```json
{
  "name": "extension-name",
  "version": "1.0.0",
  "main": "./dist/extension.js",
  "activationEvents": [
    "onLanguage:javascript",
    "onCommand:extension.command"
  ],
  "contributes": {
    "commands": [],
    "languages": [],
    "themes": []
  }
}
```

### Extension Context

```typescript
interface ExtensionContext {
  // Extension Info
  id: string;
  extensionPath: string;
  
  // Storage
  globalState: Memento;
  workspaceState: Memento;
  
  // Registration
  subscriptions: { dispose(): any }[];
}
```

## Error Handling

### Error Types

```typescript
interface EditorError extends Error {
  code: string;
  severity: 'error' | 'warning' | 'info';
}
```

## Usage Examples

### Basic Editor Operations

```typescript
// Open a file
await editor.openFile('/path/to/file.ts');

// Get content
const content = editor.getValue();

// Save changes
await editor.saveFile();
```

### Extension Development

```typescript
export function activate(context: ExtensionContext) {
  // Register a command
  const disposable = vscode.commands.registerCommand(
    'extension.helloWorld',
    () => {
      vscode.window.showInformationMessage('Hello World!');
    }
  );
  
  context.subscriptions.push(disposable);
}
```

### IPC Communication

```typescript
// In renderer process
await window.electron.invoke('editor:save', {
  path: '/path/to/file.ts',
  content: 'file content'
});

// In main process
ipcMain.handle('editor:save', async (event, args) => {
  const { path, content } = args;
  await fs.writeFile(path, content);
});
```

---

For detailed implementation examples and best practices, refer to the [Architecture Guide](ARCHITECTURE.md) and [Contributing Guide](CONTRIBUTING.md).
