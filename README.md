# Monitor Stack

A lightweight, self-hosted monitoring solution built with Docker Compose. This stack includes:

* **Prometheus**: metrics collection and alerting system
* **Node Exporter**: host-level hardware and OS metrics
* **Grafana**: dashboards and visualization
* **Alertmanager**: alert routing and notifications
* **cAdvisor**: container resource usage monitoring

## Prerequisites

* [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) installed
* A mail server for Alertmanager notifications (see `alertmanager-example.yml`)

## Getting Started

1. Clone this repository:

   ```bash
   git clone https://github.com/Vantasin/Monitor.git
   cd monitor-stack
   ```
2. Copy and configure Alertmanager settings:

   ```bash
   cp alertmanager-example.yml alertmanager.yml
   # Edit alertmanager.yml with your SMTP credentials and alert recipients
   ```
3. (Optional) Tune Prometheus scrape settings and alert rules in `prometheus.yml` and `alert_rules.yml`:

   ```yaml
   # prometheus.yml
   rule_files:
     - "alert_rules.yml"  # Defines alert rules
   ```
4. Launch the stack:

   ```bash
   docker-compose up -d
   ```

## Accessing the UIs

* **Prometheus**:  [http://localhost:9090/](http://localhost:9090/)
* **Grafana**:     [http://localhost:3030/](http://localhost:3030/) (default credentials: `admin` / `admin`)
* **Alertmanager**: [http://localhost:9093/](http://localhost:9093/)
* **cAdvisor**:    [http://localhost:8088/](http://localhost:8088/)
* **Node Exporter**: [http://localhost:9100/](http://localhost:9100/)

## Configuration Details

* **Prometheus** configuration: `prometheus.yml`
* **Alert rules**: `alert_rules.yml`
* **Alertmanager** config: `alertmanager.yml` (from example)
* **Grafana data** is persisted in the `grafana-storage` volume defined in `docker-compose.yml`

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for improvements or fixes.

## License

MIT © 2025 Vantasin
