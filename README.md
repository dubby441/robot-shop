# Stan's Robot Shop — DevOps Capstone 2

## Project Overview

This project deploys **Stan's Robot Shop**, a microservices-based e-commerce application, on Amazon EKS and adds monitoring, centralized logging, alerting, and incident-response testing.

The project builds on the Kubernetes and cloud infrastructure skills developed in Capstone 1, with a focus on operational visibility and service reliability.

## Architecture

* **Application:** Stan's Robot Shop microservices deployed to Kubernetes.
* **Container orchestration:** Amazon EKS.
* **Metrics and monitoring:** Prometheus and Grafana.
* **Centralized logging:** Fluent Bit and Loki.
* **Alerting:** Grafana alert rule for Cart service availability.
* **Incident response:** Simulated Cart service outage and recovery.

### Technology Stack

| Area            | Technologies                           |
| --------------- | -------------------------------------- |
| Cloud           | AWS                                    |
| Containers      | Docker                                 |
| Orchestration   | Kubernetes, Amazon EKS                 |
| Deployment      | Helm                                   |
| Infrastructure  | Terraform, existing EKS infrastructure |
| Monitoring      | Prometheus, Grafana                    |
| Logging         | Fluent Bit, Loki                       |
| Application     | Stan's Robot Shop microservices        |
| Version control | Git, GitHub                            |

## Implementation

### 1. Application Deployment

Deployed Stan's Robot Shop to the existing EKS cluster in the `eu-north-1` AWS region.

The application includes microservices such as Web, Catalogue, Cart, Payment, User, Shipping, and Dispatch, alongside supporting data stores and messaging services.

The application was accessed through its Kubernetes LoadBalancer endpoint.

### 2. Monitoring with Prometheus and Grafana

Deployed the kube-prometheus-stack to collect and expose monitoring metrics.

Configured a Cart service monitor and verified that Prometheus successfully scraped the Cart metrics endpoint.

Created a Grafana dashboard visualization using the `items_added` metric to demonstrate application-level monitoring.

### 3. Centralized Logging with Fluent Bit and Loki

Deployed Fluent Bit to collect Kubernetes container logs and forward them to Loki.

Configured Grafana to query Loki and verified that application logs could be searched through Grafana Explore.

This provides a centralized location for investigating application behavior and troubleshooting service issues.

### 4. Alerting and Reliability

Created a Grafana alert rule named `RobotShopCartUnavailable` to detect when the Cart service becomes unavailable.

The alert evaluates the Prometheus query:

```promql
up{job="cart"}
```

The alert is configured to trigger when the Cart target is unavailable.

**Notification limitation:** The alert rule was tested in Grafana, but external email or Slack notification delivery was not completed.

### 5. Incident Drill

Performed a controlled availability test by scaling the Cart deployment to zero replicas:

```bash
kubectl scale deployment cart -n robot-shop --replicas=0
```

Grafana detected the simulated outage and the alert entered the Firing state.

Restored the Cart deployment:

```bash
kubectl scale deployment cart -n robot-shop --replicas=1

kubectl rollout status deployment/cart -n robot-shop --timeout=120s
```

Verified recovery:

```bash
kubectl get deployment cart -n robot-shop
```

The deployment returned to `1/1` ready, confirming that the Cart service recovered.

This exercise demonstrated how monitoring and alerting can support incident detection and service recovery.

## Service-Level Objective (SLO)

The project uses a proposed **99% Cart availability target** over a rolling 30-day period.

For a 30-day period:

* **SLO:** 99% availability.
* **Error budget:** 1% unavailability, equivalent to 7 hours and 12 minutes.

The 30-day availability result has not been measured, so this is a defined target rather than a verified compliance result.

## Screenshots and Evidence

Screenshots captured during implementation should be added to this repository to demonstrate:

1. Robot Shop running in the browser.
2. Grafana dashboard displaying the `items_added` metric.
3. Application logs queried through Grafana Explore and Loki.
4. Grafana alert in the Firing state during the Cart incident drill.
5. Cart deployment restored to `1/1` ready.

Suggested folder structure:

```text
docs/
└── screenshots/
    ├── robot-shop-application.png
    ├── grafana-dashboard.png
    ├── loki-logs.png
    ├── cart-alert-firing.png
    └── cart-recovery.png
```

Update the filenames to match the screenshots you actually save.

## Outstanding Improvements

The following work remains outstanding:

* Configure OpenTelemetry instrumentation and demonstrate a trace in Jaeger.
* Configure and verify external Slack or email alert delivery.
* Collect sufficient monitoring history to evaluate the 30-day availability SLO.
* Extend dashboard coverage to include request rate, errors, and latency across the key application services.

## Key Learnings

* Deploying and operating a microservices application on Amazon EKS.
* Using Prometheus and Grafana for application monitoring.
* Centralizing Kubernetes logs with Fluent Bit and Loki.
* Creating availability alerts based on Prometheus metrics.
* Simulating a service outage and verifying recovery.
* Defining an availability objective and theoretical error budget.

## Repository

Source code and deployment configuration: [github.com/dubby441/robot-shop](https://github.com/dubby441/robot-shop)

---

**Project status:** Application deployment, metrics visualization, centralized logging, Cart alert configuration, and the incident drill were completed. Distributed tracing, external alert delivery, and measured SLO compliance remain outstanding.


