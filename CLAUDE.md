# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

Don't use Docker.

### Local Development
- `bin/dev` - Start all services (Rails server, worker, Tailwind watcher)
- `bin/rails test` - Run unit/integration tests
- `bin/rails test:system` - Run system tests with headless browser
- `bin/rails db:prepare` - Set up and prepare database
- `bin/rails db:fixtures:load` - Load test data

### Production Deployment
- `RAILS_ENV=production RUN_SOLID_QUEUE_IN_PUMA=true DATABASE_URL=... rails s -p 8081` - Run production server

## Architecture Overview

HostedGPT is a Ruby on Rails application that provides a ChatGPT-like interface supporting multiple AI providers (OpenAI, Anthropic, Google Gemini, Groq). The application uses PostgreSQL for data storage and Solid Queue for background job processing.

### Core Models
- **User/Person**: Authentication and user management with multiple auth methods (password, Google OAuth, Microsoft Graph, HTTP headers)
- **Assistant**: AI assistants with configurable instructions and language models
- **Conversation**: Chat conversations with versioning support for message threading
- **Message**: Individual messages with support for text, images, and tool calls
- **LanguageModel**: Configuration for different AI models (GPT-4, Claude, Gemini, etc.)
- **ApiService**: API service configurations with credentials and endpoints

### AI Backend Architecture
- **AIBackend**: Base class for AI integrations (`app/services/ai_backend.rb`)
- **Streaming**: Real-time message streaming using ActionCable
- **Tool Support**: Function calling with tools like Gmail, Google Tasks, Google Search
- **Job Processing**: `GetNextAIMessageJob` handles async AI message generation

### Key Features
- **Multi-provider Support**: OpenAI, Anthropic, Google Gemini, Groq in single interface
- **Real-time Streaming**: ActionCable for live message updates
- **Tool Integration**: Gmail, Google Tasks, Google Search, Memory system
- **Image Support**: Upload and process images in conversations
- **Conversation Versioning**: Message threading and editing with history
- **Authentication**: Multiple auth methods with feature flags

### Configuration
- **Feature Flags**: Controlled via environment variables (see `config/options.yml`)
- **Models Configuration**: `models.yml` defines available language models
- **API Services**: Configurable API endpoints and credentials
- **Tool Integration**: Optional Google tools require OAuth setup

### File Structure
- `app/controllers/` - Rails controllers with nested settings namespace
- `app/models/` - ActiveRecord models with concerns and custom scopes
- `app/services/` - Business logic including AI backends and toolbox
- `app/jobs/` - Background jobs for AI processing
- `app/javascript/` - Stimulus controllers and audio/speech services
- `test/` - Comprehensive test suite with fixtures and system tests

### Database
- PostgreSQL with ActiveRecord
- Active Storage for file uploads (supports Cloudflare R2)
- Solid Queue for background job processing
- Encrypted credentials for sensitive data

### Development Notes
- Uses Overmind for process management in development
- Tailwind CSS for styling with custom components
- Stimulus for JavaScript interactions
- WebMock for HTTP request stubbing in tests
- Comprehensive fixture data for development and testing

## Testing
- Full test coverage with unit, integration, and system tests
- Fixtures provide realistic test data
- WebMock stubs external API calls
- Minitest with retry logic for flaky tests
- System tests use headless Chrome

## Key Patterns
- **Current Context**: Uses `Current.set` for thread-local user/message context
- **Streaming Responses**: Chunked AI responses with real-time updates
- **Tool Execution**: Parallel tool calls with formatted responses
- **Error Handling**: Comprehensive error handling for AI service failures
- **Feature Management**: Environment-based feature toggling
