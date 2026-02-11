# UI Policy and Business Rule Configuration for Escalations

## Overview
This repository demonstrates how a UI Policy and two Business Rules were configured to control escalation behavior for Infinity (HHD) incidents in ServiceNow. The focus is on conditional field visibility, automated reminder messaging, and dynamic population of related data using dot-walking.

This project simulates how a ServiceNow administrator enforces escalation standards for critical Infinity (HHD) incidents while maintaining structured, rule-driven behavior within the Incident table.

## Use Case
Cloud Dimensions requires that Priority 1 – Critical incidents for the Infinity (HHD) service offering support escalation to the manufacturer when necessary.

When an incident meets defined conditions, escalation-related fields must become visible. Additionally, selecting escalation should trigger a reminder message, and the Manufacturer contact should automatically populate based on the related Service Offering record.

This project reflects a real-world configuration scenario where UI Policies and Business Rules are used together to enforce process compliance and reduce manual effort.

## Features
- Incident form field creation and configuration
- Service Offering form enhancement
- UI Policy condition and UI Policy Actions
- Conditional field visibility logic
- Business Rule reminder message display
- Business Rule field value population using dot-walking
- End-to-end verification of UI and server-side logic

## Technologies Used
- ServiceNow Platform
- Incident Management
- Service Offering Configuration
- UI Policies
- UI Policy Actions
- Business Rules
- Dot-walking
- Form Configuration

## Implementation Walkthrough

### Objective
Configure escalation-related fields, enforce conditional visibility using a UI Policy, create Business Rules to display a reminder message and populate Manufacturer contact automatically, and validate the behavior using Infinity (HHD) incidents.

### Step 1: Prepare Forms and Fields
The Manufacturers form was updated to include the **Contact** field using Form Builder.

<img width="1901" height="504" alt="Screen Shot 2026-02-11 at 12 26 07 PM" src="https://github.com/user-attachments/assets/ce42f493-3ad3-488c-8fe3-38c951c6d3a4" />

The Service Offering form was updated to include the **Manufacturer** field under the Vendor section.

<img width="1901" height="504" alt="Screen Shot 2026-02-11 at 12 30 24 PM" src="https://github.com/user-attachments/assets/bf367b57-3928-4bea-9263-56d042a04749" />

The Incident form was modified to include two new fields:
- **Escalate to manufacturer**  
  - Type: True / False
  
<img width="1901" height="611" alt="Screen Shot 2026-02-11 at 12 18 45 PM" src="https://github.com/user-attachments/assets/84633055-2e76-4748-920b-f9141f664188" />

- **Manufacturer contact**  
  - Type: Reference  
  - Table: User [sys_user]  

<img width="1901" height="504" alt="Screen Shot 2026-02-11 at 12 23 07 PM" src="https://github.com/user-attachments/assets/94fdb800-83db-42ce-9c33-b7a961c680f1" />

The Escalate to manufacturer field was positioned under the Assigned to field, and the Manufacturer contact field was positioned under Escalate to manufacturer.

<img width="1901" height="504" alt="Screen Shot 2026-02-11 at 12 24 08 PM" src="https://github.com/user-attachments/assets/5c84b82a-75a8-4ead-8aa9-78573161c612" />

### Step 2: Prepare Supporting Data
A new user record was created:
- User ID: Test.Contact  
- First Name: Test  
- Last Name: Contact  
- Email: test.contact@test.com

<img width="1901" height="504" alt="Screen Shot 2026-02-11 at 1 06 13 PM" src="https://github.com/user-attachments/assets/dc73e99b-2aa7-4ce0-8b94-159d0ea91db2" />

A Manufacturer record was created for **Infinity Products, Inc.**, with:
- Contact: Test Contact  
- Manufacturer checkbox selected
  
<img width="1901" height="347" alt="Screen Shot 2026-02-11 at 1 09 18 PM" src="https://github.com/user-attachments/assets/e05bdcaf-fbe0-40ec-9ca0-034bb31a03d0" />

The Infinity (HHD) Service Offering record was updated to reference **Infinity Products, Inc.** as the Manufacturer.

<img width="1901" height="347" alt="Screen Shot 2026-02-11 at 1 15 17 PM" src="https://github.com/user-attachments/assets/95602b21-63e1-4a94-a18e-3dd9f0433167" />

### Step 3: Create UI Policy
A new UI Policy was created on the Incident table with the following configuration:

- Short description: Display Escalation for Infinity (HHD) service offerings when Priority = 1 - Critical  

On the **When to Apply** tab:
- Priority | is | 1 - Critical  
- Service offering | is | Infinity (HHD)  
- On load: Selected  
- Reverse if false: Selected  

The record was saved.

<img width="1901" height="518" alt="Screen Shot 2026-02-11 at 6 33 04 PM" src="https://github.com/user-attachments/assets/899a2e05-a3b0-45e0-93a5-4fcd9527a5db" />

### Step 4: Create UI Policy Actions
Two UI Policy Actions were created for the UI Policy.

First UI Policy Action:
- Field name: Escalate to manufacturer  
- Visible: True

Second UI Policy Action:
- Field name: Manufacturer contact  
- Visible: True  

Both actions were submitted.

This configuration ensured both escalation fields display only when Priority = 1 - Critical and Service Offering = Infinity (HHD).

<img width="1901" height="283" alt="Screen Shot 2026-02-11 at 6 17 14 PM" src="https://github.com/user-attachments/assets/a8dee1c0-ac1e-40c5-852a-941ab9db7c35" />

<img width="1901" height="283" alt="Screen Shot 2026-02-11 at 6 19 57 PM" src="https://github.com/user-attachments/assets/e17894e6-9b5c-49b1-a33e-960797a9ae68" />

### Step 5: Verify UI Policy Behavior
A new Incident was created with:
- Service offering: Infinity (HHD)  
- Short description: Test UI Policy and Actions  
- Impact: High  
- Urgency: High  

The Escalate to manufacturer and Manufacturer contact fields were confirmed visible when Priority auto-calculated to 1 - Critical.

Impact was then changed to Medium, and it was verified that both escalation-related fields no longer displayed.

<img width="1901" height="426" alt="Screen Shot 2026-02-11 at 6 23 10 PM" src="https://github.com/user-attachments/assets/2d6e4101-e0a6-4496-ada2-788f99bff9b1" />

<img width="1901" height="394" alt="Screen Shot 2026-02-11 at 6 25 08 PM" src="https://github.com/user-attachments/assets/888c90ae-3802-4550-bf69-74b6170ce512" />

### Step 6: Create Business Rule – Escalation Reminder
A new Business Rule was created on the Incident table:

- Name: Escalate to manufacturer reminder  

On the **When to run** tab:
- Service offering | is | Infinity (HHD)  
- Escalate to manufacturer | is | True  
- Insert: Selected  
- Update: Selected  

On the **Actions** tab:
- Add message: Selected  
- Message body:  
  Reminder: To escalate to manufacturer, send mail to Manufacturer contact.

The Business Rule was submitted.

<img width="1901" height="324" alt="Screen Shot 2026-02-11 at 6 30 06 PM" src="https://github.com/user-attachments/assets/a396a73d-0f35-454f-811b-043d9d6aec1b" />

<img width="1901" height="496" alt="Screen Shot 2026-02-11 at 6 31 11 PM" src="https://github.com/user-attachments/assets/b7bdcb1d-b4dc-4f63-b133-cf2db009745c" />

### Step 7: Create Business Rule – Populate Manufacturer Contact
A second Business Rule was created:

- Name: Populate Manufacturer Contact Name  
- Table: Incident [incident]  

On the **When to run** tab:
- Service offering | is | Infinity (HHD)  
- Priority | is | 1 - Critical  
- Insert: Selected  
- Update: Selected  

On the **Actions** tab under Set field values:
- Manufacturer contact | Same as | Service offering.Manufacturer.Contact  

Dot-walking was used to reference:
Service offering → Manufacturer → Contact  

The Business Rule was submitted.

<img width="1901" height="554" alt="Screen Shot 2026-02-11 at 6 38 14 PM" src="https://github.com/user-attachments/assets/4ca611d6-22fa-48c9-888d-023e38ffbf3e" />

<img width="1901" height="465" alt="Screen Shot 2026-02-11 at 6 41 04 PM" src="https://github.com/user-attachments/assets/d498090e-3096-4a6e-8043-d7c525740152" />

### Step 8: Verify Business Rule Behavior
A new Incident was created with:
- Service offering: Infinity (HHD)  
- Short Description: Test Business Rules  
- Impact: High  
- Urgency: High  

The Escalate to manufacturer and Manufacturer contact fields displayed.

The Manufacturer contact field was confirmed populated with **Test Contact**.

The Escalate to manufacturer checkbox was selected and the record was saved.

The reminder message was confirmed displayed at the top of the Incident form:
Reminder: To escalate to manufacturer, send mail to Manufacturer contact.

<img width="1901" height="422" alt="Screen Shot 2026-02-11 at 6 44 27 PM" src="https://github.com/user-attachments/assets/9c88f5c9-48e0-46a7-8173-cf286c9d56a7" />

<img width="1901" height="462" alt="Screen Shot 2026-02-11 at 6 46 48 PM" src="https://github.com/user-attachments/assets/313127e6-7eec-41f2-a51d-270e97df45dc" />


## Lessons Learned
- UI Policies control client-side field visibility based on defined conditions  
- Reverse if false enforces clean field behavior when conditions are no longer met  
- UI Policy Actions determine how individual fields respond to policy conditions  
- Business Rules execute server-side logic for insert and update events  
- Dot-walking enables dynamic population of related data across tables  
- Combining UI Policies and Business Rules enforces structured escalation workflows  
