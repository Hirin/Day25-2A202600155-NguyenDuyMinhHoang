# GPU FinOps & Cost Optimization - Hands-on Lab

## Architecture

```
LOCAL (Docker Compose)                  REMOTE (Kaggle/Colab)
┌─────────────────────────┐            ┌──────────────────────┐
│ gpu-node-manager :8011  │            │                      │
│ billing-api      :8012  │◄──tunnel──►│  Jupyter Notebook    │
│ spot-manager     :8013  │            │  (GPU workload +     │
│ autoscaler       :8014  │            │   visualization)     │
│ cost-tracker     :8015  │            │                      │
│ gateway          :8010  │            └──────────────────────┘
└─────────────────────────┘
```

## Quick Start

Chọn guide theo hệ điều hành:

- **macOS** → [`QUICKSTART-MAC.md`](QUICKSTART-MAC.md)
- **Windows (WSL)** → [`QUICKSTART-WINDOWS.md`](QUICKSTART-WINDOWS.md)
- **Linux** → [`QUICKSTART-LINUX.md`](QUICKSTART-LINUX.md)

### Prerequisites (Yêu cầu trước khi bắt đầu)

| Thứ cần chuẩn bị | Bắt buộc? | Ghi chú |
|-------------------|-----------|---------|
| **Docker** (Desktop trên Mac/Win, Engine trên Linux) | **Bắt buộc** | Cài từ https://docs.docker.com/get-docker/ |
| **Tài khoản Kaggle** | **Bắt buộc** | Miễn phí tại https://www.kaggle.com. Dùng để upload & chạy notebook với GPU. |
| `cloudflared` hoặc `ngrok` | Khuyến nghị | `cloudflared` miễn phí, không cần đăng ký. Chi tiết trong từng OS guide ở trên. |
| Python / packages local | **Không cần** | Services chạy trong Docker. Notebook tự cài `requests`, `pandas`, `matplotlib`, `plotly`, `torch`, `torchvision`, `pynvml` trên Kaggle/Colab. |

### Step 1: Start Docker Compose (local)

```bash
cd gpu-finops-lab
docker compose up --build -d
```

Verify all services are running:
```bash
curl http://localhost:8010/
```

### Step 2: Expose via tunnel (free options)

**Option A: ngrok (free tier)**
```bash
# Install: https://ngrok.com/download
ngrok http 8010
# Copy the https://xxxx.ngrok-free.app URL
```

**Option B: cloudflared (free, no account needed)**
```bash
# Install: brew install cloudflare/cloudflare/cloudflared
cloudflared tunnel --url http://localhost:8010
# Copy the https://xxxx.trycloudflare.com URL
```

**Option C: localhost.run (zero install)**
```bash
ssh -R 80:localhost:8010 nokey@localhost.run
# Copy the generated URL
```

### Step 3: Open notebook in Kaggle/Colab

1. Upload `notebook/gpu_finops_lab.ipynb` to Kaggle or Colab
2. Replace `GATEWAY_URL` in Cell 2 with your tunnel URL
3. Run all cells

## Services

| Service | Port | Description |
|---------|------|-------------|
| Gateway | 8010 | Single entry point for notebook |
| GPU Node Manager | 8011 | Mock multi-node GPU cluster |
| Billing API | 8012 | Cloud billing simulation |
| Spot Manager | 8013 | Spot instance bidding & preemption |
| Autoscaler | 8014 | KEDA-like GPU autoscaling |
| Cost Tracker | 8015 | OpenCost-like cost allocation |

## Lab Sections

1. **GPU Cluster Monitoring** - View nodes, utilization, memory, power
2. **Workload Submission & Billing** - Submit jobs, track costs
3. **Spot Instance Management** - Bid, preemption, savings analysis
4. **Autoscaling** - Policy config, threshold-based scaling
5. **Cost Analysis** - Waste detection, recommendations
6. **Visualization** - Charts for cost breakdown & trends
7. **Full Workflow** - End-to-end FinOps optimization cycle

## API Endpoints (via Gateway)

### Cluster
- `GET /cluster/nodes` - List all GPU nodes
- `GET /cluster/metrics` - Aggregated metrics
- `POST /cluster/workloads/submit` - Submit workload
- `POST /cluster/scale-up` - Add nodes

### Billing
- `GET /billing/pricing` - GPU pricing
- `POST /billing/record` - Record billing event
- `GET /billing/summary` - Cost summary
- `GET /billing/forecast` - Cost forecast

### Spot
- `GET /spot/pricing` - Current spot prices
- `POST /spot/request` - Request spot instance
- `POST /spot/simulate-preemption` - Trigger preemption
- `GET /spot/savings-report` - Savings analysis

### Autoscaler
- `GET /autoscaler/policy` - Current policy
- `POST /autoscaler/policy` - Update policy
- `POST /autoscaler/evaluate` - Trigger evaluation

### Cost Tracker
- `POST /cost/snapshot` - Take cost snapshot
- `GET /cost/waste-report` - Waste analysis
- `POST /cost/recommendations` - Optimization tips
- `GET /cost/dashboard` - Unified dashboard
