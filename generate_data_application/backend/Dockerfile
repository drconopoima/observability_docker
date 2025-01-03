FROM docker.io/library/python:3.10.16-bullseye
RUN apt-get update && apt-get install --no-install-recommends -y systemctl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /usr/local/bin/

# Configure Promtail to send logs to Grafana Agent
RUN /bin/bash -c 'ARCH=`uname -m` && \
    if [ "$ARCH" == "x86_64" ]; then \
        curl -fsSL -o promtail-linux-amd64.zip https://github.com/grafana/loki/releases/download/v2.9.11/promtail-linux-amd64.zip && \
        unzip promtail-linux-amd64.zip && \
        mv ./promtail-linux-amd64 ./promtail; \
    elif [ "$ARCH" == "arm64" ]; then \
        curl -fsSL -o promtail-linux-arm64.zip https://github.com/grafana/loki/releases/download/v2.9.11/promtail-linux-amd64.zip && \
        unzip promtail-linux-arm64.zip && \
        mv ./promtail-linux-arm64 ./promtail; \
    elif [ "$ARCH" == "arm" ]; then \
        curl -fsSL -o promtail-linux-arm.zip  https://github.com/grafana/loki/releases/download/v2.9.11/promtail-linux-arm.zip && \
        unzip promtail-linux-arm.zip && \
        mv ./promtail-linux-arm ./promtail; \
    else \
        curl -fsSL -o promtail-linux-amd64.zip https://github.com/grafana/loki/releases/download/v2.9.11/promtail-linux-amd64.zip && \
        unzip promtail-linux-amd64.zip && \
        mv ./promtail-linux-amd64 ./promtail; \
    fi'
COPY config/promtail-config.yaml /etc/promtail/
RUN useradd --system promtail
COPY config/promtail.service /etc/systemd/system/

# Configure FastAPI application
WORKDIR /usr/src/app
RUN chmod a+rwx /usr/src/app
COPY config/requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY app .
COPY config/start.sh ./config/
EXPOSE 5050
CMD ["/bin/sh", "./config/start.sh"]
