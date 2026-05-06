# Smart Delivery Dispatch System

## Team Information
- **Team Name**: [codezilla bmsce]
- **Year**: [2nd]
- **All-Female Team**: [yes]

## Architecture Overview

Our system uses a **priority-first, score-based dispatch strategy**. Orders are stored in a min-heap priority queue ordered by priority level (high → normal → low) and timestamp (FIFO within same priority). For each pending order, the system identifies all available agents (those with fewer than 2 active orders) that have a valid path to the order location using precomputed Floyd-Warshall shortest paths. Each candidate agent is scored across five weighted criteria: (1) delivery time — total travel + prep time, lower is better; (2) SLA risk — urgency based on time remaining until deadline; (3) workload fairness — agents with fewer cumulative assignments are preferred; (4) priority boost — high-priority orders receive a scoring advantage; (5) agent rating — higher-rated agents are favored. Scores are combined using configurable weights from `config/config.ini`. The highest-scoring agent is assigned; ties are broken deterministically by agent ID. If no agents are available, the order remains PENDING and is retried automatically after any agent completes a delivery. Orders transition through four states: PENDING → ASSIGNED → IN_TRANSIT → DELIVERED. Metrics (delivery time via Welford's algorithm, SLA compliance, workload fairness) are tracked incrementally and exported to JSON after all orders are processed.

