# AI Inference Infrastructure – ONNX → TensorRT → Triton

This repo is my **AI Infrastructure lab** – focused on running AI models efficiently in production rather than training them.

I’m working on the full path:

> Model → ONNX → TensorRT → Docker → Triton → Monitoring → Security → CI/CD

My background is in **DevOps, automation, Kubernetes and telecom performance testing**, and I’m using those skills to move into **GPU + AI inference infrastructure**.

---

## 🔹 What I’m focusing on (AI Infra, not model training)

I’m not trying to become a data scientist.  
I’m focused on the **infrastructure that makes AI models run fast, safe, and reliable**.

### 1. Model Standardization (ONNX)
- AI models come from different frameworks (PyTorch, TensorFlow, etc.)
- I convert them to **ONNX** so they are:
  - Portable
  - Framework-agnostic
  - Ready for multiple runtimes (TensorRT, ONNX Runtime, Triton)

### 2. Model Optimization (TensorRT)
- Raw models are often too slow for real-time use.
- I use **NVIDIA TensorRT** to:
  - Optimize graph execution
  - Use FP16 / INT8 where appropriate
  - Reduce latency and increase throughput
- Tools: `trtexec`, TensorRT Python APIs, basic benchmarking.

### 3. Model Serving (Triton Inference Server)
- Instead of writing ad-hoc Flask/REST servers, I use **NVIDIA Triton** as the model server.
- Triton handles:
  - Model loading
  - Dynamic batching
  - Versioning
  - Multi-model, multi-framework serving
- Think of it as **Nginx but for AI models**.

### 4. AI CI/CD (GitHub Actions)
- I don’t want manual rebuilds every time the model or config changes.
- I use **GitHub Actions** to automate:
  - ONNX conversion scripts
  - TensorRT engine build steps
  - Docker image build for Triton
  - Push to a container registry
- Goal: “git push” → updated, optimized model is ready to deploy.

### 5. Monitoring GPU & Inference (Prometheus + Grafana)
- AI in production is useless if we don’t know:
  - GPU utilization
  - GPU memory pressure
  - Inference latency
  - Throughput (requests/sec)
- I rely on **Prometheus** + **Grafana** to monitor:
  - GPU metrics (via NVIDIA exporters)
  - Triton model metrics (latency, errors, QPS)

### 6. Security & License Compliance
- AI stacks depend on many open-source libraries and CUDA-based containers.
- I use tools like:
  - **Trivy** (container vulnerability scanning)
  - **Syft/Grype** (SBOM & license reporting)
- Goal: make sure inference images are:
  - Free from critical CVEs
  - Compliant with license policies

---

## 🔹 Example Project: ResNet50 GPU Inference Pipeline

I’m building a small but realistic pipeline around **ResNet50**:

1. **ONNX model**  
   Download `resnet50-v2-7.onnx` and treat it as the base model.

2. **TensorRT Optimization**  
   - Use `trtexec` to generate a `.plan` engine file  
   - Benchmark latency & throughput

3. **Triton Model Repository**
   - Create a `model_repo/resnet50/` folder:
     - `1/model.plan`
     - `config.pbtxt` describing inputs/outputs and batching
   - Run Triton server pointing to this repo

4. **Dockerized Triton Server**
   - Use NVIDIA’s Triton base image
   - COPY the model repo into the container
   - Expose HTTP/gRPC endpoints on `8000/8001/8002`

5. **CI/CD with GitHub Actions**
   - Workflow idea:
     - On push:
       - Validate ONNX model
       - (Optionally) run conversion/optimization step
       - Build Docker image
       - Push to registry (e.g., GHCR / Docker Hub)

6. **Monitoring**
   - Attach GPU metrics exporter (DCGM or similar)
   - Scrape metrics with Prometheus
   - Visualize GPU + inference metrics in Grafana

7. **Security**
   - Scan the Triton image with Trivy
   - Generate SBOM with Syft
   - Capture reports as CI artifacts

---

## 🔹 Tech Stack

- **AI Runtime & Optimization**
  - ONNX
  - NVIDIA TensorRT
  - NVIDIA Triton Inference Server

- **Infrastructure**
  - Docker
  - (Optional later) Kubernetes with GPU nodes

- **CI/CD**
  - GitHub Actions

- **Monitoring**
  - Prometheus
  - Grafana
  - NVIDIA DCGM exporter (for GPU metrics)

- **Security & Compliance**
  - Trivy (image vulnerability scanning)
  - Syft (SBOM generation)

---

## 🔹 How I explain this in interviews

> “I work on the infrastructure that makes AI models run efficiently in production. I standardize models into ONNX, optimize them with TensorRT to reduce inference latency, and serve them through Triton Inference Server running on GPUs. I automate the build and deployment using GitHub Actions, and I integrate Prometheus/Grafana for GPU and inference monitoring, plus image and license scanning for security and compliance.”

This repo is where I’m organizing that learning into a concrete, end-to-end project.

---

## 🔹 Status

- [ ] Initial project setup
- [ ] Basic ONNX → TensorRT flow scripted
- [ ] Triton model repository structure created
- [ ] Dockerfile for Triton + model repo
- [ ] GitHub Actions workflow (build + push)
- [ ] Monitoring stack added
- [ ] Security scanning integrated

This is **work in progress**, but the direction and architecture are clear.
