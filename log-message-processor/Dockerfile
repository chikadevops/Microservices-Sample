FROM python:3.6-slim

# Install system dependencies
RUN apt-get update && \
    apt-get install -y gcc python3-dev build-essential libpython3-dev && \
    apt-get clean

WORKDIR /app

# Copy requirements and install
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy app files
COPY . .

CMD ["python", "main.py"]
