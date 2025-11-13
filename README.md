# PowerApp-Form-v2

A working Microsoft Power Apps form application with source control integration. This version was created from a real .msapp file exported from Power Apps Studio and includes a complete form with name, email, and message input fields.

## 🎯 Overview

This repository demonstrates the proper workflow for Power Apps development with source control:
1. ✅ Started with real .msapp from Power Apps Studio
2. ✅ Unpacked using Power Platform CLI
3. ✅ Modified source files (added form controls)
4. ✅ Successfully repacked into working .msapp
5. ✅ Ready for deployment to Power Platform

## ✨ Form Features

The form includes:
- **Header Label**: Blue header with "Form Title"
- **Name Input**: Text field for name entry with hint text
- **Email Input**: Text field for email entry with hint text
- **Message Input**: Multi-line text area for messages
- **Submit Button**: Blue button that displays success notification
- **Status Label**: Label for displaying form status

## 🛠️ Development Workflow

### Prerequisites

1. **Power Platform CLI installed**:
   ```bash
   # Install .NET SDK
   brew install dotnet
   
   # Install PAC CLI
   dotnet tool install --global Microsoft.PowerApps.CLI.Tool
   
   # Add to PATH
   export PATH="$PATH:/Users/$USER/.dotnet/tools"
   ```

### Making Changes

1. **Edit source files**:
   ```bash
   cd /path/to/PowerApp-Form-v2
   # Edit Src/Screen1.fx.yaml or other files
   ```

2. **Commit changes to Git**:
   ```bash
   git add .
   git commit -m "Your commit message"
   git push
   ```

3. **Pack for deployment**:
   ```bash
   pac canvas pack --sources . --msapp PowerApp-Form-v2.msapp
   ```

4. **Import to Power Apps**:
   - Go to https://make.powerapps.com
   - Click **Apps** → **Import canvas app**
   - Upload `PowerApp-Form-v2.msapp`
   - Configure and import

### Testing the App

After importing to Power Apps:
1. Open the app in Power Apps Studio
2. Verify all form controls are present
3. Test the submit button functionality
4. Publish when satisfied

## 📁 Repository Structure

```
PowerApp-Form-v2/
├── .gitignore                    # Git ignore rules
├── README.md                     # This file
├── CanvasManifest.json          # App manifest
├── ComponentReferences.json     # Component references
├── ControlTemplates.json        # Control templates
│
├── Connections/                 # Data source connections
├── Entropy/                     # Auto-generated metadata
│
├── Other/                       # Additional files
│   ├── References/
│   │   └── ModernThemes.json
│   └── Src/                     # Alternate source format
│
└── Src/                         # Main source code (Power Fx)
    ├── App.fx.yaml              # App-level settings
    ├── Screen1.fx.yaml          # Main screen with form
    ├── Themes.json              # Theme configuration
    └── EditorState/             # Editor state files
```

## 📝 Form Code Example

The form is defined in `Src/Screen1.fx.yaml`:

```yaml
Screen1 As screen:
    Fill: =RGBA(255, 255, 255, 1)
    
    HeaderLabel As label:
        Text: ="Form Title"
        Fill: =RGBA(0, 120, 212, 1)
        Color: =RGBA(255, 255, 255, 1)
    
    NameInput As text:
        HintText: ="Enter your name"
        Mode: =TextMode.SingleLine
    
    # ... more controls
```

## 🔄 Workflow Commands

```bash
# Unpack an .msapp file
pac canvas unpack --msapp YourApp.msapp --sources .

# Pack source files into .msapp
pac canvas pack --sources . --msapp YourApp.msapp

# Initialize Git repository
git init
git add .
git commit -m "Initial commit"

# Push to GitHub
git remote add origin https://github.com/yourusername/your-repo.git
git push -u origin main
```

## ⚠️ Important Notes

- The checksum warning when packing is **normal** and expected after editing source files
- Always test packed .msapp files in Power Apps Studio before deploying
- Keep sensitive connection strings out of version control (use .gitignore)
- The `pac canvas unpack/pack` commands are deprecated but still functional

## 🔗 Resources

- [Power Apps Documentation](https://docs.microsoft.com/powerapps/)
- [Power Platform CLI](https://docs.microsoft.com/power-platform/developer/cli/introduction)
- [Power Fx Language](https://docs.microsoft.com/power-platform/power-fx/)
- [Source Control for Canvas Apps](https://docs.microsoft.com/power-platform/alm/source-control-canvas-apps)

## 👤 Author

**Lance McDaniel**
- GitHub: [@mcdugal19](https://github.com/mcdugal19)

## 📄 License

This project is provided as-is for educational and development purposes.

---

## 🎉 Success!

This repository demonstrates a **working** Power Apps development workflow with source control. Unlike manually created files, this version:
- ✅ Unpacks from real .msapp
- ✅ Packs back into valid .msapp
- ✅ Imports successfully into Power Apps
- ✅ Maintains full functionality

**Ready for team collaboration and continuous development!**
