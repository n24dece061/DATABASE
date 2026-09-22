```mermaid
erDiagram
    PERSON ||--o| OWNER : "overlapping ISA"
    PERSON ||--o| AGENT : "overlapping ISA"
    PERSON ||--o| TENANT : "overlapping ISA"

    PERSON {
        INT PersonID PK
        VARCHAR FullName
        VARCHAR Phone
        VARCHAR Email
    }
    OWNER {
        INT PersonID PK, FK
        VARCHAR Address
    }
    AGENT {
        INT PersonID PK, FK
        DECIMAL CommissionRate
    }
    TENANT {
        INT PersonID PK, FK
        VARCHAR IDCardNumber
    }

    OWNER ||--o{ PROPERTY : "owns"
    AGENT |o--o{ PROPERTY : "manages"
    PROPERTY ||--|| RESIDENTIAL_PROPERTY : "total disjoint ISA"
    PROPERTY ||--|| COMMERCIAL_PROPERTY : "total disjoint ISA"

    PROPERTY {
        INT PropertyID PK
        VARCHAR Title
        VARCHAR Address
        VARCHAR ListingType "Rent or Sale"
        DECIMAL Price
        VARCHAR Status
        INT OwnerID FK
        INT AgentID FK "optional"
    }
    RESIDENTIAL_PROPERTY {
        INT PropertyID PK, FK
        INT Bedrooms
        INT Bathrooms
        VARCHAR FurnishedStatus
    }
    COMMERCIAL_PROPERTY {
        INT PropertyID PK, FK
        VARCHAR BusinessType
        DECIMAL FloorArea
        INT ParkingSpaces
    }

    PROPERTY ||--o{ PROPERTY_IMAGE : "has"
    PROPERTY_IMAGE {
        INT ImageID PK
        VARCHAR ImageURL
        VARCHAR Caption
        BOOLEAN IsPrimary "max 1 per property"
        DATE UploadedDate
        INT PropertyID FK
    }

    PROPERTY ||--o{ VIEWING : "viewed in"
    PERSON ||--o{ VIEWING : "books"
    AGENT ||--o{ VIEWING : "handles"
    VIEWING {
        INT ViewingID PK
        DATE ViewingDate
        TIME ViewingTime
        VARCHAR Status
        TEXT Notes
        INT PropertyID FK
        INT ViewerID FK "-> PERSON"
        INT AgentID FK
    }

    PROPERTY ||--o{ LEASE : "leased via"
    TENANT ||--o{ LEASE : "signs"
    AGENT |o--o{ LEASE : "closed by (optional)"
    LEASE {
        INT LeaseID PK
        DATE StartDate
        DATE EndDate
        DECIMAL MonthlyRent
        DECIMAL DepositAmount
        VARCHAR Status
        INT PropertyID FK
        INT TenantID FK
        INT AgentID FK "optional"
    }

    PROPERTY ||--o{ SALE : "sold via"
    PERSON ||--o{ SALE : "buys"
    AGENT |o--o{ SALE : "closed by (optional)"
    SALE {
        INT SaleID PK
        DATE SaleDate
        DECIMAL SalePrice
        VARCHAR Status
        INT PropertyID FK
        INT BuyerID FK "-> PERSON"
        INT AgentID FK "optional"
    }

    LEASE |o--o{ PAYMENT : "incurs"
    SALE |o--o{ PAYMENT : "incurs"
    PAYMENT {
        INT PaymentID PK
        DATE PaymentDate
        DECIMAL Amount
        VARCHAR PaymentType
        VARCHAR PaymentMethod
        INT LeaseID FK "nullable, XOR with SaleID"
        INT SaleID FK "nullable, XOR with LeaseID"
    }

    PROPERTY ||--o{ MAINTENANCE_REQUEST : "reported on"
    LEASE |o--o{ MAINTENANCE_REQUEST : "optionally tied to"
    MAINTENANCE_REQUEST {
        INT RequestID PK
        TEXT Description
        DATE ReportDate
        DECIMAL EstimatedCost
        DECIMAL ActualCost
        VARCHAR Status
        INT PropertyID FK
        INT LeaseID FK "optional"
    }
