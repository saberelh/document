# Strategy Analytics - Drill-Down Endpoints Guide

This document provides comprehensive documentation for all drill-down endpoints in the Strategy Analytics system (Strategy V2).

## Table of Contents
1. [Overall Achievement](#1-overall-achievement)
2. [Budget Consumption](#2-budget-consumption)
3. [On-Time vs Delayed Milestones](#3-on-time-vs-delayed-milestones)
4. [KPIs at Risk](#4-kpis-at-risk)
5. [Overall Health Index](#5-overall-health-index)
6. [Execution Progress Over Time by Strategy](#6-execution-progress-over-time-by-strategy)
7. [Strategy Performance vs. Target (%)](#7-strategy-performance-vs-target-)
8. [Budget Forecast vs Actual Burn](#8-budget-forecast-vs-actual-burn)
9. [Budget Utilization vs. Achievement](#9-budget-utilization-vs-achievement)
10. [On-Time vs Delayed Performance by Strategy](#10-on-time-vs-delayed-performance-by-strategy)
11. [Budget Allocation Across Strategies](#11-budget-allocation-across-strategies)
12. [Budget Allocation & Consumption by Vision Pillar](#12-budget-allocation--consumption-by-vision-pillar)
13. [Common Patterns](#common-patterns)

## Overview

The Strategy Analytics system provides specialized drill-down capabilities for various strategic performance metrics. Each endpoint returns a specific Data Transfer Object (DTO) tailored to the context of the drill-down, ensuring efficient data transfer and clear API contracts.

**All endpoints support:**
- ✅ Pagination (0-indexed)
- ✅ Multi-language translations (`Map<String, Object>`)
- ✅ Filtering by Strategy IDs

---

### 1. Overall Achievement
**Purpose:** Lists strategy details focusing on progress execution and variance against the plan.

- **Original API:** `GET /strategy/achievement-summary`
- **Drill-Down API:** `GET /strategy/achievement-summary/details`
- **Response Type:** `StrategyDrillDownDto.Achievement`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `asOf` | `LocalDate` | No | Current Date | Calculate progress as of this date (ISO 8601: YYYY-MM-DD) |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/achievement-summary/details?asOf=2024-11-23&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 101,
      "nameTranslations": {
        "en": "Strategic Plan A",
        "ar": "الخطة الاستراتيجية أ"
      },
      "descriptionTranslations": { "en": "Description...", "ar": "وصف..." },
      "startDate": "2024-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "progress": 45.5,
      "variance": -5.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Analyze progress of specific strategies.
- Identify strategies with significant negative variance (delays).

---

### 2. Budget Consumption
**Purpose:** Lists strategy details focusing on financial performance, including budget, spent amount, and consumption percentage.

- **Original API:** `GET /strategy/budget-consumption`
- **Drill-Down API:** `GET /strategy/budget-consumption/details`
- **Response Type:** `StrategyDrillDownDto.Budget`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/budget-consumption/details?page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 102,
      "nameTranslations": {
        "en": "Infrastructure Project",
        "ar": "مشروع البنية التحتية"
      },
      "startDate": "2024-01-01",
      "endDate": "2026-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "budget": 1000000.0,
      "spent": 250000.0,
      "remaining": 750000.0,
      "percentage": 25.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Monitor budget utilization.
- Identify over-budget or under-budget strategies.

---

### 3. On-Time vs Delayed Milestones
**Purpose:** Lists strategy details focusing on milestone execution within the current quarter, with optional filtering by status.

- **Original API:** `GET /strategy/milestone-summary`
- **Drill-Down API:** `GET /strategy/milestone-summary/details`
- **Response Type:** `StrategyDrillDownDto.Milestone`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `status` | `String` | No | - | Filter milestones by status: `ON_TIME`, `DELAYED` |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/milestone-summary/details?status=DELAYED&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 103,
      "nameTranslations": { "en": "IT Upgrade", "ar": "تحديث تقنية المعلومات" },
      "status": { "en": "Active", "ar": "نشط" },
      "totalMilestones": 10,
      "onTimeMilestones": 8,
      "delayedMilestones": 2,
      "milestones": [
        {
          "id": 501,
          "name": { "en": "Phase 1 Complete", "ar": "اكتمال المرحلة 1" },
          "dueDate": "2024-03-15",
          "status": "DELAYED",
          "planned": 100.0,
          "actual": 80.0
        }
      ]
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Track milestone delivery for the current quarter.
- Identify strategies with high numbers of delayed milestones.
- Filter to see only "Delayed" milestones and view their specific details.

---

### 4. KPIs at Risk
**Purpose:** Lists strategy details focusing on KPI performance and risk status.

- **Original API:** `GET /strategy/kpi/risk-summary`
- **Drill-Down API:** `GET /strategy/kpi/risk-summary/details`
- **Response Type:** `StrategyDrillDownDto.KpiRisk`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `planYear` | `Integer` | No | Current Year | The year to analyze KPI performance for |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/kpi/risk-summary/details?planYear=2024"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 104,
      "nameTranslations": {
        "en": "Service Excellence",
        "ar": "التميز في الخدمة"
      },
      "status": { "en": "Active", "ar": "نشط" },
      "totalKpis": 5,
      "onTrackKpis": 3,
      "atRiskKpis": 1,
      "offTrackKpis": 1,
      "indicators": [
        {
          "name": { "en": "Customer Satisfaction", "ar": "رضا العملاء" },
          "status": "On Track"
        },
        {
          "name": { "en": "Service Time", "ar": "وقت الخدمة" },
          "status": "At Risk"
        }
      ]
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Assess the health of KPIs associated with strategies.
- Drill down into specific indicators causing risk.

---

### 5. Overall Health Index
**Purpose:** Lists strategy details with a comprehensive health score derived from progress, budget, and indicators.

- **Original API:** `GET /strategy/overall-health`
- **Drill-Down API:** `GET /strategy/overall-health/details`
- **Response Type:** `StrategyDrillDownDto.OverallHealth`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/overall-health/details"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 105,
      "nameTranslations": {
        "en": "Transformation Program",
        "ar": "برنامج التحول"
      },
      "status": { "en": "Active", "ar": "نشط" },
      "healthScore": 85.5,
      "progressHealth": 90.0,
      "budgetHealth": 80.0,
      "indicatorHealth": 86.5
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- High-level view of strategy health.
- Identify strategies that are "Critical", "Watch", or "Healthy".

---

### 6. Execution Progress Over Time by Strategy
**Purpose:** Lists items (Goals, Programs, Initiatives) sorted by their progress growth over a specific period.

- **Original API:** `GET /strategy/progress-vs-planned-series`
- **Drill-Down API:** `GET /strategy/execution-progress/top-movers`
- **Response Type:** `StrategyDrillDownDto.TopMover`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `level` | `String` | No | `GOAL` | Level to drill down: `GOAL`, `PROGRAM`, `INITIATIVE` |
| `startDate` | `LocalDate` | **Yes** | - | Start of the period (ISO 8601: YYYY-MM-DD) |
| `endDate` | `LocalDate` | **Yes** | - | End of the period (ISO 8601: YYYY-MM-DD) |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/execution-progress/top-movers?startDate=2025-01-01&endDate=2025-03-31&level=GOAL&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 108,
      "nameTranslations": { "en": "Cloud Migration", "ar": "ترحيل السحابة" },
      "descriptionTranslations": {
        "en": "Migrating legacy systems...",
        "ar": "ترحيل الأنظمة القديمة..."
      },
      "type": "Goal",
      "strategyNameTranslations": {
        "en": "Digital Transformation",
        "ar": "التحول الرقمي"
      },
      "strategyStatus": { "en": "Active", "ar": "نشط" },
      "strategyStartDate": "2024-01-01",
      "strategyEndDate": "2026-12-31",
      "startProgress": 20.0,
      "endProgress": 45.0,
      "growth": 25.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Identify which goals/programs drove the progress increase during a specific quarter.
- Find stagnant items (0% growth) during a period.
- Analyze performance trends over time.

---

### 7. Strategy Performance vs. Target (%)
**Purpose:** Compare strategy performance against planned targets.

*   **Original API:** `GET /strategy/progress-vs-planned-avg`
*   **Drill-Down API:** **IN PROGRESS**
*   **Status:** Drill-down functionality is currently under development.

---

### 8. Budget Forecast vs Actual Burn
**Purpose:** Compare budget forecast against actual burn over time.

*   **Original API:** `GET /strategy/budget-forecast-vs-actual-burn`
*   **Drill-Down API:** **IN PROGRESS**
*   **Status:** Drill-down functionality is currently under development.

---

### 9. Budget Utilization vs. Achievement
**Purpose:** Lists strategy details comparing financial spend against physical progress.

- **Original API:** `GET /strategy/budget-vs-achievement-bubbles`
- **Drill-Down API:** `GET /strategy/budget-vs-achievement-bubbles/details`
- **Response Type:** `StrategyDrillDownDto.BudgetVsAchievement`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `asOf` | `LocalDate` | No | Current Date | Calculate metrics as of this date |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/budget-vs-achievement-bubbles/details"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 106,
      "nameTranslations": {
        "en": "Construction Phase 1",
        "ar": "مرحلة البناء 1"
      },
      "status": { "en": "Active", "ar": "نشط" },
      "progress": 60.0,
      "percentage": 55.0,
      "spent": 550000.0,
      "variance": 5.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Compare "Value for Money" (Progress vs Spend).
- Identify strategies where spend is outpacing progress.

---

### 10. On-Time vs Delayed Performance by Strategy
**Purpose:** Lists strategy details focusing on on-time vs delayed performance across Goals, Programs, Initiatives, and Indicators.

- **Original API:** `GET /strategy/on-time-vs-delayed-breakdown`
- **Drill-Down API:** `GET /strategy/on-time-vs-delayed/details`
- **Response Type:** `StrategyDrillDownDto.OnTimeVsDelayed`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `level` | `String` | No | `GOAL` | Level to drill down: `GOAL`, `PROGRAM`, `INITIATIVE`, `INDICATOR` |
| `status` | `String` | No | `ON_TIME` | Performance status: `ON_TIME`, `DELAYED` |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/on-time-vs-delayed/details?level=GOAL&status=ON_TIME&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 107,
      "nameTranslations": {
        "en": "Strategic Goal 1",
        "ar": "الهدف الاستراتيجي 1"
      },
      "descriptionTranslations": { "en": "Description...", "ar": "وصف..." },
      "type": "Strategic Goal",
      "startDate": "2024-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "progress": 75.5,
      "planned": 80.0,
      "variance": -4.5,
      "performanceStatus": "DELAYED"
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Analyze performance at different levels (Goals, Programs, Initiatives, Indicators).
- Identify specific items that are delayed or on-time.
- Drill down into specific levels to find bottlenecks.

---

### 11. Budget Allocation Across Strategies
**Purpose:** Lists detailed financial data for strategies, including budget, spent, remaining, and forecast metrics.

- **Original API:** `GET /strategy/budget-allocation`
- **Drill-Down API:** `GET /strategy/budget-allocation/details`
- **Response Type:** `StrategyDrillDownDto.BudgetAllocationDetail`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/budget-allocation/details?page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 109,
      "nameTranslations": { "en": "Strategy X", "ar": "الاستراتيجية س" },
      "descriptionTranslations": { "en": "Desc...", "ar": "وصف..." },
      "status": { "en": "Active", "ar": "نشط" },
      "startDate": "2024-01-01",
      "endDate": "2025-12-31",
      "totalBudget": 1000000.0,
      "totalSpent": 400000.0,
      "remaining": 600000.0,
      "consumptionPercentage": 40.0,
      "forecastSpent": 500000.0,
      "variance": -100000.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
- Detailed financial review of specific strategies.
- Analyze forecast vs actual spending.

---

### 12. Budget Allocation & Consumption by Vision Pillar
**Purpose:** Lists strategies linked to a specific Vision Pillar, providing their financial details.

- **Original API:** `GET /strategy/budget-by-vision-pillar`
- **Drill-Down API:** `GET /strategy/budget-by-vision-pillar/details`
- **Response Type:** `StrategyDrillDownDto.BudgetAllocationDetail`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `pillarId` | `Long` | **Yes** | - | The ID of the Vision Pillar |
| `strategyIds` | `List<Long>` | No | - | Filter by specific strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/strategy/budget-by-vision-pillar/details?pillarId=1&page=0&size=10"
```

**Response Structure:**
(Same as Budget Allocation Drill-Down)

**Use Cases:**
- Drill down from the "Budget by Vision Pillar" chart.
- See which strategies are consuming the budget of a specific pillar.

---

## Common Patterns

### Translation Maps

All text fields that support multiple languages (e.g., `nameTranslations`, `descriptionTranslations`, `status`) are returned as `Map<String, Object>`.

```json
{
  "nameTranslations": {
    "en": "English Name",
    "ar": "الاسم العربي",
    "fr": "Nom français"
  }
}
```

### Pagination Structure

All endpoints return a consistent list wrapper:

```json
{
  "items": [ ... ],
  "totalElements": 100,    // Total number of items across all pages
  "totalPages": 10         // Total number of pages
}
```

### Empty Results

Endpoints return empty lists (not errors) when no data matches filters:

```json
{
  "items": [],
  "totalElements": 0,
  "totalPages": 0
}
```
