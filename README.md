# Godzilla Alert Network (GAN)

A distributed monitoring and alert system designed to detect large anomalous disturbances on the sea surface and infer the possible position of a Godzilla-like entity.

## Table of Contents

- [Project Vision](#project-vision)
- [Architecture](#architecture)
- [Technologies](#technologies)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Usage Scenarios](#usage-scenarios)
- [Learning Goals](#learning-goals)
- [Contributors](#contributors)

## Project Vision

The Godzilla Alert Network (GAN) is a distributed monitoring and alert system designed to detect large anomalous disturbances on the sea surface and infer the possible position of a Godzilla-like entity.

In the assumed scenario, Godzilla is not a passive phenomenon but an active and potentially adversarial presence. The monitoring infrastructure itself must be considered exposed to failures caused by extreme events, including the destruction of sensing and processing nodes.

### Key Features

- Distributed data acquisition from multiple unreliable sensor nodes
- Fault tolerance and resilience to partial and total node failures
- Automatic leader election among elaboration nodes
- Horizontal scalability with dynamic sensor deployment
- Real-time monitoring through web dashboard
- Historical data analysis and system health inspection

## Architecture

The system is composed of three logical node types:

### Buoy Nodes

Distributed sensor nodes deployed over a maritime area. Each buoy periodically measures wave and sea-surface characteristics (simulated signals). New buoys can be deployed dynamically at runtime without requiring system reconfiguration.

Buoy nodes may experience intermittent connectivity and are designed to tolerate delays, temporary disconnections, or permanent destruction during detection events.

### Elaboration Nodes

A logically central but replicated and horizontally scalable processing layer that gathers observations from multiple buoy nodes.

At any time, a single elaboration node acts as leader, responsible for correlating spatial and temporal anomalies, aggregating data over time windows, and computing an estimated position.

Leadership is managed through a lease-based leader election mechanism, allowing standby elaboration nodes to automatically take over if the current leader becomes unavailable due to failures or destruction.

### Control Nodes

User-facing nodes providing a web interface to visualize real-time detections and historical data.

Users can inspect current alerts, browse past detection events, and observe the operational status of buoy nodes and processing components.

### System Diagram

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Buoy Node  │────▶│    MQTT     │◀────│  Buoy Node  │
│   (Buoy 1)  │     │   Broker    │     │   (Buoy N)  │
└─────────────┘     └──────┬──────┘     └─────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │  Elaboration Nodes     │
              │  (Leader Election)     │
              │  ┌──────┐   ┌──────┐   │
              │  │Leader│   │Standby│  │
              │  └──┬───┘   └──────┘   │
              └─────┼──────────────────┘
                    ▼
              ┌──────────┐
              │ MongoDB  │
              └────┬─────┘
                   ▼
         ┌──────────────────┐
         │   Control Node   │
         │  (Web Dashboard) │
         └──────────────────┘
```

## Technologies

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Backend | Node.js | Strong support for asynchronous, event-driven programming and suitability for building scalable distributed services |
| Database | MongoDB | Storage for sensor observations and detection events |
| Messaging | MQTT | Asynchronous communication between buoy nodes and backend services, enabling publish/subscribe interaction model and tolerating intermittent connectivity |
| Containerization | Docker | Reproducible deployment, service replication, and controlled simulation of distributed environments |
| User Interface | Tailwind CSS + shadcn | Rapid development of clean, consistent, and modern web interface suitable for dashboards and data exploration |

## Quick Start

### Prerequisites

- Docker >= 20.0.0
- Docker Compose >= 2.0.0
- Node.js >= 18.0.0 (for local development)

### Running the System

```bash
# Clone the repository
git clone https://github.com/yourusername/godzilla-alert-network.git
cd godzilla-alert-network

# Start all components
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f elaboration-node
```

### Accessing Services

- Control Dashboard: http://localhost:3000
- MQTT Broker: mqtt://localhost:1883
- MongoDB: mongodb://localhost:27017

### Testing the System

```bash
# Add new buoys dynamically
docker-compose up -d --scale buoy-simulator=5

# Simulate elaboration node failure
docker-compose stop elaboration-node-1

# Verify automatic leader election
docker-compose logs -f elaboration-node-2
```

## Project Structure

```
godzilla-alert-network/
├── buoy-node/              # Simulated sensor nodes
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── elaboration-node/       # Processing layer
│   ├── src/
│   │   ├── leader-election/
│   │   ├── detection-logic/
│   │   └── mqtt-subscriber/
│   ├── Dockerfile
│   └── package.json
├── control-node/           # Web dashboard
│   ├── src/
│   │   ├── api/
│   │   └── ui/
│   ├── Dockerfile
│   └── package.json
├── shared/                 # Shared contracts and utilities
│   ├── contracts.js
│   └── constants.js
├── docker-compose.yml
├── .env.example
└── README.md
```

## Usage Scenarios

### Real-time Monitoring

Buoy nodes continuously send observations to the system. When a significant anomaly is detected, the elaboration leader generates a Godzilla detection event, which is immediately displayed on the control dashboard.

### Historical and System Health Analysis

Users can browse past detections, filter events by time range or confidence level, and inspect the responsiveness and availability of system nodes, including those that may be temporarily unavailable or permanently destroyed.

### Fault Tolerance Demonstration

Some buoy nodes may become unavailable or be destroyed, and elaboration nodes may crash or be taken offline. The system continues operating via replicated elaboration nodes and automatic leader election, producing best-effort detections and updating results when additional data becomes available.

## Learning Goals

This project is highly relevant to the Distributed Systems course as it provides a concrete, end-to-end case study involving:

- Distributed data acquisition from multiple, unreliable and dynamically joining producers
- Event-driven architectures and asynchronous communication
- Handling partial failures and network delays
- Data aggregation and consistency in a distributed environment
- Horizontal scalability of processing components

Through this project, we expect to gain practical experience in designing, implementing, deploying, and reasoning about non-trivial distributed systems beyond purely theoretical models.

## Contributors

- Daniele Merighi - daniele.merighi2@studio.unibo.it
- Giacomo Ruscelli - giacomo.ruscelli2@studio.unibo.it

## License

This project is developed for educational purposes as part of the Distributed Systems course at the University of Bologna.

## Acknowledgments

Project developed for the Distributed Systems course, Academic Year 2025/2026.
