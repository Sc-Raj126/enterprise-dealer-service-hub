# Enterprise Dealer Service Hub — Data Model

## 1. Purpose

This document defines the initial Salesforce data model for the Enterprise Dealer Service Hub.

The application manages dealer information, order lifecycles, order items, pricing approvals, and customer support cases.

The model will be implemented incrementally in a Salesforce Developer org.

## 2. Design Principles

- Reuse standard Salesforce objects when they fit the business requirement.
- Use custom objects for business entities that require a dedicated data model.
- Define relationships deliberately to maintain data integrity.
- Enforce appropriate object permissions, field-level security, and record access.
- Keep business logic maintainable and testable.
- Avoid introducing unnecessary objects or integrations during the initial build.

## 3. Objects

### A. Account — Dealer

**Purpose:** Represents a dealer business.

We will reuse the standard Account object instead of creating a duplicate custom Dealer object.

Planned configuration:

- Account Type: Dealer
- Dealer Code: Unique dealer identifier
- Dealer Status: Active, Inactive, Pending
- Dealer Tier: Standard, Silver, Gold, Platinum
- Standard business name and address fields

Contacts related to the Account will represent people working for the dealer.

### B. Dealer_Order__c — Order Header

**Purpose:** Stores the main details and lifecycle status of a dealer order.

Planned fields:

- Order Number: Auto Number
- Dealer: Lookup to Account
- Order Status: Draft, Submitted, Pending Approval, Approved, Rejected, Processing, Shipped, Delivered, Cancelled
- Order Date: Date
- Requested Delivery Date: Date
- Discount Percentage: Percent
- Subtotal: Currency
- Final Amount: Currency

The order will later support pricing validation and approval automation.

### C. Dealer_Order_Item__c — Order Line Item

**Purpose:** Represents an individual product or service line within a dealer order.

Planned fields:

- Dealer Order: Master-Detail relationship to Dealer_Order__c
- Product: Lookup to Product2
- SKU: Text
- Quantity: Number
- Unit Price: Currency
- Line Total: Formula (Quantity multiplied by Unit Price)

An order can contain multiple order items. The parent order will control the ownership and sharing behaviour of these child records through the master-detail relationship.

### D. Case — Support Case

**Purpose:** Uses the standard Salesforce Case object to manage dealer support issues.

Planned configuration:

- Account: Standard relationship to the dealer Account
- Subject: Support issue summary
- Status: Standard Case status values
- Priority: Standard Case priority
- Origin: Web, Email, Phone, or other configured values
- Dealer Order: Optional custom Lookup to Dealer_Order__c

This allows support teams to associate a case with a dealer and, where relevant, the specific order involved.

## 4. Relationship Summary

| Parent | Child | Relationship |
|---|---|---|
| Account | Contact | Standard relationship |
| Account | Dealer_Order__c | Lookup |
| Dealer_Order__c | Dealer_Order_Item__c | Master-Detail |
| Product2 | Dealer_Order_Item__c | Lookup |
| Account | Case | Standard relationship |
| Dealer_Order__c | Case | Optional Lookup |

## 5. Business Rules

1. Every dealer order must reference an Account identified as a dealer.
2. An order cannot be submitted without at least one order item.
3. Order quantities must be greater than zero.
4. Unit prices cannot be negative.
5. Pricing and discount rules must be validated before approval.
6. Order status transitions must follow the agreed business process.
7. Only authorized users should be able to approve orders or perform restricted actions.
8. Support cases should reference an order only when the issue relates to that order.
9. Calculations and updates must remain correct when multiple records are processed in one transaction.

## 6. Security Considerations

The implementation will evaluate:

- Object-level permissions
- Field-Level Security (FLS)
- Record-level sharing
- Apex execution and access enforcement
- Authorized access to pricing and approval actions

The final security configuration will be documented and tested in the Salesforce org.

## 7. Implementation Sequence

1. Configure Account fields for dealer management.
2. Create Dealer_Order__c and its fields.
3. Create Dealer_Order_Item__c and its relationships.
4. Configure the Product2 lookup and sample product records.
5. Configure the Case-to-order lookup.
6. Create representative sample records.
7. Verify relationships and business validations.
8. Add automated tests as business logic is implemented.

## 8. Project Status

**Current status:** Initial design.

The objects, fields, and relationships described here are planned until they have been created and verified in the Salesforce Developer org.

The design may be refined as implementation requirements become clearer.
