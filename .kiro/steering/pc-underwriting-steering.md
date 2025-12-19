---
inclusion: always
---

# P&C Underwriting Product Steering

## Product Overview

This is a Property & Casualty (P&C) underwriting platform built on Appian Connected Underwriting that accelerates the quote-to-bind process by consolidating disparate data sources and automating critical underwriting workflows. The platform reduces time to quote and improves underwriter decision-making through intelligent data ingestion, clearance, and triage capabilities.

**Key Business Objectives:**
- Accelerate time to quote for P&C insurance submissions
- Consolidate broker submission data from multiple channels
- Improve underwriter productivity through intelligent triage and prioritization
- Ensure compliance with sanctions regulations and duplicate detection
- Enable seamless integration with downstream quoting systems (Guidewire)

**Target Users:**
- Underwriters (primary users reviewing and deciding on submissions)
- Brokers (submitting policy inquiries via email or portal)
- Compliance/Operations (monitoring workflows and data quality)
- Managers (tracking performance metrics and SLAs)

## Domain-Specific Concepts

### Core Entities

**Submission** - The primary record representing a broker's request for a quote
- Created via email ingestion or manual entry
- Contains policy information, applicant/location data, risk details
- Tracks status through workflow (received, cleared, quoted)
- Associated with submission scores and triage rules

**Broker Correspondence** - All communication related to a submission
- Incoming emails with attachments and contextual data
- AI-generated message summaries for quick underwriter review
- Automated response workflows and follow-up tracking

**Submission Data** - Extracted and structured information
- Policy type and coverage details
- Applicant and location information
- Risk assessment factors
- Premium and limits

**Submission Score** - Business-driven prioritization metric
- Calculated based on configurable business rules
- Used for workload triage and prioritization
- Considers submission complexity, risk profile, urgency

### Key Workflows

1. **Data Ingestion** - Automated extraction from broker emails and documents using AI Skills
2. **Submission Clearance** - Validation, completeness checks, sanctions screening, duplicate detection
3. **Triage & Prioritization** - Underwriters filter and sort submissions by business criteria
4. **Quote Generation** - Integration with Guidewire for automated quote creation
5. **Broker Communications** - Templated responses and follow-ups with AI-assisted messaging

## Technology Stack

**Core Platform**
- Appian (v25.2.1.7+) - Low-code BPM platform
- Connected Underwriting P&C - Appian insurance module
- Appian AI Skills - Intelligent document extraction and summarization

**Optional Modules**
- AI Submission Document Assistant - Enhanced document analysis
- Guidewire Quote Module - Integration for quote generation
- OpenAI Module - Custom AI capabilities
- Submission AI - Advanced analytics on submission data
- Automated Routing Module - Intelligent workload distribution

**Integration Points**
- Email/IMAP - Broker submission ingestion
- Guidewire - Quote system integration
- External data sources - Customer checks, sanctions lists, third-party data
- Process HQ - Process analytics and monitoring

## Appian Architecture Patterns

### Data Model Structure
- Use Record types for all core entities (Submission, Correspondence, Score Rules)
- Leverage Related Record Fields for associations (Submissions → Correspondence)
- Implement Calculated Fields for derived data (submission scores, status summaries)
- Use Process Models for long-running workflows (submission clearance, quoting)

### Process Flow Patterns
- Event-driven processes for email ingestion (triggered by shared inbox activity)
- Asynchronous jobs for document extraction and data enrichment
- Human task assignments based on submission scores and underwriter expertise
- Service-oriented design for external integrations (Guidewire, sanctions checks)

### User Interface Patterns
- Workbench - Grid-based submission list with filtering and sorting
- Record View - Detailed submission review with related correspondence
- Forms - Clearance and triage decision capture
- Dashboards - KPI tracking (time to quote, submission volume, aging)

### Security & Compliance
- Implement role-based access control (Underwriter, Broker, Admin, Compliance)
- Mask sensitive data (SSN, financial information) in logs and summaries
- Track audit trails for all underwriting decisions
- Enforce sanctions screening before submission clearance
- Implement duplicate detection to prevent redundant quotes

## Data Model Best Practices

### Submission Record Type
- **Primary Fields**: Submission ID, Broker reference, Policy Type, Status
- **Calculated Fields**: Days in submission, SLA status, Submission Score, Duplicate flag
- **Relationships**: Many-to-many with Correspondence, one-to-one with Quote Request
- **Historical Tracking**: Archive status changes and decision history

### Document & Extraction Best Practices
- Store extracted data in structured fields, not unstructured text
- Maintain confidence scores from AI extraction for audit and quality tracking
- Link extracted documents to submission for reference
- Version extracted data if requirements change

### Business Rules
- Triage rules should be configurable without code changes
- Use decision tables for complex scoring logic
- Score ranges: 1-100, with clear thresholds for prioritization bands
- Document business rule owners and update dates

## UI/UX Conventions

### Submission List (Workbench)
- Default sort: Submission Score (descending) → Date Received (newest)
- Key columns: Submission ID, Broker, Policy Type, Premium, Score, Status, Days Aging
- Quick filters: Status (New, Cleared, Quoted), Policy Type, SLA Status (At Risk, Critical)
- Bulk actions: Assign to underwriter, change status, add notes

### Submission Detail View
- Left panel: Submission metadata and status
- Center: Correspondence history with AI summaries
- Right panel: Extracted data, clearance checklist, next steps
- Action buttons: Clear, Assign, Submit Quote, Contact Broker

### Clearance Workflow
- Step 1: Data completeness validation with missing field indicators
- Step 2: Sanctions/duplicate screening results with exception handling
- Step 3: Preliminary risk assessment with business rule flags
- Step 4: Final clearance decision with optional notes
- Estimated time: 2-5 minutes per submission

### Alerts & Status Indicators
- Red: Critical (failed sanctions, duplicate detected, SLA at risk)
- Yellow: Warning (missing data, high complexity score, aged submissions)
- Green: Ready for review (fully cleared, low risk)
- Blue: Informational (awaiting broker response, escalated for review)

## Integration Patterns

### Email Ingestion
- Parse sender email and attachment metadata
- Extract key fields using AI Skills (applicant, location, coverage, limits)
- Link correspondence to existing submissions by broker reference number
- Handle multi-message threads as single submission conversation

### Guidewire Integration
- Map Appian submission fields to Guidewire Job/Quote schema
- Trigger quote creation only on cleared submissions
- Sync premium and coverage selections back to Appian
- Handle Guidewire quote statuses in Appian workflow

### External Data Enrichment
- Call sanctions screening services before submission clearance
- Query customer database to flag renewal vs. new business
- Integrate third-party risk assessment tools for complexity scoring
- Log all enrichment calls for compliance audit trails

## Development Best Practices

### Code Organization
- Use Expression Rules for reusable business logic (scoring, validation)
- Use Constants for all configurable thresholds and messages
- Keep Process Models focused on orchestration, not complex logic
- Use Service Accounts for system-initiated actions (extractions, enrichment)

### Testing Approach
- Create test submissions with varied document types and field completeness
- Validate extraction accuracy against known-good submissions
- Test triage scoring with boundary cases
- Verify Guidewire integration with sandbox environment
- Load test workbench performance with 5000+ submissions

### Performance Considerations
- Archive closed submissions after 90 days to workbench size
- Use pagination for correspondence lists (limit to 20 per page)
- Cache business rules data (triage scoring, policy types)
- Use batch jobs for bulk extraction during off-peak hours
- Monitor extraction job queues for backlog

### Documentation Standards
- Document all custom business rules with business owner and effective date
- Maintain decision table documentation for triage scoring logic
- Include extraction field mapping documentation for each document type
- Create runbooks for common underwriter questions and escalations

## Compliance & Risk Controls

- All submissions require evidence of sanctions clearance before quote
- Automatic flagging of duplicate submissions within 30-day window
- Audit trail of all underwriting decisions with user and timestamp
- PII masking in logs and system summaries
- Role-based visibility (Brokers see only their submissions)
- Monthly compliance reporting on screening results

## Success Metrics

- Time to quote: Target <24 hours from submission receipt
- Submission clearance rate: >95% automated clearance without escalation
- Extraction accuracy: >92% for key fields (applicant, location, coverage)
- Underwriter productivity: 30% increase in submissions processed per day
- SLA compliance: 98% of submissions cleared within service level

## Known Constraints & Assumptions

- Broker submissions predominantly via email (integration may be needed for portal)
- Policy types limited to standard P&C lines (HO, Auto, Commercial)
- Guidewire Quote system is downstream dependency
- Extraction quality depends on document quality and standardization
- External data sources have rate limits (batch during off-peak if needed)
