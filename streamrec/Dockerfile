FROM python:3.12-slim

# Add a non-root user
RUN useradd -ms /bin/bash appuser \
    && mkdir -p /app /recordings \
    && chown -R appuser:appuser /app /recordings

# Install necessary system packages and yt-dlp in one layer
RUN apt-get update && apt-get install -y ffmpeg curl \
    && rm -rf /var/lib/apt/lists/* \
    && curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp \
    && chmod a+rx /usr/local/bin/yt-dlp

WORKDIR /app

# Copy dependency file and install Python packages
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application files
COPY main.py .
COPY index.html .

USER appuser

# Expose port and use environment variable for flexibility
ENV PORT=8080
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]