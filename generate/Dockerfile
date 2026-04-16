# Stage 1: Build
FROM node:22-bookworm-slim AS builder

WORKDIR /app

# System-Abhängigkeiten für Canvas und Rendering
RUN apt-get update && apt-get install -y \
    python3 \
    make \
    g++ \
    pkg-config \
    libcairo2-dev \
    libpango1.0-dev \
    libjpeg-dev \
    libgif-dev \
    librsvg2-dev

COPY package.json bun.lockb* ./
RUN npm install

COPY . .
RUN npm run build

# Stage 2: Runner
FROM node:22-bookworm-slim

WORKDIR /app

# Installation von Chrome für Remotion & FFmpeg für Video-Encoding
RUN apt-get update && apt-get install -y \
    libnss3 libdbus-1-3 libatk1.0-0 libgbm-dev libasound2 \
    libxrandr2 libxkbcommon-dev libxfixes3 libxcomposite1 \
    libxdamage1 libatk-bridge2.0-0 libpango-1-0-0 libcairo2 libcups2 \
    ffmpeg chromium \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /app /app

# Umgebungsvariablen für Headless Chrome
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium
ENV REMOTION_CHROME_EXECUTABLE_PATH=/usr/bin/chromium

EXPOSE 3000

CMD ["npm", "run", "start"]
