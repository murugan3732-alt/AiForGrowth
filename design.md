# Smart Village AI Platform - Technical Design Document

## Document Information

**Version:** 1.0  
**Date:** February 15, 2026  
**Status:** Design Phase  
**Project:** Smart Village AI Platform

---

## Table of Contents

1. [System Architecture](#1-system-architecture)
2. [Component Design](#2-component-design)
3. [Data Flow Diagrams](#3-data-flow-diagrams)
4. [AI Model Design](#4-ai-model-design)
5. [Database Schema](#5-database-schema)
6. [API Design](#6-api-design)
7. [UI/UX Design](#7-uiux-design)
8. [Deployment Architecture](#8-deployment-architecture)
9. [Scalability Plan](#9-scalability-plan)

---

## 1. System Architecture

### 1.1 High-Level Architecture

The Smart Village AI Platform follows a microservices architecture pattern with the following layers:

```
┌─────────────────────────────────────────────────────────────┐
│                     Presentation Layer                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Mobile App   │  │  Web Portal  │  │ Admin Panel  │      │
│  │ (React Native)│  │  (React.js)  │  │  (React.js)  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                       │
│  ┌────────────────────────────────────────────────────────┐ │
│  │  Kong / AWS API Gateway / NGINX                        │ │
│  │  - Authentication & Authorization                      │ │
│  │  - Rate Limiting & Throttling                         │ │
│  │  - Request Routing & Load Balancing                   │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Microservices Layer                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   User       │  │   Disease    │  │    Price     │      │
│  │  Service     │  │  Detection   │  │  Prediction  │      │
│  │              │  │   Service    │  │   Service    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Marketplace  │  │   Payment    │  │ Notification │      │
│  │   Service    │  │   Service    │  │   Service    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Digital    │  │  Analytics   │  │   Content    │      │
│  │  Services    │  │   Service    │  │   Service    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    AI/ML Infrastructure                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  CNN Model   │  │  LSTM Model  │  │   Model      │      │
│  │   Serving    │  │   Serving    │  │  Registry    │      │
│  │ (TF Serving) │  │ (TF Serving) │  │  (MLflow)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Data Layer                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  PostgreSQL  │  │   MongoDB    │  │    Redis     │      │
│  │  (Primary)   │  │   (Logs)     │  │   (Cache)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │     S3       │  │ Elasticsearch│  │   RabbitMQ   │      │
│  │  (Storage)   │  │   (Search)   │  │  (Message Q) │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Architecture Principles


**Microservices Architecture**: Each service is independently deployable, scalable, and maintainable.

**Event-Driven Communication**: Services communicate asynchronously via message queues for loose coupling.

**API-First Design**: All services expose RESTful APIs with comprehensive documentation.

**Cloud-Native**: Containerized services orchestrated with Kubernetes for portability and scalability.

**Security by Design**: Authentication, authorization, and encryption at every layer.

**Observability**: Comprehensive logging, monitoring, and tracing across all components.

### 1.3 Technology Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | React Native, React.js | Mobile and web applications |
| API Gateway | Kong / NGINX | Request routing, authentication |
| Backend Services | Python (FastAPI/Django), Node.js | Microservices implementation |
| AI/ML | TensorFlow, PyTorch, TF Serving | Model training and inference |
| Databases | PostgreSQL, MongoDB, Redis | Data persistence and caching |
| Message Queue | RabbitMQ / Apache Kafka | Asynchronous communication |
| Storage | AWS S3 / MinIO | Object storage for images |
| Search | Elasticsearch | Full-text search capabilities |
| Container Orchestration | Kubernetes | Service deployment and scaling |
| CI/CD | Jenkins, GitLab CI | Automated deployment pipeline |
| Monitoring | Prometheus, Grafana, ELK | System observability |

---

## 2. Component Design

### 2.1 User Service

**Responsibilities:**
- User registration and authentication
- Profile management
- Role-based access control (RBAC)
- Session management

**Technology:** Python FastAPI, PostgreSQL, Redis

**Key Components:**

```
UserService
├── AuthController
│   ├── register()
│   ├── login()
│   ├── verifyOTP()
│   ├── refreshToken()
│   └── logout()
├── ProfileController
│   ├── getProfile()
│   ├── updateProfile()
│   ├── uploadAvatar()
│   └── deleteAccount()
├── AuthMiddleware
│   ├── validateJWT()
│   ├── checkPermissions()
│   └── rateLimiter()
└── UserRepository
    ├── createUser()
    ├── findUserById()
    ├── updateUser()
    └── deleteUser()
```

**API Endpoints:**
- `POST /api/v1/auth/register` - User registration
- `POST /api/v1/auth/login` - User login
- `POST /api/v1/auth/verify-otp` - OTP verification
- `GET /api/v1/users/profile` - Get user profile
- `PUT /api/v1/users/profile` - Update profile

### 2.2 Disease Detection Service

**Responsibilities:**
- Image preprocessing and validation
- CNN model inference for disease detection
- Disease information retrieval
- Detection history management

**Technology:** Python FastAPI, TensorFlow Serving, PostgreSQL, S3

**Key Components:**

```
DiseaseDetectionService
├── ImageController
│   ├── uploadImage()
│   ├── preprocessImage()
│   └── validateImage()
├── DetectionController
│   ├── detectDisease()
│   ├── getDetectionHistory()
│   └── getDetectionDetails()
├── ModelInferenceEngine
│   ├── loadModel()
│   ├── predict()
│   ├── postprocess()
│   └── getConfidenceScore()
├── DiseaseRepository
│   ├── getDiseaseInfo()
│   ├── getTreatmentRecommendations()
│   └── saveDetectionResult()
└── ImageStorageService
    ├── uploadToS3()
    ├── generatePresignedURL()
    └── deleteImage()
```


**API Endpoints:**
- `POST /api/v1/disease/detect` - Upload and detect disease
- `GET /api/v1/disease/history` - Get detection history
- `GET /api/v1/disease/{id}` - Get detection details
- `GET /api/v1/disease/info/{disease_id}` - Get disease information

### 2.3 Price Prediction Service

**Responsibilities:**
- Historical price data management
- LSTM model inference for price forecasting
- Market trend analysis
- Price alert management

**Technology:** Python FastAPI, TensorFlow Serving, PostgreSQL, Redis

**Key Components:**

```
PricePredictionService
├── PriceController
│   ├── getPricePrediction()
│   ├── getHistoricalPrices()
│   ├── comparePrices()
│   └── subscribeToPriceAlerts()
├── PredictionEngine
│   ├── loadLSTMModel()
│   ├── preprocessData()
│   ├── predict()
│   └── generateForecast()
├── MarketDataCollector
│   ├── fetchMarketData()
│   ├── aggregateData()
│   └── updateDatabase()
├── AlertService
│   ├── checkPriceThresholds()
│   ├── triggerAlerts()
│   └── manageSubscriptions()
└── PriceRepository
    ├── saveHistoricalPrice()
    ├── getPriceHistory()
    └── savePrediction()
```

**API Endpoints:**
- `GET /api/v1/price/predict` - Get price prediction
- `GET /api/v1/price/history` - Get historical prices
- `POST /api/v1/price/alerts` - Subscribe to price alerts
- `GET /api/v1/price/compare` - Compare prices across markets

### 2.4 Marketplace Service

**Responsibilities:**
- Product listing management
- Search and filtering
- Order processing
- Transaction management
- Rating and review system

**Technology:** Node.js (Express), PostgreSQL, Elasticsearch, Redis


**Key Components:**

```
MarketplaceService
├── ListingController
│   ├── createListing()
│   ├── updateListing()
│   ├── deleteListing()
│   ├── searchListings()
│   └── getListingDetails()
├── OrderController
│   ├── createOrder()
│   ├── updateOrderStatus()
│   ├── getOrderHistory()
│   └── cancelOrder()
├── ReviewController
│   ├── addReview()
│   ├── getReviews()
│   └── reportReview()
├── SearchEngine
│   ├── indexListing()
│   ├── searchByFilters()
│   └── autoComplete()
└── MarketplaceRepository
    ├── saveProduct()
    ├── findProducts()
    └── updateInventory()
```

**API Endpoints:**
- `POST /api/v1/marketplace/listings` - Create product listing
- `GET /api/v1/marketplace/listings` - Search listings
- `POST /api/v1/marketplace/orders` - Create order
- `GET /api/v1/marketplace/orders/{id}` - Get order details
- `POST /api/v1/marketplace/reviews` - Add review

### 2.5 Payment Service

**Responsibilities:**
- Payment gateway integration
- Transaction processing
- Payment verification
- Refund management
- Invoice generation

**Technology:** Node.js (Express), PostgreSQL, Redis

**Key Components:**

```
PaymentService
├── PaymentController
│   ├── initiatePayment()
│   ├── verifyPayment()
│   ├── processRefund()
│   └── getTransactionHistory()
├── PaymentGatewayAdapter
│   ├── RazorpayAdapter
│   ├── StripeAdapter
│   └── UPIAdapter
├── TransactionManager
│   ├── createTransaction()
│   ├── updateTransactionStatus()
│   └── handleWebhook()
└── InvoiceGenerator
    ├── generateInvoice()
    ├── sendInvoiceEmail()
    └── downloadInvoice()
```


**API Endpoints:**
- `POST /api/v1/payment/initiate` - Initiate payment
- `POST /api/v1/payment/verify` - Verify payment
- `POST /api/v1/payment/refund` - Process refund
- `GET /api/v1/payment/transactions` - Get transaction history

### 2.6 Notification Service

**Responsibilities:**
- Push notification delivery
- SMS notification
- Email notification
- In-app notification management

**Technology:** Node.js (Express), MongoDB, Redis, Firebase FCM

**Key Components:**

```
NotificationService
├── NotificationController
│   ├── sendNotification()
│   ├── getNotifications()
│   ├── markAsRead()
│   └── deleteNotification()
├── NotificationDispatcher
│   ├── PushNotificationHandler
│   ├── SMSHandler
│   ├── EmailHandler
│   └── InAppHandler
├── TemplateEngine
│   ├── loadTemplate()
│   ├── renderTemplate()
│   └── localizeContent()
└── NotificationRepository
    ├── saveNotification()
    ├── findNotifications()
    └── updateStatus()
```

**API Endpoints:**
- `POST /api/v1/notifications/send` - Send notification
- `GET /api/v1/notifications` - Get user notifications
- `PUT /api/v1/notifications/{id}/read` - Mark as read

### 2.7 Digital Services Module

**Responsibilities:**
- Government scheme information
- Weather updates
- Agricultural advisory
- Community forum
- Educational content

**Technology:** Python FastAPI, PostgreSQL, MongoDB

**Key Components:**

```
DigitalServicesModule
├── SchemeController
│   ├── getSchemes()
│   ├── getSchemeDetails()
│   └── applyForScheme()
├── WeatherController
│   ├── getCurrentWeather()
│   ├── getWeatherForecast()
│   └── getWeatherAlerts()
├── AdvisoryController
│   ├── getAdvisories()
│   ├── getSeasonalTips()
│   └── getCropCalendar()
└── ForumController
    ├── createPost()
    ├── getPosts()
    ├── addComment()
    └── likePost()
```


**API Endpoints:**
- `GET /api/v1/services/schemes` - Get government schemes
- `GET /api/v1/services/weather` - Get weather information
- `GET /api/v1/services/advisory` - Get agricultural advisory
- `GET /api/v1/services/forum/posts` - Get forum posts

### 2.8 Analytics Service

**Responsibilities:**
- User behavior tracking
- Platform usage analytics
- Business intelligence reporting
- AI model performance monitoring

**Technology:** Python, Apache Spark, PostgreSQL, MongoDB

**Key Components:**

```
AnalyticsService
├── EventCollector
│   ├── trackEvent()
│   ├── batchEvents()
│   └── storeEvents()
├── AnalyticsEngine
│   ├── aggregateMetrics()
│   ├── generateReports()
│   └── calculateKPIs()
├── DashboardController
│   ├── getUserMetrics()
│   ├── getMarketplaceMetrics()
│   └── getAIModelMetrics()
└── ReportGenerator
    ├── generateDailyReport()
    ├── generateWeeklyReport()
    └── exportReport()
```

---

## 3. Data Flow Diagrams

### 3.1 Disease Detection Flow

```
User (Mobile App)
    │
    │ 1. Capture/Upload Crop Image
    ▼
API Gateway
    │
    │ 2. Authenticate & Route Request
    ▼
Disease Detection Service
    │
    ├─► 3. Validate Image (size, format)
    │
    ├─► 4. Upload to S3 Storage
    │       │
    │       └─► S3 Bucket (Images)
    │
    ├─► 5. Preprocess Image
    │       │
    │       ├─► Resize (224x224)
    │       ├─► Normalize
    │       └─► Convert to Tensor
    │
    ├─► 6. Send to CNN Model
    │       │
    │       ▼
    │   TensorFlow Serving (CNN Model)
    │       │
    │       ├─► Load Model
    │       ├─► Inference
    │       └─► Return Predictions
    │
    ├─► 7. Post-process Results
    │       │
    │       ├─► Get Top-K Predictions
    │       ├─► Calculate Confidence
    │       └─► Fetch Disease Info
    │
    ├─► 8. Save Detection Result
    │       │
    │       ▼
    │   PostgreSQL (Detection History)
    │
    └─► 9. Return Response to User
            │
            ▼
        Mobile App (Display Results)
```


### 3.2 Price Prediction Flow

```
User Request (Crop Type, Location, Duration)
    │
    ▼
API Gateway
    │
    ▼
Price Prediction Service
    │
    ├─► 1. Check Redis Cache
    │       │
    │       ├─► Cache Hit? Return Cached Prediction
    │       │
    │       └─► Cache Miss? Continue
    │
    ├─► 2. Fetch Historical Data
    │       │
    │       ▼
    │   PostgreSQL (Price History)
    │       │
    │       └─► Last 5 Years Data
    │
    ├─► 3. Preprocess Data
    │       │
    │       ├─► Normalize Prices
    │       ├─► Create Sequences
    │       └─► Handle Missing Values
    │
    ├─► 4. Send to LSTM Model
    │       │
    │       ▼
    │   TensorFlow Serving (LSTM Model)
    │       │
    │       ├─► Load Model
    │       ├─► Predict Future Prices
    │       └─► Return Predictions
    │
    ├─► 5. Post-process Results
    │       │
    │       ├─► Denormalize Prices
    │       ├─► Calculate Trends
    │       └─► Generate Insights
    │
    ├─► 6. Cache Results (Redis)
    │       │
    │       └─► TTL: 6 hours
    │
    ├─► 7. Save Prediction
    │       │
    │       ▼
    │   PostgreSQL (Predictions Table)
    │
    └─► 8. Return Response
            │
            ▼
        Mobile App (Display Chart & Insights)
```

### 3.3 Marketplace Transaction Flow

```
Farmer                          Buyer
    │                              │
    │ 1. Create Listing            │
    ▼                              │
Marketplace Service               │
    │                              │
    ├─► Save to PostgreSQL         │
    ├─► Index in Elasticsearch     │
    │                              │
    │                              │ 2. Search Products
    │                              ▼
    │                          Marketplace Service
    │                              │
    │                              ├─► Query Elasticsearch
    │                              └─► Return Results
    │                              │
    │                              │ 3. Place Order
    │                              ▼
    │                          Order Service
    │                              │
    │                              ├─► Create Order (PostgreSQL)
    │                              │
    │                              ├─► 4. Initiate Payment
    │                              ▼
    │                          Payment Service
    │                              │
    │                              ├─► Call Payment Gateway
    │                              ├─► Verify Payment
    │                              └─► Update Order Status
    │                              │
    │ 5. Notify Farmer              │ 6. Notify Buyer
    ◄────────────────────────────────────────────────
    │                              │
Notification Service          Notification Service
    │                              │
    └─► Push/SMS/Email             └─► Push/SMS/Email
```


### 3.4 Authentication Flow

```
User (Mobile App)
    │
    │ 1. Enter Mobile Number
    ▼
API Gateway
    │
    ▼
User Service
    │
    ├─► 2. Validate Mobile Number
    │
    ├─► 3. Generate OTP
    │
    ├─► 4. Store OTP in Redis
    │       │
    │       └─► TTL: 5 minutes
    │
    ├─► 5. Send OTP via SMS
    │       │
    │       ▼
    │   Notification Service
    │       │
    │       └─► SMS Gateway (Twilio)
    │
    │ 6. User Enters OTP
    ◄───────────────────
    │
    ├─► 7. Verify OTP (Redis)
    │
    ├─► 8. Generate JWT Token
    │       │
    │       ├─► Access Token (15 min)
    │       └─► Refresh Token (7 days)
    │
    ├─► 9. Store Session (Redis)
    │
    └─► 10. Return Tokens
            │
            ▼
        Mobile App (Store Tokens)
```

---

## 4. AI Model Design

### 4.1 CNN Model for Disease Detection

**Architecture: ResNet50 with Transfer Learning**

```
Input Layer (224x224x3)
    │
    ▼
ResNet50 Base (Pre-trained on ImageNet)
    │
    ├─► Conv Layers (Frozen)
    ├─► Batch Normalization
    ├─► ReLU Activation
    └─► Max Pooling
    │
    ▼
Global Average Pooling
    │
    ▼
Dense Layer (512 units, ReLU)
    │
    ▼
Dropout (0.5)
    │
    ▼
Dense Layer (256 units, ReLU)
    │
    ▼
Dropout (0.3)
    │
    ▼
Output Layer (num_classes, Softmax)
```

**Training Configuration:**

```python
model_config = {
    "architecture": "ResNet50",
    "input_shape": (224, 224, 3),
    "num_classes": 38,  # Number of disease classes
    "optimizer": "Adam",
    "learning_rate": 0.0001,
    "loss": "categorical_crossentropy",
    "metrics": ["accuracy", "top_k_accuracy"],
    "batch_size": 32,
    "epochs": 50,
    "early_stopping_patience": 10,
    "reduce_lr_patience": 5
}
```


**Data Augmentation:**

```python
augmentation_config = {
    "rotation_range": 20,
    "width_shift_range": 0.2,
    "height_shift_range": 0.2,
    "horizontal_flip": True,
    "vertical_flip": True,
    "zoom_range": 0.2,
    "brightness_range": [0.8, 1.2],
    "fill_mode": "nearest"
}
```

**Model Performance Metrics:**
- Target Accuracy: >85%
- Inference Time: <3 seconds
- Model Size: <100MB (for mobile deployment)
- Top-5 Accuracy: >95%

**Deployment Strategy:**
- TensorFlow Serving for cloud inference
- TensorFlow Lite for on-device inference (offline mode)
- Model versioning with A/B testing capability

### 4.2 LSTM Model for Price Prediction

**Architecture: Stacked LSTM with Attention**

```
Input Layer (sequence_length, num_features)
    │
    ▼
LSTM Layer 1 (128 units, return_sequences=True)
    │
    ├─► Dropout (0.2)
    │
    ▼
LSTM Layer 2 (64 units, return_sequences=True)
    │
    ├─► Dropout (0.2)
    │
    ▼
Attention Layer
    │
    ▼
LSTM Layer 3 (32 units)
    │
    ├─► Dropout (0.2)
    │
    ▼
Dense Layer (16 units, ReLU)
    │
    ▼
Output Layer (prediction_horizon)
```

**Training Configuration:**

```python
lstm_config = {
    "sequence_length": 60,  # 60 days lookback
    "prediction_horizon": 30,  # 30 days forecast
    "features": [
        "price",
        "volume",
        "seasonal_index",
        "day_of_week",
        "month",
        "rainfall",
        "temperature"
    ],
    "optimizer": "Adam",
    "learning_rate": 0.001,
    "loss": "mean_squared_error",
    "metrics": ["mae", "mape"],
    "batch_size": 64,
    "epochs": 100,
    "validation_split": 0.2
}
```

**Feature Engineering:**

```python
features = {
    "temporal": ["day", "month", "year", "day_of_week", "quarter"],
    "lag_features": ["price_lag_7", "price_lag_14", "price_lag_30"],
    "rolling_stats": ["rolling_mean_7", "rolling_std_7", "rolling_mean_30"],
    "seasonal": ["seasonal_index", "trend_component"],
    "external": ["rainfall", "temperature", "market_demand"]
}
```


**Model Performance Metrics:**
- Target MAPE: <15%
- Target MAE: <10% of mean price
- Inference Time: <1 second
- Update Frequency: Weekly retraining

### 4.3 Model Training Pipeline

```
Data Collection
    │
    ├─► Web Scraping (Market Prices)
    ├─► API Integration (Weather Data)
    └─► User Uploads (Disease Images)
    │
    ▼
Data Preprocessing
    │
    ├─► Data Cleaning
    ├─► Feature Engineering
    ├─► Data Augmentation
    └─► Train/Val/Test Split
    │
    ▼
Model Training
    │
    ├─► Hyperparameter Tuning
    ├─► Cross-Validation
    └─► Model Selection
    │
    ▼
Model Evaluation
    │
    ├─► Performance Metrics
    ├─► Error Analysis
    └─► Validation Tests
    │
    ▼
Model Registry (MLflow)
    │
    ├─► Version Control
    ├─► Metadata Storage
    └─► Model Artifacts
    │
    ▼
Model Deployment
    │
    ├─► TensorFlow Serving
    ├─► A/B Testing
    └─► Monitoring
```

### 4.4 Model Monitoring and Retraining

**Monitoring Metrics:**
- Prediction accuracy drift
- Inference latency
- Model confidence distribution
- Error rate by category
- Data distribution shift

**Retraining Triggers:**
- Accuracy drops below threshold (85% for CNN, 70% for LSTM)
- Significant data drift detected
- New disease patterns emerge
- Scheduled monthly retraining
- Manual trigger by admin

---

## 5. Database Schema

### 5.1 PostgreSQL Schema (Primary Database)

**Users Table:**

```sql
CREATE TABLE users (
    user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    mobile_number VARCHAR(15) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE,
    full_name VARCHAR(255) NOT NULL,
    user_type VARCHAR(20) NOT NULL CHECK (user_type IN ('farmer', 'buyer', 'admin')),
    profile_image_url TEXT,
    language_preference VARCHAR(10) DEFAULT 'en',
    location JSONB,
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP
);

CREATE INDEX idx_users_mobile ON users(mobile_number);
CREATE INDEX idx_users_type ON users(user_type);
CREATE INDEX idx_users_location ON users USING GIN(location);
```


**Farmer Profiles Table:**

```sql
CREATE TABLE farmer_profiles (
    profile_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(user_id) ON DELETE CASCADE,
    farm_size DECIMAL(10, 2),
    farm_location GEOGRAPHY(POINT),
    crops_grown TEXT[],
    farming_experience INTEGER,
    certifications TEXT[],
    bank_account_details JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_farmer_user ON farmer_profiles(user_id);
CREATE INDEX idx_farmer_location ON farmer_profiles USING GIST(farm_location);
```

**Disease Detection Table:**

```sql
CREATE TABLE disease_detections (
    detection_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(user_id),
    image_url TEXT NOT NULL,
    crop_type VARCHAR(100),
    detected_disease VARCHAR(255),
    confidence_score DECIMAL(5, 4),
    severity_level VARCHAR(20),
    predictions JSONB,
    location GEOGRAPHY(POINT),
    detection_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    model_version VARCHAR(50),
    processing_time_ms INTEGER
);

CREATE INDEX idx_detection_user ON disease_detections(user_id);
CREATE INDEX idx_detection_timestamp ON disease_detections(detection_timestamp);
CREATE INDEX idx_detection_disease ON disease_detections(detected_disease);
```

**Disease Information Table:**

```sql
CREATE TABLE diseases (
    disease_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    disease_name VARCHAR(255) UNIQUE NOT NULL,
    scientific_name VARCHAR(255),
    crop_types TEXT[],
    symptoms TEXT[],
    causes TEXT,
    treatment_recommendations JSONB,
    prevention_measures TEXT[],
    severity_levels JSONB,
    images TEXT[],
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_disease_name ON diseases(disease_name);
CREATE INDEX idx_disease_crops ON diseases USING GIN(crop_types);
```

**Price History Table:**

```sql
CREATE TABLE price_history (
    price_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    crop_type VARCHAR(100) NOT NULL,
    market_name VARCHAR(255) NOT NULL,
    location GEOGRAPHY(POINT),
    price_per_unit DECIMAL(10, 2) NOT NULL,
    unit VARCHAR(20) NOT NULL,
    volume_traded DECIMAL(12, 2),
    price_date DATE NOT NULL,
    source VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_price_crop ON price_history(crop_type);
CREATE INDEX idx_price_date ON price_history(price_date);
CREATE INDEX idx_price_market ON price_history(market_name);
CREATE INDEX idx_price_location ON price_history USING GIST(location);
```


**Price Predictions Table:**

```sql
CREATE TABLE price_predictions (
    prediction_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    crop_type VARCHAR(100) NOT NULL,
    market_name VARCHAR(255),
    prediction_date DATE NOT NULL,
    predicted_price DECIMAL(10, 2) NOT NULL,
    confidence_interval JSONB,
    model_version VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_prediction_crop ON price_predictions(crop_type);
CREATE INDEX idx_prediction_date ON price_predictions(prediction_date);
```

**Marketplace Listings Table:**

```sql
CREATE TABLE marketplace_listings (
    listing_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    farmer_id UUID REFERENCES users(user_id),
    crop_type VARCHAR(100) NOT NULL,
    variety VARCHAR(100),
    quantity DECIMAL(10, 2) NOT NULL,
    unit VARCHAR(20) NOT NULL,
    price_per_unit DECIMAL(10, 2) NOT NULL,
    quality_grade VARCHAR(20),
    harvest_date DATE,
    available_from DATE,
    location GEOGRAPHY(POINT),
    images TEXT[],
    description TEXT,
    status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'sold', 'expired', 'cancelled')),
    views_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP
);

CREATE INDEX idx_listing_farmer ON marketplace_listings(farmer_id);
CREATE INDEX idx_listing_crop ON marketplace_listings(crop_type);
CREATE INDEX idx_listing_status ON marketplace_listings(status);
CREATE INDEX idx_listing_location ON marketplace_listings USING GIST(location);
CREATE INDEX idx_listing_created ON marketplace_listings(created_at);
```

**Orders Table:**

```sql
CREATE TABLE orders (
    order_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    listing_id UUID REFERENCES marketplace_listings(listing_id),
    buyer_id UUID REFERENCES users(user_id),
    farmer_id UUID REFERENCES users(user_id),
    quantity DECIMAL(10, 2) NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    total_amount DECIMAL(12, 2) NOT NULL,
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'paid', 'shipped', 'delivered', 'cancelled', 'refunded')),
    payment_status VARCHAR(20) DEFAULT 'pending',
    delivery_address JSONB,
    delivery_date DATE,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_order_buyer ON orders(buyer_id);
CREATE INDEX idx_order_farmer ON orders(farmer_id);
CREATE INDEX idx_order_status ON orders(status);
CREATE INDEX idx_order_created ON orders(created_at);
```


**Transactions Table:**

```sql
CREATE TABLE transactions (
    transaction_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID REFERENCES orders(order_id),
    payment_gateway VARCHAR(50),
    gateway_transaction_id VARCHAR(255),
    amount DECIMAL(12, 2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'INR',
    status VARCHAR(20) DEFAULT 'pending',
    payment_method VARCHAR(50),
    transaction_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    metadata JSONB
);

CREATE INDEX idx_transaction_order ON transactions(order_id);
CREATE INDEX idx_transaction_status ON transactions(status);
CREATE INDEX idx_transaction_timestamp ON transactions(transaction_timestamp);
```

**Reviews Table:**

```sql
CREATE TABLE reviews (
    review_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID REFERENCES orders(order_id),
    reviewer_id UUID REFERENCES users(user_id),
    reviewee_id UUID REFERENCES users(user_id),
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    comment TEXT,
    images TEXT[],
    is_verified_purchase BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_review_reviewee ON reviews(reviewee_id);
CREATE INDEX idx_review_order ON reviews(order_id);
CREATE INDEX idx_review_rating ON reviews(rating);
```

### 5.2 MongoDB Schema (Logs and Unstructured Data)

**Application Logs Collection:**

```javascript
{
  _id: ObjectId,
  timestamp: ISODate,
  level: String,  // "info", "warning", "error", "critical"
  service: String,  // "user-service", "disease-detection", etc.
  message: String,
  context: {
    user_id: String,
    request_id: String,
    ip_address: String,
    user_agent: String
  },
  stack_trace: String,
  metadata: Object
}
```

**Notifications Collection:**

```javascript
{
  _id: ObjectId,
  user_id: String,
  notification_type: String,  // "push", "sms", "email", "in-app"
  title: String,
  body: String,
  data: Object,
  status: String,  // "pending", "sent", "delivered", "failed"
  read: Boolean,
  created_at: ISODate,
  sent_at: ISODate,
  read_at: ISODate
}
```


**Forum Posts Collection:**

```javascript
{
  _id: ObjectId,
  user_id: String,
  title: String,
  content: String,
  category: String,
  tags: [String],
  images: [String],
  likes_count: Number,
  comments_count: Number,
  views_count: Number,
  is_pinned: Boolean,
  is_locked: Boolean,
  created_at: ISODate,
  updated_at: ISODate,
  comments: [
    {
      comment_id: String,
      user_id: String,
      content: String,
      likes_count: Number,
      created_at: ISODate
    }
  ]
}
```

### 5.3 Redis Schema (Caching and Sessions)

**Session Cache:**
```
Key: session:{user_id}
Value: {
  "access_token": "jwt_token",
  "refresh_token": "refresh_token",
  "user_type": "farmer",
  "last_activity": "timestamp"
}
TTL: 15 minutes (access), 7 days (refresh)
```

**OTP Cache:**
```
Key: otp:{mobile_number}
Value: {
  "otp": "123456",
  "attempts": 0,
  "created_at": "timestamp"
}
TTL: 5 minutes
```

**Price Prediction Cache:**
```
Key: price_prediction:{crop_type}:{market}:{date}
Value: {
  "predictions": [...],
  "confidence": 0.85,
  "generated_at": "timestamp"
}
TTL: 6 hours
```

**Rate Limiting:**
```
Key: rate_limit:{user_id}:{endpoint}
Value: request_count
TTL: 1 minute
```

---

## 6. API Design

### 6.1 API Standards

**Base URL:** `https://api.smartvillage.com/v1`

**Authentication:** Bearer Token (JWT)

**Request Headers:**
```
Authorization: Bearer {access_token}
Content-Type: application/json
Accept-Language: en
X-Device-ID: {device_id}
X-App-Version: 1.0.0
```

**Response Format:**
```json
{
  "success": true,
  "data": {},
  "message": "Success message",
  "timestamp": "2026-02-15T10:30:00Z",
  "request_id": "uuid"
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": {}
  },
  "timestamp": "2026-02-15T10:30:00Z",
  "request_id": "uuid"
}
```


### 6.2 Authentication APIs

**POST /auth/register**
```json
Request:
{
  "mobile_number": "+919876543210",
  "full_name": "John Farmer",
  "user_type": "farmer",
  "language_preference": "en"
}

Response:
{
  "success": true,
  "data": {
    "user_id": "uuid",
    "otp_sent": true
  }
}
```

**POST /auth/verify-otp**
```json
Request:
{
  "mobile_number": "+919876543210",
  "otp": "123456"
}

Response:
{
  "success": true,
  "data": {
    "access_token": "jwt_token",
    "refresh_token": "refresh_token",
    "user": {
      "user_id": "uuid",
      "full_name": "John Farmer",
      "user_type": "farmer"
    }
  }
}
```

### 6.3 Disease Detection APIs

**POST /disease/detect**
```json
Request (multipart/form-data):
{
  "image": File,
  "crop_type": "tomato",
  "location": {
    "latitude": 28.7041,
    "longitude": 77.1025
  }
}

Response:
{
  "success": true,
  "data": {
    "detection_id": "uuid",
    "detected_disease": "Tomato Late Blight",
    "confidence_score": 0.92,
    "severity_level": "high",
    "top_predictions": [
      {
        "disease": "Tomato Late Blight",
        "confidence": 0.92
      },
      {
        "disease": "Tomato Early Blight",
        "confidence": 0.05
      }
    ],
    "disease_info": {
      "symptoms": ["..."],
      "treatment": ["..."],
      "prevention": ["..."]
    },
    "processing_time_ms": 2500
  }
}
```

**GET /disease/history**
```json
Query Parameters:
- page: 1
- limit: 20
- crop_type: "tomato" (optional)
- start_date: "2026-01-01" (optional)
- end_date: "2026-02-15" (optional)

Response:
{
  "success": true,
  "data": {
    "detections": [...],
    "pagination": {
      "current_page": 1,
      "total_pages": 5,
      "total_items": 100,
      "items_per_page": 20
    }
  }
}
```


### 6.4 Price Prediction APIs

**GET /price/predict**
```json
Query Parameters:
- crop_type: "wheat"
- market: "Delhi Mandi"
- prediction_days: 30

Response:
{
  "success": true,
  "data": {
    "crop_type": "wheat",
    "market": "Delhi Mandi",
    "current_price": 2500,
    "predictions": [
      {
        "date": "2026-02-16",
        "predicted_price": 2520,
        "confidence_interval": {
          "lower": 2480,
          "upper": 2560
        }
      },
      // ... 29 more days
    ],
    "trend": "increasing",
    "insights": [
      "Prices expected to rise by 5% in next 30 days",
      "Peak price expected around March 1st"
    ],
    "model_accuracy": 0.78
  }
}
```

**GET /price/history**
```json
Query Parameters:
- crop_type: "wheat"
- market: "Delhi Mandi"
- start_date: "2025-02-15"
- end_date: "2026-02-15"

Response:
{
  "success": true,
  "data": {
    "prices": [
      {
        "date": "2025-02-15",
        "price": 2300,
        "volume": 1500
      },
      // ... more data points
    ],
    "statistics": {
      "min_price": 2100,
      "max_price": 2600,
      "avg_price": 2400,
      "volatility": 0.15
    }
  }
}
```

### 6.5 Marketplace APIs

**POST /marketplace/listings**
```json
Request:
{
  "crop_type": "wheat",
  "variety": "HD-2967",
  "quantity": 1000,
  "unit": "kg",
  "price_per_unit": 25,
  "quality_grade": "A",
  "harvest_date": "2026-02-10",
  "available_from": "2026-02-15",
  "location": {
    "latitude": 28.7041,
    "longitude": 77.1025
  },
  "images": ["url1", "url2"],
  "description": "Fresh wheat harvest"
}

Response:
{
  "success": true,
  "data": {
    "listing_id": "uuid",
    "status": "active",
    "created_at": "2026-02-15T10:30:00Z"
  }
}
```

**GET /marketplace/listings**
```json
Query Parameters:
- crop_type: "wheat" (optional)
- min_price: 20 (optional)
- max_price: 30 (optional)
- location: "28.7041,77.1025" (optional)
- radius_km: 50 (optional)
- quality_grade: "A" (optional)
- sort_by: "price_asc" | "price_desc" | "date_desc"
- page: 1
- limit: 20

Response:
{
  "success": true,
  "data": {
    "listings": [...],
    "pagination": {...}
  }
}
```


**POST /marketplace/orders**
```json
Request:
{
  "listing_id": "uuid",
  "quantity": 500,
  "delivery_address": {
    "address_line": "123 Main St",
    "city": "Delhi",
    "state": "Delhi",
    "pincode": "110001",
    "phone": "+919876543210"
  },
  "notes": "Please deliver before 5 PM"
}

Response:
{
  "success": true,
  "data": {
    "order_id": "uuid",
    "total_amount": 12500,
    "status": "pending",
    "payment_required": true
  }
}
```

### 6.6 Payment APIs

**POST /payment/initiate**
```json
Request:
{
  "order_id": "uuid",
  "payment_method": "upi",
  "amount": 12500
}

Response:
{
  "success": true,
  "data": {
    "transaction_id": "uuid",
    "payment_gateway": "razorpay",
    "gateway_order_id": "order_xyz",
    "payment_url": "https://...",
    "qr_code": "data:image/png;base64,..."
  }
}
```

**POST /payment/verify**
```json
Request:
{
  "transaction_id": "uuid",
  "gateway_payment_id": "pay_xyz",
  "gateway_signature": "signature"
}

Response:
{
  "success": true,
  "data": {
    "payment_status": "success",
    "order_status": "confirmed",
    "invoice_url": "https://..."
  }
}
```

### 6.7 Digital Services APIs

**GET /services/weather**
```json
Query Parameters:
- latitude: 28.7041
- longitude: 77.1025

Response:
{
  "success": true,
  "data": {
    "current": {
      "temperature": 25,
      "humidity": 60,
      "rainfall": 0,
      "wind_speed": 10,
      "condition": "clear"
    },
    "forecast": [
      {
        "date": "2026-02-16",
        "max_temp": 28,
        "min_temp": 18,
        "rainfall_probability": 20,
        "condition": "partly_cloudy"
      },
      // ... 6 more days
    ],
    "alerts": []
  }
}
```

**GET /services/advisory**
```json
Query Parameters:
- crop_type: "wheat"
- location: "28.7041,77.1025"

Response:
{
  "success": true,
  "data": {
    "advisories": [
      {
        "title": "Irrigation Advisory",
        "content": "...",
        "priority": "high",
        "valid_until": "2026-02-20"
      }
    ],
    "seasonal_tips": [...],
    "crop_calendar": [...]
  }
}
```


### 6.8 API Rate Limiting

| User Type | Rate Limit | Burst Limit |
|-----------|-----------|-------------|
| Anonymous | 10 req/min | 20 |
| Authenticated | 100 req/min | 200 |
| Premium | 500 req/min | 1000 |
| Admin | Unlimited | Unlimited |

**Rate Limit Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1645012800
```

---

## 7. UI/UX Design

### 7.1 Mobile App Architecture

**Navigation Structure:**

```
Bottom Tab Navigation
├── Home
│   ├── Quick Actions (Disease Detection, Price Check)
│   ├── Recent Activities
│   └── Notifications
├── Marketplace
│   ├── Browse Listings
│   ├── My Listings (Farmers)
│   ├── My Orders (Buyers)
│   └── Search & Filters
├── Services
│   ├── Weather
│   ├── Advisory
│   ├── Government Schemes
│   └── Community Forum
├── AI Tools
│   ├── Disease Detection
│   ├── Price Prediction
│   └── History
└── Profile
    ├── Personal Info
    ├── Settings
    ├── Language
    └── Help & Support
```

### 7.2 Key User Flows

**Disease Detection Flow:**

```
1. Home Screen
   ↓
2. Tap "Detect Disease" Button
   ↓
3. Camera/Gallery Selection
   ↓
4. Capture/Select Image
   ↓
5. Crop Type Selection (Optional)
   ↓
6. Processing Screen (Loading Animation)
   ↓
7. Results Screen
   ├── Disease Name & Confidence
   ├── Severity Indicator
   ├── Symptoms
   ├── Treatment Recommendations
   └── Save to History
```

**Marketplace Listing Flow (Farmer):**

```
1. Marketplace Tab
   ↓
2. Tap "Create Listing" Button
   ↓
3. Crop Selection
   ↓
4. Enter Details (Quantity, Price, Quality)
   ↓
5. Upload Images
   ↓
6. Set Location
   ↓
7. Review & Confirm
   ↓
8. Listing Published
```

**Order Placement Flow (Buyer):**

```
1. Browse Listings
   ↓
2. Apply Filters (Crop, Location, Price)
   ↓
3. View Listing Details
   ↓
4. Contact Farmer (Optional)
   ↓
5. Place Order
   ↓
6. Enter Delivery Details
   ↓
7. Payment
   ↓
8. Order Confirmation
```


### 7.3 Design System

**Color Palette:**

```
Primary Colors:
- Green: #2E7D32 (Agriculture, Growth)
- Light Green: #66BB6A (Accents)
- Dark Green: #1B5E20 (Headers)

Secondary Colors:
- Orange: #FF9800 (Alerts, CTAs)
- Blue: #1976D2 (Information)
- Red: #D32F2F (Errors, Warnings)

Neutral Colors:
- White: #FFFFFF
- Light Gray: #F5F5F5
- Gray: #9E9E9E
- Dark Gray: #424242
- Black: #000000
```

**Typography:**

```
Font Family: Roboto (Android), SF Pro (iOS)

Headings:
- H1: 32px, Bold
- H2: 24px, Bold
- H3: 20px, Semi-Bold
- H4: 18px, Semi-Bold

Body:
- Large: 16px, Regular
- Medium: 14px, Regular
- Small: 12px, Regular

Buttons:
- 16px, Semi-Bold, Uppercase
```

**Component Library:**

```
Buttons:
- Primary Button (Filled, Green)
- Secondary Button (Outlined, Green)
- Text Button (Text only)
- Icon Button

Cards:
- Listing Card
- Detection Result Card
- Price Chart Card
- Advisory Card

Forms:
- Text Input
- Number Input
- Dropdown Select
- Date Picker
- Image Upload
- Location Picker

Navigation:
- Bottom Tab Bar
- Top App Bar
- Drawer Menu
- Breadcrumbs
```

### 7.4 Accessibility Features

- Minimum touch target size: 48x48 dp
- Color contrast ratio: 4.5:1 (WCAG AA)
- Screen reader support
- Voice navigation
- Large text mode
- High contrast mode
- Haptic feedback
- Multi-language support (5+ regional languages)

### 7.5 Responsive Design

**Mobile Breakpoints:**
- Small: 320px - 375px
- Medium: 375px - 414px
- Large: 414px+

**Tablet Support:**
- Portrait: 768px
- Landscape: 1024px

**Web Portal:**
- Mobile: < 768px
- Tablet: 768px - 1024px
- Desktop: > 1024px

---

## 8. Deployment Architecture

### 8.1 Cloud Infrastructure (AWS)

```
┌─────────────────────────────────────────────────────────────┐
│                        Route 53 (DNS)                        │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   CloudFront (CDN)                           │
│  - Static Content Delivery                                  │
│  - SSL/TLS Termination                                      │
│  - DDoS Protection                                          │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Application Load Balancer (ALB)                 │
│  - Health Checks                                            │
│  - SSL Termination                                          │
│  - Path-based Routing                                       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    EKS Cluster (Kubernetes)                  │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Node Group 1 (General Services)         │   │
│  │  - User Service (3 replicas)                        │   │
│  │  - Marketplace Service (3 replicas)                 │   │
│  │  - Payment Service (2 replicas)                     │   │
│  │  - Notification Service (2 replicas)                │   │
│  │  - Digital Services (2 replicas)                    │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Node Group 2 (AI/ML - GPU Instances)         │   │
│  │  - Disease Detection Service (2 replicas)           │   │
│  │  - Price Prediction Service (2 replicas)            │   │
│  │  - TensorFlow Serving (2 replicas)                  │   │
│  └──────────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Node Group 3 (Analytics)                │   │
│  │  - Analytics Service (2 replicas)                   │   │
│  │  - Logging Aggregator                               │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      Data Layer                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │     RDS      │  │  DocumentDB  │  │ ElastiCache  │      │
│  │ (PostgreSQL) │  │  (MongoDB)   │  │   (Redis)    │      │
│  │ Multi-AZ     │  │  Multi-AZ    │  │  Cluster     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │      S3      │  │ OpenSearch   │  │     SQS      │      │
│  │   (Images)   │  │  (Search)    │  │  (Queues)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### 8.2 Kubernetes Deployment Configuration

**Deployment YAML Example (Disease Detection Service):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: disease-detection-service
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: disease-detection
  template:
    metadata:
      labels:
        app: disease-detection
    spec:
      containers:
      - name: disease-detection
        image: smartvillage/disease-detection:v1.2.0
        ports:
        - containerPort: 8000
        resources:
          requests:
            memory: "2Gi"
            cpu: "1000m"
            nvidia.com/gpu: 1
          limits:
            memory: "4Gi"
            cpu: "2000m"
            nvidia.com/gpu: 1
        env:
        - name: MODEL_PATH
          value: "/models/disease_detection_v1"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: url
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: disease-detection-service
  namespace: production
spec:
  selector:
    app: disease-detection
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8000
  type: ClusterIP
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: disease-detection-hpa
  namespace: production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: disease-detection-service
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```


### 8.3 CI/CD Pipeline

```
Developer Push to Git
    │
    ▼
GitHub/GitLab Repository
    │
    ├─► Webhook Trigger
    │
    ▼
Jenkins/GitLab CI
    │
    ├─► 1. Code Checkout
    │
    ├─► 2. Run Tests
    │   ├─► Unit Tests
    │   ├─► Integration Tests
    │   └─► Code Quality (SonarQube)
    │
    ├─► 3. Build Docker Image
    │   └─► Tag: {service}:{version}
    │
    ├─► 4. Push to Container Registry
    │   └─► AWS ECR / Docker Hub
    │
    ├─► 5. Security Scan
    │   └─► Trivy / Snyk
    │
    ├─► 6. Deploy to Staging
    │   ├─► Update Kubernetes Manifests
    │   ├─► Apply to Staging Namespace
    │   └─► Run Smoke Tests
    │
    ├─► 7. Manual Approval (Production)
    │
    └─► 8. Deploy to Production
        ├─► Blue-Green Deployment
        ├─► Health Checks
        └─► Rollback on Failure
```

### 8.4 Monitoring and Observability

**Monitoring Stack:**

```
Application Metrics
    │
    ▼
Prometheus (Metrics Collection)
    │
    ├─► Service Metrics
    ├─► Infrastructure Metrics
    ├─► Custom Business Metrics
    └─► AI Model Metrics
    │
    ▼
Grafana (Visualization)
    │
    ├─► System Dashboards
    ├─► Service Dashboards
    ├─► Business Dashboards
    └─► Alerting Rules
```

**Logging Stack:**

```
Application Logs
    │
    ▼
Fluentd/Fluent Bit (Log Collection)
    │
    ▼
Elasticsearch (Log Storage & Indexing)
    │
    ▼
Kibana (Log Visualization & Analysis)
```

**Tracing:**

```
Application Requests
    │
    ▼
Jaeger/Zipkin (Distributed Tracing)
    │
    ├─► Request Flow Visualization
    ├─► Latency Analysis
    └─► Error Tracking
```

**Key Metrics to Monitor:**

```
Infrastructure:
- CPU Usage
- Memory Usage
- Disk I/O
- Network Traffic
- Pod Health

Application:
- Request Rate
- Response Time (p50, p95, p99)
- Error Rate
- Active Users
- API Endpoint Performance

Business:
- Daily Active Users
- Disease Detections per Day
- Marketplace Transactions
- Revenue
- User Retention

AI Models:
- Inference Latency
- Model Accuracy
- Prediction Confidence
- Model Version Distribution
```


### 8.5 Disaster Recovery

**Backup Strategy:**

```
Database Backups:
- Automated daily snapshots (RDS)
- Point-in-time recovery (35 days)
- Cross-region replication
- Weekly full backups to S3

Object Storage:
- S3 versioning enabled
- Cross-region replication
- Lifecycle policies for archival

Configuration:
- GitOps approach (all configs in Git)
- Kubernetes manifests versioned
- Infrastructure as Code (Terraform)
```

**Recovery Procedures:**

```
RTO (Recovery Time Objective): 1 hour
RPO (Recovery Point Objective): 15 minutes

Disaster Scenarios:
1. Service Failure
   - Auto-restart by Kubernetes
   - Failover to healthy pods
   
2. Database Failure
   - Automatic failover to standby (Multi-AZ)
   - Recovery time: < 5 minutes
   
3. Region Failure
   - Manual failover to secondary region
   - Recovery time: < 1 hour
   - Data loss: < 15 minutes
   
4. Complete System Failure
   - Restore from backups
   - Rebuild infrastructure from IaC
   - Recovery time: < 4 hours
```

---

## 9. Scalability Plan

### 9.1 Horizontal Scaling Strategy

**Auto-Scaling Configuration:**

```yaml
Service Scaling Rules:

User Service:
  min_replicas: 3
  max_replicas: 20
  scale_up_threshold: 70% CPU or 80% Memory
  scale_down_threshold: 30% CPU and 40% Memory
  cooldown_period: 5 minutes

Disease Detection Service:
  min_replicas: 2
  max_replicas: 15
  scale_up_threshold: 75% CPU or 5 requests in queue
  scale_down_threshold: 30% CPU and queue empty
  cooldown_period: 10 minutes

Marketplace Service:
  min_replicas: 3
  max_replicas: 25
  scale_up_threshold: 70% CPU or 80% Memory
  scale_down_threshold: 30% CPU and 40% Memory
  cooldown_period: 5 minutes

Price Prediction Service:
  min_replicas: 2
  max_replicas: 10
  scale_up_threshold: 70% CPU
  scale_down_threshold: 30% CPU
  cooldown_period: 10 minutes
```

**Database Scaling:**

```
PostgreSQL (RDS):
- Read Replicas: 2-5 (auto-scaling)
- Write: Single master with Multi-AZ
- Connection Pooling: PgBouncer
- Sharding Strategy: By user_id for horizontal scaling

MongoDB (DocumentDB):
- Replica Set: 3 nodes minimum
- Auto-scaling storage
- Sharding by user_id or timestamp

Redis (ElastiCache):
- Cluster Mode: Enabled
- Nodes: 3-10 (auto-scaling)
- Replication: Multi-AZ
```

### 9.2 Vertical Scaling Strategy

**Instance Types by Load:**

```
Low Traffic (< 1000 users):
- Application: t3.medium (2 vCPU, 4GB RAM)
- Database: db.t3.medium
- AI Inference: g4dn.xlarge (1 GPU)

Medium Traffic (1000-10000 users):
- Application: t3.large (2 vCPU, 8GB RAM)
- Database: db.r5.large
- AI Inference: g4dn.2xlarge (1 GPU)

High Traffic (10000-100000 users):
- Application: c5.xlarge (4 vCPU, 8GB RAM)
- Database: db.r5.2xlarge
- AI Inference: g4dn.4xlarge (1 GPU)

Very High Traffic (> 100000 users):
- Application: c5.2xlarge (8 vCPU, 16GB RAM)
- Database: db.r5.4xlarge
- AI Inference: g4dn.8xlarge (1 GPU) or p3.2xlarge
```


### 9.3 Caching Strategy

**Multi-Layer Caching:**

```
Layer 1: CDN (CloudFront)
- Static assets (images, CSS, JS)
- TTL: 7 days
- Cache hit ratio target: > 90%

Layer 2: Application Cache (Redis)
- API responses
- User sessions
- Price predictions
- TTL: 5 minutes - 6 hours
- Cache hit ratio target: > 70%

Layer 3: Database Query Cache
- Frequently accessed data
- Read replicas
- Query result caching
- TTL: 1-5 minutes

Layer 4: Browser Cache
- Static resources
- API responses (with ETag)
- TTL: Varies by resource type
```

**Cache Invalidation Strategy:**

```
Strategies:
1. Time-based (TTL)
2. Event-based (on data update)
3. Manual (admin trigger)

Implementation:
- Cache keys with version numbers
- Pub/Sub for cache invalidation events
- Graceful degradation on cache miss
```

### 9.4 Load Balancing Strategy

**Multi-Level Load Balancing:**

```
Level 1: DNS Load Balancing (Route 53)
- Geographic routing
- Latency-based routing
- Health checks

Level 2: Application Load Balancer (ALB)
- Path-based routing
- Host-based routing
- SSL termination
- Health checks

Level 3: Service Mesh (Istio - Optional)
- Intelligent routing
- Circuit breaking
- Retry logic
- Traffic splitting (A/B testing)

Level 4: Kubernetes Service
- Round-robin
- Session affinity (if needed)
- Internal load balancing
```

### 9.5 Database Optimization

**Query Optimization:**

```sql
-- Indexing Strategy
CREATE INDEX CONCURRENTLY idx_users_mobile ON users(mobile_number);
CREATE INDEX CONCURRENTLY idx_detection_user_timestamp 
  ON disease_detections(user_id, detection_timestamp DESC);
CREATE INDEX CONCURRENTLY idx_listing_crop_status 
  ON marketplace_listings(crop_type, status) 
  WHERE status = 'active';

-- Partitioning Strategy (Time-series data)
CREATE TABLE price_history (
  price_id UUID,
  crop_type VARCHAR(100),
  price_date DATE,
  ...
) PARTITION BY RANGE (price_date);

CREATE TABLE price_history_2026_q1 
  PARTITION OF price_history
  FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');

-- Materialized Views for Analytics
CREATE MATERIALIZED VIEW daily_marketplace_stats AS
SELECT 
  DATE(created_at) as date,
  crop_type,
  COUNT(*) as listing_count,
  AVG(price_per_unit) as avg_price
FROM marketplace_listings
WHERE status = 'active'
GROUP BY DATE(created_at), crop_type;

-- Refresh strategy
REFRESH MATERIALIZED VIEW CONCURRENTLY daily_marketplace_stats;
```

**Connection Pooling:**

```python
# PgBouncer Configuration
[databases]
smartvillage = host=rds-endpoint port=5432 dbname=smartvillage

[pgbouncer]
pool_mode = transaction
max_client_conn = 1000
default_pool_size = 25
reserve_pool_size = 5
reserve_pool_timeout = 3
```


### 9.6 AI Model Optimization

**Model Serving Optimization:**

```
Strategies:
1. Model Quantization
   - Convert FP32 to INT8
   - Reduce model size by 75%
   - Minimal accuracy loss (< 1%)

2. Batch Inference
   - Batch size: 8-16 images
   - Reduces inference time per image
   - Better GPU utilization

3. Model Caching
   - Keep models in GPU memory
   - Warm-up on service start
   - Version-based cache keys

4. TensorFlow Serving Optimization
   - Enable batching
   - Configure thread pools
   - Use gRPC for internal communication
```

**TensorFlow Serving Configuration:**

```
model_config_list {
  config {
    name: 'disease_detection'
    base_path: '/models/disease_detection'
    model_platform: 'tensorflow'
    model_version_policy {
      specific {
        versions: 3
        versions: 4
      }
    }
  }
}

batching_parameters {
  max_batch_size { value: 16 }
  batch_timeout_micros { value: 50000 }
  max_enqueued_batches { value: 100 }
  num_batch_threads { value: 4 }
}
```

### 9.7 Content Delivery Optimization

**CDN Configuration:**

```
CloudFront Distribution:
- Origin: S3 bucket (images) + ALB (API)
- Edge Locations: Global
- Compression: Enabled (gzip, brotli)
- HTTP/2: Enabled
- Cache Behaviors:
  - /static/*: Cache everything (7 days)
  - /images/*: Cache with query string (30 days)
  - /api/*: No cache (dynamic content)

Image Optimization:
- Automatic format conversion (WebP, AVIF)
- Responsive images (multiple sizes)
- Lazy loading
- Progressive JPEG
```

### 9.8 Capacity Planning

**Growth Projections:**

```
Year 1:
- Users: 50,000
- Daily Active Users: 10,000
- Disease Detections: 5,000/day
- Marketplace Transactions: 500/day
- Storage: 2TB

Year 2:
- Users: 200,000
- Daily Active Users: 50,000
- Disease Detections: 25,000/day
- Marketplace Transactions: 2,500/day
- Storage: 10TB

Year 3:
- Users: 500,000
- Daily Active Users: 150,000
- Disease Detections: 75,000/day
- Marketplace Transactions: 7,500/day
- Storage: 30TB
```

**Resource Requirements by Year:**

```
Year 1:
- Application Servers: 10-15 instances
- Database: db.r5.large (read replicas: 2)
- AI Inference: 3-5 GPU instances
- Storage: 5TB (with growth buffer)
- Bandwidth: 10TB/month
- Estimated Cost: $5,000-7,000/month

Year 2:
- Application Servers: 30-50 instances
- Database: db.r5.xlarge (read replicas: 4)
- AI Inference: 10-15 GPU instances
- Storage: 15TB
- Bandwidth: 50TB/month
- Estimated Cost: $15,000-20,000/month

Year 3:
- Application Servers: 80-120 instances
- Database: db.r5.2xlarge (read replicas: 6)
- AI Inference: 25-35 GPU instances
- Storage: 40TB
- Bandwidth: 150TB/month
- Estimated Cost: $40,000-50,000/month
```

### 9.9 Performance Optimization Checklist

```
✓ Database Optimization
  - Proper indexing
  - Query optimization
  - Connection pooling
  - Read replicas
  - Partitioning

✓ Caching Strategy
  - Multi-layer caching
  - Cache invalidation
  - CDN integration

✓ API Optimization
  - Response compression
  - Pagination
  - Field filtering
  - Rate limiting

✓ Frontend Optimization
  - Code splitting
  - Lazy loading
  - Image optimization
  - Bundle size reduction

✓ Infrastructure
  - Auto-scaling
  - Load balancing
  - Health checks
  - Monitoring

✓ AI/ML Optimization
  - Model quantization
  - Batch inference
  - Model caching
  - GPU optimization
```

---

## 10. Security Architecture

### 10.1 Security Layers

```
Layer 1: Network Security
- VPC with private subnets
- Security groups
- Network ACLs
- WAF (Web Application Firewall)
- DDoS protection (AWS Shield)

Layer 2: Application Security
- API Gateway authentication
- JWT token validation
- Rate limiting
- Input validation
- SQL injection prevention
- XSS protection

Layer 3: Data Security
- Encryption at rest (AES-256)
- Encryption in transit (TLS 1.3)
- Database encryption
- Secrets management (AWS Secrets Manager)
- Key rotation

Layer 4: Access Control
- RBAC (Role-Based Access Control)
- Principle of least privilege
- MFA for admin access
- Audit logging
```


### 10.2 Authentication & Authorization Flow

```
Request Flow:
1. Client sends request with JWT token
2. API Gateway validates token signature
3. Extract user_id and roles from token
4. Check rate limits (Redis)
5. Route to appropriate service
6. Service validates permissions
7. Execute business logic
8. Return response

Token Structure:
{
  "user_id": "uuid",
  "user_type": "farmer",
  "roles": ["user", "verified"],
  "iat": 1645012800,
  "exp": 1645013700
}
```

### 10.3 Data Privacy Compliance

```
GDPR Compliance:
- User consent management
- Right to access data
- Right to deletion
- Data portability
- Privacy by design

Data Retention:
- User data: Until account deletion
- Transaction data: 7 years (legal requirement)
- Logs: 90 days
- Backups: 35 days

PII Handling:
- Encryption at rest and in transit
- Access logging
- Data minimization
- Anonymization for analytics
```

---

## 11. Testing Strategy

### 11.1 Testing Pyramid

```
                    ▲
                   / \
                  /   \
                 /  E2E \
                /  Tests \
               /-----------\
              /             \
             / Integration   \
            /     Tests       \
           /-------------------\
          /                     \
         /      Unit Tests       \
        /_________________________\

Unit Tests: 70%
Integration Tests: 20%
E2E Tests: 10%
```

### 11.2 Test Coverage Requirements

```
Component          | Coverage Target
-------------------|----------------
Backend Services   | 80%
AI Models         | 90% (accuracy)
API Endpoints     | 85%
Frontend          | 70%
Critical Paths    | 95%
```

### 11.3 Testing Tools

```
Unit Testing:
- Python: pytest, unittest
- Node.js: Jest, Mocha
- React Native: Jest, React Testing Library

Integration Testing:
- Postman/Newman
- pytest with fixtures
- Testcontainers

E2E Testing:
- Selenium
- Appium (mobile)
- Cypress (web)

Performance Testing:
- JMeter
- Locust
- k6

AI Model Testing:
- TensorFlow Model Analysis
- Custom validation scripts
- A/B testing framework
```

---

## 12. Appendix

### 12.1 Technology Alternatives

| Component | Primary Choice | Alternative 1 | Alternative 2 |
|-----------|---------------|---------------|---------------|
| Backend | Python FastAPI | Node.js Express | Go Gin |
| Database | PostgreSQL | MySQL | CockroachDB |
| Cache | Redis | Memcached | Hazelcast |
| Message Queue | RabbitMQ | Apache Kafka | AWS SQS |
| Container Orchestration | Kubernetes | Docker Swarm | AWS ECS |
| AI Framework | TensorFlow | PyTorch | ONNX Runtime |
| Mobile | React Native | Flutter | Native (Swift/Kotlin) |
| Cloud Provider | AWS | Google Cloud | Azure |

### 12.2 Glossary

- **CNN**: Convolutional Neural Network
- **LSTM**: Long Short-Term Memory
- **API**: Application Programming Interface
- **JWT**: JSON Web Token
- **RBAC**: Role-Based Access Control
- **CDN**: Content Delivery Network
- **RTO**: Recovery Time Objective
- **RPO**: Recovery Point Objective
- **HPA**: Horizontal Pod Autoscaler
- **ALB**: Application Load Balancer
- **VPC**: Virtual Private Cloud
- **WAF**: Web Application Firewall

### 12.3 References

- TensorFlow Documentation: https://www.tensorflow.org/
- Kubernetes Documentation: https://kubernetes.io/docs/
- AWS Best Practices: https://aws.amazon.com/architecture/
- React Native Documentation: https://reactnative.dev/
- PostgreSQL Documentation: https://www.postgresql.org/docs/

---

## Document Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-15 | Development Team | Initial design document |

---

*End of Technical Design Document*
