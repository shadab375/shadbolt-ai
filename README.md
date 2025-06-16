# ShadBolt AI - Full-Stack Web App Generator

ShadBolt AI is a powerful AI-driven web application that allows users to generate complete full-stack web applications through natural language prompts. Inspired by Bolt.new, this platform enables developers and non-developers alike to create, edit, and deploy web applications using conversational AI.

## 🚀 Features

- **AI-Powered Code Generation**: Generate complete React applications with just a text prompt
- **Real-time Code Preview**: See your applications come to life with integrated Sandpack preview
- **Interactive Chat Interface**: Communicate with AI to iterate and improve your applications
- **Google Authentication**: Secure login with Google OAuth integration
- **Token-Based System**: Usage tracking with flexible pricing tiers
- **Workspace Management**: Save and manage multiple projects
- **Responsive Design**: Modern, mobile-friendly interface built with Tailwind CSS
- **File Management**: Complete file structure generation and editing capabilities

## 🛠️ Technology Stack

- **Frontend**: Next.js 15, React 18, Tailwind CSS
- **Backend**: Convex (Real-time database and backend)
- **AI Integration**: Google Generative AI (Gemini)
- **Authentication**: Google OAuth
- **Code Preview**: Sandpack (CodeSandbox)
- **UI Components**: Radix UI, Lucide React
- **Styling**: Tailwind CSS with custom themes
- **Payment**: PayPal integration for premium features

## 📦 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/shadab375/shadbolt-ai.git
   cd shadbolt-ai
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   Create a `.env.local` file in the root directory and add:
   ```env
   NEXT_PUBLIC_CONVEX_URL=your_convex_url
   NEXT_PUBLIC_GOOGLE_CLIENT_ID=your_google_client_id
   GOOGLE_GENERATIVE_AI_API_KEY=your_gemini_api_key
   NEXT_PUBLIC_PAYPAL_CLIENT_ID=your_paypal_client_id
   ```

4. **Set up Convex database**
   ```bash
   npx convex dev
   ```

5. **Run the development server**
   ```bash
   npm run dev
   ```

6. **Open your browser**
   Navigate to `http://localhost:3000`

## 🎯 Usage

### Getting Started

1. **Sign In**: Use Google authentication to create an account
2. **Create Project**: Enter a prompt describing what you want to build
3. **Generate Code**: The AI will generate a complete React application
4. **Preview**: View your application in the integrated code preview
5. **Iterate**: Chat with the AI to make modifications and improvements
6. **Save**: Your workspace is automatically saved for future access

### Example Prompts

- "Create a ToDo App in React"
- "Build a Budget Tracker with charts"
- "Create a Gym Management Dashboard"
- "Build a Quiz App about History"
- "Create a Login/Signup screen"

### Token System

The platform uses a token-based system for usage tracking:
- **50K tokens**: $4.99 (Basic)
- **120K tokens**: $9.99 (Starter)
- **2.5M tokens**: $19.99 (Pro)
- **Unlimited**: $49.99 (License)

## 🏗️ Architecture

### Frontend Structure
```
app/
├── (main)/
│   ├── workspace/[id]/     # Individual workspace pages
│   └── pricing/            # Pricing page
├── api/                    # API routes
│   └── ai-chat/           # AI chat endpoint
├── components/
│   ├── ui/                # Reusable UI components
│   └── custom/            # Custom components
├── context/               # React context providers
├── data/                  # Configuration and constants
├── lib/                   # Utility functions
└── hooks/                 # Custom React hooks
```

### Backend Structure (Convex)
```
convex/
├── schema.js              # Database schema
├── users.js               # User management functions
└── workspace.js           # Workspace management functions
```

### Key Components

- **Hero.jsx**: Landing page with prompt input
- **ChatView.jsx**: AI chat interface
- **CodeView.jsx**: Code editor and preview
- **SandpackPreviewClient.jsx**: Live code preview
- **WorkspaceHistory.jsx**: Project management
- **PricingModel.jsx**: Subscription management

## 🔧 Configuration

### Tailwind CSS
The project uses a custom Tailwind configuration with:
- Custom colors and themes
- Scrollbar utilities
- Animation support
- Responsive design utilities

### Convex Schema
The database includes:
- **Users**: Authentication and token management
- **Workspaces**: Project data and message history

## 🚀 Deployment

### Prerequisites
- Node.js 18+ 
- Convex account
- Google Cloud Console project (for OAuth)
- Google AI Studio API key

### Steps
1. **Build the application**
   ```bash
   npm run build
   ```

2. **Deploy to Vercel/Netlify**
   ```bash
   # For Vercel
   vercel deploy

   # For Netlify
   netlify deploy --prod
   ```

3. **Configure environment variables** in your hosting platform

4. **Set up Convex production environment**
   ```bash
   npx convex deploy
   ```

## 🔄 How AI Code Generation & Live Preview Works

### Code Generation Flow

1. **User Input Processing**
   - User enters a prompt (e.g., "Create a ToDo App in React")
   - The prompt is combined with a structured system prompt from `data/Prompt.jsx`

2. **AI Code Generation**
   ```javascript
   // The system prompt instructs AI to return structured JSON
   {
     "projectTitle": "React Todo App",
     "explanation": "Project description...",
     "files": {
       "/App.js": { "code": "import React..." },
       "/components/TodoList.js": { "code": "..." }
     },
     "generatedFiles": ["/App.js", "/components/TodoList.js"]
   }
   ```

3. **File Structure Creation**
   - AI response is parsed and merged with default template files
   - Each file in the `files` object becomes a virtual file in Sandpack
   - Default files include `index.html`, `tailwind.config.js`, and CSS setup

4. **Live Preview Rendering**
   - **Sandpack Integration**: Uses CodeSandbox's Sandpack for in-browser execution
   - **Real-time Compilation**: Code is compiled and executed in an isolated iframe
   - **Hot Reloading**: Changes are reflected instantly without page refresh

### Technical Implementation

#### AI Model Configuration (`configs/AiModel.jsx`)
```javascript
// Separate AI sessions for different purposes
export const chatSession = model.startChat({
  generationConfig: { responseMimeType: 'text/plain' }
});

export const GenAiCode = model.startChat({
  generationConfig: { responseMimeType: 'application/json' }
});
```

#### Code View Component (`components/custom/CodeView.jsx`)
```javascript
const GenerateAiCode = async () => {
  // 1. Send structured prompt to AI
  const PROMPT = JSON.stringify(messages) + ' ' + Prompt.CODE_GEN_PROMPT;
  const result = await axios.post('/api/gen-ai-code', { prompt: PROMPT });
  
  // 2. Parse AI response and merge with defaults
  const aiResp = result.data;
  const mergedFiles = { ...Lookup.DEFAULT_FILE, ...aiResp?.files };
  
  // 3. Update state and database
  setFiles(mergedFiles);
  await UpdateFiles({ workspaceId: id, files: aiResp?.files });
};
```

#### Sandpack Preview System
```javascript
// SandpackProvider wraps the entire code environment
<SandpackProvider
  files={files}                    // Generated file structure
  template="react"                 // React template
  theme={'dark'}                   // UI theme
  customSetup={{
    dependencies: { ...Lookup.DEPENDANCY }  // NPM packages
  }}
  options={{ 
    externalResources: ['https://cdn.tailwindcss.com'] 
  }}
>
  <SandpackLayout>
    <SandpackFileExplorer />       // File tree navigation
    <SandpackCodeEditor />         // Code editor
    <SandpackPreview />            // Live preview iframe
  </SandpackLayout>
</SandpackProvider>
```

### File Structure Generation

The AI is instructed to create a complete file structure:

```javascript
// Example AI response structure
{
  "files": {
    "/App.js": {
      "code": "import React from 'react';\n// Main app component..."
    },
    "/components/TodoList.js": {
      "code": "import React from 'react';\n// TodoList component..."
    }
  }
}
```

### Live Preview Features

- **Instant Compilation**: Code changes are compiled in real-time
- **Error Handling**: Syntax errors are displayed in the preview console
- **Package Management**: Pre-configured dependencies are automatically available
- **Export/Deploy**: Generated apps can be exported to CodeSandbox or deployed

### Supported Technologies

The AI can generate code using:
- **React** with hooks and functional components
- **Tailwind CSS** for styling
- **Lucide React** for icons (curated icon set)
- **Date-fns** for date formatting
- **React-Chart.js-2** for data visualization
- **Firebase** for backend integration (when needed)

## 📝 API Reference

### AI Chat Endpoint
```javascript
POST /api/ai-chat
{
  "prompt": "Combined user messages and system prompt"
}
// Response: { "result": "AI response text" }
```

### AI Code Generation Endpoint
```javascript
POST /api/gen-ai-code
{
  "prompt": "Structured prompt with user request + system instructions"
}
// Response: { "projectTitle": "...", "files": {...}, "generatedFiles": [...] }
```

### Convex Functions
- `CreateWorkspace`: Create new project workspace
- `UpdateMessages`: Update chat history
- `UpdateFiles`: Save generated file structure
- `GetWorkspace`: Retrieve workspace data
- `UpdateToken`: Update user token count

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by [Bolt.new](https://bolt.new)
- Built with [Next.js](https://nextjs.org/)
- Database powered by [Convex](https://convex.dev/)
- AI powered by [Google Gemini](https://ai.google.dev/)
- Code preview by [Sandpack](https://sandpack.codesandbox.io/)

## 🐛 Bug Reports & Feature Requests

Please use the [GitHub Issues](https://github.com/yourusername/shadbolt-ai/issues) page to report bugs or request features.

## 📞 Support

For support, email support@shadbolt-ai.com or join our community Discord server.

---

**Built with ❤️ by the ShadBolt AI team**

