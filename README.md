# Implement Client Script & UI Policy (Incident)

Dynamic form behavior to improve user experience and data quality.

**Platform:** ServiceNow  
**Table:** Incident `[incident]`

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Objectives](#objectives)
3. [Scope and Prerequisites](#scope-and-prerequisites)
4. [UI Policy vs Client Script](#ui-policy-vs-client-script)
5. [UI Policy Implementation](#ui-policy-implementation)
6. [Client Script Implementation](#client-script-implementation)
7. [Testing and Validation](#testing-and-validation)
8. [Best Practices](#best-practices)
9. [Expected Benefits](#expected-benefits)
10. [Conclusion](#conclusion)

---

## 1. Overview

This project implements **Client Scripts and UI Policies** on the ServiceNow Incident form to improve user experience, enforce consistent field behavior, and reduce incorrect data entry.

Form fields are controlled dynamically based on user actions and incident conditions, without requiring the form to be submitted.

A **UI Policy** is configured on the Incident table to make selected fields mandatory, read-only, or visible when a defined condition is met.

**Client Scripts** complement UI Policies by handling logic that needs scripting, such as reacting to field changes, validating data before saving, and displaying helpful messages.

---

## 2. Objectives

The main objectives of this project are:

- Ensure users provide the information required for a given incident state.
- Prevent edits to key fields once an incident reaches a final state.
- Show only fields that are relevant to the current context.
- Validate data on the client side before the record is submitted.
- Promote consistent data-entry practices across agents and requesters.

---

## 3. Scope and Prerequisites

### Scope

The implementation applies to the **Incident table** and the standard Incident form view.

It covers:

- 3 UI Policies
- 3 Client Scripts
- onLoad Client Script
- onChange Client Script
- onSubmit Client Script

### Prerequisites

- Access to a ServiceNow instance.
- A user with the admin role or appropriate UI Policy and Client Script permissions.
- Basic knowledge of the Incident form.
- Basic knowledge of field names and JavaScript.

---

## 4. UI Policy vs Client Script

| Aspect | UI Policy | Client Script |
|---|---|---|
| Purpose | Controls field behavior declaratively | Runs custom JavaScript logic |
| Setup | Low; no coding required | Requires writing and testing scripts |
| Triggers | Form load and field changes based on conditions | onLoad, onChange, onSubmit, onCellEdit |
| Best Used For | Mandatory, read-only and visible rules | Validation, messages, lookups and setting/clearing values |

### Recommendation

Use **UI Policies first** whenever they can satisfy the requirement.

Use **Client Scripts** when scripting is genuinely needed.

---

# 5. UI Policy Implementation

The project contains three UI Policies on the Incident table.

---

## 5.1 UI Policy 1 – Resolution Details Required When Resolved

**Short Description:**  
`Incident - Require resolution details when Resolved`

**Table:**  
`Incident [incident]`

**Active:**  
`True`

**Condition:**  
`State is Resolved`

### UI Policy Actions

| Field | Mandatory | Visible | Read-only |
|---|---|---|---|
| Resolution code (`close_code`) | True | True | Leave alone |
| Resolution notes (`close_notes`) | True | True | Leave alone |

When the Incident state becomes **Resolved**, the Resolution Code and Resolution Notes fields become mandatory and visible.

---

## 5.2 UI Policy 2 – Hold Reason When On Hold

**Short Description:**  
`Incident - Show hold reason when On Hold`

**Table:**  
`Incident [incident]`

**Condition:**  
`State is On Hold`

### UI Policy Action

| Field | Mandatory | Visible | Read-only |
|---|---|---|---|
| Hold reason (`hold_reason`) | True | True | Leave alone |

When the Incident state becomes **On Hold**, the Hold Reason field becomes visible and mandatory.

**Reverse if false** is enabled, so the field returns to its normal behavior when the state changes away from On Hold.

---

## 5.3 UI Policy 3 – Lock Key Fields When Closed

**Short Description:**  
`Incident - Make key fields read-only when Closed`

**Table:**  
`Incident [incident]`

**Condition:**  
`State is Closed`

### UI Policy Actions

| Field | Mandatory | Visible | Read-only |
|---|---|---|---|
| Short description | Leave alone | Leave alone | True |
| Category | Leave alone | Leave alone | True |
| Impact | Leave alone | Leave alone | True |
| Urgency | Leave alone | Leave alone | True |
| Assignment group | Leave alone | Leave alone | True |

When an Incident is **Closed**, important fields become read-only to prevent further modification.

---

# 6. Client Script Implementation

The project contains three Client Scripts:

1. **onLoad**
2. **onChange**
3. **onSubmit**

---

## 6.1 onLoad – Welcome Message for Critical Incidents

**Type:** `onLoad`

This Client Script displays an information message when an existing Incident has **Priority 1**.

### Purpose

It reminds the agent to follow the major incident process.

### Script

```javascript
function onLoad() {

    // Show a banner for Priority 1 incidents on existing records
    if (!g_form.isNewRecord() && g_form.getValue('priority') == '1') {

        g_form.addInfoMessage(
            'This is a Priority 1 (Critical) incident. ' +
            'Please follow the major incident process.'
        );
    }
}# Implement-Client-Script-UI-Policy-Incident
