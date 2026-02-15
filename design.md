
1. **Frontend (Mobile Web App)**  
   - UI for farmers and buyers  
   - Image upload & chat interface  
   - Localized languages, offline support

2. **Backend APIs**  
   - Handles requests from app  
   - Routes data to AI model & DB  
   - Push notifications, matchmaking

3. **AI Engine**  
   - **Disease Detection**: CNN image classification  
   - **Price Prediction**: LSTM time-series forecasting  
   - Decision support & recommendations

4. **Database & Marketplace**  
   - Store users, listings, transaction history  
   - Match farmers to buyers

---

## 🧠 Machine Learning Models

### 1. Crop Disease Detection (CNN)

**Input:** Crop image  
**Model:** Convolutional Neural Network (CNN)  
**Output:** Disease class + severity score  
**Dataset:** PlantVillage / Field images

**Steps:**
- Image preprocessing  
- Augmentation  
- Train/validate/test split  
- Classifier outputs disease label

---

### 2. Price Prediction (LSTM)

**Input:** Market history data (prices, seasons)  
**Model:** Long Short-Term Memory (LSTM) network  
**Output:** Predicted future prices

**Steps:**
- Time-series normalization  
- Train on historical market data  
- Forecast next 7–14 days

---

## 🔄 User Flow

### Farmer

1. Login/Signup  
2. Upload crop image  
3. Receive diagnosis + treatment  
4. View price forecast  
5. List crop  
6. Connect with buyers

### Buyer

1. Login  
2. Browse crop listings  
3. Chat & negotiate  
4. Place order

---

## 📱 UI/UX Wireframes

### Farmer App
