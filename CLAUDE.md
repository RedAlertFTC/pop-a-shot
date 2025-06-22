# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is "Quiz & Swish" - a modified Pop-A-Shot basketball game for Center Grove FTC Robotics outreach. The application runs on a Raspberry Pi and provides an interactive trivia game where players shoot basketballs at correct answers.

## Architecture

### Core Components

- **Express.js Web Server** (`app.js`, `bin/www`) - Serves the game interface and static assets
- **Socket.IO Real-time Communication** (`socketapi.js`) - Handles real-time communication between server and clients for basket detection
- **Raspberry Pi GPIO Integration** - Detects physical basket shots using GPIO pins (530, 529) with debounce
- **P5.js Game Engine** (`public/js/scoreboard.js`) - Handles game rendering, state management, and user interactions
- **Quiz System** (`public/js/quizdata.json`) - Contains robotics trivia questions with multiple choice answers

### Game Flow

1. **Start Screen** - Welcome screen with "GET READY!" button
2. **Main Game** - 45-second timer, displays questions with left/right answer choices, tracks scores
3. **End Screen** - Shows final scores and winner

### Hardware Integration

The application automatically detects if running on Raspberry Pi:
- **Raspberry Pi**: Uses GPIO pins for basket detection via `onoff` library
- **Development**: Uses keyboard input (keys 1,2,9,0) to simulate basket shots

### Key Features

- Dual-player scoring system
- Answer swapping mechanism (spacebar/B key after 3-second countdown)
- Shot clock when answers are swapped (3 seconds)
- Gamepad controller support
- Responsive design for different screen sizes
- Automatic game state management

## Development Commands

### Starting the Application

```bash
# Development mode with auto-restart
npx supervisor ./bin/www

# Production mode
npm start

# Static file server only
npm run static
```

### Deployment to Raspberry Pi

```bash
# Sync files to Pi (excluding node_modules)
rsync -avz --exclude 'node_modules' ~/src/pop-a-shot user@pi_ip_address:/home/user/

# Monitor GPIO pins on Pi
watch -n.2 gpio readall
```

### Browser Setup

```bash
# Mac - Kiosk mode for production display
"/Applications/Microsoft Edge.app/Contents/MacOS/Microsoft Edge" http://localhost:3000 --kiosk --edge-kiosk-type=public-browsing --no-first-run

# Windows 11 - Kiosk mode for production display
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" http://localhost:3000 --kiosk --edge-kiosk-type=public-browsing --no-first-run
```

## File Structure

- `app.js` - Main Express application setup
- `bin/www` - Server startup script with Socket.IO attachment
- `socketapi.js` - GPIO/keyboard input handling and Socket.IO events
- `public/js/scoreboard.js` - Main game logic, rendering, and state management
- `public/js/quizdata.json` - Trivia questions database
- `public/index.htm` - Main game HTML template
- `routes/index.js` - Express routing
- `views/` - Pug templates for server-rendered pages
- `public/css/` - Stylesheets
- `public/images/` - Game background images

## Technical Notes

### Socket.IO Events

- `"basket"` - Emitted when basket is detected, payload: `{num: 1|2}`
- GPIO pins 530 (left basket) and 529 (right basket) with falling edge detection

### Game Controls

**Keyboard (Development)**:
- `1` - Left basket, Player 1 shot
- `2` - Right basket, Player 1 shot  
- `9` - Left basket, Player 2 shot
- `0` - Right basket, Player 2 shot
- `Space/B` - Start game, swap answers, restart
- `5` - Force end game

**Gamepad**: Buttons mapped for A/B (shots) and Start (navigation)

### Quiz Format

Questions stored in `quizdata.json` with structure:
```json
{
  "robotics": {
    "questions": [
      {
        "question": "Question text",
        "answers": ["Correct", "Wrong1", "Wrong2", "Wrong3"],
        "correct_answer": "Correct"
      }
    ]
  }
}
```

## Dependencies

Key libraries:
- `express` - Web framework
- `socket.io` - Real-time communication
- `onoff` - Raspberry Pi GPIO access
- `pug` - Template engine
- `supervisor` - Development auto-restart
- P5.js (CDN) - Canvas-based game rendering