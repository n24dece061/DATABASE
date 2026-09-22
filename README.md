```mermaid erDiagram PERSON ||--o| OWNER : "overlapping ISA" PERSON ||--o| AGENT : "overlapping ISA" PERSON ||--o| TENANT : "overlapping ISA"

PERSON {
    int PersonID PK
    varchar FullName
    varchar Phone
    varchar Email
}
OWNER {
    int PersonID PK_FK
    varchar Address
}
AGENT {
    int PersonID PK_FK
    decimal CommissionRate
}
TENANT {
    int PersonID PK_FK
    varchar IDCardNumber
}

OWNER ||--o{ PROPERTY : owns
AGENT |o--o{ PROPERTY : manages
PROPERTY ||--|| RESIDENTIAL_PROPERTY : "total disjoint ISA"
PROPERTY ||--|| COMMERCIAL_PROPERTY : "total disjoint ISA"

PROPERTY {
    int PropertyID PK
    varchar Title
    varchar Address
    decimal Price "giá thuê niêm yết"
    varchar Status
    int OwnerID FK
    int AgentID FK "optional"
}
RESIDENTIAL_PROPERTY {
    int PropertyID PK_FK
    int Bedrooms
    int Bathrooms
    varchar FurnishedStatus
}
COMMERCIAL_PROPERTY {
    int PropertyID PK_FK
    varchar BusinessType
    decimal FloorArea
    int ParkingSpaces
}

PROPERTY ||--o{ PROPERTY_IMAGE : has
PROPERTY_IMAGE {
    int ImageID PK
    varchar ImageURL
    varchar Caption
    boolean IsPrimary "max 1 per property"
    date UploadedDate
    int PropertyID FK
}

PROPERTY ||--o{ VIEWING : "viewed in"
PERSON ||--o{ VIEWING : books
AGENT ||--o{ VIEWING : handles
VIEWING {
    int ViewingID PK
    date ViewingDate
    time ViewingTime
    varchar Status
    text Notes
    int PropertyID FK
    int ViewerID FK "-> PERSON, chưa chắc là Tenant"
    int AgentID FK
}

PROPERTY ||--o{ LEASE : "leased via"
TENANT ||--o{ LEASE : signs
AGENT |o--o{ LEASE : "closed by (optional)"
LEASE {
    int LeaseID PK
    date StartDate
    date EndDate
    decimal MonthlyRent
    decimal DepositAmount
    varchar Status
    int PropertyID FK
    int TenantID FK
    int AgentID FK "optional"
}

LEASE ||--o{ PAYMENT : incurs
PAYMENT {
    int PaymentID PK
    date PaymentDate
    decimal Amount
    varchar PaymentType "Rent, Deposit, Fine"
    varchar PaymentMethod
    int LeaseID FK
}

PROPERTY ||--o{ MAINTENANCE_REQUEST : "reported on"
LEASE |o--o{ MAINTENANCE_REQUEST : "optionally tied to"
MAINTENANCE_REQUEST {
    int RequestID PK
    text Description
    date ReportDate
    decimal EstimatedCost
    decimal ActualCost
    varchar Status
    int PropertyID FK
    int LeaseID FK "optional"
}
```
