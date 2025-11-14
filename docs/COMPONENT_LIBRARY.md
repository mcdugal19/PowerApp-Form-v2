# Component Library Documentation
## Reusable UI Components for Field Technician Forms

This document outlines the reusable components designed for the multi-form field technician application. These components ensure consistency, reduce duplication, and speed up development of new forms.

---

## 📋 Table of Contents
1. [Overview](#overview)
2. [Component Architecture](#component-architecture)
3. [Core Components](#core-components)
4. [Form Components](#form-components)
5. [Navigation Components](#navigation-components)
6. [Usage Examples](#usage-examples)
7. [Best Practices](#best-practices)

---

## Overview

### Design Principles
- **Consistency**: All forms share the same look and feel
- **Reusability**: Write once, use everywhere
- **Maintainability**: Update in one place, applies to all forms
- **Responsiveness**: Components adapt to screen size automatically
- **Accessibility**: Built with proper labels and navigation

### Component Benefits
- ✅ 70% faster form development
- ✅ Consistent user experience
- ✅ Easier to maintain and update
- ✅ Smaller app file size
- ✅ Better performance

---

## Component Architecture

### Component Organization

```
Components/
├── Layout/
│   ├── FormHeader
│   ├── FormFooter
│   └── FormCard
├── Input/
│   ├── TextInput
│   ├── EmailInput
│   ├── PhoneInput
│   ├── NumberInput
│   └── DateInput
├── Selection/
│   ├── DropdownField
│   ├── RadioGroup
│   └── CheckboxField
├── Media/
│   ├── PhotoCapture
│   ├── SignaturePad
│   └── FileAttachment
├── Display/
│   ├── StatusBadge
│   ├── InfoCard
│   └── ProgressIndicator
└── Navigation/
    ├── FormSelector
    └── BackButton
```

---

## Core Components

### 1. FormHeader Component

**Purpose**: Standard header for all forms with title and subtitle

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `HeaderTitle` | Input (Text) | "Form Title" | Main heading text |
| `HeaderSubtitle` | Input (Text) | "" | Subtitle text |
| `ShowBack` | Input (Boolean) | true | Show back button |
| `BackAction` | Output (Boolean) | false | True when back clicked |

**Visual Properties**:
- Height: Responsive (80-120px based on screen size)
- Background: Gradient (Primary blue)
- Title: Bold, responsive font size
- Subtitle: Semi-transparent white

**Power Fx Formula Example**:
```powerfx
// Component Height
Height = If(App.Width < 600, 80, If(App.Width < 900, 100, 120))

// Title Font Size
TitleSize = If(App.Width < 600, 20, If(App.Width < 900, 24, 28))

// BackAction Output
BackAction = varBackButtonPressed
```

**Usage**:
```powerfx
// Insert component and set properties
FormHeader.HeaderTitle = "Safety Inspection"
FormHeader.HeaderSubtitle = "Daily Safety Check - Building A"

// Handle back button
If(FormHeader.BackAction, Navigate(HomeScreen, ScreenTransition.Fade))
```

---

### 2. FormFooter Component

**Purpose**: Standard footer with copyright and app info

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `CopyrightText` | Input (Text) | "© 2024 Company" | Footer text |
| `ShowVersion` | Input (Boolean) | false | Show app version |

**Visual Properties**:
- Height: Fixed (35px)
- Position: Bottom of screen
- Text: Centered, small gray font

---

### 3. FormCard Component

**Purpose**: Container card for form content with rounded corners and shadow

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `CardPadding` | Input (Number) | 20 | Interior padding |
| `MaxWidth` | Input (Number) | 900 | Maximum card width |

**Visual Properties**:
- Background: White
- Border: Light gray, 1px
- Border Radius: Responsive (8-12px)
- Shadow: Subtle drop shadow
- Width: Responsive, centered

**Responsive Behavior**:
```powerfx
// Card Width
Width = Min(
    Parent.Width - If(App.Width < 600, 20, If(App.Width < 900, 40, 60)),
    MaxWidth
)

// X Position (centered)
X = (Parent.Width - Self.Width) / 2
```

---

## Form Components

### 4. TextInput Component

**Purpose**: Standardized text input field with label

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FieldLabel` | Input (Text) | "Field Label" | Label text |
| `Required` | Input (Boolean) | false | Is field required |
| `PlaceholderText` | Input (Text) | "" | Hint text |
| `MaxLength` | Input (Number) | 255 | Character limit |
| `InputValue` | Output (Text) | "" | Current value |
| `IsValid` | Output (Boolean) | true | Validation status |

**Visual Properties**:
- Label: Bold, required asterisk if needed
- Input: Responsive height (45-50px)
- Border: 2px, blue when focused
- Border Radius: 4px
- Padding: 15px left

**Validation**:
```powerfx
// IsValid Output
IsValid = If(
    Required,
    !IsBlank(TextInput.Text) && Len(TextInput.Text) <= MaxLength,
    true
)

// Border Color
BorderColor = If(
    !IsBlank(TextInput.Text),
    RGBA(0, 120, 212, 1),  // Blue when has content
    If(Required && !IsValid, RGBA(212, 0, 0, 1), RGBA(200, 200, 200, 1))  // Red if invalid
)
```

**Usage**:
```powerfx
// Configure component
TextInput_Name.FieldLabel = "Full Name"
TextInput_Name.Required = true
TextInput_Name.PlaceholderText = "John Doe"
TextInput_Name.MaxLength = 100

// Get value
Set(varName, TextInput_Name.InputValue)

// Check validation
If(!TextInput_Name.IsValid, 
    Notify("Please enter a name", NotificationType.Error)
)
```

---

### 5. DropdownField Component

**Purpose**: Standardized dropdown selection with label

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FieldLabel` | Input (Text) | "Select Option" | Label text |
| `Required` | Input (Boolean) | false | Is field required |
| `Options` | Input (Table) | [] | Dropdown options |
| `HintText` | Input (Text) | "Select..." | Placeholder |
| `SelectedValue` | Output (Record) | Blank() | Selected item |
| `IsValid` | Output (Boolean) | true | Validation status |

**Usage**:
```powerfx
// Configure component
DropdownField_Dept.FieldLabel = "Department"
DropdownField_Dept.Required = true
DropdownField_Dept.Options = ["Engineering", "Sales", "Marketing"]
DropdownField_Dept.HintText = "Select your department"

// Get selected value
Set(varDepartment, DropdownField_Dept.SelectedValue)
```

---

### 6. DateInput Component

**Purpose**: Standardized date picker with label

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FieldLabel` | Input (Text) | "Date" | Label text |
| `Required` | Input (Boolean) | false | Is field required |
| `MinDate` | Input (Date) | Date(1900,1,1) | Minimum allowed date |
| `MaxDate` | Input (Date) | Date(2100,12,31) | Maximum allowed date |
| `SelectedDate` | Output (Date) | Today() | Selected date |
| `IsValid` | Output (Boolean) | true | Validation status |

---

### 7. SignaturePad Component

**Purpose**: Capture technician signature

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FieldLabel` | Input (Text) | "Signature" | Label text |
| `Required` | Input (Boolean) | true | Is signature required |
| `SignatureImage` | Output (Image) | Blank() | Captured signature |
| `IsSigned` | Output (Boolean) | false | Has signature |

**Visual Properties**:
- Canvas: White background with border
- Height: 150-200px responsive
- Clear button: Red, positioned top-right
- Instructions: "Sign here" hint text

**Power Fx Example**:
```powerfx
// Check if signed
If(SignaturePad.IsSigned,
    Patch(Forms, {Signature: SignaturePad.SignatureImage}),
    Notify("Signature required", NotificationType.Error)
)
```

---

### 8. PhotoCapture Component

**Purpose**: Capture photos with device camera

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FieldLabel` | Input (Text) | "Photos" | Label text |
| `MaxPhotos` | Input (Number) | 5 | Maximum photo count |
| `Required` | Input (Boolean) | false | At least 1 photo required |
| `PhotoCollection` | Output (Table) | [] | Captured photos |
| `PhotoCount` | Output (Number) | 0 | Number of photos |

**Features**:
- Take new photo button
- Gallery of captured photos
- Delete individual photos
- Thumbnail preview
- Full-size view on tap

**Usage**:
```powerfx
// Configure component
PhotoCapture.FieldLabel = "Equipment Photos"
PhotoCapture.MaxPhotos = 3
PhotoCapture.Required = true

// Save photos
ForAll(
    PhotoCapture.PhotoCollection,
    Patch(FormPhotos, {
        FormID: varCurrentFormID,
        Photo: ThisRecord.Image
    })
)
```

---

### 9. GPSLocation Component

**Purpose**: Capture and display current GPS coordinates

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `ShowMap` | Input (Boolean) | true | Display map preview |
| `Latitude` | Output (Number) | 0 | Current latitude |
| `Longitude` | Output (Number) | 0 | Current longitude |
| `LocationString` | Output (Text) | "" | "lat,long" format |
| `Accuracy` | Output (Number) | 0 | GPS accuracy in meters |

**Features**:
- Auto-capture on component load
- Refresh button
- Map preview (optional)
- Accuracy indicator
- Address lookup (if available)

**Power Fx Example**:
```powerfx
// Get location
Location.Latitude  // Built-in Power Apps
Location.Longitude

// Component usage
GPSLocation.Latitude
GPSLocation.Longitude
GPSLocation.LocationString  // "40.7128,-74.0060"
```

---

## Navigation Components

### 10. FormSelector Component

**Purpose**: Gallery-based form selection with search and filter

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `FormData` | Input (Table) | FormTemplates | Form catalog data |
| `SearchEnabled` | Input (Boolean) | true | Show search bar |
| `FilterEnabled` | Input (Boolean) | true | Show filter chips |
| `SelectedForm` | Output (Record) | Blank() | Selected form |
| `OnFormSelect` | Output (Boolean) | false | True when form selected |

**Visual Structure**:
```
[Search Bar]
[Filter Chips: All | Safety | Maintenance | Quality]
[
  Gallery:
    - Icon (colored)
    - Form Name
    - Form Number
    - Description
    - Status Badge
    - Arrow >
]
```

**Search Logic**:
```powerfx
// Gallery Items with search and filter
Gallery.Items = 
    Search(
        Filter(
            FormData,
            'Is Active' = true &&
            (varFilterCategory = "All" || Category = varFilterCategory)
        ),
        SearchBox.Text,
        "Form Template Name", "Form Number", "Tags"
    )
```

**Usage**:
```powerfx
// On form selection
If(FormSelector.OnFormSelect,
    Set(varSelectedForm, FormSelector.SelectedForm);
    Switch(
        varSelectedForm.'Form Type',
        "Safety Inspection", Navigate(SafetyScreen, Fade),
        "Equipment Maintenance", Navigate(MaintenanceScreen, Fade),
        Navigate(GenericFormScreen, Fade)
    )
)
```

---

### 11. StatusBadge Component

**Purpose**: Display form status with colored badge

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `StatusValue` | Input (Text) | "Draft" | Status text |
| `StatusColor` | Output (Color) | Gray | Badge color |

**Status Colors**:
- **Draft**: Gray (#808080)
- **Submitted**: Blue (#0078D4)
- **Under Review**: Orange (#FFA500)
- **Approved**: Green (#107C10)
- **Rejected**: Red (#D13438)

**Visual**:
```powerfx
Fill = Switch(
    StatusValue,
    "Draft", RGBA(128, 128, 128, 1),
    "Submitted", RGBA(0, 120, 212, 1),
    "Under Review", RGBA(255, 165, 0, 1),
    "Approved", RGBA(16, 124, 16, 1),
    "Rejected", RGBA(209, 52, 56, 1),
    RGBA(128, 128, 128, 1)  // Default gray
)
```

---

### 12. ProgressIndicator Component

**Purpose**: Show form completion progress

**Custom Properties**:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `TotalFields` | Input (Number) | 10 | Total form fields |
| `CompletedFields` | Input (Number) | 0 | Filled fields |
| `ShowPercentage` | Input (Boolean) | true | Display percentage |
| `ProgressPercent` | Output (Number) | 0 | Completion % |

**Visual**:
- Progress bar (0-100%)
- Percentage text
- Color: Green when complete, blue otherwise

---

## Usage Examples

### Example 1: Building a New Form Screen

```powerfx
// Screen setup
SafetyInspectionScreen As screen:
    
    // 1. Add FormHeader
    FormHeader_Safety As FormHeader:
        HeaderTitle: "Safety Inspection"
        HeaderSubtitle: "Daily Safety Checklist"
        ShowBack: true
    
    // 2. Add FormCard container
    FormCard_Main As FormCard:
        Y: FormHeader_Safety.Y + FormHeader_Safety.Height + 15
        MaxWidth: 900
    
    // 3. Add form fields inside card
    TextInput_InspectorName As TextInput:
        Parent: FormCard_Main
        Y: 20
        FieldLabel: "Inspector Name"
