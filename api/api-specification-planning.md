# KaiABC Circadian Oscillator API Specification

**Version:** 1.0.0  
**Date:** October 6, 2025  
**Architecture:** Client-Server Model with RESTful and WebSocket APIs

---

## Table of Contents

1. [Overview](#overview)
2. [Architecture Design](#architecture-design)
3. [API Endpoints](#api-endpoints)
4. [WebSocket Events](#websocket-events)
5. [Data Models](#data-models)
6. [Authentication & Security](#authentication--security)
7. [Error Handling](#error-handling)
8. [Rate Limiting](#rate-limiting)

---

## Overview

### Purpose

This API specification defines the interface for the KaiABC Circadian Oscillator system, transitioning from a standalone embedded architecture to a distributed client-server model. The API enables:

- Real-time monitoring of circadian oscillator state
- Remote sensor data ingestion from multiple ESP32 nodes
- Centralized ODE computation and entrainment control
- Multi-client visualization and parameter management
- Historical data logging and analysis

### Design Principles

1. **Real-Time Performance:** WebSocket support for sub-100ms state updates
2. **Modularity:** Separate endpoints for sensors, simulation, and control
3. **Scalability:** Support multiple sensor nodes and client applications
4. **Fault Tolerance:** Graceful degradation when network connectivity fails
5. **Scientific Fidelity:** Preserve biological accuracy of the oscillator model

---

## Architecture Design

### System Components

```
┌──────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│ RPi Pico / ELM11 │────────▶│  KaiABC Server   │◀────────│  Web Dashboard  │
│  (Sensor Node)   │  MQTT/  │  (Computation)   │  HTTP/  │   (Monitor)     │
│  - BME280        │  HTTP   │  - ODE Solver    │  WS     │  - Visualization│
│  - Local PWM     │         │  - Kalman Filter │         │  - Control      │
│  - MicroPython/  │         │  - ETF Engine    │         └─────────────────┘
│    Lua Runtime   │         │  - State DB      │
└──────────────────┘         └──────────────────┘
                                     │
                            ┌────────▼────────┐
                            │  Time Series DB │
                            │  (InfluxDB/     │
                            │   TimescaleDB)  │
                            └─────────────────┘
```

### Communication Protocols

- **HTTP/REST:** Configuration, queries, and non-time-critical operations
- **WebSocket:** Real-time state streaming and low-latency control
- **MQTT:** Lightweight sensor data ingestion from ESP32 nodes (optional)

---

## API Endpoints

### Base URL

```
Production:  https://api.kaiabc.example.com/v1
Development: http://localhost:8000/v1
```

---

## 1. System Status & Health

### `GET /health`

Health check endpoint for monitoring service availability.

**Response:**
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "uptime_seconds": 86400,
  "timestamp": "2025-10-06T12:00:00Z",
  "components": {
    "ode_solver": "operational",
    "database": "operational",
    "sensor_nodes": {
      "connected": 3,
      "total": 5
    }
  }
}
```

---

## 2. Oscillator State Management

### `GET /oscillator/state`

Retrieve the current state of the circadian oscillator.

**Query Parameters:**
- `node_id` (optional): Filter by specific sensor node
- `detail_level`: `minimal` | `standard` | `full` (default: `standard`)

**Response:**
```json
{
  "timestamp": "2025-10-06T12:00:00Z",
  "circadian_time": 14.5,
  "phase": 0.604,
  "period_hours": 24.1,
  "state_variables": {
    "C_U": 0.45,
    "C_S": 0.15,
    "C_T": 0.08,
    "C_ST": 0.32,
    "A_free": 0.58,
    "CABC_complex": 0.22
  },
  "koa_metric": 0.67,
  "output_pwm": 170,
  "entrainment_active": true,
  "temperature_kelvin": 298.5
}
```

### `POST /oscillator/reset`

Reset the oscillator to initial conditions.

**Request Body:**
```json
{
  "node_id": "pico_001",
  "initial_conditions": {
    "C_U": 0.5,
    "C_S": 0.0,
    "C_T": 0.0,
    "C_ST": 0.0,
    "A_free": 1.0,
    "CABC_complex": 0.0
  },
  "reason": "manual_reset"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Oscillator reset to initial conditions",
  "node_id": "pico_001",
  "timestamp": "2025-10-06T12:00:00Z"
}
```

### `GET /oscillator/history`

Retrieve historical oscillator state data.

**Query Parameters:**
- `start_time`: ISO 8601 timestamp
- `end_time`: ISO 8601 timestamp
- `resolution`: `1s` | `10s` | `1m` | `10m` | `1h` (default: `1m`)
- `variables`: Comma-separated list (default: all)
- `node_id` (optional): Filter by node

**Response:**
```json
{
  "node_id": "esp32_001",
  "start_time": "2025-10-05T00:00:00Z",
  "end_time": "2025-10-06T00:00:00Z",
  "resolution": "1m",
  "data_points": 1440,
  "series": {
    "timestamp": ["2025-10-05T00:00:00Z", "2025-10-05T00:01:00Z", "..."],
    "C_ST": [0.32, 0.33, "..."],
    "koa_metric": [0.67, 0.68, "..."],
    "temperature_kelvin": [298.5, 298.6, "..."]
  }
}
```

---

## 3. Sensor Data Ingestion

### `POST /sensors/temperature`

Submit temperature sensor reading from ESP32 node.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:00:00.123Z",
  "temperature_kelvin": 298.65,
  "raw_value": 25.5,
  "sensor_type": "BME280",
  "quality": "good"
}
```

**Response:**
```json
{
  "accepted": true,
  "filtered_value": 298.60,
  "kalman_variance": 0.02,
  "message": "Sensor data processed"
}
```

### `POST /sensors/batch`

Submit batch sensor readings (temperature, humidity, pressure).

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "readings": [
    {
      "timestamp": "2025-10-06T12:00:00.000Z",
      "temperature_kelvin": 298.65,
      "humidity_percent": 45.2,
      "pressure_pa": 101325
    },
    {
      "timestamp": "2025-10-06T12:00:01.000Z",
      "temperature_kelvin": 298.66,
      "humidity_percent": 45.3,
      "pressure_pa": 101326
    }
  ]
}
```

**Response:**
```json
{
  "accepted": 2,
  "rejected": 0,
  "latest_filtered": {
    "temperature_kelvin": 298.65,
    "humidity_percent": 45.25,
    "pressure_pa": 101325.5
  }
}
```

### `GET /sensors/nodes`

List all registered sensor nodes.

**Response:**
```json
{
  "nodes": [
    {
      "node_id": "pico_001",
      "name": "Lab Incubator A",
      "hardware_type": "raspberry_pi_pico",
      "runtime": "micropython",
      "status": "online",
      "last_seen": "2025-10-06T12:00:00Z",
      "sensor_types": ["temperature", "humidity", "pressure"],
      "firmware_version": "1.2.3",
      "location": "Building 3, Room 201"
    },
    {
      "node_id": "elm11_002",
      "name": "Field Station B",
      "hardware_type": "elm11",
      "runtime": "lua",
      "status": "offline",
      "last_seen": "2025-10-06T10:30:00Z",
      "sensor_types": ["temperature"],
      "firmware_version": "1.2.1",
      "location": "Outdoor Site 1"
    }
  ]
}
```

### `POST /sensors/nodes`

Register a new sensor node.

**Request Body:**
```json
{
  "node_id": "pico_003",
  "name": "Growth Chamber C",
  "hardware_type": "raspberry_pi_pico" | "elm11",
  "runtime": "micropython" | "lua",
  "sensor_types": ["temperature", "humidity", "pressure", "light"],
  "location": "Building 2, Room 105",
  "calibration": {
    "temperature_offset": 0.0,
    "humidity_offset": 0.0
  }
}
```

**Response:**
```json
{
  "success": true,
  "node_id": "pico_003",
  "hardware_type": "raspberry_pi_pico",
  "api_key": "kaiabc_node_abc123xyz789",
  "mqtt_topic": "kaiabc/sensors/pico_003"
}
```

---

## 4. Entrainment Control

### `GET /entrainment/parameters`

Retrieve current entrainment transfer function parameters.

**Query Parameters:**
- `node_id` (optional): Filter by node

**Response:**
```json
{
  "node_id": "esp32_001",
  "etf_active": true,
  "reference_temperature_kelvin": 298.15,
  "parameters": {
    "arrhenius_scaling": {
      "activation_energy_kJ_mol": 40.0,
      "pre_exponential_factor": 1.5e8
    },
    "structural_coupling": {
      "baseline_d_i": 0.85,
      "modulation_function": "nonlinear_sigmoid",
      "sensitivity": 1.2
    },
    "atpase_constraint": {
      "baseline_f_hyd": 0.5,
      "temperature_dependence": "case_ii_mutant",
      "coupling_strength": 0.9
    }
  },
  "prc_model": "standard",
  "phase_lock_target": null
}
```

### `PUT /entrainment/parameters`

Update entrainment transfer function parameters.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "parameters": {
    "structural_coupling": {
      "sensitivity": 1.5
    }
  },
  "reason": "Increase entrainment responsiveness"
}
```

**Response:**
```json
{
  "success": true,
  "message": "ETF parameters updated",
  "effective_timestamp": "2025-10-06T12:00:01Z",
  "previous_parameters": { "...": "..." },
  "new_parameters": { "...": "..." }
}
```

### `POST /entrainment/phase-shift`

Trigger a manual phase shift using PRC logic.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "shift_type": "advance" | "delay",
  "magnitude_hours": 2.0,
  "method": "temperature_step",
  "temperature_delta_kelvin": 3.0,
  "duration_minutes": 30
}
```

**Response:**
```json
{
  "success": true,
  "shift_id": "shift_20251006_001",
  "expected_phase_change": 1.8,
  "current_circadian_time": 14.5,
  "optimal_ct_window": "16-22",
  "estimated_completion": "2025-10-06T12:30:00Z"
}
```

### `GET /entrainment/prc`

Retrieve the Phase Response Curve data.

**Query Parameters:**
- `model`: `standard` | `custom` (default: `standard`)
- `resolution`: Number of CT points (default: 24)

**Response:**
```json
{
  "model": "standard",
  "reference": "Rust et al. 2007",
  "resolution": 24,
  "data": [
    {"circadian_time": 0, "phase_shift_advance": 0.0, "phase_shift_delay": 0.0},
    {"circadian_time": 1, "phase_shift_advance": 0.1, "phase_shift_delay": -0.05},
    {"circadian_time": 16, "phase_shift_advance": 1.8, "phase_shift_delay": -1.2},
    {"circadian_time": 22, "phase_shift_advance": 1.5, "phase_shift_delay": -1.5}
  ]
}
```

---

## 5. Simulation Control

### `GET /simulation/config`

Retrieve current ODE solver configuration.

**Response:**
```json
{
  "solver_type": "RKF45",
  "tolerance": {
    "absolute": 1e-6,
    "relative": 1e-5
  },
  "max_step_size": 0.1,
  "min_step_size": 1e-6,
  "integration_method": "adaptive",
  "performance_metrics": {
    "average_steps_per_second": 8500,
    "average_iteration_time_ms": 45,
    "stability_index": 0.98
  }
}
```

### `PUT /simulation/config`

Update solver configuration parameters.

**Request Body:**
```json
{
  "tolerance": {
    "absolute": 1e-7,
    "relative": 1e-6
  },
  "max_step_size": 0.05
}
```

**Response:**
```json
{
  "success": true,
  "message": "Solver configuration updated",
  "restart_required": false
}
```

### `POST /simulation/pause`

Pause the oscillator simulation.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "reason": "maintenance"
}
```

**Response:**
```json
{
  "success": true,
  "state_saved": true,
  "paused_at_ct": 14.5
}
```

### `POST /simulation/resume`

Resume a paused simulation.

**Request Body:**
```json
{
  "node_id": "esp32_001"
}
```

**Response:**
```json
{
  "success": true,
  "resumed_from_ct": 14.5,
  "timestamp": "2025-10-06T12:05:00Z"
}
```

---

## 6. Output Control

### `GET /output/koa`

Retrieve current KOA (Kai-complex Output Activity) metric.

**Query Parameters:**
- `node_id` (optional): Filter by node

**Response:**
```json
{
  "node_id": "esp32_001",
  "koa_value": 0.67,
  "koa_normalized": 0.67,
  "koa_phase": "ascending",
  "contributing_states": {
    "C_ST": 0.32,
    "C_S": 0.15
  },
  "pwm_mapping": {
    "duty_cycle": 170,
    "duty_cycle_percent": 66.7,
    "frequency_hz": 1000
  }
}
```

### `PUT /output/mapping`

Configure the KOA to PWM output mapping.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "mapping_function": "linear" | "sigmoid" | "custom",
  "pwm_min": 0,
  "pwm_max": 255,
  "koa_threshold_low": 0.1,
  "koa_threshold_high": 0.9,
  "invert": false
}
```

**Response:**
```json
{
  "success": true,
  "message": "Output mapping updated",
  "test_values": {
    "koa_0.0": {"pwm": 0},
    "koa_0.5": {"pwm": 128},
    "koa_1.0": {"pwm": 255}
  }
}
```

### `POST /output/manual-override`

Manually override the output signal.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "override_enabled": true,
  "pwm_value": 200,
  "duration_minutes": 60,
  "reason": "Testing LED response"
}
```

**Response:**
```json
{
  "success": true,
  "override_id": "override_20251006_001",
  "expires_at": "2025-10-06T13:00:00Z"
}
```

---

## 7. Kalman Filter Management

### `GET /filter/state`

Retrieve current Kalman filter state.

**Query Parameters:**
- `node_id`: Sensor node identifier

**Response:**
```json
{
  "node_id": "esp32_001",
  "filter_type": "extended_kalman",
  "state_estimate": {
    "temperature_kelvin": 298.60,
    "humidity_percent": 45.25,
    "pressure_pa": 101325.5
  },
  "covariance_matrix": [
    [0.02, 0.001, 0.0],
    [0.001, 0.05, 0.0],
    [0.0, 0.0, 0.1]
  ],
  "innovation": {
    "temperature": 0.05,
    "humidity": 0.1,
    "pressure": 0.5
  },
  "measurement_count": 8642
}
```

### `PUT /filter/parameters`

Update Kalman filter parameters.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "process_noise": {
    "temperature": 0.01,
    "humidity": 0.02,
    "pressure": 0.05
  },
  "measurement_noise": {
    "temperature": 0.05,
    "humidity": 0.1,
    "pressure": 0.2
  }
}
```

**Response:**
```json
{
  "success": true,
  "message": "Kalman filter parameters updated",
  "filter_reset": false
}
```

### `POST /filter/reset`

Reset the Kalman filter to initial state.

**Request Body:**
```json
{
  "node_id": "esp32_001",
  "reason": "Sensor recalibration"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Kalman filter reset",
  "timestamp": "2025-10-06T12:00:00Z"
}
```

---

## 8. Analytics & Reporting

### `GET /analytics/period-stability`

Analyze period stability over time.

**Query Parameters:**
- `node_id`: Sensor node identifier
- `days`: Number of days to analyze (default: 7)

**Response:**
```json
{
  "node_id": "esp32_001",
  "analysis_period": {
    "start": "2025-09-29T00:00:00Z",
    "end": "2025-10-06T00:00:00Z"
  },
  "statistics": {
    "mean_period_hours": 24.08,
    "std_deviation_hours": 0.15,
    "min_period_hours": 23.85,
    "max_period_hours": 24.32,
    "q10_value": 1.06,
    "coefficient_variation": 0.006
  },
  "temperature_correlation": {
    "pearson_r": 0.08,
    "p_value": 0.45
  },
  "quality_assessment": "excellent"
}
```

### `GET /analytics/entrainment-efficiency`

Evaluate entrainment response efficiency.

**Query Parameters:**
- `node_id`: Sensor node identifier
- `shift_id` (optional): Specific phase shift event

**Response:**
```json
{
  "node_id": "esp32_001",
  "recent_shifts": [
    {
      "shift_id": "shift_20251005_001",
      "timestamp": "2025-10-05T08:00:00Z",
      "type": "advance",
      "target_shift_hours": 2.0,
      "actual_shift_hours": 1.8,
      "efficiency": 0.90,
      "settling_time_hours": 4.5,
      "temperature_delta_applied": 3.0
    }
  ],
  "average_efficiency": 0.88,
  "recommendations": [
    "Consider increasing structural coupling sensitivity to 1.8 for better response"
  ]
}
```

### `GET /analytics/export`

Export comprehensive data for external analysis.

**Query Parameters:**
- `node_id`: Sensor node identifier
- `start_time`: ISO 8601 timestamp
- `end_time`: ISO 8601 timestamp
- `format`: `json` | `csv` | `hdf5` (default: `json`)
- `variables`: Comma-separated list (default: all)

**Response:**
- For JSON/CSV: Direct download
- For HDF5: Pre-signed URL to download location

```json
{
  "export_id": "export_20251006_001",
  "status": "completed",
  "download_url": "https://api.kaiabc.example.com/v1/downloads/export_20251006_001.hdf5",
  "expires_at": "2025-10-07T12:00:00Z",
  "size_bytes": 15728640,
  "record_count": 86400
}
```

---

## WebSocket Events

### Connection

**Endpoint:** `wss://api.kaiabc.example.com/v1/ws`

**Authentication:** JWT token in query parameter or header

```
wss://api.kaiabc.example.com/v1/ws?token=eyJhbGc...
```

### Subscription Model

Clients subscribe to specific event streams:

```json
{
  "action": "subscribe",
  "streams": [
    "oscillator.state.esp32_001",
    "sensors.temperature.esp32_001",
    "output.koa.esp32_001"
  ]
}
```

### Event: `oscillator.state`

Real-time oscillator state updates (typically every 1-10 seconds).

```json
{
  "event": "oscillator.state",
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:00:00.123Z",
  "data": {
    "circadian_time": 14.52,
    "phase": 0.605,
    "state_variables": {
      "C_ST": 0.32,
      "A_free": 0.58
    },
    "koa_metric": 0.67
  }
}
```

### Event: `sensors.reading`

Real-time sensor data after Kalman filtering.

```json
{
  "event": "sensors.reading",
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:00:00.123Z",
  "data": {
    "temperature_kelvin": 298.60,
    "temperature_raw": 298.65,
    "kalman_variance": 0.02,
    "quality": "good"
  }
}
```

### Event: `output.pwm`

PWM output changes.

```json
{
  "event": "output.pwm",
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:00:00.123Z",
  "data": {
    "duty_cycle": 170,
    "duty_cycle_percent": 66.7,
    "koa_source": 0.67,
    "manual_override": false
  }
}
```

### Event: `alert`

System alerts and warnings.

```json
{
  "event": "alert",
  "severity": "warning",
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:00:00.123Z",
  "message": "Integration error exceeded tolerance threshold",
  "code": "ODE_STABILITY_WARNING",
  "details": {
    "error_magnitude": 1.5e-5,
    "tolerance": 1.0e-5,
    "action_taken": "step_size_reduced"
  }
}
```

### Event: `phase_shift.complete`

Phase shift operation completed.

```json
{
  "event": "phase_shift.complete",
  "shift_id": "shift_20251006_001",
  "node_id": "esp32_001",
  "timestamp": "2025-10-06T12:30:00Z",
  "data": {
    "target_shift_hours": 2.0,
    "actual_shift_hours": 1.8,
    "efficiency": 0.90,
    "duration_minutes": 30
  }
}
```

---

## Data Models

### OscillatorState

```typescript
interface OscillatorState {
  timestamp: string;              // ISO 8601
  circadian_time: number;         // 0-24 hours
  phase: number;                  // 0-1 (fractional cycle)
  period_hours: number;           // Current period estimate
  state_variables: {
    C_U: number;                  // Unphosphorylated KaiC
    C_S: number;                  // Singly phosphorylated
    C_T: number;                  // Threonine phosphorylated
    C_ST: number;                 // Doubly phosphorylated
    A_free: number;               // Free KaiA
    CABC_complex: number;         // Sequestration complex
  };
  koa_metric: number;             // 0-1
  output_pwm: number;             // 0-255
  entrainment_active: boolean;
  temperature_kelvin: number;
}
```

### SensorReading

```typescript
interface SensorReading {
  node_id: string;
  timestamp: string;
  temperature_kelvin?: number;
  humidity_percent?: number;
  pressure_pa?: number;
  light_lux?: number;
  sensor_type: string;
  quality: 'good' | 'fair' | 'poor' | 'invalid';
  raw_values?: {
    temperature?: number;
    humidity?: number;
    pressure?: number;
  };
}
```

### ETFParameters

```typescript
interface ETFParameters {
  arrhenius_scaling: {
    activation_energy_kJ_mol: number;
    pre_exponential_factor: number;
  };
  structural_coupling: {
    baseline_d_i: number;
    modulation_function: 'linear' | 'nonlinear_sigmoid' | 'exponential';
    sensitivity: number;
  };
  atpase_constraint: {
    baseline_f_hyd: number;
    temperature_dependence: 'compensated' | 'case_ii_mutant';
    coupling_strength: number;
  };
}
```

### PhaseShiftRequest

```typescript
interface PhaseShiftRequest {
  node_id: string;
  shift_type: 'advance' | 'delay';
  magnitude_hours: number;
  method: 'temperature_step' | 'parameter_modulation';
  temperature_delta_kelvin?: number;
  duration_minutes?: number;
}
```

---

## Authentication & Security

### Authentication Methods

1. **API Keys** (for ESP32 nodes)
   - Header: `X-API-Key: kaiabc_node_abc123xyz789`
   - Scoped to specific node_id

2. **JWT Tokens** (for web clients)
   - Header: `Authorization: Bearer eyJhbGc...`
   - Includes user permissions and roles

### Authorization Roles

- **admin**: Full system access
- **researcher**: Read/write access to simulation and entrainment
- **observer**: Read-only access
- **node**: Sensor node access (data submission only)

### Rate Limiting

- **Sensor data ingestion**: 100 requests/minute per node
- **State queries**: 60 requests/minute per client
- **Configuration updates**: 10 requests/minute per client
- **WebSocket connections**: 5 concurrent per client

---

## Error Handling

### Standard Error Response

```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "Temperature value out of valid range",
    "details": {
      "parameter": "temperature_kelvin",
      "provided": 400.0,
      "valid_range": [273.15, 323.15]
    },
    "timestamp": "2025-10-06T12:00:00Z",
    "request_id": "req_abc123"
  }
}
```

### HTTP Status Codes

- `200 OK`: Successful request
- `201 Created`: Resource created successfully
- `400 Bad Request`: Invalid parameters
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `409 Conflict`: State conflict (e.g., node already registered)
- `422 Unprocessable Entity`: Valid syntax but semantic errors
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error
- `503 Service Unavailable`: System overload or maintenance

### Error Codes

- `INVALID_PARAMETER`: Parameter validation failed
- `NODE_NOT_FOUND`: Sensor node does not exist
- `NODE_OFFLINE`: Sensor node not responding
- `ODE_INSTABILITY`: Numerical instability detected
- `FILTER_DIVERGENCE`: Kalman filter diverged
- `RATE_LIMIT_EXCEEDED`: Too many requests
- `AUTHENTICATION_FAILED`: Invalid credentials
- `PERMISSION_DENIED`: Insufficient permissions

---

## Rate Limiting

Rate limits are applied per API key/token:

**Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1696598400
```

**Rate Limit Exceeded Response:**
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit of 100 requests per minute exceeded",
    "retry_after_seconds": 45
  }
}
```

---

## Implementation Notes

### ESP32 Client Integration

Example ESP32 MicroPython client code:

```python
import urequests
import ujson
from machine import Pin, I2C
import time

API_BASE = "http://192.168.1.100:8000/v1"
API_KEY = "kaiabc_node_esp32_001"
NODE_ID = "esp32_001"

def send_sensor_data(temp_k, humidity, pressure):
    headers = {
        "X-API-Key": API_KEY,
        "Content-Type": "application/json"
    }
    payload = {
        "node_id": NODE_ID,
        "timestamp": get_iso_timestamp(),
        "temperature_kelvin": temp_k,
        "humidity_percent": humidity,
        "pressure_pa": pressure,
        "sensor_type": "BME280",
        "quality": "good"
    }
    response = urequests.post(
        f"{API_BASE}/sensors/batch",
        headers=headers,
        data=ujson.dumps({"node_id": NODE_ID, "readings": [payload]})
    )
    return response.json()

def get_current_state():
    headers = {"X-API-Key": API_KEY}
    response = urequests.get(
        f"{API_BASE}/oscillator/state?node_id={NODE_ID}",
        headers=headers
    )
    return response.json()
```

### Server-Side Fallback

If network connectivity is lost, ESP32 nodes should:
1. Continue local PWM output based on last known state
2. Buffer sensor readings (up to 1 hour)
3. Resume data transmission when connectivity restored
4. Flag data gaps in metadata

---

## Versioning & Changelog

**Version 1.0.0** (October 6, 2025)
- Initial API specification
- REST endpoints for core functionality
- WebSocket support for real-time streaming
- Multi-node architecture support

**Future Considerations (v1.1.0):**
- MQTT broker integration for high-volume sensor networks
- GraphQL alternative endpoint
- Batch simulation mode (run multiple parameter sets)
- Machine learning model integration for adaptive entrainment
- Redox sensor integration (LdpA pathway)
- Multi-oscillator synchronization endpoints

---

## Support & Documentation

- **API Reference:** https://docs.kaiabc.example.com/api
- **Client Libraries:** Python, JavaScript, MicroPython
- **Issue Tracker:** https://github.com/aptitudetechnology/KaiABC/issues
- **Discussion Forum:** https://forum.kaiabc.example.com

---

**End of API Specification v1.0.0**
