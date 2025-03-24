# Plazza Healthcare Platform

## System Overview
Plazza is a comprehensive healthcare platform that enables users to search for medicines, upload prescriptions, place orders, and track deliveries. The platform is built using a microservices architecture, with each service handling specific business functionalities.

## Architecture Overview
![System Architecture](docs/architecture.png)

The platform consists of the following core microservices:

## Core Services

### 1. Catalogue Service (`plazza-catalogue/`)
**Purpose:** Medicine search and prescription processing engine
- **Key Features:**
  - Real-time medicine search with Elasticsearch
  - Prescription image processing (OCR)
  - AI-powered medicine matching
  - Product recommendations

- **Tech Stack:**
  - Elasticsearch
  - AWS Textract
  - Claude AI
  - Firebase Functions

- **Key Endpoints:**
  ```
  POST /scan_prescription - Process prescription images
  POST /search_medicine  - Direct medicine search
  ```

### 2. Payment Service (`Firebase_payments/`)
**Purpose:** Handle all payment-related operations
- **Key Features:**
  - Razorpay integration
  - Payment tracking
  - Refund processing
  - Webhook handling

- **Tech Stack:**
  - Firebase Functions
  - Razorpay API
  - Firebase Realtime Database

- **Key Endpoints:**
  ```
  POST /createRazorpayOrder
  POST /razorpayWebhook
  POST /TestRazorpayWebhook
  ```

### 3. Order Service (`Firebase_Order_Services/`)
**Purpose:** Order management and processing
- **Key Features:**
  - Order creation and tracking
  - Multi-database synchronization
  - Delivery integration
  - Status updates

- **Tech Stack:**
  - Firebase Functions
  - CockroachDB
  - Airtable
  - Tookan API

### 4. Location Service (`Firebase_location_services/`)
**Purpose:** Handle location-based operations
- **Key Features:**
  - Address management
  - Serviceability checks
  - Geolocation services
  - Area mapping

- **Tech Stack:**
  - Firebase Functions
  - Google Maps API
  - PostgreSQL

- **Key Endpoints:**
  ```
  GET  /getAddressesByContactID
  POST /createAddress
  PUT  /updateAddress
  POST /checkServiceability
  ```

### 5. User Events Service (`Firebase_User_Events/`)
**Purpose:** Track user activity and sessions
- **Key Features:**
  - Session management
  - Event tracking
  - User activity logging
  - Analytics support

- **Tech Stack:**
  - Firebase Functions
  - Firebase Realtime Database

- **Key Endpoints:**
  ```
  GET  /getSession
  GET  /listSessions
  POST /createEvent
  GET  /getEvent
  GET  /listEvents
  ```

### 6. Discount Service (`Discount_Coupon_Codes/`)
**Purpose:** Manage discounts and coupon codes
- **Key Features:**
  - Coupon generation
  - Validation logic
  - Usage tracking
  - Export functionality

- **Tech Stack:**
  - Firebase Functions
  - PostgreSQL
  - Anthropic API

### 7. Database Operations (`plazza_db_ops/`)
**Purpose:** Handle database synchronization and maintenance
- **Components:**
  - ES catalogue deduplication
  - Airtable-CockroachDB sync
  - Inventory product matching
  - Vendor data standardization

- **Tech Stack:**
  - Python
  - CockroachDB
  - Elasticsearch
  - Airtable API

### 8. WhatsApp Service (`whatsapp_service/`)
**Purpose:** Handle WhatsApp communications
- **Key Features:**
  - Order notifications
  - Status updates
  - Customer support
  - Automated responses

- **Tech Stack:**
  - WhatsApp Business API
  - Firebase Functions

### 9. Point of Sale (`plazza-pos/`)
**Purpose:** Handle in-store transactions
- **Key Features:**
  - Inventory management
  - Sales processing
  - Receipt generation
  - Stock updates

### 10. Proof of Delivery (`plazza-POD/`)
**Purpose:** Manage delivery confirmations
- **Key Features:**
  - Delivery tracking
  - Proof collection
  - Status updates
  - Integration with order service

## Database Architecture

### Primary Databases
1. **CockroachDB**
   - Product catalog
   - Order data
   - Transactional information

2. **Elasticsearch**
   - Product search index
   - Fast text search
   - Medicine matching

3. **Firebase Realtime Database**
   - User events
   - Real-time updates
   - Session data

4. **Airtable**
   - Product management
   - Order tracking
   - Vendor management

## External Service Integrations

### Payment Processing
- **Razorpay**
  - Payment gateway integration
  - Webhook handling
  - Refund processing

### Image Processing
- **AWS Textract**
  - Prescription OCR
  - Image text extraction

### Location Services
- **Google Maps API**
  - Address validation
  - Geocoding
  - Distance calculation

### AI/ML Services
- **Claude AI**
  - Prescription analysis
  - Medicine matching
  - Text processing

### Communication
- **WhatsApp Business API**
  - Customer notifications
  - Order updates
  - Support messages

## Development Setup

### Prerequisites
- Node.js 18+
- Python 3.8+
- Firebase CLI
- Docker
- PostgreSQL client

### Environment Setup
1. Clone the repository:
   ```bash
   git clone https://github.com/your-org/plazza.git
   cd plazza
   ```

2. Install dependencies for all services:
   ```bash
   ./scripts/install-all.sh
   ```

3. Set up environment variables:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

### Running Services Locally
1. Start core services:
   ```bash
   docker-compose up -d
   ```

2. Run Firebase emulators:
   ```bash
   firebase emulators:start
   ```

## Deployment

### Production Deployment
- All services are deployed on Firebase Functions
- Databases are hosted on their respective cloud platforms
- CI/CD is handled through GitHub Actions

### Monitoring
- Firebase Functions monitoring
- Error tracking through Sentry
- Custom logging solutions

## Security

### API Security
- Environment-specific secrets
- API key management
- Webhook signature verification

### Database Security
- Secure connections
- Access control
- Data encryption

### Payment Security
- PCI compliance
- Secure payment processing
- Transaction monitoring

## Documentation
- Individual service documentation in respective folders
- API documentation in `/docs/api`
- Database schemas in `/docs/schemas`
- Architecture diagrams in `/docs/architecture`

## Contributing
Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support
For support, email dev@plazza.in or join our Slack channel. 