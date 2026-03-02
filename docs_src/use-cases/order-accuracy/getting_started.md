# Getting Started

## 📋 Prerequisites

- Ubuntu 24.04 or newer (Linux recommended), Desktop edition (or Server edition with GUI installed)
- [Docker](https://docs.docker.com/engine/install/) 24.0+
- [Docker Compose](https://docs.docker.com/compose/install/) V2+
- [Make](https://www.gnu.org/software/make/) (`sudo apt install make`)
- Intel hardware (CPU, iGPU, dGPU)
- Intel drivers:
    - [Intel GPU drivers](https://dgpu-docs.intel.com/driver/client/overview.html)
- Sufficient disk space for models, videos, and results (50GB minimum)

!!! note
    First-time setup downloads AI models (~7GB) and Docker images - this may take 30-60 minutes depending on your internet connection.

## Choose Your Application

### 🍽️ Dine-In Order Accuracy
**Purpose**: Validate food plates at serving stations before delivery to tables  
**Use When**: You need image-based validation for restaurant table service  
**Input**: Static images of food trays/plates  
**Features**: Gradio web interface, REST API for POS integration

### 🥡 Take-Away Order Accuracy
**Purpose**: Real-time order validation for drive-through and counter service  
**Use When**: You need continuous video stream validation at multiple stations  
**Input**: RTSP video streams  
**Features**: Multi-station parallel processing, VLM request batching

## Quick Start Reference

### 🍽️ Dine-In Quick Commands

| Configuration | Command | Description |
|---------------|---------|-------------|
| **Start Services** | `make up` | Start all dine-in services |
| **Build Locally** | `make build REGISTRY=false` | Build images from source |
| **View Logs** | `make logs` | View service logs |
| **Stop Services** | `make down` | Stop all containers |

### 🥡 Take-Away Quick Commands

| Configuration | Command | Description |
|---------------|---------|-------------|
| **Single Mode** | `make up` | Start in single worker mode (development) |
| **Parallel Mode** | `make up-parallel WORKERS=4` | Start with 4 parallel workers (production) |
| **Build Locally** | `make build REGISTRY=false` | Build images from source |
| **View Logs** | `make logs` | View service logs |

!!! tip
    **Single Mode** is best for development and testing. **Parallel Mode** is recommended for production with multiple camera stations.

## Step-by-Step Instructions

### Option 1: Dine-In Order Accuracy

1. **Clone the Repository**
    ```bash
    git clone -b <release-or-tag> --single-branch https://github.com/intel-retail/order-accuracy
    ```
    >Replace `<release-or-tag>` with the version you want to clone (for example, **v2026.0**).
    ```bash
    git clone -b v2026.0 --single-branch https://github.com/intel-retail/order-accuracy
    cd order-accuracy/dine-in
    ```

2. **Setup OVMS Models (First Time Only)**
    ```bash
    cd ../ovms-service
    ./setup_models.sh
    cd ../dine-in
    ```
    This downloads and converts the Qwen2.5-VL-7B model (~7GB). This only needs to be done once.

3. **Prepare Test Data**
    - Add your food tray/plate images to the `images/` folder
    - Update `configs/orders.json` with test orders
    - Update `configs/inventory.json` with your menu items

4. **Build and Start Services**
    ```bash
    # Using pre-built images (recommended for first run)
    make build
    make up
    
    # OR build locally from source
    make build REGISTRY=false
    make up
    ```

5. **Access the Application**
    - **Gradio UI**: http://localhost:7861
    - **REST API Docs**: http://localhost:8083/docs

---

### Option 2: Take-Away Order Accuracy

1. **Clone the Repository**
    ```bash
    git clone -b <release-or-tag> --single-branch https://github.com/intel-retail/order-accuracy
    ```
    >Replace `<release-or-tag>` with the version you want to clone (for example, **v2026.0**).
    ```bash
    git clone -b v2026.0 --single-branch https://github.com/intel-retail/order-accuracy
    cd order-accuracy/take-away
    ```

2. **Setup OVMS Models (First Time Only)**
    ```bash
    cd ../ovms-service
    ./setup_models.sh
    cd ../take-away
    ```
    This downloads the VLM and EasyOCR models. This only needs to be done once.

3. **Initialize Environment**
    ```bash
    make init-env
    ```

4. **Build and Start Services**
    ```bash
    # Single worker mode (development/testing)
    make build
    make up
    
    # OR Parallel mode (production)
    make build
    make up-parallel WORKERS=4
    ```

5. **Access the Application**
    - **Gradio UI**: http://localhost:7860
    - **MinIO Console**: http://localhost:9001 (minioadmin/minioadmin)

## What You'll See When Working

### 🍽️ Dine-In Results
- **Visual**: Gradio UI displays detected items with confidence scores
- **Validation**: Order match/mismatch status with detailed comparison
- **Logs**: Detection results and semantic matching scores

### 🥡 Take-Away Results
- **Visual**: Real-time frame processing with item detection
- **Validation**: Continuous order validation against POS data
- **Storage**: Frames and results stored in MinIO buckets

### Expected Performance
- **Startup Time**: 2-5 minutes (first run includes model loading)
- **Processing**: Sub-15-second validation latency (Dine-In), real-time stream processing (Take-Away)
- **Results**: JSON files appear in `results/` directory

## Verify Results

After starting the application, you can verify it's working:

**Dine-In:**
```bash
# Check running containers
docker ps

# View application logs
make logs

# Test the API
curl http://localhost:8083/health
```

**Take-Away:**
```bash
# Check running containers
docker ps

# View application logs
make logs

# Check MinIO storage
# Visit http://localhost:9001
```

## Stop the Services

```bash
# Stop all services
make down

# Stop and remove volumes (clean restart)
make down-volumes
```

## [Proceed to Advanced Settings](advanced.md)
