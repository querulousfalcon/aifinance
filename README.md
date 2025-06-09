# AI Financial Advisor Agent

A comprehensive AI agent system designed for financial advisors that integrates with Gmail, Google Calendar, and HubSpot CRM to provide intelligent assistance, automation, and client management.

## Features

### Core Capabilities
- **ChatGPT-like Interface**: Natural language conversation with your AI assistant
- **Multi-platform Integration**: Seamless connection with Gmail, Google Calendar, and HubSpot
- **RAG (Retrieval Augmented Generation)**: Uses PostgreSQL with pgvector for intelligent information retrieval
- **Tool Calling**: AI agent can perform actions like scheduling meetings, sending emails, and managing contacts
- **Proactive Behavior**: Agent monitors events and takes automatic actions based on ongoing instructions
- **Task Management**: Background task processing with memory and persistence

### Integrations

#### Google Services (OAuth 2.0)
- **Gmail API**: Read/write access to emails for context and automation
- **Google Calendar API**: Access to calendar events and availability
- **Permissions**: Email read/write, calendar read/write, profile information

#### HubSpot CRM (OAuth 2.0)
- **Contacts Management**: Access and create contacts
- **Notes and Activities**: Read and create notes and activities
- **Self-serve OAuth**: Free testing account and OAuth app creation available

### AI Capabilities

#### Question Answering
- "Who mentioned their kid plays baseball?"
- "Why did Greg say he wanted to sell AAPL stock?"
- Ambiguous name resolution with contact search

#### Task Automation
- "Schedule an appointment with Sara Smith"
- "Send an email to John about the quarterly report"
- "Create a HubSpot contact for new leads"

#### Ongoing Instructions
- "When someone emails me that is not in HubSpot, create a contact"
- "When I create a contact in HubSpot, send them a welcome email"
- "When I add a calendar event, notify attendees"

## Architecture

### Backend (FastAPI)
```
backend/
├── app/
│   ├── api/           # API routes (auth, chat, webhooks, tasks)
│   ├── core/          # Configuration and authentication
│   ├── models/        # Database models and schemas
│   ├── services/      # Business logic (AI agent, OAuth, webhooks)
│   └── tests/         # Comprehensive test suite
├── requirements.txt
└── init_db.py
```

### Frontend (React)
```
frontend/
├── src/
│   ├── components/    # React components
│   ├── contexts/      # React contexts (Auth)
│   ├── hooks/         # Custom hooks
│   └── utils/         # Utility functions
├── package.json
└── tailwind.config.js
```

### Database (PostgreSQL + pgvector)
- **Users**: User accounts and OAuth tokens
- **Conversations**: Chat conversation history
- **Messages**: Individual chat messages
- **Tasks**: Background tasks with status tracking
- **Vector Documents**: RAG embeddings for emails, contacts, and calendar events
- **Ongoing Instructions**: User-defined automation rules

## Setup and Installation

### Prerequisites
- Python 3.8+
- Node.js 16+
- PostgreSQL 13+ with pgvector extension
- Redis (for task queuing)

### Backend Setup

1. **Install dependencies**:
```bash
cd backend
pip install -r requirements.txt
```

2. **Configure environment**:
```bash
cp .env.example .env
# Edit .env with your configuration
```

3. **Set up OAuth applications**:

#### Google OAuth Setup
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable Gmail API and Google Calendar API
4. Create OAuth 2.0 credentials
5. Add authorized redirect URI: `http://localhost:8000/auth/google/callback`
6. Add test user: `webshookeng@gmail.com`

#### HubSpot OAuth Setup
1. Go to [HubSpot Developer Portal](https://developers.hubspot.com/)
2. Create a new app
3. Configure OAuth with redirect URI: `http://localhost:8000/auth/hubspot/callback`
4. Set required scopes: contacts, content, reports, timeline, automation

4. **Initialize database**:
```bash
python init_db.py
```

5. **Run the application**:
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

### Frontend Setup

1. **Install dependencies**:
```bash
cd frontend
npm install
```

2. **Start development server**:
```bash
npm start
```

The application will be available at `http://localhost:3000`

### Webhook Configuration

#### Gmail Webhooks (Google Cloud Pub/Sub)
1. Create a Pub/Sub topic in Google Cloud
2. Set up push subscription to your webhook endpoint
3. Configure Gmail watch requests via API

#### Calendar Webhooks
1. Create watch channels for calendar events
2. Configure webhook endpoint for notifications

#### HubSpot Webhooks
1. Configure webhook URLs in HubSpot app settings
2. Subscribe to contact and deal events

## Usage Examples

### Basic Chat Interactions
```
User: "Who mentioned their kid plays baseball?"
Agent: "I found 2 contacts who mentioned baseball:
1. John Smith mentioned his son's baseball game in an email on Jan 15
2. Sarah Johnson talked about her daughter's baseball practice in our last meeting"

User: "Schedule an appointment with Sara Smith"
Agent: "I'll help you schedule an appointment with Sara Smith. Let me check your calendar availability and send her an email with available times."
```

### Ongoing Instructions Examples
```
"When someone emails me that is not in HubSpot, please create a contact in HubSpot with a note about the email."

"When I create a contact in HubSpot, send them an email telling them thank you for being a client."

"When I add an event in my calendar, send an email to attendees telling them about the meeting."
```

## Testing

### Backend Tests
```bash
cd backend
pytest app/tests/ -v
```

Test coverage includes:
- Authentication flows
- Chat functionality  
- Webhook processing
- AI agent behavior
- Tool calling
- Database operations

### Frontend Tests
```bash
cd frontend
npm test
```

## API Documentation

When running the backend, API documentation is available at:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

### Key Endpoints

#### Authentication
- `GET /auth/google` - Initiate Google OAuth
- `POST /auth/google/callback` - Handle Google OAuth callback
- `GET /auth/hubspot` - Initiate HubSpot OAuth
- `POST /auth/hubspot/callback` - Handle HubSpot OAuth callback

#### Chat
- `POST /chat/send` - Send message to AI agent
- `GET /chat/conversations` - Get user conversations
- `GET /chat/conversations/{id}/messages` - Get conversation messages
- `WebSocket /chat/ws/{user_id}` - Real-time chat connection

#### Tasks
- `GET /tasks/` - Get user tasks
- `GET /tasks/{id}` - Get specific task
- `PUT /tasks/{id}` - Update task

#### Webhooks
- `POST /webhooks/gmail` - Gmail webhook endpoint
- `POST /webhooks/calendar` - Calendar webhook endpoint
- `POST /webhooks/hubspot` - HubSpot webhook endpoint

## Security Considerations

- OAuth 2.0 with secure token handling
- JWT authentication for API access
- Webhook signature verification
- Environment variable configuration
- CORS protection
- Input validation and sanitization

## Deployment

### Docker Deployment
```bash
# Build and run with Docker Compose
docker-compose up -d
```

### Production Considerations
- Use production PostgreSQL database
- Configure Redis for task queuing
- Set up proper webhook endpoints with HTTPS
- Configure environment variables securely
- Set up monitoring and logging

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Run the test suite
6. Submit a pull request

## License

This project is licensed under the MIT License.

## Support

For questions and support, please contact the development team or create an issue in the repository.
