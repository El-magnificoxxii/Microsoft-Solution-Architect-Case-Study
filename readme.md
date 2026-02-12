# Tailwind Traders – Azure Storage & Web Architecture Case Study

## Case Study Question

Tailwind Traders has the following types of data:

- Media files: Product photos (JPEG) and feature videos (MP4)
- Marketing literature: Sales flyers, customer stories, sizing charts, manufacturing information (PDF)
- Internal corporate documents: Sensitive and non-sensitive Word and Excel files

Currently, users access this content over the internet through a web application hosted on a website.

### Design a storage and access solution in Azure that addresses:

- The types of data represented
- Performance, scalability, and availability
- Security and identity-based access
- Cost optimization
- Support for both external and internal access patterns
- Web application integration

---

## Data Classification

| Data Type | Examples | Classification |
|-------------|----------|----------------|
| Media Files | JPEG, MP4 | Unstructured binary object data |
| Marketing Literature | PDFs | Unstructured document data |
| Internal Documents | Word, Excel | Semi-structured business documents (sensitive) |

---

## Option A — Cloud-Native & Collaboration-Optimized Architecture

### Architecture Overview

- Azure Front Door / Azure CDN
- Azure App Service (Web Application)
- Azure Blob Storage (Media + Marketing content)
- SharePoint Online / OneDrive (Internal Word & Excel documents)
- Microsoft Entra ID for authentication and authorization
- Optional Immutable Blob Storage for compliance workloads

### Design Rationale

#### Media & Marketing Content
- Stored in Azure Blob Storage
- Delivered via Azure Front Door / CDN for global performance
- Blob access tiers (Hot, Cool, Archive) for cost optimization
- Lifecycle management policies for automated tier transitions

#### Internal Corporate Documents
- Stored in SharePoint Online / OneDrive
- Native integration with Microsoft Office
- Supports co-authoring, versioning, and collaboration
- Built-in compliance features (DLP, retention, eDiscovery)

#### Security & Identity
- Microsoft Entra ID for all user authentication
- Managed Identity for App Service to access Blob Storage
- Role-Based Access Control (RBAC)
- HTTPS-only access

### Strengths

- Best-in-class Office collaboration experience
- Strong compliance and governance capabilities
- Lower operational overhead
- More cloud-native and future-proof
- Lower long-term total cost of ownership (TCO)

### Limitations

- Not suitable for legacy applications requiring SMB access
- Less friendly for lift-and-shift file server migrations
- Requires organizational readiness for SaaS collaboration tools

---

## Option B — Enterprise Web + Lift-and-Shift Friendly Architecture (Author’s Approach)

### Architecture Overview

- Azure Application Gateway (with WAF)
- Azure App Service (Web Application)
- Azure Blob Storage (Media + Marketing content)
- Azure Files (Internal Word & Excel documents)
- Microsoft Entra ID for authentication and authorization
- Blob access tiers and lifecycle management
- Private Endpoints for storage access

### Design Rationale

#### Web Security
- Azure Application Gateway provides:
  - Layer 7 load balancing
  - Web Application Firewall (WAF)
  - SSL termination
  - OWASP Top 10 protection

#### Media & Marketing Content
- Stored in Azure Blob Storage
- Access tiers (Hot, Cool, Archive) for cost optimization
- App Service acts as a gatekeeper for storage access

#### Internal Corporate Documents
- Stored in Azure Files
- SMB-based access model
- Supports lift-and-shift from on-prem file servers
- NTFS-style permissions and file share semantics

#### Security & Identity
- Microsoft Entra ID authentication
- Managed Identity for App Service
- Azure Files + Entra ID + NTFS ACLs
- Private Endpoints for secure, private access

### Strengths

- Strong perimeter security using WAF
- Easy migration from on-prem file servers
- Supports legacy and SMB-based applications
- Centralized Azure infrastructure ownership
- Strong zero-trust networking posture

### Limitations

- Poor collaboration experience for Office documents
- No native co-authoring
- Increased operational complexity (SMB, NTFS, networking)
- Higher infrastructure and management overhead
- Less cloud-native, higher long-term technical debt

---

## Service-by-Service Tradeoffs

### Edge & Web Security

| Service | Option A | Option B |
|----------|----------|----------|
| Edge Security | Azure Front Door / CDN | Azure Application Gateway (WAF) |
| Focus | Global performance | Enterprise perimeter security |
| Global Acceleration | Yes | No |
| WAF | Optional (Premium) | Built-in |

---

### Media & Marketing Storage

| Area | Option A | Option B |
|------|----------|----------|
| Service | Azure Blob Storage | Azure Blob Storage |
| Delivery | CDN-optimized | App-controlled |
| Access Tiers | Hot/Cool/Archive | Hot/Cool/Archive |
| Lifecycle Mgmt | Yes | Yes |

---

### Internal Corporate Documents

| Area | Option A | Option B |
|------|----------|----------|
| Service | SharePoint Online | Azure Files |
| Access Model | Web + Office apps | SMB file shares |
| Co-authoring | Yes | No |
| Versioning | Rich | Basic |
| Legacy App Support | No | Yes |
| Lift-and-Shift | Poor | Excellent |
| Cloud-Native | High | Low |

---

### Identity & Access

| Area | Option A | Option B |
|------|----------|----------|
| Identity Provider | Microsoft Entra ID | Microsoft Entra ID |
| App Auth | Managed Identity | Managed Identity |
| Storage Auth | RBAC | RBAC + NTFS ACLs |
| Complexity | Lower | Higher |

---

### Cost & Operations

| Area | Option A | Option B |
|------|----------|----------|
| Ops Overhead | Lower | Higher |
| Network Complexity | Lower | Higher |
| Long-term TCO | Lower | Higher |
| Legacy Support | Low | High |

---

## Executive Summary

Option A is a cloud-native, collaboration-first architecture optimized for modern Microsoft 365 workloads. It provides superior user experience, governance, and lower operational overhead, making it better for long-term cloud maturity.

Option B is an enterprise, lift-and-shift-friendly architecture optimized for organizations migrating from on-premises environments. It offers stronger perimeter security and legacy compatibility but introduces higher operational complexity and reduced collaboration capabilities.

The appropriate option depends on Tailwind Traders’ cloud maturity, legacy dependencies, and long-term modernization strategy.

---

## Key Architectural Insight

There is no single “best” architecture. Mature solution design involves evaluating tradeoffs across security, collaboration, cost, operations, and modernization readiness.

Architect-level decisions are based on context, not just best practices.
