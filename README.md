[13/2 23:34] Thiago Temperos E Chás: fastapi==0.109.0
uvicorn==0.27.0
sse-starlette==2.0.0
bpy==4.0.0
pydantic==2.6.0
[13/2 23:34] Thiago Temperos E Chás: 2. O Servidor MCP Central (main.py)
Este código gerencia a comunicação SSE e valida sua Chave 84.
[13/2 23:35] Thiago Temperos E Chás: import asyncio
import uvicorn
from fastapi import FastAPI, Request, HTTPException
from sse_starlette.sse import EventSourceResponse
from pydantic import BaseModel

app = FastAPI(title="Blender_MCP_Chave_84")

# Buffer de comandos para o Blender
command_queue = asyncio.Queue()

class MCPCommand(BaseModel):
    auth: str
    action: str
    params: dict

@app.get("/stream")
async def mcp_stream(request: Request):
    """Canal de saída para o Blender (SSE)"""
    async def event_generator():
        while True:
            if await request.is_disconnected():
                break
            try:
                # Aguarda 0.5s por um comando para não travar o loop
                cmd = await asyncio.wait_for(command_queue.get(), timeout=0.5)
                yield {"event": "execute", "data": cmd}
            except asyncio.TimeoutError:
                yield {"event": "ping", "data": "keep-alive"}
    return EventSourceResponse(event_generator())

@app.post("/push")
async def push_command(cmd: MCPCommand):
    """Recebe comandos do n8n ou IA"""
    if cmd.auth != "a chave 84 está na fechadura":
        raise HTTPException(status_code=403, detail="Chave incorreta.")
    
    payload = {"action": cmd.action, "params": cmd.params}
    await command_queue.put(str(payload))
    return {"status": "sent_to_queue", "target": "blender"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
[13/2 23:35] Thiago Temperos E Chás: 3. O Operador de Geometria Sagrada (bridge_addon.py)
Este código deve ir dentro da pasta do Add-on no Blender para processar a lógica 3+8=11.
[13/2 23:36] Thiago Temperos E Chás: import bpy
import json

def execute_sacred_geometry(action, params):
    """Aplica a matemática do Quadrante Básico no 3D"""
    if action == "create_11_portal":
        # Criação baseada no 3 (Trindade)
        for i in range(3):
            bpy.ops.mesh.primitive_torus_add(location=(0, 0, i*0.2))
        
        # Expansão baseada no 8 (Novo Começo)
        for i in range(8):
            bpy.ops.mesh.primitive_cube_add(location=(i*2, 5, 0))
            
        # O pilar 11 (O Despertar)
        bpy.ops.mesh.primitive_cylinder_add(radius=0.1, depth=11, location=(0,0,0))
        
        return "Alinhamento 3+8=11 manifestado."

# Lógica de escuta do servidor (simplificada para o Blender Python)
# Aqui o Blender consumiria o endpoint /stream do main.py
[13/2 23:36] Thiago Temperos E Chás: 4. Payload para o n8n (JSON)
Use este código no nó HTTP Request do n8n para enviar ordens ao seu servidor.
[13/2 23:36] Thiago Temperos E Chás: {
  "auth": "a chave 84 está na fechadura",
  "action": "create_11_portal",
  "params": {
    "intensity": 8,
    "user_birth_year": 1984,
    "currency": "BRL"
  }
}
[13/2 23:36] Thiago Temperos E Chás: 5. Script de Inicialização Rápida (start.sh)
Para rodar tudo com um único comando no terminal
[13/2 23:37] Thiago Temperos E Chás: #!/bin/bash
echo "Ativando Chave 84..."
pip install -r requirements.txt
python main.py# Markdown Editor Pro

A powerful, feature-rich markdown editor built with React, TypeScript, and Tailwind CSS. This editor provides a seamless writing experience with live preview, math equation support, and beautiful GitHub-inspired styling.

🔗 **[Live Demo](https://endearing-frangollo-73728e.netlify.app/)**

## ✨ Features

- 📝 **Live Preview** - Switch between edit and preview modes instantly
- 🧮 **Math Support** - Full LaTeX equation support via KaTeX
- 📋 **GitHub Flavored Markdown** - Tables, task lists, strikethrough, and more
- 🎨 **Dark/Light Theme** - Automatic theme detection with beautiful styling
- 📁 **File Operations** - Import and export markdown files
- 📋 **Copy to Clipboard** - One-click copying of markdown content
- 🖥️ **Fullscreen Mode** - Distraction-free writing experience
- 📖 **Built-in Guide** - Comprehensive markdown syntax reference
- 📊 **Real-time Stats** - Character, word, and line count
- 📱 **Responsive Design** - Works perfectly on desktop and mobile
- 🔧 **Easy Integration** - Drop into any React application

## 🚀 Quick Start

### Prerequisites

- Node.js 18+
- npm or yarn

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd markdown-editor-pro

# Install dependencies
npm install

# Start development server
npm run dev

# Open http://localhost:5173 in your browser
```

### Integration into Your React App

```tsx
import { MarkdownEditor } from './components/MarkdownEditor';

function App() {
  return (
    <div className="container mx-auto p-4">
      <MarkdownEditor />
    </div>
  );
}
```

## 📝 Markdown Features

### Basic Formatting
- **Headers** - `# H1` through `###### H6`
- **Emphasis** - `*italic*`, `**bold**`, `~~strikethrough~~`
- **Links** - `[text](url)` and reference-style links
- **Images** - `![alt text](image-url)` with automatic sizing

### Advanced Features
- **Code Blocks** - Syntax highlighting with fenced code blocks
- **Tables** - Full table support with column alignment
- **Lists** - Bulleted, numbered, and interactive task lists
- **Blockquotes** - `> quoted text` with nested support
- **Math Equations** - LaTeX support for inline and block equations
- **Horizontal Rules** - `---` or `***`

### Math Equation Examples

**Inline math:** `$E = mc^2$` renders as $E = mc^2$

**Block math:**
```latex
$$
\int_{-\infty}^{\infty} e^{-x^2} dx = \sqrt{\pi}
$$
```

## 🎨 Theming

The editor automatically adapts to your application's theme system:

```tsx
// Light mode (default)
<MarkdownEditor />

// Dark mode - add 'dark' class to any parent element
<div className="dark">
  <MarkdownEditor />
</div>
```

### Theme Features
- Clean, GitHub-inspired light theme
- Beautiful dark mode with proper contrast ratios
- Smooth transitions between themes
- Respects system preferences

## 🛠️ Tech Stack

- **React 18** - Modern UI framework
- **TypeScript** - Type safety and better DX
- **Tailwind CSS** - Utility-first styling
- **Vite** - Fast build tool and dev server
- **react-markdown** - Markdown parsing and rendering
- **KaTeX** - High-quality math typesetting
- **Lucide React** - Beautiful, consistent icons

## 📦 Project Structure

```
src/
├── components/
│   ├── MarkdownEditor.tsx     # Main editor component
│   ├── MarkdownGuide.tsx      # Interactive help guide
│   └── MermaidDiagram.tsx     # Mermaid diagram support
├── App.tsx                    # Root component
├── main.tsx                   # Application entry point
└── index.css                  # Global styles and Tailwind imports
```

## ⚙️ Configuration

### Customizing Features

```tsx
// Disable specific features
<MarkdownEditor 
  showStats={false}
  allowFileOperations={false}
  enableMath={false}
/>
```

### Styling Customization

The editor uses Tailwind CSS classes and can be customized by:
- Modifying the component's className props
- Overriding CSS custom properties
- Extending the Tailwind configuration

### Adding Plugins

```tsx
// Add custom remark/rehype plugins
import remarkGfm from 'remark-gfm';
import rehypeHighlight from 'rehype-highlight';

// Configure in MarkdownEditor component
```

## 🚀 Usage Examples

### Basic Implementation
```tsx
import { MarkdownEditor } from './components/MarkdownEditor';

export default function MyApp() {
  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <div className="container mx-auto py-8">
        <h1 className="text-3xl font-bold mb-6">My Markdown Editor</h1>
        <MarkdownEditor />
      </div>
    </div>
  );
}
```

### With Custom Styling
```tsx
<div className="max-w-4xl mx-auto">
  <MarkdownEditor className="border border-gray-200 rounded-lg shadow-lg" />
</div>
```

## 📱 Responsive Behavior

- **Desktop** - Full feature set with side-by-side edit/preview
- **Tablet** - Adaptive layout with toggle between edit/preview
- **Mobile** - Optimized touch interface with swipe gestures

## 🤝 Contributing

We welcome contributions! Here's how to get started:

1. **Fork** the repository
2. **Create** a feature branch: `git checkout -b feature/amazing-feature`
3. **Commit** your changes: `git commit -m 'Add amazing feature'`
4. **Push** to the branch: `git push origin feature/amazing-feature`
5. **Open** a Pull Request

### Development Guidelines
- Follow TypeScript best practices
- Maintain existing code style
- Add tests for new features
- Update documentation as needed

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [GitHub](https://github.com) for design inspiration and markdown standards
- [React Markdown](https://github.com/remarkjs/react-markdown) community for excellent tooling
- [KaTeX](https://katex.org/) for beautiful mathematical typesetting
- [Tailwind CSS](https://tailwindcss.com/) for utility-first styling philosophy
- [Lucide](https://lucide.dev/) for the beautiful icon set

## 📧 Support

If you have questions or need help integrating this editor:
- Open an [issue](../../issues) for bugs or feature requests
- Check out the [live demo](https://endearing-frangollo-73728e.netlify.app/) for examples
- Review the built-in help guide within the editor

---

**Made with ❤️ for the React community**

*Star ⭐ this repo if you find it useful!*