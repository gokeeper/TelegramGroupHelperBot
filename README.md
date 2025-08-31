# Telegram Group Helper Bot

A Telegram bot for group chats that provides summarization, fact-checking, and question-answering capabilities using Google's Gemini AI with Google Search grounding.

## Features

- **TLDR Summary**: Summarize the last N messages in a group chat with `/tldr [number]`
- **Fact Checking**: Fact-check messages by replying with `/factcheck`
- **Question Answering**: Ask questions with `/q <your question>` and receive factual, search-grounded answers
- **Image Generation**: Generate images with `/img <description>`. Uses Gemini by default, or Vertex AI if configured (can return multiple images). Generated images are automatically uploaded to CWD.PW for external hosting with AI generation metadata (model and prompt information).
- **Video Generation**: Generates a video based on a text prompt and/or a replied-to image with `/vid <prompt>`.
- **Image Understanding**: Analyze and understand images when replying to a photo with `/factcheck` or `/q`
- **Google Search Grounding**: All responses are grounded in current information from Google Search
- **Telegraph Integration**: Automatically creates Telegraph pages for lengthy responses
- **Database Logging**: Messages are stored in a database for summarization and analysis
- **Multi-language Support**: Automatically detects and responds in the same language as the query
- **Support Integration**: Customizable support message with Ko-fi link for tip collection

## Requirements

- Python 3.13 or higher
- A Telegram Bot Token from [BotFather](https://t.me/botfather)
- Google Gemini API key from [Google AI Studio](https://aistudio.google.com/)
- Optional: Telegraph API access token (for optimal Telegraph integration)
- Optional: CWD.PW API key (for image hosting when using image generation features)

## Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/TelegramGroupHelperBot.git
   cd TelegramGroupHelperBot
   ```

2. Create a virtual environment and activate it:
   ```bash
   python -m venv venv
   source ./venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Copy the example environment file and edit it with your credentials:
   ```bash
   cp .env.example .env
   # Edit .env with your Telegram Bot Token, Gemini API key, Telegraph settings, and CWD.PW API key
   ```

5. Run the database migrations:
   ```bash
   alembic upgrade head
   ```

6. Run the bot:
   ```bash
   python -m bot.main
   ```

## Configuration

Configure the bot by editing the `.env` file:

### Required settings:
- `BOT_TOKEN`: Your Telegram Bot token from BotFather
- `GEMINI_API_KEY`: Google Gemini API key

### Telegraph settings:
- `TELEGRAM_MAX_LENGTH`: Maximum character length before using Telegraph (default: 4000)
- `TELEGRAPH_ACCESS_TOKEN`: Your Telegraph API access token
- `TELEGRAPH_AUTHOR_NAME`: Name to display as author on Telegraph pages
- `TELEGRAPH_AUTHOR_URL`: URL for the author name

### Image hosting settings:
- `CWD_PW_API_KEY`: Your CWD.PW API key for image hosting (optional, required for external image hosting)

### Support/Ko-fi settings:
- `SUPPORT_MESSAGE`: Custom message displayed with the `/support` command (default: "☕ If you find this bot helpful, consider supporting its development!")
- `KOFI_LINK`: Your Ko-fi profile URL for accepting tips (default: "https://ko-fi.com/yourusername")

### Optional settings:
- `DATABASE_URL`: Database connection URL (default: SQLite)
- `USE_WEBHOOK`: Whether to use webhook mode (default: false)
- `WEBHOOK_URL`: URL for the webhook in production
- `RATE_LIMIT_SECONDS`: Rate limiting between user requests (default: 15)
- `GEMINI_MODEL`: Gemini model to use for general text tasks (default: gemini-pro)
- `GEMINI_PRO_MODEL`: Gemini Pro model for more complex tasks, including media analysis (default: gemini-2.5-pro-exp-03-25)
- `GEMINI_IMAGE_MODEL`: Gemini model for image generation (default: gemini-2.0-flash-exp-image-generation)
- `GEMINI_VIDEO_MODEL`: The Gemini model to use for video generation. Defaults to "veo-3.0-generate-preview".

### Vertex AI Settings (Optional):
- `VERTEX_PROJECT_ID`: Your Google Cloud Project ID (required if using Vertex AI for any feature).
- `VERTEX_LOCATION`: The Google Cloud region for your Vertex AI resources (e.g., `us-central1`, required if using Vertex AI).
- `USE_VERTEX_IMAGE`: Set to "true" to use Vertex AI for image generation via the `/img` command (default: "false").
- `VERTEX_IMAGE_MODEL`: The specific Vertex AI image generation model to use (e.g., `imagegeneration@006`).
- `USE_VERTEX_VIDEO`: Set to "true" to use Vertex AI for video generation via the `/vid` command (default: "false", currently uses Gemini VEO).
- `VERTEX_VIDEO_MODEL`: The specific Vertex AI video generation model to use if `USE_VERTEX_VIDEO` is true.

## Deployment

The bot can be deployed in various ways:

1. **Local Development**: Run the bot locally with polling mode
2. **Production Webhook**: Deploy to a server and use webhook mode
3. **Docker**: Use the provided Dockerfile for containerized deployment
4. **Fly.io**: Deploy using the included `fly.toml` configuration

## Using Docker

1. Build the Docker image:
   ```bash
   docker build -t telegram-group-helper-bot .
   ```

2. Run the container:
   ```bash
   docker run --env-file .env telegram-group-helper-bot
   ```

## Using Fly.io

1. Install the Fly.io CLI
2. Log in to Fly.io:
   ```bash
   fly auth login
   ```

3. Launch the app:
   ```bash
   fly launch --generate-name
   ```

4. Set secrets:
   ```bash
   fly secrets set BOT_TOKEN=your_bot_token GEMINI_API_KEY=your_gemini_key CWD_PW_API_KEY=your_cwd_pw_key
   ```

5. Deploy:
   ```bash
   fly deploy
   ```

## Telegraph Integration

For responses that exceed the Telegram message size limit, the bot automatically:

1. Creates a Telegraph page with proper formatting
2. Sends a link to the page instead of the full content
3. Preserves markdown formatting in the Telegraph page

This feature triggers automatically when:
- The message exceeds the character limit set in `TELEGRAM_MAX_LENGTH`
- The message contains too many lines (22+)

## Google Search Grounding

This bot uses Google's Gemini AI with Google Search grounding to ensure responses are based on current, accurate information. When you ask a question or fact-check a statement, the bot:

1. Searches the web for relevant information
2. Grounds its response in the search results
3. Provides a factual, up-to-date answer
4. Includes citations where appropriate

## Image Understanding

The bot leverages Gemini's advanced image understanding capabilities to:

- Analyze photos shared in the group
- Answer questions about image content
- Fact-check claims in images or captions
- Detect objects and scenes in photos
- Process multiple image formats (JPEG, PNG, WEBP, HEIC, HEIF)

Simply reply to an image with `/q What's in this picture?` or `/factcheck` to analyze visual content.

## Image Hosting Integration

The bot includes automatic image hosting through CWD.PW for generated images:

### Features:
- **Automatic Upload**: All generated images are automatically uploaded to CWD.PW for external hosting
- **Multiple Formats**: Supports PNG, JPEG, and WebP image formats
- **Error Handling**: Upload failures don't affect image generation functionality
- **Logging**: Upload status and URLs are logged for debugging

### Configuration:
To enable image hosting, set your CWD.PW API key in the `.env` file:
```
CWD_PW_API_KEY=your_cwd_pw_api_key_here
```

### Behavior:
- When `CWD_PW_API_KEY` is configured: Generated images are uploaded to CWD.PW and URLs are logged
- When `CWD_PW_API_KEY` is not configured: Images are generated normally but not uploaded
- Upload failures are logged but don't prevent image generation or delivery to users

## Database Management

The bot uses SQLAlchemy for database operations. By default, it uses SQLite, but you can configure PostgreSQL for production.

See [README-MIGRATIONS.md](README-MIGRATIONS.md) for instructions on managing database migrations.

## Testing

Run tests with pytest:

```bash
pytest
```

To run specific test categories:

```bash
# Run unit tests only
pytest tests/unit/

# Run integration tests only
pytest tests/integration/
```

## Authors

- **Kevin Z** - Original project creator and main developer
- **Frank L** - CWD.PW image hosting integration and AI metadata enhancement

See [AUTHORS](AUTHORS) for detailed contribution information.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details. 