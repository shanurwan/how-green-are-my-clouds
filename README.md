# Carbon Footprint Tracker for Cloud Services

A cloud-native platform to measure, visualize, and reduce the carbon footprint of workloads running on AWS, GCP, or other cloud providers.

This project estimates energy consumption (kWh) and emissions (kgCO₂e) based on cloud usage data, data center efficiency (PUE), and real-time grid carbon intensity. It provides actionable insights for engineers and organizations to optimize costs and emissions.

## Features

1. Multi-cloud usage ingestion

    - GCP Billing export → BigQuery

    - AWS Cost & Usage Reports (CUR) via S3 + Athena

2. Energy & emissions estimation

    - Based on the Cloud Carbon Footprint methodology (usage → kWh → PUE → CO₂e)

    - Adjustable factors per service (compute, storage, network)

3. Grid carbon intensity integration

    - Real-time and historical data via [ElectricityMaps](https://www.electricitymaps.com/)/ [WattTime](https://watttime.org/) APIs

4. Dashboards & observability

    - Grafana dashboards for CO₂e by service, region, and trend

    - Prometheus metrics and alerting

5. Actionable insights

    - Identify idle or oversized resources

    - Recommend greener regions or scheduling in cleaner energy windows

    - Track progress against defined SLOs

6. Platform-ready

    - Infrastructure-as-Code with Terraform

    - GitOps deployment with Flux/Helm

    - Secure API key management with Kubernetes secrets



## Architecture

``` 

          +--------------------------+
Cloud →   | Usage Ingestor (AWS/GCP) | → usage_raw
          +--------------------------+
                         |
          +--------------------------+
          | Estimator Job            | → estimates (kWh, CO₂e)
          | - Cloud Carbon Footprint |
          | - Grid intensity lookup  |
          +--------------------------+
                         |
          +--------------------------+
          | Postgres / TimescaleDB   |
          +--------------------------+
              |                |
      +---------------+   +-----------+
      | Prometheus    |   | API / UI  |
      | + Grafana     |   | (Incoming)|
      +---------------+   +-----------+

``` 

## Prerequisites

- Terraform

- kubectl

- Helm

- Docker

- Cloud account (AWS/GCP) with billing exports enabled

- API key for ElectricityMaps or WattTime

## Deployment

```bash

# Clone repo
git clone https://github.com/yourname/cloud-carbon-tracker.git
cd cloud-carbon-tracker

# Provision infra
terraform init
terraform apply

# Deploy to cluster
kubectl apply -f k8s/

``` 

Access Grafana at http://localhost:3000 (default creds: admin/admin)

## Dashboards

- CO₂e by provider/service/region

- Top 10 emitting resources

- Carbon intensity vs workload over time

- What-if scenarios: moving regions, shifting schedules


## Methodology

1. Usage → Energy: Convert usage metrics (vCPU hours, GB storage, GB network) into kWh using published conversion factors.

2. Apply PUE: Multiply by Power Usage Effectiveness to account for data center overhead.

3. Energy → CO₂e: Multiply kWh by grid carbon intensity (gCO₂e/kWh) for the given region/time.

4. Aggregate: Store per-service, per-region, per-hour estimates for dashboards & analysis.


## References:

[Cloud Carbon Footprint methodology](https://www.cloudcarbonfootprint.org/docs/methodology/)

[Google Cloud Carbon Footprint](https://cloud.google.com/carbon-footprint?hl=en)

[AWS Customer Carbon Footprint Tool](https://aws.amazon.com/blogs/aws/new-customer-carbon-footprint-tool/)



## Upcoming upgrade

-  Add Azure usage ingestion

-  Export reports to CSV/BigQuery for ESG teams

-  Policy-as-code checks (block non-green regions in CI/CD)

-  Carbon-aware workload scheduling (batch jobs in low-carbon windows)


## Contributing

Pull requests are welcome! For major changes, please open an issue first to discuss your ideas.


## License

MIT License – feel free to fork, adapt, and improve.


## Acknowledgements

[Cloud Carbon Footprint](https://www.cloudcarbonfootprint.org/) for open methodology

[ElectricityMaps](https://www.electricitymaps.com/) and [WattTime](https://watttime.org/) for carbon intensity APIs

