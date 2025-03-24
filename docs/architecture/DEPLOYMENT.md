# Plazza Deployment Architecture

## Production Infrastructure

```mermaid
graph TB
    subgraph Client_Layer
        Web[Web App]
        Mobile[Mobile App]
        POS[POS System]
    end

    subgraph CDN_Layer
        CF[Cloudflare CDN]
    end

    subgraph Firebase_Platform
        subgraph Functions
            CS_F[Catalogue Service]
            PS_F[Payment Service]
            OS_F[Order Service]
            LS_F[Location Service]
            UE_F[User Events]
            DS_F[Discount Service]
            WS_F[WhatsApp Service]
            POD_F[POD Service]
        end

        subgraph Firebase_Services
            Auth[Firebase Auth]
            RTDB[Realtime Database]
            Storage[Cloud Storage]
        end
    end

    subgraph Database_Layer
        subgraph CockroachDB_Cluster
            CRDB1[(Primary)]
            CRDB2[(Secondary 1)]
            CRDB3[(Secondary 2)]
        end

        subgraph Elasticsearch_Cluster
            ES1[(ES Primary)]
            ES2[(ES Data 1)]
            ES3[(ES Data 2)]
        end

        PG[(PostgreSQL)]
        AT[(Airtable)]
    end

    subgraph External_Services
        RP[Razorpay]
        GM[Google Maps]
        WA[WhatsApp API]
        AWS[AWS Textract]
        Claude[Claude AI]
    end

    Web --> CF
    Mobile --> CF
    CF --> Functions
    POS --> Functions

    Functions --> Firebase_Services
    Functions --> Database_Layer
    Functions --> External_Services
```

## Environment Configuration

### Production Environment Variables
```env
# Firebase Configuration
FIREBASE_PROJECT_ID=plazza-prod
FIREBASE_REGION=asia-south1

# Database Connections
COCKROACH_DB_URL=postgresql://user:pass@host:26257/db?sslmode=verify-full
ELASTICSEARCH_URL=https://search.plazza.in
POSTGRES_URL=postgresql://user:pass@host:5432/db

# External Services
RAZORPAY_KEY_ID=key_prod_xxx
RAZORPAY_KEY_SECRET=secret_prod_xxx
GOOGLE_MAPS_API_KEY=xxx
WHATSAPP_API_KEY=xxx
AWS_ACCESS_KEY=xxx
AWS_SECRET_KEY=xxx
ANTHROPIC_API_KEY=xxx

# Service-Specific Config
BATCH_SIZE=500
LOG_LEVEL=info
```

## Deployment Process

### 1. Firebase Functions Deployment
```bash
# Deploy all functions
firebase deploy --only functions

# Deploy specific function
firebase deploy --only functions:serviceName
```

### 2. Database Migrations
```bash
# CockroachDB migrations
cockroach sql --url $COCKROACH_DB_URL -f migrations/

# PostgreSQL migrations
psql $POSTGRES_URL -f migrations/
```

### 3. Elasticsearch Index Management
```bash
# Update index mappings
curl -X PUT "https://search.plazza.in/products" -H "Content-Type: application/json" -d @mappings/products.json

# Reindex data
curl -X POST "https://search.plazza.in/_reindex" -H "Content-Type: application/json" -d @reindex/config.json
```

## Monitoring & Logging

### 1. Firebase Monitoring
- Function execution metrics
- Error reporting
- Performance monitoring

### 2. Database Monitoring
- CockroachDB Admin UI
- Elasticsearch monitoring
- PostgreSQL metrics

### 3. Custom Logging
```typescript
// Logging middleware
const loggingMiddleware = (req: Request, res: Response, next: NextFunction) => {
    const startTime = Date.now();
    
    res.on('finish', () => {
        const duration = Date.now() - startTime;
        logger.info({
            method: req.method,
            path: req.path,
            status: res.statusCode,
            duration,
            userAgent: req.get('user-agent')
        });
    });
    
    next();
};
```

## Scaling Configuration

### 1. Firebase Functions
```json
{
  "functions": {
    "minInstances": 1,
    "maxInstances": 10,
    "memory": "1GB",
    "timeoutSeconds": 540
  }
}
```

### 2. Database Scaling
- CockroachDB: Horizontal scaling with new nodes
- Elasticsearch: Increase data nodes and shards
- PostgreSQL: Vertical scaling and read replicas

## Backup Strategy

### 1. Database Backups
```bash
# CockroachDB backup
COCKROACH_BACKUP_PATH=gs://plazza-backups/$(date +%Y-%m-%d)
cockroach backup database plazza to $COCKROACH_BACKUP_PATH

# PostgreSQL backup
pg_dump $POSTGRES_URL > backup.sql
```

### 2. Firebase Backups
- Automated daily backups of Realtime Database
- Cloud Storage bucket versioning enabled

## Security Measures

### 1. Network Security
- All services behind Cloudflare
- Internal service communication over VPC
- SSL/TLS encryption for all endpoints

### 2. Authentication
```typescript
// Firebase Auth middleware
const authenticate = async (req: Request, res: Response, next: NextFunction) => {
    try {
        const token = req.headers.authorization?.split('Bearer ')[1];
        if (!token) throw new Error('No token provided');
        
        const decodedToken = await admin.auth().verifyIdToken(token);
        req.user = decodedToken;
        next();
    } catch (error) {
        res.status(401).json({ error: 'Unauthorized' });
    }
};
```

### 3. Data Security
- Encryption at rest for all databases
- Regular security audits
- Access control based on IAM roles

## Disaster Recovery

### 1. Failover Configuration
- CockroachDB automatic failover
- Elasticsearch cluster redundancy
- Firebase automatic failover

### 2. Recovery Procedures
```bash
# Database restore
cockroach sql --url $COCKROACH_DB_URL -e "RESTORE DATABASE plazza FROM '$COCKROACH_BACKUP_PATH';"

# Service recovery
firebase deploy --only functions:criticalService
```

## Performance Optimization

### 1. Caching Strategy
```typescript
// Redis caching example
const cacheMiddleware = async (req: Request, res: Response, next: NextFunction) => {
    const key = `cache:${req.path}`;
    const cached = await redis.get(key);
    
    if (cached) {
        return res.json(JSON.parse(cached));
    }
    
    next();
};
```

### 2. Query Optimization
- Elasticsearch query templates
- Database indexes
- Query monitoring and optimization

Would you like me to:
1. Add more specific deployment details for any service?
2. Include CI/CD pipeline configurations?
3. Add more security configurations?
4. Create service-specific scaling strategies? 