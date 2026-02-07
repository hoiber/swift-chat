# CLAUDE.md — SwiftChat

This file provides guidance to Claude Code when working with the SwiftChat project.

## Project Overview

SwiftChat is a cross-platform AI assistant built with **React Native** (frontend) and **Python FastAPI** (backend). It provides real-time streaming chat, AI image generation, instant web app creation, and multimodal support across Android, iOS, and macOS.

**Key Technologies:**
- **Frontend**: React Native, TypeScript, Hermes AOT engine, react-native-mmkv
- **Backend**: Python 3.x, FastAPI, AWS Lambda + API Gateway
- **AI Providers**: Amazon Bedrock, OpenAI, DeepSeek, Ollama, OpenAI Compatible APIs
- **Deployment**: AWS Lambda (via ECR), API Gateway with 15-minute streaming support

## Project Structure

```
swift-chat/
├── react-native/          # React Native app code (Android, iOS, macOS)
│   ├── src/              # App source code
│   ├── ios/              # iOS/macOS native code and Xcode workspace
│   ├── android/          # Android native code
│   └── package.json      # Node dependencies
├── server/               # Python FastAPI backend
│   ├── main.py          # FastAPI entry point
│   ├── requirements.txt # Python dependencies
│   ├── scripts/         # Build and deployment scripts
│   └── template/        # CloudFormation templates
└── assets/              # Marketing and demo assets
```

## Development Commands

### Frontend (React Native)

All React Native commands run from the `react-native/` directory:

```bash
cd react-native
npm i                    # Install dependencies
npm start                # Start Metro bundler
npm run android          # Run on Android emulator/device
npm run ios              # Run on iOS simulator/device
npm run format           # Run Prettier
npm run lint             # Run ESLint
```

**macOS Build:**
1. Run `npm start`
2. Open `ios/SwiftChat.xcworkspace` in Xcode
3. Change build destination to "My Mac (Mac Catalyst)"
4. Click Run (▶)

**First-time iOS setup:**
```bash
cd ios && pod install && cd ..
```

### Backend (Python FastAPI)

```bash
cd server
pip install -r requirements.txt
python main.py           # Run locally on port 8080
```

### Deployment

**Push container to ECR:**
```bash
cd server/scripts
bash ./push-to-ecr.sh
```

**Deploy to AWS Lambda:**
- Use CloudFormation template: `server/template/SwiftChatLambda.template`
- Deploys API Gateway + Lambda with streaming support (up to 15 minutes)
- Requires ECR image URI from push-to-ecr.sh

## Architecture Notes

### Performance Optimizations
- **AOT Compilation**: Hermes engine for fast app launch
- **Lazy Loading**: Complex components load on demand
- **Image Compression**: Reduces API latency
- **MMKV Storage**: 10x faster than AsyncStorage for message persistence
- **Streaming Rendering**: Real-time UI updates with `useMemo` caching

### Key Features
- Real-time streaming chat with SSE (Server-Sent Events)
- Multimodal input (text, images, videos, documents)
- Web app creation and editing (CodeSandbox-like experience)
- Web search integration (Tavily, Google, Bing, Baidu)
- Markdown rendering (code blocks, tables, LaTeX, Mermaid charts)
- Amazon Nova Sonic speech-to-speech with echo cancellation and barge-in

### Privacy & Security
- Encrypted API key storage
- Local-only data storage (no cloud sync)
- No telemetry or tracking
- Minimal permissions

## Common Tasks

### Adding New AI Model Provider
1. Update settings UI in `react-native/src/screens/Settings`
2. Add API client in `react-native/src/services/`
3. Implement streaming response handler
4. Update model selection dropdown

### Modifying Backend API
1. Edit `server/main.py` for new endpoints
2. Test locally with `python main.py`
3. Push to ECR with `server/scripts/push-to-ecr.sh`
4. Update Lambda via AWS Console ("Deploy new image")

### Updating Dependencies
- **React Native**: `cd react-native && npm update`
- **iOS Native**: `cd react-native/ios && pod update && cd ../..`
- **Python**: `cd server && pip install -r requirements.txt --upgrade`

## Testing

- **Manual Testing**: Build and run on device/simulator
- **No automated test suite currently exists**
- **Linting**: `npm run lint` in react-native directory
- **Formatting**: `npm run format` in react-native directory

## Important Conventions

1. **Always check both app and server code** when modifying API contracts
2. **Test on multiple platforms** (Android, iOS, macOS) for UI changes
3. **Compress images** before uploading to maintain performance
4. **Follow privacy-first approach** — no analytics or tracking code
5. **Use native components** where possible for performance
6. **Maintain backward compatibility** with existing user settings/data

## Deployment Targets

- **Backend**: AWS Lambda + API Gateway (streaming via response stream)
- **Frontend**:
  - Android: APK releases on GitHub
  - macOS: DMG releases on GitHub
  - iOS: Local build via Xcode (not on App Store)

## External Documentation

- [React Native Docs](https://reactnative.dev/)
- [FastAPI Docs](https://fastapi.tiangolo.com/)
- [Amazon Bedrock Docs](https://docs.aws.amazon.com/bedrock/)
- [AWS Lambda Container Images](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)

## Notes

- Server supports 15-minute streaming via Lambda (not 30-second default)
- MMKV used for ultra-fast local storage (10x faster than AsyncStorage)
- Hermes AOT compilation makes launch instant on Android
- Image compression critical for fast API responses
- Mac Catalyst target shares iOS codebase
