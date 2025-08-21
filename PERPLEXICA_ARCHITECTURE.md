# Perplexica Architecture Documentation

## Overview
Perplexica is an AI-powered search engine that uses various language models and embedding models to provide intelligent search results. This document outlines the key components and their interactions.

## Core Components

### 1. Configuration Management
- **File**: `src/lib/config.ts`
- **Purpose**: Handles loading and updating application configuration from `config.toml`
- **Key Functions**:
  - `loadConfig()`: Reads configuration from TOML file
  - `get*` functions: Retrieve specific configuration values
  - `updateConfig()`: Updates configuration values

### 2. Model Providers
- **File**: `src/lib/providers/index.ts`
- **Purpose**: Manages different AI model providers and their models
- **Key Components**:
  - `chatModelProviders`: Map of chat model provider loaders
  - `embeddingModelProviders`: Map of embedding model provider loaders
  - `getAvailableChatModelProviders()`: Loads all available chat models
  - `getAvailableEmbeddingModelProviders()`: Loads all available embedding models

### 3. API Routes

#### Config API
- **File**: `src/app/api/config/route.ts`
- **Purpose**: Provides REST API for configuration management
- **Endpoints**:
  - `GET /api/config`: Returns current configuration
  - `POST /api/config`: Updates configuration

#### Models API
- **File**: `src/app/api/models/route.ts`
- **Purpose**: Provides REST API for model information
- **Endpoints**:
  - `GET /api/models`: Returns available chat and embedding models

#### Chat API
- **File**: `src/app/api/chat/route.ts`
- **Purpose**: Handles chat interactions with AI models
- **Endpoints**:
  - `POST /api/chat`: Processes chat messages and returns AI responses

#### Search API
- **File**: `src/app/api/search/route.ts`
- **Purpose**: Handles search interactions with AI models
- **Endpoints**:
  - `POST /api/search`: Processes search queries and returns AI responses

#### Chats API
- **File**: `src/app/api/chats/route.ts` and `src/app/api/chats/[id]/route.ts`
- **Purpose**: Manages chat history and retrieval
- **Endpoints**:
  - `GET /api/chats`: Returns all chats
  - `POST /api/chats`: Creates a new chat
  - `GET /api/chats/[id]`: Returns a specific chat
  - `DELETE /api/chats/[id]`: Deletes a specific chat

#### Discover API
- **File**: `src/app/api/discover/route.ts`
- **Purpose**: Handles discovery of new content
- **Endpoints**:
  - `GET /api/discover`: Returns discovered content

#### Images API
- **File**: `src/app/api/images/route.ts`
- **Purpose**: Handles image search and retrieval
- **Endpoints**:
  - `GET /api/images`: Returns images related to a query

#### Suggestions API
- **File**: `src/app/api/suggestions/route.ts`
- **Purpose**: Provides suggestions for user queries
- **Endpoints**:
  - `POST /api/suggestions`: Returns suggested follow-up queries

#### Uploads API
- **File**: `src/app/api/uploads/route.ts`
- **Purpose**: Handles file uploads
- **Endpoints**:
  - `POST /api/uploads`: Uploads a file

#### Videos API
- **File**: `src/app/api/videos/route.ts`
- **Purpose**: Handles video search and retrieval
- **Endpoints**:
  - `GET /api/videos`: Returns videos related to a query

#### Weather API
- **File**: `src/app/api/weather/route.ts`
- **Purpose**: Provides weather information
- **Endpoints**:
  - `GET /api/weather`: Returns weather information for a location

### 4. Frontend Settings
- **File**: `src/app/settings/page.tsx`
- **Purpose**: Provides UI for configuring application settings
- **Key Features**:
  - Model provider selection
  - Model selection for each provider
  - API key configuration
  - Custom OpenAI provider configuration

## Model Loading Flow

1. **Configuration Loading**:
   - Application loads configuration from `config.toml` via `src/lib/config.ts`
   - Configuration includes API keys, URLs, and model names

2. **Provider Registration**:
   - Providers are registered in `src/lib/providers/index.ts`
   - Each provider has loader functions for chat and embedding models

3. **Model Loading**:
   - `getAvailableChatModelProviders()` and `getAvailableEmbeddingModelProviders()` load models from all providers
   - Custom OpenAI models are handled as a special case with dynamic model creation

4. **API Exposure**:
   - Loaded models are exposed via `/api/models` endpoint
   - Configuration is exposed via `/api/config` endpoint

5. **Frontend Usage**:
   - Settings page fetches configuration and available models
   - User selections are saved to both localStorage and backend config
   - Chat/search pages use selected models for interactions

## Custom OpenAI Provider Handling

The `custom_openai` provider is handled differently from other providers:
1. It's registered in provider lists with empty loader functions
2. Special case handling in `getAvailable*` functions creates model instances dynamically
3. Model names are retrieved from configuration
4. Model instances use `ChatOpenAI` and `OpenAIEmbeddings` classes with custom parameters

## Data Flow for Chat/Search Interactions

1. User sends message/query via frontend
2. Frontend sends request to `/api/chat` or `/api/search` with:
   - Message content/query
   - Selected chat model provider and model
   - Selected embedding model provider and model
   - Chat history (for chat)
3. Backend:
   - Loads selected models using provider system
   - Creates model instances with appropriate credentials
   - Processes message/query with selected models
   - Streams response back to frontend
4. Frontend displays streaming response to user

## Key Files and Their Roles

| File | Role |
|------|------|
| `src/lib/config.ts` | Configuration management |
| `src/lib/providers/index.ts` | Model provider management |
| `src/app/api/config/route.ts` | Configuration API endpoints |
| `src/app/api/models/route.ts` | Model information API endpoints |
| `src/app/api/chat/route.ts` | Chat processing API endpoint |
| `src/app/api/search/route.ts` | Search processing API endpoint |
| `src/app/settings/page.tsx` | Settings UI |
| `config.toml` | Persistent configuration storage |