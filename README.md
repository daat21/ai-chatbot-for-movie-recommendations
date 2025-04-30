# ChatBox Component Documentation

## 1. Code Structure Analysis

 The ChatBox folder contains a complete implementation of the chat interface, focusing on movie recommendations. The component is built on React and TypeScript, using modern React features such as hooks and context.

### Main File Organisation

 The folder is well organised and the components are divided by functionality:

- **Core components**:
    - `ChatBox.tsx`: the main chat interface container.
    - `MessageList.tsx`: the message list display.
    - `InputArea.tsx`: user input area
    - `ScrollableArea.tsx: auto-scrollable` area
- **Function Provider**:
    - `MovieSearchProvider.tsx`: movie search function provider
    - `InitialRecommendationProvider.tsx`: Initial Movie Recommendation Provider
- **UI component**:
    - `ThinkingIndicator.tsx`: thinking/generating indicator
    - `InitialRecommendation.tsx`: initial recommendation presentation component
- **Support files**:
    - `types.ts`: type definitions
    - `index.ts`: export all components
    - `ThinkingIndicator.module.css`: style file

## 2. Detailed code explanation

### ChatBox.tsx

**Role**: ChatBox is the main container component of the entire chat interface, responsible for coordinating the various sub-components and managing the chat state.

**Implementation principle**:

- Use `useChat` hook to handle chat logic, connect to `/api/chat` API endpoint.
- Maintains `isGenerating` state to differentiate between loaded and generating replies.
- Use Context Providers pattern to provide movie search and initial recommendation.
- Combine `MessageList`, `ScrollableArea` and `InputArea` to create a complete chat experience.

**Key Code**:

```tsx
const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
  api: '/api/chat',
})

```

```tsx
<MovieSearchProvider messages={messages}>
  {({ movieSearches, loadingMovies }) => (
    <InitialRecommendationProvider messages={messages}>
      {/* Rendering message lists and input fields */}
    </InitialRecommendationProvider>
  )}
</MovieSearchProvider>

```

### MessageList.tsx

**What it does**: Displays a list of chat messages, including user and helper messages, and embedded movie search results.

**Implementation Principle**:

- Parse the movie search pattern in the assistant's message: `[MOVIE_SEARCH:Movie name]`.
- Show movie card or loading status based on search status
- Show initial recommendation when there is no message
- Format message content to remove special tags

**Key Logic**:

```tsx
// Formatting Message Content
const formatMessageContent = (content: string) => {
  const cleanedContent = content.replace(/\\n\\nRecommended Movies:[\\s\\S]*/, '')
  return cleanedContent.replace(MOVIE_SEARCH_REGEX, '')
}

// Rendering Movie Cards
const renderMovieCards = (content: string) => {
  // Finds all [MOVIE_SEARCH:Movie Name] modes and renders the corresponding movie cards
}

```

### InputArea.tsx

**Purpose**: Provides the user input area, including the text input box and submit button.

**Implementation Principle**:

- Use `useRef` and `useEffect` to automatically adjust the height of the input area.
- Support Enter to submit (when not using Shift+Enter).
- Show ThinkingIndicator in loaded state.
- Handles form submission and input changes

**Key Code**:

```tsx
// Adjust the height of the text box
useEffect(() => {
  const textarea = textareaRef.current
  if (textarea) {
    textarea.style.height = 'auto'
    const newHeight = Math.min(Math.max(textarea.scrollHeight, 40), 200)
    textarea.style.height = `${newHeight}px`
  }
}, [input])

```

### MovieSearchProvider.tsx

**Function**: Provides movie search function, parses movie search request in chat message and gets movie data.

**Implementation Principle**:

- Use Context Provider pattern to provide movie search results and loading status.
- Use regular expression to extract movie search request from helper messages.
- Get movie data through API calls
- Maintain search result cache and load state

**Key Logic**:

```tsx
// Extract movie search mode
for (const message of messages) {
  if (message.role === 'assistant' && message.content) {
    let match
    const regex = new RegExp(MOVIE_SEARCH_REGEX)
    while ((match = regex.exec(message.content)) !== null) {
      const movieTitle = match[1].trim()
      // Get movie data
    }
  }
}

```

### InitialRecommendationProvider.tsx

**Role**: Provide initial movie recommendation before the conversation starts.

**Implementation Principle**:

- Call `getTopRatedMovies()` to get the top movies.
- Random shuffle algorithm to select 5 movies as recommendation
- Show initial recommendation only when there is no message
- Use Context Provider pattern to provide recommendation data and loading status.

**Key Code**:

```tsx
// Random selection of movies using the Fisher-Yates shuffle algorithm
for (let i = movies.length - 1; i > 0; i--) {
  const j = Math.floor(Math.random() * (i + 1))
  ;[movies[i], movies[j]] = [movies[j], movies[i]]
}
const top5Movies = movies.slice(0, 5)

```

### ScrollableArea.tsx

**What it does**: Creates a scrollable area that automatically scrolls to the latest news.

**Implementation Principle**:

- Wrapping the `ScrollArea` component
- Use `useRef` and `useEffect` to track content changes.
- Use DOM manipulation to find the scroll viewport and automatically scroll to the bottom.

**Key Code**:

```tsx
useEffect(() => {
  const timer = setTimeout(() => {
    const scrollAreaElement = scrollAreaRef.current
    if (scrollAreaElement) {
      const viewport = scrollAreaElement.querySelector<HTMLDivElement>(
        '[data-radix-scroll-area-viewport]'
      )
      if (viewport) {
        viewport.scrollTo({ top: viewport.scrollHeight, behavior: 'smooth' })
      }
    }
  }, 50)

  return () => clearTimeout(timer)
}, [children])

```

### InitialRecommendation.tsx

**Purpose**: Display the initial movie recommendation list.

**Implementation principle**:

- Receive an array of movie data and display it as a list of cards.
- Format the movie overview and limit the length
- Use MovieHorizontalCard component to display movie information.

### ThinkingIndicator.tsx and ThinkingIndicator.module.css

**Purpose**: Animated indicator showing the thinking/generating state of the AI.

**Implementation Principle**:

- Use CSS animation to create points for pulse effects
- Display different text based on the `isGenerating` property
- Isolates styles using CSS Modules

**Key Code**:

```css
.dot {
  animation: pulse 1.5s infinite ease-in-out;
}

.dot:nth-child(2) {
  animation-delay: 0.3s;
}

.dot:nth-child(3) {
  animation-delay: 0.6s;
}

```

### types.ts

**What it does**: Defines the shared types interface.

**Implementation**:

```tsx
export interface MovieResult {
  id: number
  title: string
  poster_path: string | null
  release_date: string
  overview: string
  original_title: string
}

```

# ChatBox Technical Documentation

## Overview: ChatBox is a React and TypeScript based chat interface component.

 ChatBox is a React and TypeScript based chat interface component that focuses on movie recommendations. It allows users to chat with an AI assistant, get movie recommendations, and display movie info cards. The component uses modern React features and design patterns such as Hooks, Context APIs, and component composition.

## Technology Stack

- React
- TypeScript
- Next.js (inferred from import path)
- AI/React library (for chat functionality)
- Radix UI (for ScrollArea and other UI components)

## Key Features

1. **AI chat interface**: users can type messages and receive AI replies
2. **Movie search and display**: the AI assistant can search for movies and display the details
3. **Initial movie recommendations**: Top movie recommendations are displayed before the conversation starts
4. **Responsive input area**: automatically adjusts height based on content
5. **Auto-scroll**: automatically scrolls to the bottom when a new message appears

## Component Architecture

 ChatBox adopts component combination and Context Provider pattern to realize clear separation of functions and state sharing:

```
ChatBox
├── MovieSearchProvider
│   └── InitialRecommendationProvider
│       ├── ScrollableArea
│       │   └── MessageList
│       │       └── MovieHorizontalCard(s)
│       └── InputArea
│           └── ThinkingIndicator
└── InitialRecommendation
    └── MovieHorizontalCard(s)

```

### Core Components

1. **ChatBox**: main container, managing overall chat state and UI layout.
2. **MessageList**: display chat messages and movie cards.
3. **InputArea**: handle user input and submission
4. **MovieSearchProvider**: Handles movie search logic and API calls.
5. **InitialRecommendationProvider**: manages initial movie recommendation.
6. **ScrollableArea**: Provide auto-scrolling function.

## Data Flow

1.  User enters a message in the InputArea
2.  Messages are sent to the API via the useChat hook
3.  AI replies contain special `[MOVIE_SEARCH:MovieName]` tags
4.  MovieSearchProvider detects these tags and fetches movie data.
5.  MessageList displays formatted message and movie cards
6.  ScrollableArea ensures new messages are visible

## API Integration

- **/api/chat**: handling chat messages
- **/api/movie-search**: search for movie information
- **TMDB API**: get `top rated` movies via `getTopRatedMovies()`

## Key Interaction Modes

1. **Initial loading**: display initial movie recommendations
2. **Chat interaction**: user sends message, receives AI reply
3. **Movie search**: AI reply contains movie search, automatically get and display movie cards.
4. **Load status**: display "thinking" or "generating" animation

## Extension Points

1.  More movie-related features can be added, such as actor information or similar movies.
2.  Movie cards could be enhanced to support more interactions, such as favoriting or sharing.
3.  Message formatting could be improved to support richer content types.

## Usage Guidelines

 To use ChatBox components in your project:

```tsx
import { ChatBox } from '@/components/home/ChatBox'

export default function HomePage() {
  return (
    <div>
      <ChatBox />
    </div>
  )
}

```
