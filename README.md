```mermaid
erDiagram
    %% Tầng Supertype (PERSON)
    PERSON {
        INT PersonID PK
        VARCHAR(100) FullName "NOT NULL"
        VARCHAR(20) Phone "NULL"
        VARCHAR(100) Email "NULL"
    }
    
    OWNER {
        INT PersonID PK "FK -> PERSON.PersonID"
        VARCHAR(255) Address "NOT NULL"
    }
    
    TENANT {
        INT PersonID PK "FK -> PERSON.PersonID"
        VARCHAR(20) IDCardNumber "UNIQUE NOT NULL"
    }
    
    AGENT {
        INT PersonID PK "FK -> PERSON.PersonID"
        DECIMAL(5_2) CommissionRate "NOT NULL"
    }

    PERSON ||--o| OWNER : "ISA (disjoint, total)"
    PERSON ||--o| TENANT : "ISA (disjoint, total)"
    PERSON ||--o| AGENT : "ISA (disjoint, total)"

    %% Tầng Bất động sản (PROPERTY)
    PROPERTY {
        INT PropertyID PK
        VARCHAR(100) Title "NOT NULL"
        VARCHAR(255) Address "NOT NULL"
        VARCHAR(20) PropertyType "CHECK(Residential, Commercial)"
        VARCHAR(50) ListingType
        DECIMAL(15_2) Price "NOT NULL"
        VARCHAR(20) Status "CHECK(Available, Leased, Maintenance)"
        INT OwnerID FK "-> OWNER.PersonID"
        INT AgentID FK "-> AGENT.PersonID"
    }
    
    RESIDENTIAL_PROPERTY {
        INT PropertyID PK "FK -> PROPERTY.PropertyID"
        INT Bedrooms 
        INT Bathrooms 
        DECIMAL(10_2) Area 
        VARCHAR(50) FurnishedStatus 
    }
    
    COMMERCIAL_PROPERTY {
        INT PropertyID PK "FK -> PROPERTY.PropertyID"
        VARCHAR(50) BusinessType 
        DECIMAL(10_2) FloorArea 
        INT ParkingSpaces 
    }

    PROPERTY ||--o| RESIDENTIAL_PROPERTY : "ISA (disjoint, partial)"
    PROPERTY ||--o| COMMERCIAL_PROPERTY : "ISA (disjoint, partial)"

    %% Các quan hệ cốt lõi
    OWNER ||--o{ PROPERTY : "1:N mandatory (OWNS)"
    AGENT ||--o{ PROPERTY : "1:N mandatory (MANAGES)"

    PROPERTY_IMAGE {
        INT ImageID PK
        VARCHAR(255) ImageURL "NOT NULL"
        VARCHAR(100) Caption 
        BOOLEAN IsPrimary "DEFAULT FALSE"
        DATE UploadedDate "DEFAULT CURRENT_DATE"
        INT PropertyID FK "-> PROPERTY.PropertyID"
    }
    PROPERTY ||--o{ PROPERTY_IMAGE : "1:N mandatory (HAS)"

    VIEWING {
        INT ViewingID PK
        DATE ViewingDate "NOT NULL"
        TIME ViewingTime "NOT NULL"
        VARCHAR(20) Status "CHECK(Pending, Completed, Cancelled)"
        TEXT Notes 
        INT PropertyID FK "-> PROPERTY.PropertyID"
        INT TenantID FK "-> TENANT.PersonID"
        INT AgentID FK "-> AGENT.PersonID"
    }
    PROPERTY ||--o{ VIEWING : "1:N optional (VIEWED_IN)"
    TENANT ||--o{ VIEWING : "1:N mandatory (BOOKS)"
    AGENT ||--o{ VIEWING : "1:N mandatory (HANDLES)"

    LEASE {
        INT LeaseID PK
        DATE StartDate "NOT NULL"
        DATE EndDate "NOT NULL"
        DECIMAL(15_2) MonthlyRent "NOT NULL"
        DECIMAL(15_2) DepositAmount "NOT NULL"
        VARCHAR(20) Status "CHECK(Active, Terminated)"
        INT PropertyID FK "-> PROPERTY.PropertyID"
        INT TenantID FK "-> TENANT.PersonID"
    }
    PROPERTY ||--o{ LEASE : "1:N optional (LEASED_VIA)"
    TENANT ||--o{ LEASE : "1:N mandatory (SIGNS)"

    PAYMENT {
        INT PaymentID PK
        DATE PaymentDate "NOT NULL"
        DECIMAL(15_2) Amount "NOT NULL"
        VARCHAR(50) PaymentType "CHECK(Rent, Deposit, Fine)"
        VARCHAR(50) PaymentMethod 
        INT LeaseID FK "-> LEASE.LeaseID"
    }
    LEASE ||--o{ PAYMENT : "1:N optional (INCURS)"

    MAINTENANCE_REQUEST {
        INT RequestID PK
        TEXT Description "NOT NULL"
        DATE ReportDate "NOT NULL"
        DECIMAL(15_2) EstimatedCost 
        DECIMAL(15_2) ActualCost 
        VARCHAR(20) Status "CHECK(Pending, In_Progress, Resolved)"
        INT LeaseID FK "-> LEASE.LeaseID"
    }
    LEASE ||--o{ MAINTENANCE_REQUEST : "1:N optional (SUBMITS)"
