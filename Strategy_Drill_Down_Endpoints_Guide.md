# Strategy Analytics - Drill-Down Endpoints Guide

This document provides comprehensive documentation for all drill-down endpoints in the Strategy Analytics system (Strategy V2).

## Table of Contents
1. [Overview](#overview)
2. [Achievement Summary Drill-Down](#1-achievement-summary-drill-down)
3. [Budget Consumption Drill-Down](#2-budget-consumption-drill-down)
4. [Milestone Summary Drill-Down](#3-milestone-summary-drill-down)
5. [KPI Risk Summary Drill-Down](#4-kpi-risk-summary-drill-down)
6. [Overall Health Drill-Down](#5-overall-health-drill-down)
7. [Budget vs Achievement Drill-Down](#6-budget-vs-achievement-drill-down)
8. [On-Time vs Delayed Drill-Down](#7-on-time-vs-delayed-drill-down)
9. [Top Movers Drill-Down(Execution Progress Over Time by Strategy)](#8-top-movers-drill-down)
10. [Budget Allocation Drill-Down](#9-budget-allocation-drill-down)
11. [Budget by Vision Pillar Drill-Down](#10-budget-by-vision-pillar-drill-down)
12. [Common Patterns](#common-patterns)

## Overview

The Strategy Analytics system provides specialized drill-down capabilities for various strategic performance metrics. Each endpoint returns a specific Data Transfer Object (DTO) tailored to the context of the drill-down, ensuring efficient data transfer and clear API contracts.

**All endpoints support:**
*   ✅ Pagination (0-indexed)
*   ✅ Multi-language translations (`Map<String, Object>`)
*   ✅ Filtering by Strategy IDs

---

### 1. Achievement Summary Drill-Down
**Purpose:** Lists strategy details focusing on progress execution and variance against the plan.

*   **Endpoint:** `GET /strategy/achievement-summary/details`
*   **Response Type:** `StrategyDrillDownDto.Achievement`

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
      "nameTranslations": { "en": "Strategic Plan A", "ar": "الخطة الاستراتيجية أ" },
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
*   Analyze progress of specific strategies.
*   Identify strategies with significant negative variance (delays).

---

### 2. Budget Consumption Drill-Down
**Purpose:** Lists strategy details focusing on financial performance, including budget, spent amount, and consumption percentage.

*   **Endpoint:** `GET /strategy/budget-consumption/details`
*   **Response Type:** `StrategyDrillDownDto.Budget`

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
      "nameTranslations": { "en": "Infrastructure Project", "ar": "مشروع البنية التحتية" },
      "startDate": "2024-01-01",
      "endDate": "2026-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "budget": 1000000.00,
      "spent": 250000.00,
      "remaining": 750000.00,
      "percentage": 25.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
*   Monitor budget utilization.
*   Identify over-budget or under-budget strategies.

---

### 3. Milestone Summary Drill-Down
**Purpose:** Lists strategy details focusing on milestone execution within the current quarter, with optional filtering by status.

*   **Endpoint:** `GET /strategy/milestone-summary/details`
*   **Response Type:** `StrategyDrillDownDto.Milestone`

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
*   Track milestone delivery for the current quarter.
*   Identify strategies with high numbers of delayed milestones.
*   **New:** Filter to see only "Delayed" milestones and view their specific details.

---

### 4. KPI Risk Summary Drill-Down
**Purpose:** Lists strategy details focusing on KPI performance and risk status.

*   **Endpoint:** `GET /strategy/kpi/risk-summary/details`
*   **Response Type:** `StrategyDrillDownDto.KpiRisk`

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
      "nameTranslations": { "en": "Service Excellence", "ar": "التميز في الخدمة" },
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
*   Assess the health of KPIs associated with strategies.
*   Drill down into specific indicators causing risk.

---

### 5. Overall Health Drill-Down
**Purpose:** Lists strategy details with a comprehensive health score derived from progress, budget, and indicators.

*   **Endpoint:** `GET /strategy/overall-health/details`
*   **Response Type:** `StrategyDrillDownDto.OverallHealth`

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
      "nameTranslations": { "en": "Transformation Program", "ar": "برنامج التحول" },
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
*   High-level view of strategy health.
*   Identify strategies that are "Critical", "Watch", or "Healthy".

---

### 6. Budget vs Achievement Drill-Down
**Purpose:** Lists strategy details comparing financial spend against physical progress.

*   **Endpoint:** `GET /strategy/budget-vs-achievement-bubbles/details`
*   **Response Type:** `StrategyDrillDownDto.BudgetVsAchievement`

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
      "nameTranslations": { "en": "Construction Phase 1", "ar": "مرحلة البناء 1" },
      "status": { "en": "Active", "ar": "نشط" },
      "progress": 60.0,
      "percentage": 55.0,
      "spent": 550000.00,
      "variance": 5.0
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
*   Compare "Value for Money" (Progress vs Spend).
*   Identify strategies where spend is outpacing progress.

---

### 7. On-Time vs Delayed Drill-Down
**Purpose:** Lists strategy details focusing on on-time vs delayed performance across Goals, Programs, Initiatives, and Indicators.

*   **Endpoint:** `GET /strategy/on-time-vs-delayed/details`
*   **Response Type:** `StrategyDrillDownDto.OnTimeVsDelayed`

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
      "nameTranslations": { "en": "Strategic Goal 1", "ar": "الهدف الاستراتيجي 1" },
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
*   Analyze performance at different levels (Goals, Programs, Initiatives, Indicators).
*   Identify specific items that are delayed or on-time.
*   Drill down into specific levels to find bottlenecks.

---

### 8. Top Movers Drill-Down(Execution Progress Over Time by Strategy)
**Purpose:** Lists items (Goals, Programs, Initiatives) sorted by their progress growth over a specific period.

*   **Endpoint:** `GET /strategy/execution-progress/top-movers`
*   **Response Type:** `StrategyDrillDownDto.TopMover`

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
      "descriptionTranslations": { "en": "Migrating legacy systems...", "ar": "ترحيل الأنظمة القديمة..." },
      "type": "Goal",
      "strategyNameTranslations": { "en": "Digital Transformation", "ar": "التحول الرقمي" },
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
*   Identify which goals/programs drove the progress increase during a specific quarter.
*   Find stagnant items (0% growth) during a period.
*   Analyze performance trends over time.

---

### 9. Budget Allocation Drill-Down
**Purpose:** Lists detailed financial data for strategies, including budget, spent, remaining, and forecast metrics.

*   **Endpoint:** `GET /strategy/budget-allocation/details`
*   **Response Type:** `StrategyDrillDownDto.BudgetAllocationDetail`

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
*   Detailed financial review of specific strategies.
*   Analyze forecast vs actual spending.

---

### 10. Budget by Vision Pillar Drill-Down
**Purpose:** Lists strategies linked to a specific Vision Pillar, providing their financial details.

*   **Endpoint:** `GET /strategy/budget-by-vision-pillar/details`
*   **Response Type:** `StrategyDrillDownDto.BudgetAllocationDetail`

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
*   Drill down from the "Budget by Vision Pillar" chart.
*   See which strategies are consuming the budget of a specific pillar.

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
