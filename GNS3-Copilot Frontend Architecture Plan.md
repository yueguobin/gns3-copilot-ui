

# ⚛️ GNS3-Copilot Frontend Architecture Plan

## 1. Tech Stack Selection

* **Framework:** Next.js 14/15 (App Router)
* **UI Components:** Ant Design (General UI) + **Ant Design X** (AI Chat Components)
* **Topology Engine:** **React Flow** (For interactive network diagramming)
* **State Management:** **Zustand** (Lightweight, perfect for topology and session state)
* **API Client:** Axios (with Interceptors for automatic Header injection)
* **Icons:** Lucide-React

---

## 2. Key Modules & Features

### A. The AI Chat Sidebar (Ant Design X)

* **Streaming Content:** Use `EventSource` or `fetch` with ReadableStream to handle SSE from the FastAPI backend.
* **Voice Intercom:** A "Push-to-Talk" button using the `Web Audio API` to capture audio and send it to `/api/v1/agent/voice-chat`.
* **Thought Trace:** Displaying "Tool Calls" (e.g., *“Connecting R1 to SW1...”*) using Ant Design X’s `ThoughtChain` or `Progress` components.

### B. The Topology Canvas (React Flow)

* **Live Sync:** Automatically refresh node positions and link status when the Agent performs an action.
* **Interactive Hooks:** Drag-and-drop nodes to trigger backend updates, or click a link to see live traffic/config via the AI.

### C. The API Service Layer

* **Dynamic Configuration:** A settings modal to store GNS3 IP and API Keys in `localStorage` (Privacy-First).
* **Global Interceptor:** Automatically attach `X-Project-ID` and `X-LLM-API-Key` to every outgoing request.

---

## 3. Implementation Roadmap

### Phase 1: Scaffolding & Layout

* Set up Next.js with Tailwind CSS.
* Implement a **Main Layout**: A flexible 2-column view (Left: React Flow Canvas, Right: AI Chat Panel).
* Configure Axios with a base URL pointing to `http://localhost:8000/api/v1`.

### Phase 2: AI Interaction (SSE & Voice)

* Implement the `useChat` hook to consume the SSE stream from FastAPI.
* Integrate **Ant Design X** `<Bubble />` and `<Welcome />` components.
* Add a **Voice Recorder** component to handle the audio binary upload.

### Phase 3: Topology Visualization

* Integrate **React Flow**.
* Create custom nodes for GNS3 (Router, Switch, Cloud symbols).
* Implement an "Auto-Layout" feature to organize the topology after the AI generates a new network.

### Phase 4: State Synchronization

* Use Zustand to sync the AI's "Tool Outputs" with the Canvas (e.g., if the AI creates a link, the Canvas should update immediately without a page refresh).

---

## 4. Proposed Directory Structure (`gns3-copilot-ui`)

```text
gns3-copilot-ui/
├── src/
│   ├── app/                 # Next.js App Router (Pages)
│   ├── components/
│   │   ├── chat/            # Ant Design X components & Voice Mic
│   │   ├── topology/        # React Flow canvas & Custom Nodes
│   │   └── ui/              # Common components (Modals, Buttons)
│   ├── hooks/
│   │   ├── useChat.ts       # SSE Logic
│   │   └── useGNS3.ts       # Topology & Project Logic
│   ├── store/
│   │   └── useStore.ts      # Zustand state (Project ID, Node positions)
│   └── lib/
│       └── api.ts           # Axios instance & Interceptors
├── .env.local               # Local development variables
└── tailwind.config.ts

```

---

## 5. Frontend Task Checklist

* [ ] **Init**: `npx create-next-app@latest gns3-copilot-ui` (Tailwind, TS, App Router).
* [ ] **Install Deps**: `npm install antd @ant-design/x reactflow zustand axios lucide-react`.
* [ ] **SSE Hook**: Write a utility to parse `data: {...}` chunks from the FastAPI stream.
* [ ] **UI Prototype**: Build the basic side-by-side layout with a functional chat input.
* [ ] **Voice**: Implement `MediaRecorder` to send `.wav` files to the backend.
