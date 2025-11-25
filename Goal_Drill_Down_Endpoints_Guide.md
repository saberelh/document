# Goal Analytics - Drill-Down Endpoints Guide

This document provides comprehensive documentation for all drill-down endpoints in the Goal Analytics system.

## Table of Contents
1. [Overview](#overview)
2. [Achievement Summary Drill-Down](#1-achievement-summary-drill-down)
3. [Budget Consumption Drill-Down](#2-budget-consumption-drill-down)
4. [Milestone Summary Drill-Down](#3-milestone-summary-drill-down)
5. [KPI Risk Summary Drill-Down](#4-kpi-risk-summary-drill-down)
6. [Overall Health Drill-Down](#5-overall-health-drill-down)
7. [Progress Series Point Drill-Down](#6-progress-series-point-drill-down)
8. [Progress Budget Monthly Drill-Down](#7-progress-budget-monthly-drill-down)
9. [On-Time vs Delayed Drill-Down](#8-on-time-vs-delayed-drill-down)
10. [KPI Performance Details Drill-Down](#9-kpi-performance-details-drill-down)
11. [Budget vs Achievement Drill-Down](#10-budget-vs-achievement-drill-down)
12. [Milestone Timeline Drill-Down](#11-milestone-timeline-drill-down)
13. [Health Breakdown Drill-Down](#12-health-breakdown-drill-down)
14. [Common Patterns](#common-patterns)

## Overview

The Goal Analytics system provides specialized drill-down capabilities for various goal performance metrics. Each endpoint returns a specific Data Transfer Object (DTO) tailored to the context of the drill-down, ensuring efficient data transfer and clear API contracts.

**All endpoints support:**
*   ✅ Pagination (0-indexed)
*   ✅ Multi-language translations (`Map<String, Object>`)
*   ✅ Filtering by Goal IDs, Perspective IDs, and Strategy IDs

---

### 1. Achievement Summary Drill-Down
**Purpose:** Lists goal details focusing on progress execution and variance against the plan.
**Linked Chart:** Achievement Summary Table / List

*   **Endpoint:** `GET /goal/achievement-summary/details`
*   **Response Type:** `GoalDrillDownDto.Achievement`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `asOf` | `LocalDate` | No | Current Date | Calculate progress as of this date (ISO 8601: YYYY-MM-DD) |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/achievement-summary/details?asOf=2025-03-31&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 201,
      "nameTranslations": { "en": "Increase Market Share", "ar": "زيادة الحصة السوقية" },
      "descriptionTranslations": { "en": "Expand market presence...", "ar": "توسيع التواجد..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Customer", "ar": "العميل" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "progress": 65.5,
      "variance": -4.5
    }
  ],
  "totalElements": 15,
  "totalPages": 2
}
```

**Use Cases:**
*   Drill into progress series chart points
*   Analyze goal progress at specific dates
*   Identify goals with negative variance (delays)
*   View goal details from overall health gauge

---

### 2. Budget Consumption Drill-Down
**Purpose:** Lists goal details focusing on financial performance, including budget, spent amount, and consumption percentage.
**Linked Chart:** Budget Consumption Card / Widget

*   **Endpoint:** `GET /goal/budget-consumption/details`
*   **Response Type:** `GoalDrillDownDto.Budget`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/budget-consumption/details?page=0&size=20"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 202,
      "nameTranslations": { "en": "Improve Service Quality", "ar": "تحسين جودة الخدمة" },
      "descriptionTranslations": { "en": "Enhance service delivery...", "ar": "تعزيز..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Internal Process", "ar": "العملية الداخلية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "budget": 500000.00,
      "spent": 175000.00,
      "remaining": 325000.00,
      "percentage": 35.0
    }
  ],
  "totalElements": 15,
  "totalPages": 1
}
```

**Use Cases:**
*   Monitor budget utilization for goals
*   Identify over-budget or under-budget goals
*   Drill into budget consumption card
*   View goals for specific months in progress-budget chart

---

### 3. Milestone Summary Drill-Down
**Purpose:** Lists goal details focusing on milestone execution, with optional filtering by status.
**Linked Chart:** Milestone Summary Pie/Donut Chart

*   **Endpoint:** `GET /goal/milestone-summary/details`
*   **Response Type:** `GoalDrillDownDto.Milestone`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `status` | `String` | No | - | Filter milestones by status: `ON_TIME`, `DELAYED` |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/milestone-summary/details?status=DELAYED&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 203,
      "nameTranslations": { "en": "Digital Transformation", "ar": "التحول الرقمي" },
      "descriptionTranslations": { "en": "Modernize IT systems...", "ar": "تحديث..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Learning & Growth", "ar": "التعلم والنمو" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "totalMilestones": 8,
      "onTimeMilestones": 5,
      "delayedMilestones": 3,
      "milestones": [
        {
          "id": 601,
          "name": { "en": "Q2 Checkpoint", "ar": "نقطة تفتيش Q2" },
          "dueDate": "2025-06-30",
          "status": "DELAYED",
          "planned": 50.0,
          "actual": 45.0
        },
        {
          "id": 602,
          "name": { "en": "Q3 Checkpoint", "ar": "نقطة تفتيش Q3" },
          "dueDate": "2025-09-30",
          "status": "DELAYED",
          "planned": 75.0,
          "actual": 65.0
        }
      ]
    }
  ],
  "totalElements": 8,
  "totalPages": 1
}
```

**Use Cases:**
*   Track milestone delivery
*   Filter goals by milestone status (on-time/delayed)
*   Drill into milestones timeline chart
*   View all milestones for a specific goal

---

### 4. KPI Risk Summary Drill-Down
**Purpose:** Lists goal details focusing on KPI performance and risk status.
**Linked Chart:** KPI Risk Summary Pie/Donut Chart

*   **Endpoint:** `GET /goal/kpi/risk-summary/details`
*   **Response Type:** `GoalDrillDownDto.KpiRisk`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `planYear` | `Integer` | No | Current Year | The year to analyze KPI performance for |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/kpi/risk-summary/details?planYear=2025&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 204,
      "nameTranslations": { "en": "Customer Excellence", "ar": "التميز في خدمة العملاء" },
      "descriptionTranslations": { "en": "Deliver exceptional service...", "ar": "تقديم خدمة استثنائية..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Customer", "ar": "العميل" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "totalKpis": 6,
      "onTrackKpis": 4,
      "atRiskKpis": 1,
      "offTrackKpis": 1,
      "indicators": [
        {
          "name": { "en": "Customer Satisfaction", "ar": "رضا العملاء" },
          "status": "On Track"
        },
        {
          "name": { "en": "Response Time", "ar": "وقت الاستجابة" },
          "status": "At Risk"
        },
        {
          "name": { "en": "Quality Score", "ar": "درجة الجودة" },
          "status": "Off Track"
        }
      ]
    }
  ],
  "totalElements": 10,
  "totalPages": 1
}
```

**Use Cases:**
*   Assess KPI health for goals
*   Drill into KPI performance dashboard
*   View indicator details from indicators-by-goal chart
*   Identify goals with at-risk KPIs

---

### 5. Overall Health Drill-Down
**Purpose:** Lists goal details with comprehensive health scores derived from progress, budget, and indicators.
**Linked Chart:** Overall Health Gauge

*   **Endpoint:** `GET /goal/overall-health/details`
*   **Response Type:** `GoalDrillDownDto.OverallHealth`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/overall-health/details?page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 205,
      "nameTranslations": { "en": "Innovation Leadership", "ar": "قيادة الابتكار" },
      "descriptionTranslations": { "en": "Lead market innovation...", "ar": "قيادة..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Learning & Growth", "ar": "التعلم والنمو" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "healthScore": 82.5,
      "progressHealth": 85.0,
      "budgetHealth": 78.0,
      "indicatorHealth": 84.5
    }
  ],
  "totalElements": 15,
  "totalPages": 2
}
```

**Use Cases:**
*   High-level health overview of goals
*   Filter goals by health category (Healthy/Watch/Critical)
*   Drill into overall health gauge
*   Compare health across perspectives

---

### 6. Progress Series Point Drill-Down
**Purpose:** Lists goal details at a specific point in time, showing actual vs planned progress.
**Linked Chart:** Progress Series Line Chart (Click on Point)

*   **Endpoint:** `GET /goal/progress-series/point-details`
*   **Response Type:** `GoalDrillDownDto.ProgressSeriesPoint`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `pointDate` | `LocalDate` | No | Current Date | The specific date to drill into (ISO 8601: YYYY-MM-DD) |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/progress-series/point-details?pointDate=2025-03-31&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 206,
      "nameTranslations": { "en": "Revenue Growth", "ar": "نمو الإيرادات" },
      "descriptionTranslations": { "en": "Achieve revenue targets...", "ar": "تحقيق..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Financial", "ar": "المالية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "pointDate": "2025-03-31",
      "progressAtDate": 27.5,
      "plannedProgressAtDate": 25.0,
      "varianceAtDate": 2.5
    }
  ],
  "totalElements": 15,
  "totalPages": 2
}
```

**Use Cases:**
*   Click on specific point in progress series chart
*   Compare actual vs planned at specific date
*   Analyze goal snapshot at quarter end
*   Track progress evolution over time

---

### 7. Progress Budget Monthly Drill-Down
**Purpose:** Lists goal details for a specific month, showing progress and budget consumption.
**Linked Chart:** Progress vs Budget Monthly Chart (Click on Month Column)

*   **Endpoint:** `GET /goal/progress-budget-monthly/month-details`
*   **Response Type:** `GoalDrillDownDto.ProgressBudgetMonth`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `month` | `String` | No | Current Month | The month to analyze (Format: YYYY-MM) |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/progress-budget-monthly/month-details?month=2025-03&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 207,
      "nameTranslations": { "en": "Cost Reduction", "ar": "خفض التكاليف" },
      "descriptionTranslations": { "en": "Reduce operational costs...", "ar": "تقليل..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Internal Process", "ar": "العملية الداخلية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "month": "2025-03",
      "progressForMonth": 25.5,
      "budgetSpentInMonth": 45000.00,
      "cumulativeBudget": 135000.00
    }
  ],
  "totalElements": 12,
  "totalPages": 2
}
```

**Use Cases:**
*   Click on month in progress-budget monthly chart
*   Analyze monthly budget consumption
*   Compare progress vs spend for specific month
*   Track monthly burn rate

---

### 8. On-Time vs Delayed Drill-Down
**Purpose:** Lists goal details filtered by milestone performance status.
**Linked Chart:** On-Time vs Delayed Bar Chart

*   **Endpoint:** `GET /goal/on-time-vs-delayed/details`
*   **Response Type:** `GoalDrillDownDto.OnTimeVsDelayed`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `status` | `String` | No | `ON_TIME` | Performance status: `ON_TIME`, `DELAYED` |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/on-time-vs-delayed/details?status=DELAYED&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 208,
      "nameTranslations": { "en": "Process Automation", "ar": "أتمتة العمليات" },
      "descriptionTranslations": { "en": "Automate manual processes...", "ar": "أتمتة..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Internal Process", "ar": "العملية الداخلية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "totalMilestones": 6,
      "onTimeMilestones": 3,
      "delayedMilestones": 3,
      "onTimePercentage": 50.0,
      "milestoneDetails": [
        {
          "id": 701,
          "name": { "en": "Phase 2 Complete", "ar": "اكتمال المرحلة 2" },
          "dueDate": "2025-06-30",
          "status": "DELAYED"
        }
      ]
    }
  ],
  "totalElements": 8,
  "totalPages": 1
}
```

**Use Cases:**
*   Filter goals by milestone performance
*   Identify goals with delayed milestones
*   View milestone completion rates
*   Drill into milestone charts by status

---

### 9. KPI Performance Details Drill-Down
**Purpose:** Provides detailed information about a specific KPI, including its performance and context.
**Linked Chart:** KPI Performance Dashboard / Indicator List

*   **Endpoint:** `GET /goal/kpi/performance-details`
*   **Response Type:** `GoalDrillDownDto.KpiPerformance`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `kpiId` | `Long` | **Yes** | - | The ID of the specific KPI |
| `planYear` | `Integer` | No | Current Year | The year to analyze KPI performance for |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/kpi/performance-details?kpiId=123&planYear=2025"
```

**Response Structure:**
```json
{
  "items": [
    {
      "kpiId": 123,
      "kpiName": { "en": "Customer Satisfaction Index", "ar": "مؤشر رضا العملاء" },
      "kpiDescription": { "en": "Measure overall customer satisfaction...", "ar": "قياس..." },
      "goalId": 204,
      "goalName": { "en": "Customer Excellence", "ar": "التميز في خدمة العملاء" },
      "perspectiveName": { "en": "Customer", "ar": "العميل" },
      "strategyName": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "target": 85.0,
      "actual": 88.5,
      "achievement": 104.1,
      "status": "ON_TRACK",
      "measurementUnit": "%",
      "historicalTrend": [
        { "period": "Q1", "achievement": 95.0 },
        { "period": "Q2", "achievement": 102.0 },
        { "period": "Q3", "achievement": 104.1 }
      ]
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
*   Click on specific KPI in performance dashboard
*   View detailed KPI information
*   Analyze KPI trend over time
*   Compare target vs actual achievement

---

### 10. Budget vs Achievement Drill-Down
**Purpose:** Lists goal details comparing budget consumption against progress achievement.
**Linked Chart:** Budget vs Achievement Scatter/Bubble Chart

*   **Endpoint:** `GET /goal/budget-vs-achievement/details`
*   **Response Type:** `GoalDrillDownDto.BudgetVsAchievement`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `asOf` | `LocalDate` | No | Current Date | Calculate metrics as of this date |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/budget-vs-achievement/details?asOf=2025-03-31&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 209,
      "nameTranslations": { "en": "Market Expansion", "ar": "التوسع في السوق" },
      "descriptionTranslations": { "en": "Expand into new markets...", "ar": "التوسع..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Financial", "ar": "المالية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "progress": 60.0,
      "budgetConsumption": 350000.00,
      "budgetConsumptionPercentage": 70.0,
      "performanceCategory": "OVERSPENDING"
    }
  ],
  "totalElements": 15,
  "totalPages": 2
}
```

**Performance Categories:**
- `EFFICIENT` - Budget consumption < Progress (spending efficiently)
- `ON_TRACK` - Budget consumption ≈ Progress (balanced)
- `OVERSPENDING` - Budget consumption > Progress (spending ahead of progress)
- `UNDERSPENDING` - Both progress and budget < 50% (slow start)

**Use Cases:**
*   Identify goals spending ahead of progress
*   Find efficient goals (high progress, low spend)
*   Analyze budget vs progress correlation
*   Prioritize goals needing budget attention

---

### 11. Milestone Timeline Drill-Down
**Purpose:** Lists goal details with complete progress timeline showing all points and milestones.
**Linked Chart:** Milestones Timeline / Gantt Chart

*   **Endpoint:** `GET /goal/milestones/timeline-details`
*   **Response Type:** `GoalDrillDownDto.MilestoneTimeline`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/milestones/timeline-details?goalIds=123&page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 210,
      "nameTranslations": { "en": "Employee Engagement", "ar": "مشاركة الموظفين" },
      "descriptionTranslations": { "en": "Improve employee satisfaction...", "ar": "تحسين..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Learning & Growth", "ar": "التعلم والنمو" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "timeline": [
        {
          "date": "2025-01-31",
          "percent": 8.5,
          "isMilestone": false
        },
        {
          "date": "2025-03-31",
          "percent": 25.0,
          "isMilestone": true
        },
        {
          "date": "2025-04-30",
          "percent": 35.5,
          "isMilestone": false
        },
        {
          "date": "2025-06-30",
          "percent": 50.0,
          "isMilestone": true
        }
      ],
      "totalProgressPoints": 12,
      "totalMilestones": 4
    }
  ],
  "totalElements": 1,
  "totalPages": 1
}
```

**Use Cases:**
*   View complete progress timeline for a goal
*   Identify which points are milestones
*   Analyze progress patterns over time
*   Export timeline data for reporting

---

### 12. Health Breakdown Drill-Down
**Purpose:** Lists goal details with detailed breakdown of health components.
**Linked Chart:** Health Breakdown Radar/Bar Chart

*   **Endpoint:** `GET /goal/health-breakdown/details`
*   **Response Type:** `GoalDrillDownDto.HealthBreakdown`

**Parameters:**
| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `goalIds` | `List<Long>` | No | - | Filter by specific goal IDs |
| `perspectiveIds` | `List<Long>` | No | - | Filter by perspective IDs |
| `strategyIds` | `List<Long>` | No | - | Filter by strategy IDs |
| `page` | `int` | No | 0 | Page number (0-indexed) |
| `size` | `int` | No | 10 | Number of items per page |

**Example Request:**
```bash
curl -X GET "http://localhost:8080/goal/health-breakdown/details?page=0&size=10"
```

**Response Structure:**
```json
{
  "items": [
    {
      "id": 211,
      "nameTranslations": { "en": "Operational Excellence", "ar": "التميز التشغيلي" },
      "descriptionTranslations": { "en": "Achieve operational excellence...", "ar": "تحقيق..." },
      "startDate": "2025-01-01",
      "endDate": "2025-12-31",
      "status": { "en": "Active", "ar": "نشط" },
      "perspectiveNameTranslations": { "en": "Internal Process", "ar": "العملية الداخلية" },
      "strategyNameTranslations": { "en": "2025 Strategic Plan", "ar": "الخطة..." },
      "overallHealth": 78.5,
      "progressComponent": {
        "name": "Progress",
        "score": 85.0,
        "status": "HEALTHY",
        "details": "Current progress achievement"
      },
      "budgetComponent": {
        "name": "Budget",
        "score": 72.0,
        "status": "WARNING",
        "details": "Budget efficiency"
      },
      "milestoneComponent": {
        "name": "Milestones",
        "score": 75.0,
        "status": "HEALTHY",
        "details": "Milestone completion rate"
      },
      "kpiComponent": {
        "name": "KPIs",
        "score": 82.0,
        "status": "HEALTHY",
        "details": "KPI achievement rate"
      }
    }
  ],
  "totalElements": 15,
  "totalPages": 2
}
```

**Health Component Status:**
- `HEALTHY` - Score ≥ 75%
- `WARNING` - Score 50-74%
- `CRITICAL` - Score < 50%

**Use Cases:**
*   Identify which health components are problematic
*   Drill into overall health to see component breakdown
*   Prioritize goals needing attention by component
*   Compare health profiles across goals

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
  "totalElements": 50,    // Total number of items across all pages
  "totalPages": 5         // Total number of pages
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

### Parent Context
All goal drill-downs include parent hierarchy context:
- `perspectiveNameTranslations` - The perspective this goal belongs to
- `strategyNameTranslations` - The strategy this goal belongs to

This allows UI to display the full context: Strategy → Perspective → Goal

### Date Filtering
Date parameters accept ISO 8601 format (YYYY-MM-DD):
- `asOf` - Calculate metrics as of this specific date
- `pointDate` - Drill into specific point in time
- `month` - Month in YYYY-MM format for monthly analysis

### Health Calculations
Health scores are calculated using different formulas:
- **Progress Health** - Based on latest progress % from progress logs
- **Budget Health** - Based on budget efficiency (100 - consumption %)
- **Indicator Health** - Averaged from KPI achievement scores
- **Overall Health** - Average of all component health scores

---

## Chart-to-API Mapping

| Dashboard Chart | User Action | Drill-Down API | Parameters |
|-----------------|-------------|----------------|------------|
| **Progress Series** | Click point | `/goal/progress-series/point-details` | `pointDate`, `goalIds` |
| **Progress-Budget Monthly** | Click month | `/goal/progress-budget-monthly/month-details` | `month`, `goalIds` |
| **Overall Health Gauge** | Click gauge | `/goal/overall-health/details` | Filter by health category on frontend |
| **Achievement Summary** | Click card | `/goal/achievement-summary/details` | `asOf`, filters |
| **Budget Consumption Card** | Click card | `/goal/budget-consumption/details` | filters |
| **Milestone Summary** | Click status | `/goal/milestone-summary/details` | `status=DELAYED` or `ON_TIME` |
| **KPI Performance Dashboard** | Click KPI bar (Goals tab) | `/goal/achievement-summary/details` | `goalIds` |
| **KPI Performance Dashboard** | Click KPI bar (KPIs tab) | Use data from response (no API call) | - |
| **Indicators by Goal** | Click goal | `/goal/kpi/risk-summary/details` | `goalIds` |
| **Milestones Timeline** | Click milestone | `/goal/milestone-summary/details` | `goalIds` |
| **Budget vs Achievement** | Analyze efficiency | `/goal/budget-vs-achievement/details` | `asOf`, filters |
| **Health Breakdown** | Drill into health | `/goal/health-breakdown/details` | `goalIds` |

---

## Summary

**Total Goal Drill-Down APIs: 12**

### Core APIs (First 4 - Fully Tested):
1. Achievement Summary Details
2. Budget Consumption Details
3. Milestone Summary Details
4. KPI Risk Summary Details

### Extended APIs (New 8 - Recently Implemented):
5. Overall Health Details
6. Progress Series Point Details
7. Progress Budget Monthly Details
8. On-Time vs Delayed Details
9. KPI Performance Details
10. Budget vs Achievement Details
11. Milestone Timeline Details
12. Health Breakdown Details

All endpoints support pagination, multi-language translations, and flexible filtering. They provide comprehensive drill-down capabilities for the entire Goal Analytics dashboard.
