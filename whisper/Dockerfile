# Base image updated by Renovate, update versionCompatibility on Debian base bump
ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:trixie-2026.10.3@sha256:a9fb29d0ffab1a81d6379e6252db21e0edbd1263c94d9de2cfc54a396aad8611
FROM ${BUILD_FROM}

# Install Whisper
WORKDIR /usr/src
ARG \
    WYOMING_WHISPER_VERSION=3.5.0 \
    WYOMING_VERSION=1.9.0
# Must match the exact pins of wyoming-faster-whisper
ARG \
    TRANSFORMERS_VERSION=4.52.4 \
    ONNX_ASR_VERSION=0.11.0 \
    FUNASR_VERSION=1.4.3 \
    ONNXRUNTIME_VERSION=1.29.0 \
    FASTER_WHISPER_VERSION=1.2.1 \
    CTRANSLATE2_VERSION=4.8.5 \
    SHERPA_ONNX_VERSION=1.13.6 \
    TORCH_VERSION=2.7.1

RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        libgomp1 \
        netcat-traditional \
        python3 \
        python3-dev \
        python3-pip \
        python3-venv \
    \
    && python3 -m venv .venv \
    && .venv/bin/pip3 install --no-cache-dir -U \
        setuptools \
        wheel \
    && .venv/bin/pip3 install --no-cache-dir \
        "wyoming[zeroconf]==${WYOMING_VERSION}" \
        "wyoming-faster-whisper[sherpa] @ https://github.com/rhasspy/wyoming-faster-whisper/archive/refs/tags/v${WYOMING_WHISPER_VERSION}.tar.gz" \
        "transformers==${TRANSFORMERS_VERSION}" \
        "onnx-asr[cpu,hub]==${ONNX_ASR_VERSION}" \
        "funasr==${FUNASR_VERSION}" \
        "onnxruntime==${ONNXRUNTIME_VERSION}" \
        "faster-whisper==${FASTER_WHISPER_VERSION}" \
        "ctranslate2==${CTRANSLATE2_VERSION}" \
        "sherpa-onnx==${SHERPA_ONNX_VERSION}" \
    \
    && .venv/bin/pip3 install --no-cache-dir \
        --index-url 'https://download.pytorch.org/whl/cpu' \
        "torch==${TORCH_VERSION}" \
        "torchaudio==${TORCH_VERSION}" \
    \
    && apt-get purge -y --auto-remove \
        build-essential \
        python3-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PATH="/usr/src/.venv/bin:$PATH"

ENV ORT_DISABLE_TELEMETRY=1
WORKDIR /
COPY rootfs /

HEALTHCHECK --start-period=10m --start-interval=5s --interval=30s \
    CMD echo '{ "type": "describe" }' \
        | nc -w 1 localhost 10300 \
        | grep -q "faster-whisper" \
        || exit 1

LABEL \
    org.opencontainers.image.title="Home Assistant App: Whisper" \
    org.opencontainers.image.description="Speech-to-text with Whisper" \
    org.opencontainers.image.source="https://github.com/home-assistant/app-whisper" \
    org.opencontainers.image.licenses="Apache License 2.0"
