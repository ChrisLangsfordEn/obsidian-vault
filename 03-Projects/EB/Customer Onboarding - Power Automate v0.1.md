
# 🚀 Power Automate Solution for Customer Onboarding Tracking

## 🎯 Goal
- Track the **progress of onboarding cases** through multiple handoffs.  
- CEO wants a **bird’s-eye view**: how many cases are at each stage, bottlenecks, etc.  
- Keep it lightweight: minimal automation + clear reporting.

---

## 🔑 Solution Design

### 1. Define Stages in the Process
Model each step of the process:
- Stage 1: Customer Discussion  
- Stage 2: Quote Preparation  
- Stage 3: Quote Review with Customer  
- Stage 4: Document Generation  
- Stage 5: Signature Capture  
- Stage 6: Final Documents & Analyst Handoff  

Each case should have a unique **ID** (e.g. application number, customer name + timestamp).

---

### 2. Central Data Backbone
Use a single source of truth to log stage transitions:
- **Excel Online (OneDrive/SharePoint)** → simple, best for <5k rows.  
- **SharePoint List** → more robust, supports views and permissions.  
- **Dataverse (Power Platform)** → best for scale, requires licensing.  

👉 Recommendation: **SharePoint List** or **Excel Online**.

---

### 3. Capturing Events (Power Automate Triggers)
Automate logging when a case moves stages:
- **Email-based**:  
  - Flow triggers on incoming email to the Distribution List.  
  - Extract case ID from subject/body.  
  - Log “Case X moved to Stage Y” in the tracking table.  

- **Teams-based**:  
  - Flow triggers on new message in a Teams channel.  

- **Manual Update**:  
  - Provide a simple Power App or Adaptive Card in Teams → users log stage completion with one click.

---

### 4. Writing to the Tracking Table
- Columns: Case ID | Customer Name | Current Stage | Timestamp | Owner  
- Approaches:  
  - **Append a new row** (keeps history + timeline of events).  
  - **Update a row** (shows current status only).  

👉 Recommendation: **Append rows** for audit and bottleneck analysis.

---

### 5. Reporting / Dashboard
Options:
- **Excel Pivot Charts** (if using Excel Online).  
- **Power BI Dashboard** (recommended):  
  - Connects directly to SharePoint/Excel/Dataverse.  
  - Funnel view: count of cases in each stage.  
  - Drilldowns: average duration per stage, bottleneck detection.

---

### 6. Notifications / Visibility
Enhance visibility during the process:
- Post updates in Teams when cases progress.  
- Send reminders/escalations if a case is stuck > X days.  
- CEO can get a daily Teams/Email digest with counts per stage.

---

## 📘 Example Flow (Email-triggered, Excel backend)
1. Trigger: Email arrives in "Quote Preparation DL".  
2. Extract case number from subject/body.  
3. Add row to Excel/SharePoint table:  
   - CaseID = extracted  
   - Stage = "Quote Preparation"  
   - Timestamp = now()  
   - Owner = email sender  
4. Power BI refreshes daily → CEO sees live funnel chart.

---

## 🧩 Alternatives
- **Power Automate Approvals** → built-in status tracking.  
- **Model-Driven Power App** → structured case management.  
- **Lightweight approach** → DLs/Teams + Excel/SharePoint List + Flows.

---

## ✅ Recommendation
- Use **SharePoint List** as the central tracker.  
- Automate updates with **Power Automate flows** (email or Teams triggers).  
- Build a **Power BI dashboard** for reporting.  
- Add reminders/escalations once baseline visibility is in place.


```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

title Customer Onboarding Tracking - High-Level Architecture

Person(user, "Process Participants", "Sales, Analysts, Operations staff")
Person(ceo, "CEO", "Wants visibility of pipeline status")

System(email, "Email / Distribution Lists", "Work items arrive by email")
System(teams, "Microsoft Teams", "Channels for each role, status notifications")
System_Boundary(pa, "Power Automate Flows") {
    System(powerautomate, "Power Automate", "Flows triggered by Email or Teams events. Logs case status.")
}

SystemDb(sp, "SharePoint List (or Excel Online)", "Central tracker of case stages")
System(bi, "Power BI Dashboard", "Visualizes onboarding pipeline and bottlenecks")

Rel(user, email, "Sends/receives work items")
Rel(user, teams, "Collaborates, receives task notifications")
Rel(email, powerautomate, "Triggers flow when DL receives mail")
Rel(teams, powerautomate, "Triggers flow when new Teams message posted")
Rel(powerautomate, sp, "Writes stage updates (Case ID, Stage, Timestamp)")
Rel(sp, bi, "Data source connection")
Rel(ceo, bi, "Views dashboard for live funnel & bottlenecks")
Rel(powerautomate, teams, "Sends process update notifications")

@enduml
```