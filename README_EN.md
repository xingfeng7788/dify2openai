![banner](https://io.onenov.cn/file/202412240230660.webp)

# Dify2OpenAI Gateway

[![爱发电](https://afdian.moeci.com/13/badge.svg)](https://afdian.com/@orence)
<a href="./README.md"><img alt="简体中文版自述文件" src="https://img.shields.io/badge/简体中文-d9d9d9"></a>
<a href="./README_EN.md"><img alt="README in English" src="https://img.shields.io/badge/English-d9d9d9"></a>

**Dify2OpenAI** is a gateway service that transforms Dify applications into OpenAI API-compatible interfaces, allowing you to access Dify's LLM, Knowledge Base, Tools, and Workflows using OpenAI API-compatible methods.

---

## Features

- Convert Dify API to OpenAI API
- Support streaming and blocking
- Support Chat, Completion, Agent, and Workflow bots API on Dify

## Support

- Image Support
- Variable Support
- Continuous Conversation
- Workflow Bot
- Streaming & Blocking
- Agent & Chat bots

---

## Quick Start

### Installation

```bash
git clone https://github.com/onenov/Dify2OpenAI.git
cd Dify2OpenAI
npm install
```

### Start Service

Using PM2 (Recommended):
```bash
npm start
```

Or start directly:
```bash
node app.js
```

The service will run on `http://localhost:3099` by default.

### PM2 Commands

Start service:
```bash
pm2 start ecosystem.config.js
```

View logs:
```bash
pm2 logs
```

Stop service:
```bash
pm2 stop ecosystem.config.js
```

Restart service:
```bash
pm2 restart ecosystem.config.js
```

---

## One-Click Deploy

### Deploy on Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fonenov%2FDify2OpenAI)

1. Click the button above to go to Vercel
2. Create and import the project
3. Deploy directly, no environment variables needed
4. After deployment, you can access it in three ways:
   - Pass all configurations in the Authorization Header
   - Pass API_KEY in the Authorization Header, other configurations through the model parameter
   - Pass DIFY_API_URL in the Authorization Header, other configurations through the model parameter

Note: Vercel serverless functions have a 10-second timeout limit.

---

## Access Methods

### Method One: All Configurations in Authorization Header

**Authorization Header Format:**
```
Authorization: Bearer DIFY_API_URL|API_KEY|BOT_TYPE|INPUT_VARIABLE|OUTPUT_VARIABLE
```

Example:
```
Authorization: Bearer https://cloud.dify.ai/v1|app-xxxx|Chat
```

Set `model` parameter to `dify`

### Method Two: API_KEY in Authorization Header

**Authorization Header Format:**
```
Authorization: Bearer API_KEY
```

**Model Parameter Format:**
```
"model": "dify|BOT_TYPE|DIFY_API_URL|INPUT_VARIABLE|OUTPUT_VARIABLE"
```

Example:
```
Authorization: Bearer app-xxxx
"model": "dify|Chat|https://cloud.dify.ai/v1"
```

### Method Three: DIFY_API_URL in Authorization Header

**Authorization Header Format:**
```
Authorization: Bearer DIFY_API_URL
```

**Model Parameter Format:**
```
"model": "dify|API_KEY|BOT_TYPE|INPUT_VARIABLE|OUTPUT_VARIABLE"
```

Example:
```
Authorization: Bearer https://cloud.dify.ai/v1
"model": "dify|app-xxxx|Chat"
```

## Examples

### Basic Chat

```bash
curl http://localhost:3099/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer https://cloud.dify.ai/v1|app-xxxx|Chat" \
  -X POST \
  -d '{
    "model": "dify",
    "stream": true,
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful assistant."
      },
      {
        "role": "user",
        "content": "Hello"
      }
    ]
  }'
```

### Chat with Image

```bash
curl http://localhost:3099/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer https://cloud.dify.ai/v1|app-xxxx|Chat" \
  -X POST \
  -d '{
    "model": "dify",
    "stream": true,
    "messages": [
      {
        "role": "user",
        "content": [
          "Please analyze this image.",
          {
            "type": "image_url",
            "image_url": {
              "url": "https://example.com/image.jpg"
            }
          }
        ]
      }
    ]
  }'
```

---

## Development Guide

### Project Structure

```
.
├── app.js              # Application entry file
├── botType/           # Bot type handlers
│   ├── chatHandler.js     # Chat handler
│   ├── completionHandler.js # Completion handler
│   ├── utils.js           # Utility functions
│   └── workflowHandler.js  # Workflow handler
├── config/            # Configuration directory
│   └── logger.js         # Logger configuration
├── public/            # Static files directory
│   └── index.html        # API documentation page
├── ecosystem.config.js # PM2 configuration file
├── nodemon.json       # Nodemon configuration file
└── package.json       # Project configuration file
```

### Development Mode Configuration

The project uses nodemon for hot reloading in development mode:

```json
{
  "watch": ["*.js", "botType/*.js", "config/*.js"],
  "ext": "js,json,env",
  "ignore": [
    "node_modules/",
    "*.test.js",
    "logs/*",
    ".git",
    "public/*"
  ],
  "delay": "500",
  "verbose": true
}
```

- `watch`: Files and directories to monitor
- `ext`: File extensions to monitor
- `ignore`: Files and directories to ignore
- `delay`: Restart delay in milliseconds
- `verbose`: Show detailed logs

### Development Process

1. Clone the project
```bash
git clone https://github.com/onenov/Dify2OpenAI.git
cd Dify2OpenAI
```

2. Install dependencies
```bash
npm install
```

3. Start development server
```bash
npm run dev
```

4. Production deployment
```bash
npm start
# or using PM2
pm2 start ecosystem.config.js
```

### Code Style

- Use ES Modules for imports/exports
- Use async/await for asynchronous operations
- Use try/catch for error handling
- Use winston for logging

---

## Logging System

### Log Configuration

By default:
- Production environment (`npm start`): Only logs error level, console output only
- Development environment (`npm run dev`): Logs all levels, outputs to both console and file

Log files are stored in the `logs` directory:
- `combined-%DATE%.log`: Logs of all levels
- `error-%DATE%.log`: Error level logs only

### Log Levels

Supports the following log levels (in order of severity):
- `error`: Error messages
- `warn`: Warning messages
- `info`: General information
- `debug`: Debug information

### Log Format

Each log entry contains:
- Timestamp
- Log level
- Detailed message
- Metadata (if any)

Example:
```json
{
  "level": "info",
  "message": "Server started successfully",
  "timestamp": "2024-12-24T01:51:10+08:00",
  "port": 3099
}
```

### Log Rotation

Log files are automatically rotated according to:
- Daily rotation (new file each day)
- Maximum file size of 20MB
- Keep logs for the last 14 days
- Automatically delete logs exceeding limits

### Performance Optimization

For performance, the logging system:
- Uses buffered writing to reduce I/O operations
- Writes asynchronously to avoid blocking the main thread
- Automatically cleans up expired logs to control disk usage

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Thank you for using Dify2OpenAI! If you encounter any problems during use, please feel free to ask and we will assist you as soon as possible.**