FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV GF_PATHS_PROVISIONING=/etc/grafana/provisioning

# Install dependencies
RUN apt-get update && apt-get install -y \
    supervisor \
    wget \
    curl \
    adduser \
    libfontconfig1 \
    musl \
    && rm -rf /var/lib/apt/lists/*

# Install Prometheus
ARG PROMETHEUS_VERSION=2.52.0
RUN wget https://github.com/prometheus/prometheus/releases/download/v${PROMETHEUS_VERSION}/prometheus-${PROMETHEUS_VERSION}.linux-amd64.tar.gz \
    && tar xvf prometheus-${PROMETHEUS_VERSION}.linux-amd64.tar.gz \
    && cp prometheus-${PROMETHEUS_VERSION}.linux-amd64/prometheus /usr/local/bin/ \
    && cp prometheus-${PROMETHEUS_VERSION}.linux-amd64/promtool /usr/local/bin/ \
    && mkdir -p /etc/prometheus /prometheus \
    && cp -r prometheus-${PROMETHEUS_VERSION}.linux-amd64/consoles /etc/prometheus/ \
    && cp -r prometheus-${PROMETHEUS_VERSION}.linux-amd64/console_libraries /etc/prometheus/ \
    && rm -rf prometheus-${PROMETHEUS_VERSION}.linux-amd64*

# Install Grafana
ARG GRAFANA_VERSION=11.0.0
RUN wget https://dl.grafana.com/oss/release/grafana_${GRAFANA_VERSION}_amd64.deb \
    && dpkg -i grafana_${GRAFANA_VERSION}_amd64.deb \
    && rm grafana_${GRAFANA_VERSION}_amd64.deb

# Copy configs
COPY prometheus.yml /etc/prometheus/prometheus.yml
COPY grafana/provisioning /etc/grafana/provisioning
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Create Grafana dashboard directory and copy dashboards
RUN mkdir -p /var/lib/grafana/dashboards
COPY grafana/provisioning/dashboards/*.json /var/lib/grafana/dashboards/

# Expose ports
# 9090 = Prometheus, 3000 = Grafana
EXPOSE 9090 3000

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
