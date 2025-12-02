# MCP Transport: Telegram

## Project Overview
MCP Transport Telegram bridges the Model Context Protocol with Telegram messaging, enabling bi-directional communication between MIA and Telegram users. This allows MIA to:
- Receive messages from Telegram chats
- Process them through MIA's orchestration and tools
- Reply directly in-channel with results
- Manage threads and topics for organized workflows

## Integration with MIA

Telegram MCP is the **remote control and UI layer** for MIA. When integrated:
1. Telegram users send commands/queries to a bot
2. MCP server translates Telegram messages to MCP format
3. MIA receives message > routes to tools/prompts > gets results
4. Results sent back to Telegram (same chat/thread)

### Capabilities
- List dialogs and read unread messages
- Retrieve media and contact information
- Draft and send messages for summarization
- Two-way message editing and management
- Thread/topic support for conversation organization

## Boot Sequence Integration

MIA Startup:
  1. Start mcp-prompts (fetch persona/tool-routing templates)
  2. Start TinyMCP tool servers (stdio transport for C++ utilities)
  3. Start mcp-transport-telegram (connect to Telegram API)
  
Message Flow:
  Telegram User > Telegram MCP Server > MIA Orchestrator
                                          |
                                    Routes to Tools/Prompts
                                          |
                          Results > Telegram MCP > Reply in-chat

## Tasks

### Phase 1: Telegram API Integration
- [ ] Setup Telegram Bot API client (TDLib or telethon)
- [ ] Implement message receiving (polling or webhook)
- [ ] Implement message sending and editing
- [ ] Add media handling (photos, documents, voice messages)
- [ ] Implement contact and dialog management
- [ ] Test with live Telegram account

### Phase 2: MCP Protocol Implementation
- [ ] Implement MCP Tools interface:
  - list_chats - enumerate available dialogs
  - get_messages - fetch unread messages from chat
  - send_message - reply in Telegram
  - edit_message - modify sent messages
  - get_media - download media from messages
- [ ] Implement MCP Resources interface
- [ ] Error handling and timeouts
- [ ] Structured logging

### Phase 3: MIA Integration
- [ ] Register Telegram MCP as external tool in MIA
- [ ] Setup Telegram API credentials in env/SSM
- [ ] Create message routing pipeline
- [ ] Implement context preservation (chat_id, thread_id)
- [ ] Add rate limiting and quota management
- [ ] Testing with MIA + Prompts + Tool stack

### Phase 4: Advanced Features
- [ ] Conversation threading
- [ ] Message summarization workflows
- [ ] Media triage and processing
- [ ] Reply-to-message semantics
- [ ] Command detection and parsing
- [ ] User authentication and permissions

## Configuration

Environment variables (for MIA integration):
TELEGRAM_API_ID=<from telegram.org>
TELEGRAM_API_HASH=<from telegram.org>
TELEGRAM_BOT_TOKEN=<from @BotFather>
TELEGRAM_PHONE=<phone number for client auth if needed>

## Message Flow Example

User Query in Telegram:
[User in @mia_bot group]
User: What devices are offline?

> Telegram MCP Server
[Tool: get_messages]
"What devices are offline?"

> MIA Orchestrator
[Apply prompt: "system_status"]
[Call tool: check_device_status]
Result: "Device 3 (ESP32-Living) is offline since 15min"

> Format & Reply
[MCP: send_message]
Reply: "Device 3 (ESP32-Living) offline for 15 minutes"

[User sees reply in Telegram]

## Key Files
- src/telegram_mcp.py - Main MCP server implementation
- src/telegram_client.py - Telegram API wrapper
- src/tools/ - MCP Tools implementation
- src/resources/ - MCP Resources implementation
- config/ - Configuration templates
- tests/ - Integration tests with mock Telegram

## Dependencies
- MCP SDK (latest)
- telethon or TDLib (Telegram API client)
- FastAPI (for webhook support, optional)
- pydantic (validation)

## Status
- Priority: High (user-facing control layer for MIA)
- Phase: 1 (API Integration) - Ready to start
- Target Integration: MIA Core + Prompts + Tools

## See Also
- MIA - Lean IoT Assistant (github.com/sparesparrow/mia)
- MCP Prompts - Prompt Catalog (github.com/sparesparrow/mcp-prompts)
- TinyMCP - Lightweight Tool Servers
- MCPServer.cpp - High-throughput Server

## Contributors
- Maintainer: TBD
- Integration Lead: TBD
