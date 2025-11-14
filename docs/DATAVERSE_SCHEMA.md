# Dataverse Schema Design
## Field Technician Multi-Form Application

This document outlines the recommended Dataverse table structures for the multi-form field technician application. This design supports scalable form management, offline capabilities, and future expansion.

---

## 📋 Table of Contents
1. [Overview](#overview)
2. [Primary Tables](#primary-tables)
3. [Supporting Tables](#supporting-tables)
4. [Relationships](#relationships)
5. [Security Model](#security-model)
6. [Implementation Guide](#implementation-guide)

---

## Overview

### Design Philosophy
- **Single Table Pattern**: Use one main table with FormType discriminator for flexibility
- **Metadata-Driven**: Store form configurations to enable dynamic form rendering
- **Audit-Ready**: Include comprehensive tracking fields
- **Offline-First**: Support local storage and sync patterns

### Key Benefits
- ✅ Easy to add new form types without schema changes
- ✅ Simplified cross-form reporting and analytics
- ✅ Centralized form lifecycle management
- ✅ Consistent validation and business rules
- ✅ Scalable to 100+ form types

---

## Primary Tables

### 1. FieldTechnicianForms (Main Data Table)

**Purpose**: Stores all form submissions from field technicians across all form types

**Table Configuration**:
- **Display Name**: Field Technician Forms
- **Plural Name**: Field Technician Forms
- **Schema Name**: cr6f3_fieldtechnicianforms
- **Primary Column**: Form Number (Auto-numbering: FT-{SEQNUM:7})
- **Ownership**: User or Team owned
- **Track Changes**: Yes (for audit trail)
- **Enable for Mobile**: Yes

**Columns**:

| Column Name | Type | Required | Description | Example |
|------------|------|----------|-------------|---------|
| **Form Number** | Auto-number | Yes | Unique form identifier | FT-0001234 |
| **Form Type** | Choice | Yes | Discriminator for form category | Safety Inspection, Equipment Maintenance, Incident Report |
| **Form Name** | Text (200) | Yes | Display name of the form | "Daily Safety Inspection", "Equipment Check" |
| **Form Version** | Text (20) | No | Version of form template used | "v2.1", "v1.0" |
| **Status** | Choice | Yes | Current form status | Draft, Submitted, Under Review, Approved, Rejected |
| **Priority** | Choice | No | Form priority level | Low, Medium, High, Critical |
| **Technician** | Lookup (User) | Yes | Field technician who created form | John Doe |
| **Facility** | Lookup (Facilities) | Yes | Location/facility where form was completed | Building A, Site 123 |
| **Equipment** | Lookup (Equipment) | No | Related equipment (if applicable) | Generator #45 |
| **Work Order** | Lookup (Work Orders) | No | Related work order (if applicable) | WO-12345 |
| **Submission Date** | Date and Time | Yes | When form was submitted | 2024-11-14 09:30 AM |
| **Completion Date** | Date and Time | No | When work was completed | 2024-11-14 10:15 AM |
| **Scheduled Date** | Date Only | No | Scheduled inspection/work date | 2024-11-15 |
| **Form Data JSON** | Multi-line Text (Max) | No | Flexible storage for variable form fields | {"temperature": 72, "pressure": 45} |
| **Signature** | Image | No | Technician signature | [Image data] |
| **GPS Location** | Text (100) | No | GPS coordinates at submission | "40.7128,-74.0060" |
| **Photos** | File (multiple) | No | Attached photos | [Photo attachments] |
| **Comments** | Multi-line Text (4000) | No | Additional notes/comments | "Equipment shows signs of wear" |
| **Reviewed By** | Lookup (User) | No | Supervisor who reviewed | Jane Smith |
| **Review Date** | Date and Time | No | When form was reviewed | 2024-11-14 11:00 AM |
| **Review Notes** | Multi-line Text (2000) | No | Reviewer comments | "Approved - schedule maintenance" |
| **Is Offline Created** | Yes/No | No | Created while offline | Yes/No |
| **Sync Status** | Choice | No | Sync status for offline forms | Pending, Synced, Error |
| **Last Modified Date** | Date and Time | Auto | System field for auditing | [Auto-populated] |

**Choice Field Values**:

**Form Type** (Extensible for future types):
- Safety Inspection
- Equipment Maintenance
- Incident Report
- Pre-Operational Check
- Post-Work Inspection
- Quality Control
- Environmental Assessment
- Compliance Audit
- [Add as needed]

**Status**:
- Draft (0)
- Submitted (1)
- Under Review (2)
- Approved (3)
- Rejected (4)
- Requires Revision (5)

**Priority**:
- Low (0)
- Medium (1)
- High (2)
- Critical (3)

**Sync Status**:
- Pending (0)
- Synced (1)
- Error (2)

---

### 2. FormTemplates (Configuration Table)

**Purpose**: Stores metadata about available forms - enables dynamic form catalog

**Table Configuration**:
- **Display Name**: Form Templates
- **Plural Name**: Form Templates
- **Schema Name**: cr6f3_formtemplates
- **Primary Column**: Form Template Name
- **Ownership**: Organization owned
- **Track Changes**: Yes

**Columns**:

| Column Name | Type | Required | Description | Example |
|------------|------|----------|-------------|---------|
| **Form Template Name** | Text (200) | Yes | Name of the form template | "Daily Safety Inspection v2.1" |
| **Form Type** | Choice | Yes | Links to Form Type in main table | Safety Inspection |
| **Form Number** | Text (50) | Yes | Unique identifier for searching | SF-001 |
| **Description** | Multi-line Text (2000) | No | What this form is used for | "Required daily safety check..." |
| **Icon Name** | Text (100) | No | Icon identifier for UI | "Shield", "Wrench", "Alert" |
| **Icon Color** | Text (20) | No | Hex color for icon | "#0078D4", "#107C10" |
| **Version** | Text (20) | Yes | Template version | "v2.1" |
| **Is Active** | Yes/No | Yes | Whether form is available | Yes |
| **Is Required** | Yes/No | No | Whether form is mandatory | Yes |
| **Frequency** | Choice | No | How often required | Daily, Weekly, Monthly, As Needed |
| **Estimated Duration** | Whole Number | No | Minutes to complete | 15 |
| **Category** | Choice | No | Form category | Safety, Maintenance, Quality, Compliance |
| **Department** | Choice | No | Which department uses it | Operations, Safety, Engineering |
| **Screen Name** | Text (100) | No | PowerApps screen to navigate to | "SafetyInspectionScreen" |
| **Required Fields JSON** | Multi-line Text (Max) | No | Defines required form fields | {"fields": ["temperature", "status"]} |
| **Validation Rules JSON** | Multi-line Text (Max) | No | Custom validation logic | {"temperature": {"min": 0, "max": 100}} |
| **Sort Order** | Whole Number | No | Display order in gallery | 10, 20, 30 |
| **Tags** | Text (500) | No | Searchable keywords | "safety, inspection, daily" |
| **Last Updated** | Date and Time | Auto | When template was modified | [Auto-populated] |
| **Created By** | Lookup (User) | Auto | Who created template | [Auto-populated] |

**Choice Field Values**:

**Frequency**:
- As Needed (0)
- Daily (1)
- Weekly (2)
- Bi-Weekly (3)
- Monthly (4)
- Quarterly (5)
- Annually (6)

**Category**:
- Safety (0)
- Maintenance (1)
- Quality (2)
- Compliance (3)
- Environmental (4)
- Operations (5)
- Training (6)

---

## Supporting Tables

### 3. Facilities

**Purpose**: Locations where field work is performed

| Column Name | Type | Description |
|------------|------|-------------|
| **Facility Name** | Text (200) | Site/location name |
| **Facility Code** | Text (50) | Short identifier |
| **Address** | Text (500) | Physical address |
| **GPS Coordinates** | Text (100) | Lat/Long coordinates |
| **Region** | Choice | Geographic region |
| **Is Active** | Yes/No | Currently operational |

### 4. Equipment

**Purpose**: Equipment that requires inspection/maintenance

| Column Name | Type | Description |
|------------|------|-------------|
| **Equipment Name** | Text (200) | Equipment identifier |
| **Equipment Number** | Text (100) | Serial/asset number |
| **Equipment Type** | Choice | Category of equipment |
| **Facility** | Lookup (Facilities) | Where it's located |
| **Manufacturer** | Text (100) | Equipment manufacturer |
| **Model** | Text (100) | Model number |
| **Installation Date** | Date Only | When installed |
| **Last Service Date** | Date Only | Last maintenance |
| **Next Service Due** | Date Only | Upcoming maintenance |
| **Status** | Choice | Operational status |

### 5. Users (System Table - Reference Only)

**Purpose**: Built-in Dataverse Users table

**Key Fields to Use**:
- Full Name
- Primary Email
- Business Phone
- Job Title
- Department

---

## Relationships

### One-to-Many Relationships

1. **User → FieldTechnicianForms** (Technician)
   - One technician creates many forms
   - Cascade: Restrict (cannot delete user with forms)

2. **User → FieldTechnicianForms** (Reviewer)
   - One reviewer reviews many forms
   - Cascade: Restrict

3. **Facilities → FieldTechnicianForms**
   - One facility has many forms
   - Cascade: Restrict

4. **Equipment → FieldTechnicianForms**
   - One equipment has many inspection forms
   - Cascade: Restrict (optional relationship)

5. **FormTemplates → FieldTechnicianForms** (via Form Type)
   - Logical relationship via Choice values
   - Not a direct lookup to maintain flexibility

---

## Security Model

### Security Roles

#### Field Technician Role
**Permissions**:
- **FieldTechnicianForms**: Create, Read (Own), Write (Own), Append (Own)
- **FormTemplates**: Read (Organization)
- **Facilities**: Read (Organization)
- **Equipment**: Read (Organization)

#### Supervisor Role
**Permissions**:
- **FieldTechnicianForms**: Create, Read (Business Unit), Write (Business Unit), Approve
- **FormTemplates**: Read (Organization), Write (Organization)
- **Facilities**: Read (Organization)
- **Equipment**: Read (Organization)

#### Administrator Role
**Permissions**:
- Full access to all tables
- Can configure FormTemplates
- Can manage security roles

### Field-Level Security

**Sensitive Fields** (Optional):
- Review Notes (only reviewers can see)
- Signature (only technician and reviewers)

---

## Implementation Guide

### Phase 1: Create Tables (Week 1)

```powershell
# Using Power Platform CLI or admin portal
# 1. Create FieldTechnicianForms table with all columns
# 2. Create FormTemplates table
# 3. Create or configure Facilities table
# 4. Create or configure Equipment table
# 5. Set up relationships
```

### Phase 2: Seed Initial Data (Week 1)

**FormTemplates Initial Records**:

| Form Template Name | Form Type | Form Number | Category | Sort Order |
|-------------------|-----------|-------------|----------|------------|
| Daily Safety Inspection | Safety Inspection | SF-001 | Safety | 10 |
| Equipment Maintenance Check | Equipment Maintenance | MF-001 | Maintenance | 20 |
| Incident Report | Incident Report | IR-001 | Safety | 30 |
| Pre-Operational Checklist | Pre-Operational Check | PC-001 | Operations | 40 |

### Phase 3: Connect to Power App (Week 2)

```powerfx
// In App.OnStart or App.Formulas
FormCatalog = FormTemplates;
ActiveForms = Filter(FormTemplates, 'Is Active' = true);
```

### Phase 4: Testing (Week 2)

1. Create test forms via Power App
2. Verify data saves correctly
3. Test offline scenarios
4. Test form approval workflow
5. Verify reporting queries

---

## Data Migration Considerations

### From Existing Systems

If migrating from spreadsheets or other systems:

1. **Export existing data to Excel/CSV**
2. **Use Power Platform Data Import Tool**
3. **Map columns carefully**
4. **Validate imported data**
5. **Test thoroughly before go-live**

### Backwards Compatibility

- Form Data JSON field provides flexibility for schema evolution
- New fields can be added without breaking existing forms
- Version field tracks changes over time

---

## Performance Optimization

### Indexing Strategy

**Create indexes on**:
- Form Type (high selectivity)
- Status (frequently filtered)
- Technician (lookup field)
- Facility (lookup field)
- Submission Date (range queries)

### Views for Quick Access

**Recommended Views**:
1. **My Active Forms** - Current user's draft/submitted forms
2. **Pending Review** - Forms awaiting approval
3. **Today's Forms** - Forms submitted today
4. **By Facility** - Grouped by location
5. **By Form Type** - Grouped by type

---

## Reporting & Analytics

### Common Queries

```powerfx
// Forms submitted today
TodaysForms = Filter(
    FieldTechnicianForms,
    'Submission Date' >= Today()
)

// Pending approvals
PendingReview = Filter(
    FieldTechnicianForms,
    Status = 'Status (FieldTechnicianForms)'.'Submitted'
)

// Safety forms this month
SafetyFormsThisMonth = Filter(
    FieldTechnicianForms,
    'Form Type' = 'Form Type (FieldTechnicianForms)'.'Safety Inspection' &&
    'Submission Date' >= DateAdd(Today(), -30, Days)
)
```

### Power BI Integration

Export data to Power BI for:
- Completion rate dashboards
- Technician performance metrics
- Facility compliance tracking
- Trend analysis over time

---

## Future Enhancements

### Potential Additions

1. **FormAttachments** table for better file management
2. **FormComments** table for threaded discussions
3. **FormHistory** table for audit trail
4. **NotificationPreferences** table for alerts
5. **FormSchedule** table for recurring forms

### Advanced Features

- Workflow automation with Power Automate
- Integration with Azure Maps for location services
- AI Builder for form recognition
- Teams integration for notifications

---

## Best Practices

### Do's ✅
- Use descriptive names for columns
- Document all choice values
- Plan for offline scenarios
- Use proper data types
- Implement proper security

### Don'ts ❌
- Don't create too many tables (start simple)
- Don't put large files in JSON fields
- Don't skip indexes on filtered fields
- Don't hardcode values in app (use tables)
- Don't forget to backup before major changes

---

## Support & Maintenance

### Regular Tasks
- Monitor table size and performance
