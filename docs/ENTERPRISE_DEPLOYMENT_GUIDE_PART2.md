# Enterprise Deployment Guide - Part 2

**Continuation from ENTERPRISE_DEPLOYMENT_GUIDE.md**

---

## 🐛 Bug Reporting & Issue Tracking System (Continued)

### 1. Dataverse Bug Reports Table (Complete Schema)

**Table: BugReports**
```
Display Name: Bug Reports
Logical Name: cr_bugreports
Primary Column: Bug ID

Columns:
├── cr_bugreportid (Primary Key, Auto-number: BUG-{0000})
├── cr_title (Text, 200, required)
├── cr_description (Multiline text, required)
├── cr_severity (Choice: Low, Medium, High, Critical)
├── cr_status (Choice: New, In Progress, Resolved, Closed, Cannot Reproduce)
├── cr_screen (Text, 100)
├── cr_formtype (Text, 50)
├── cr_screenshot (Image)
├── cr_deviceinfo (Multiline text/JSON)
├── cr_appversion (Text, 20)
├── cr_reportedby (Lookup: User)
├── cr_reportedon (DateTime)
├── cr_assignedto (Lookup: User)
├── cr_resolvedon (DateTime)
├── cr_resolution (Multiline text)
├── cr_clientid (Text, 50, indexed)
├── cr_category (Choice: UI, Data, Performance, Crash, Other)
├── cr_reproducible (Boolean)
└── cr_stepstoreproduce (Multiline text)
```

### 2. Bug Report UI Implementation

**Create: `Src/BugReportScreen.fx.yaml`**

**Key Features:**
- Floating action button (FAB) accessible from any screen
- Auto-capture context (screen, user, time, device)
- Screenshot attachment
- Severity selection
- Quick submit with minimal friction

**UI Components:**

```powerfx
BugReportScreen As screen:
    Fill: =ColorGray50
    AccessibleLabel: ="Report a bug or issue"
    OnVisible: |
        =// Pre-fill context
        Set(varBugContext, {
            Screen: varCurrentScreen,
            User: User().FullName,
            Email: User().Email,
            Timestamp: Now(),
            DeviceInfo: JSON(Device, JSONFormat.IncludeBinaryData),
            AppVersion: "1.0.0"
        })
    
    // ==============================================
    // FLOATING ACTION BUTTON (FAB) - Global
    // ==============================================
    BugReportFAB As button:
        Height: =56
        Width: =56
        X: =Parent.Width - 72
        Y: =Parent.Height - 120
        Text: =""
        Icon: =Icon.Bug
        Fill: =ColorDanger
        HoverFill: =ColorDangerDark
        Color: =ColorWhite
        BorderRadius: =28
        OnSelect: =Set(varShowBugReportModal, true)
        AccessibleLabel: ="Report a bug or issue"
        Visible: =FeatureBugReporting && !varShowBugReportModal
        TabIndex: =99
        
    // ==============================================
    // BUG REPORT MODAL
    // ==============================================
    BugReportOverlay As rectangle:
        Fill: =RGBA(0, 0, 0, 0.6)
        Height: =Parent.Height
        Width: =Parent.Width
        X: =0
        Y: =0
        Visible: =varShowBugReportModal
        OnSelect: =Set(varShowBugReportModal, false)
        
    BugReportCard As rectangle:
        Fill: =ColorWhite
        Width: =Min(600, Parent.Width - 32)
        Height: =Min(700, Parent.Height - 100)
        X: =(Parent.Width - Self.Width) / 2
        Y: =(Parent.Height - Self.Height) / 2
        Visible: =varShowBugReportModal
        BorderColor: =ColorBorderDefault
        BorderThickness: =1
        RadiusTopLeft: =12
        RadiusTopRight: =12
        RadiusBottomLeft: =12
        RadiusBottomRight: =12
        
    // Header
    BugReportHeader As label:
        Text: ="Report an Issue"
        X: =BugReportCard.X + 24
        Y: =BugReportCard.Y + 24
        Width: =BugReportCard.Width - 48
        Height: =40
        FontSize: =24
        FontWeight: =FontWeight.Bold
        Color: =ColorTextPrimary
        
    CloseButton As icon.Cancel:
        Icon: =Icon.Cancel
        Height: =32
        Width: =32
        X: =BugReportCard.X + BugReportCard.Width - 48
        Y: =BugReportCard.Y + 24
        Color: =ColorTextSecondary
        OnSelect: =Set(varShowBugReportModal, false); Reset(BugTitleInput); Reset(BugDescriptionInput)
        
    // Context Info (Auto-filled)
    ContextLabel As label:
        Text: ="Context (auto-captured)"
        X: =BugReportCard.X + 24
        Y: =BugReportHeader.Y + BugReportHeader.Height + 16
        FontSize: =12
        FontWeight: =FontWeight.Semibold
        Color: =ColorTextSecondary
        
    ContextInfo As label:
        Text: |
            ="Screen: " & varBugContext.Screen & Char(10) &
             "User: " & varBugContext.User & Char(10) &
             "Time: " & Text(varBugContext.Timestamp, "yyyy-mm-dd hh:mm:ss") & Char(10) &
             "Version: " & varBugContext.AppVersion
        X: =BugReportCard.X + 24
        Y: =ContextLabel.Y + ContextLabel.Height + 8
        Width: =BugReportCard.Width - 48
        Height: =80
        FontSize: =12
        Color: =ColorTextSecondary
        Font: =Font.'Courier New'
        
    // Title Input
    TitleLabel As label:
        Text: ="Issue Title *"
        X: =BugReportCard.X + 24
        Y: =ContextInfo.Y + ContextInfo.Height + 16
        FontSize: =14
        FontWeight: =FontWeight.Semibold
        Color: =ColorTextPrimary
        
    BugTitleInput As text:
        X: =BugReportCard.X + 24
        Y: =TitleLabel.Y + TitleLabel.Height + 8
        Width: =BugReportCard.Width - 48
        Height: =48
        HintText: ="Brief description of the issue"
        Mode: =TextMode.SingleLine
        BorderColor: =ColorBorderDefault
        BorderThickness: =2
        PaddingLeft: =16
        FontSize: =16
        
    // Severity Selection
    SeverityLabel As label:
        Text: ="Severity *"
        X: =BugReportCard.X + 24
        Y: =BugTitleInput.Y + BugTitleInput.Height + 16
        FontSize: =14
        FontWeight: =FontWeight.Semibold
        Color: =ColorTextPrimary
        
    SeverityDropdown As dropdown:
        X: =BugReportCard.X + 24
        Y: =SeverityLabel.Y + SeverityLabel.Height + 8
        Width: =BugReportCard.Width - 48
        Height: =48
        Items: =["Low - Minor inconvenience", 
                 "Medium - Impacts workflow", 
                 "High - Blocks critical task", 
                 "Critical - App unusable"]
        DefaultSelectedItems: =["Medium - Impacts workflow"]
        BorderColor: =ColorBorderFocus
        FontSize: =16
        
    // Description Input
    DescriptionLabel As label:
        Text: ="What happened? *"
        X: =BugReportCard.X + 24
        Y: =SeverityDropdown.Y + SeverityDropdown.Height + 16
        FontSize: =14
        FontWeight: =FontWeight.Semibold
        Color: =ColorTextPrimary
        
    BugDescriptionInput As text:
        X: =BugReportCard.X + 24
        Y: =DescriptionLabel.Y + DescriptionLabel.Height + 8
        Width: =BugReportCard.Width - 48
        Height: =120
        HintText: ="Describe what you were doing and what went wrong..."
        Mode: =TextMode.MultiLine
        BorderColor: =ColorBorderDefault
        BorderThickness: =2
        PaddingLeft: =16
        PaddingTop: =16
        FontSize: =14
        
    // Submit Button
    SubmitBugButton As button:
        X: =BugReportCard.X + 24
        Y: =BugDescriptionInput.Y + BugDescriptionInput.Height + 24
        Width: =BugReportCard.Width - 48
        Height: =48
        Text: ="Submit Bug Report"
        Fill: =If(!IsBlank(BugTitleInput.Text) && !IsBlank(BugDescriptionInput.Text),
                 ColorPrimary,
                 ColorGray300)
        HoverFill: =ColorPrimaryDark
        Color: =ColorWhite
        FontSize: =16
        FontWeight: =FontWeight.Bold
        DisplayMode: =If(!IsBlank(BugTitleInput.Text) && !IsBlank(BugDescriptionInput.Text),
                        DisplayMode.Edit,
                        DisplayMode.Disabled)
        OnSelect: |
            =// Submit bug report
            Patch(BugReports, Defaults(BugReports), {
                cr_title: BugTitleInput.Text,
                cr_description: BugDescriptionInput.Text,
                cr_severity: First(Split(SeverityDropdown.Selected.Value, " -")).Result,
                cr_status: "New",
                cr_screen: varBugContext.Screen,
                cr_formtype: varCurrentFormType,
                cr_deviceinfo: varBugContext.DeviceInfo,
                cr_appversion: varBugContext.AppVersion,
                cr_reportedby: {User: User()},
                cr_reportedon: Now(),
                cr_clientid: Param("ClientId"),
                cr_category: "Other"
            });
            
            // Show confirmation
            Notify("Bug report submitted successfully! Thank you for your feedback.", 
                   NotificationType.Success, 
                   3000);
            
            // Reset and close
            Set(varShowBugReportModal, false);
            Reset(BugTitleInput);
            Reset(BugDescriptionInput);
            Reset(SeverityDropdown)
```

### 3. Admin Bug Tracking Dashboard

**Create: Power BI Report or Screen for Admins**

**Metrics to Track:**
- Total bugs by status
- Bugs by severity
- Average resolution time
- Top reported issues
- Bugs by screen/form
- Reporter leaderboard (most helpful users)

**Dataverse View: ActiveBugs**
```powerfx
Filter(BugReports, 
       cr_status <> "Closed" && 
       cr_clientid = Param("ClientId"))
OrderBy: cr_severity (Critical first), cr_reportedon (newest first)
```

---

## 📚 Training & Knowledge Transfer System

### Objective
Provide contextual, non-intrusive training that doesn't disrupt primary workflow.

### 1. Training Content Architecture

**Dataverse Table: TrainingContent**
```
Display Name: Training Content
Logical Name: cr_trainingcontent

Columns:
├── cr_trainingid (Primary Key, GUID)
├── cr_title (Text, 200, required)
├── cr_description (Multiline text)
├── cr_contenttype (Choice: Article, Video, Tutorial, FAQ, Quick Tip)
├── cr_category (Choice: Getting Started, Forms, Features, Troubleshooting)
├── cr_screen (Text, 100) // Which screen this relates to
├── cr_formtype (Text, 50) // Which form type
├── cr_contenturl (URL)
├── cr_thumbnailurl (URL)
├── cr_duration (Number) // Minutes for videos
├── cr_difficulty (Choice: Beginner, Intermediate, Advanced)
├── cr_tags (Text, 500) // Comma-separated
├── cr_viewcount (Number)
├── cr_helpful (Number) // Thumbs up count
├── cr_nothelpful (Number) // Thumbs down count
├── cr_clientid (Text, 50, indexed)
├── cr_isactive (Boolean)
├── cr_order (Number)
└── cr_lastupdated (DateTime)
```

### 2. In-App Help System Implementation

**Help Icon (Available on All Screens):**
```powerfx
// In each screen's header
HelpIcon As icon.Help:
    Icon: =Icon.Help
    Height: =40
    Width: =40
    X: =Parent.Width - 56
    Y: =16
    Color: =ColorPrimary
    OnSelect: =Set(varShowHelpPanel, !varShowHelpPanel)
    AccessibleLabel: ="Open help panel"
    Visible: =FeatureTrainingMode
    TabIndex: =98
```

**Slide-Out Help Panel:**
```powerfx
// Side panel that slides in from right
HelpPanel As rectangle:
    Fill: =ColorWhite
    Width: =Min(400, Parent.Width - 32)
    Height: =Parent.Height
    X: =If(varShowHelpPanel, Parent.Width - Self.Width, Parent.Width)
    Y: =0
    BorderColor: =ColorBorderDefault
    BorderThickness: =1
    // Animation using X position change
    
HelpPanelHeader As label:
    Text: ="Help & Training"
    FontSize: =20
    FontWeight: =FontWeight.Bold
    Color: =ColorTextPrimary
    X: =HelpPanel.X + 24
    Y: =24
    
CloseHelpButton As icon.Cancel:
    Icon: =Icon.Cancel
    Height: =32
    Width: =32
    X: =HelpPanel.X + HelpPanel.Width - 48
    Y: =24
    Color: =ColorTextSecondary
    OnSelect: =Set(varShowHelpPanel, false)
    
// Context-specific help
ContextualHelp As gallery.galleryVertical:
    X: =HelpPanel.X + 16
    Y: =HelpPanelHeader.Y + HelpPanelHeader.Height + 24
    Width: =HelpPanel.Width - 32
    Height: =300
    Items: =Filter(
        TrainingContent,
        cr_screen = varCurrentScreen &&
        cr_isactive = true &&
        cr_clientid = Param("ClientId")
    )
    TemplatePadding: =8
    TemplateSize: =100
    
    HelpCard As rectangle:
        Fill: =ColorGray100
        Height: =92
        Width: =Parent.TemplateWidth - 16
        RadiusTopLeft: =8
        RadiusTopRight: =8
        RadiusBottomLeft: =8
        RadiusBottomRight: =8
        OnSelect: =Launch(ThisItem.cr_contenturl)
        
    HelpIcon As icon.Document:
