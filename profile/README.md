#  RealCatcha: AI 기반 SaaS형 캡차 서비스

카카오 클라우드 오브젝트 스토리지, AI 모델(AutoEncoder, YOLO, CRNN), MongoDB, MySQL, Redis를 결합한 고성능 캡차 솔루션입니다.  
모노레포 구조로 프론트엔드, 백엔드, ML 서비스 및 인프라 설정을 통합 관리합니다.

##  목차

- [Repository Structure](#-repository-structure)
- [기술 스택 상세](#-기술-스택-상세)
- [빠른 시작 (로컬 개발)](#-빠른-시작)
- [주요 기능](#-주요-기능)
- [보안 기능](#-보안-기능)
- [클라우드 아키텍처](#-클라우드-아키텍처-및-인프라-설계)
- [모니터링 및 로깅](#-모니터링-및-로깅)
- [테스트](#-테스트)
- [배포](#-배포)

---

## 📁 Repository Structure

###  프론트엔드 서비스
- **`website/`**: 프로젝트 랜딩 페이지 및 통합 대시보드 (React + Vite)
  - 메인 홈페이지, 제품 소개, 회사 정보, 문서
  - 로그인/회원가입 (Google OAuth 지원)
  - 네이티브 통합 대시보드 (사용자/관리자)
  - 결제 시스템 (토스페이먼츠 연동)
  - 기술 스택: React 18, React Router, Material-UI, Chart.js, Axios

- **`dashboard/`**: API 키 및 통계 관리 대시보드 (React + TypeScript)
  - API 키 생성 및 관리
  - 실시간 통계 및 분석 (Recharts)
  - 사용량 모니터링 및 요금 관리
  - 기술 스택: React 18, TypeScript, Material-UI, Recharts

- **`captcha-widget/`**: 웹사이트 임베딩용 캡차 위젯 (React + TypeScript)
  - CDN 배포 가능한 독립 실행형 위젯
  - 다양한 캡차 유형 지원 (손글씨, 추상 이미지, 이미지 그리드)
  - 행동 데이터 수집 및 전송
  - 기술 스택: React 18, TypeScript, Webpack

###  백엔드 서비스
- **`gateway-api/`**: 메인 서비스 엔트리 포인트 (FastAPI + Python)
  - 사용자 인증 및 권한 관리 (JWT, Google OAuth)
  - API 키 발급 및 검증
  - 결제 및 구독 관리
  - 대시보드 API (사용자/관리자)
  - 사용량 추적 및 통계
  - 기술 스택: FastAPI, PyMySQL, Python-JOSE, Passlib, HTTPx

- **`captcha-api/`**: 캡차 로직 및 검증 (FastAPI + Python)
  - 다양한 캡차 유형 생성 및 검증
    - Handwriting (손글씨 OCR)
    - Abstract (추상 이미지 선택)
    - ImageGrid (이미지 그리드 선택)
  - 세션 관리 (Redis + In-Memory)
  - 행동 데이터 수집 및 저장 (MongoDB)
  - 카카오 오브젝트 스토리지 연동 (Presigned URL)
  - IP 관리 및 제한
  - 기술 스택: FastAPI, Redis, PyMongo, Boto3, Pillow, PyMySQL

- **`ml-service/`**: AI 기반 봇 탐지 및 이미지 처리 (FastAPI + Python)
  - **AutoEncoder 기반 봇 탐지**: 사용자 행동 패턴 분석
  - **CRNN OCR**: 손글씨 텍스트 인식 (PyTorch)
  - **YOLO v8**: 객체 탐지 및 세그멘테이션
  - **Keras 이미지 분류**: 추상 이미지 감정 분석
  - 실시간 신뢰도 스코어링 (0-100)
  - 기술 스택: PyTorch, TensorFlow/Keras, Ultralytics YOLO, scikit-learn

###  인프라 및 배포
- **`deploy-manifests/`**: Kubernetes 배포 매니페스트
  - Argo CD 기반 GitOps
  - 서비스별 Deployment, Service, Ingress 설정
  - Autoscaling (HPA) 설정
  - 환경별 설정 (development, production)

---

## 🏗️ 기술 스택 상세

### 프론트엔드
| 서비스 | 언어/프레임워크 | 주요 라이브러리 |
|--------|-----------------|----------------|
| website | React 18 + Vite | React Router, Material-UI, Chart.js, Axios, 토스페이먼츠 |
| dashboard | React 18 + TypeScript | Material-UI, Recharts, Axios, React Router |
| captcha-widget | React 18 + TypeScript | Webpack, Buffer Polyfills |

### 백엔드
| 서비스 | 언어/프레임워크 | 데이터베이스 | 주요 기능 |
|--------|-----------------|-------------|----------|
| gateway-api | FastAPI + Python | MySQL | 인증, API 키, 결제, 대시보드 |
| captcha-api | FastAPI + Python | Redis, MongoDB, MySQL | 캡차 생성/검증, 세션 관리 |
| ml-service | FastAPI + Python | - | 봇 탐지, OCR, 객체 탐지, 이미지 분류 |

### AI/ML 모델
| 모델 | 용도 | 프레임워크 | 정확도/성능 |
|------|-----|-----------|----------|
| AutoEncoder | 봇 탐지 및 행동 분석 | PyTorch | 신뢰도 스코어 0-100 |
| CRNN | 손글씨 OCR | PyTorch + CTC Decoder | 제약 디코딩 지원 |
| YOLO v8 | 객체 탐지 | Ultralytics | Conf 0.25, IoU 0.45 |
| Keras CNN | 추상 이미지 분류 | TensorFlow/Keras | 224x224 입력 |

### 인프라
- **컨테이너**: Docker, Kubernetes
- **CI/CD**: GitHub Actions, Argo CD
- **스토리지**: 카카오 오브젝트 스토리지 (S3 호환)
- **캐시/세션**: Redis
- **문서 DB**: MongoDB
- **관계형 DB**: MySQL (PyMySQL)

---

##  빠른 시작

### 사전 요구사항
- Docker & Docker Compose
- Node.js 18+ (프론트엔드)
- Python 3.9+ (백엔드)
- MySQL 8.0+
- Redis 5.0+
- MongoDB 4.4+

### 로컬 개발 환경 설정

#### 1. 프론트엔드 서비스 실행

```powershell
# Website (랜딩 페이지 + 통합 대시보드)
cd website
npm install
npm run dev  # http://localhost:5173

# Dashboard (독립 대시보드)
cd dashboard
npm install
npm start  # http://localhost:3000

# Captcha Widget (위젯)
cd captcha-widget
npm install
npm run build:cdn  # CDN 빌드
```

#### 2. 백엔드 서비스 실행

```powershell
# Gateway API (메인 엔트리포인트)
cd gateway-api
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn main:app --reload --port 8000

# Captcha API (캡차 로직)
cd captcha-api
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn main:app --reload --port 8001

# ML Service (AI 모델)
cd ml-service
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn src.api.app:app --reload --port 8002
```

#### 3. 환경 변수 설정

각 서비스의 `.env` 파일을 설정합니다:

**gateway-api/.env**
```bash
DATABASE_URL=mysql+pymysql://user:password@localhost:3306/captcha_db
SECRET_KEY=your-secret-key
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
```

**captcha-api/.env**
```bash
REDIS_HOST=localhost
REDIS_PORT=6379
MONGO_URI=mongodb://localhost:27017
OBJECT_STORAGE_ENDPOINT=https://objectstorage.kr-central-2.kakaoi.io
OBJECT_STORAGE_BUCKET=your-bucket
OBJECT_STORAGE_ACCESS_KEY=your-access-key
OBJECT_STORAGE_SECRET_KEY=your-secret-key
```

**ml-service/.env**
```bash
OCR_MODEL_PATH=/path/to/crnn_model.pth
YOLO_IMG_SIZE=768
YOLO_CONF=0.25
```

---

##  주요 기능

### 캡차 유형
1. **Handwriting (손글씨 OCR)**
   - CRNN 기반 손글씨 인식
   - 제약 디코딩 (Constrained Decoding)
   - 동적 단어 사전 지원

2. **Abstract (추상 이미지 선택)**
   - Keras CNN 기반 감정 분류
   - 5장 이미지 중 정답 선택
   - 카카오 오브젝트 스토리지 연동

3. **ImageGrid (이미지 그리드 선택)**
   - YOLO v8 기반 객체 탐지
   - 3x3 그리드에서 객체 포함 이미지 선택
   - 동적 난이도 조절

### 봇 탐지 시스템
- **AutoEncoder 기반 행동 분석**
  - 마우스 움직임, 클릭 패턴, 타이밍 분석
  - 실시간 재구성 오차 계산
  - 신뢰도 스코어 (0-100) 반환
- **적응형 난이도 조절**
  - 낮은 신뢰도 → 어려운 캡차
  - 높은 신뢰도 → 간단한 캡차

### 관리 기능
- **API 키 관리**: 생성, 조회, 비활성화
- **사용량 추적**: 실시간 통계 및 차트
- **결제 시스템**: 토스페이먼츠 연동
- **사용자 관리**: 권한 관리 (일반/관리자)

---

##  보안 기능

- JWT 기반 인증
- API 키 기반 요청 검증
- Rate Limiting (요청 제한)
- IP 블랙리스트/화이트리스트
- HTTPS 통신
- CORS 정책
- Presigned URL (시간 제한 이미지 액세스)

---

## 🏗️ 클라우드 아키텍처 및 인프라 설계

이 섹션은 카카오클라우드를 기반으로 구축된 SaaS형 캡차 서비스의 클라우드 아키텍처를 상세히 설명합니다. 서비스의 초기 트래픽 예측부터 각 컴포넌트의 역할, 보안, CI/CD 전략까지 포괄적으로 다룹니다.

### 1. 예상 트래픽 규모

새로운 캡차 서비스의 초기 트래픽은 다음과 같이 가정합니다. 실제 서비스 런칭 후에는 지속적인 모니터링을 통해 예상치를 검증하고 아키텍처를 유연하게 조정해야 합니다. 특히 AI/ML 연산이 필요한 챌린지 발생률에 따라 GPU 리소스 요구량이 크게 달라질 수 있습니다.

* **초기 (런칭 ~ 3개월):**
    * 일반 API 요청 (대시보드, 홈페이지): 초당 10~50회
    * 캡차 요청 (JS 파일 로드, 챌린지 요청): 초당 100~500회
    * 일일 총 캡차 요청: 100만 ~ 500만 건
* **성장기 (3개월 이후 ~ 1년):**
    * 일반 API 요청: 초당 50~200회
    * 캡차 요청: 초당 500~2,000회 (피크 시 3,000~5,000회 이상)
    * 일일 총 캡차 요청: 500만 ~ 2,000만 건 이상

### 2. 클라우드 서비스 제공업체

모든 서비스는 **카카오클라우드 한국 리전**에 배포됩니다.

### 3. 인스턴스 분리 전략 및 컴포넌트 개요

Frontend와 Backend를 분리하고, 특히 캡차 서비스 백엔드를 다른 백엔드와 분리하여 확장성과 안정성을 확보합니다. **Kubernetes**를 활용하여 각 서비스를 마이크로서비스 형태로 관리합니다.

#### 서비스 구성 개요:

1.  **메인 홈페이지:**
    * **프론트엔드 (React):** 정적 파일 호스팅 (Object Storage + CDN)
    * **백엔드:** (필요시) 간략한 정보 제공 API (Kubernetes 파드)
2.  **대시보드:**
    * **프론트엔드 (React):** 정적 파일 호스팅 (Object Storage + CDN)
    * **백엔드 (FastAPI):** API 키 발급/관리, 통계, 설정 등 (Kubernetes 파드)
3.  **캡차 서비스:**
    * **프론트엔드 (JS 파일):** 웹사이트에 임베드되는 JS 파일 (Object Storage + CDN)
    * **백엔드 (FastAPI):** 캡차 챌린지 생성, 유효성 검증, 봇/DDoS 방어 로직 (Kubernetes 파드)
    * **AI/ML 워크로드:** ImageNet, YOLO를 이용한 이미지 세그멘테이션 (Kubernetes GPU 노드 파드)
4.  **공통 인프라:**
    * **Kubernetes 클러스터:** 카카오클라우드 Kubernetes Service (KCKMS)
    * **데이터베이스:** 카카오클라우드 Managed MySQL 8.0
    * **스토리지:** 카카오클라우드 Object Storage, Persistent Volume (PVC)
    * **네트워킹:** 카카오클라우드 VPC, Load Balancer, Public IP
    * **보안:** Web Application Firewall (WAF), DDoS Protection
    * **모니터링/로깅:** 카카오클라우드 Monitoring, Logging Service

### 4. 상세 컴포넌트 및 서비스 설명

#### 4.1. 컴퓨팅: 카카오클라우드 Kubernetes Service (KCKMS)

모든 백엔드 서비스는 KCKMS 상에서 컨테이너화되어 배포됩니다.

* **KCKMS 클러스터:**
    * **일반 노드 풀 (Node Pool - General):** CPU 중심의 인스턴스 (예: `c2.xlarge`, `c2.2xlarge` 등)를 사용하여 메인 홈페이지 백엔드, 대시보드 백엔드, 캡차 백엔드(AI 연산 제외) 파드를 호스팅합니다. **Horizontal Pod AutoScaler (HPA)**와 **Cluster AutoScaler (CA)**를 통해 자동으로 스케일 아웃/인 되어 트래픽 변화에 대응합니다.
    * **GPU 노드 풀 (Node Pool - GPU):**
        * **`gn1i.xlarge` 5개**를 별도의 노드 풀로 구성하여 캡차 AI/ML 연산 파드(YOLO/ImageNet 추론)를 전담합니다.
        * **리소스 제어 전략:**
            * **예약 기반 오토 스케일링 (Scheduled-based auto scaling):** 카카오클라우드 KCKMS의 예약 기반 오토 스케일링 기능을 활용하여 평일 (월-금) 지정된 10시간 동안 GPU 노드 풀을 5개로 유지하고, 나머지 시간 및 주말/공휴일에는 노드 수를 최소(예: 0개 또는 1개)로 축소하여 불필요한 비용 발생을 막습니다. (단, 2개의 규칙 제한 고려하여 설정)
            * **Kubernetes 스케줄링:** GPU를 사용하는 파드는 Kubernetes `nodeSelector` 또는 `affinity`를 사용하여 이 GPU 노드 풀에만 배포되도록 합니다. `resource.limits`와 `resource.requests`를 정확히 설정하여 파드가 필요한 GPU 자원을 명시합니다.
            * **기간 한정:** **2025년 7월 24일 ~ 2025년 9월 22일** 기간 동안만 이 GPU 노드 풀을 활성화하고 운영하며, 이후에는 GPU 자원 사용 여부 및 방식을 재검토하여 다른 GPU 자원 (예: 더 작은 GPU 인스턴스, 온디맨드 서버리스 GPU 옵션 등)으로 전환하거나 사용 계획을 변경합니다.
            * **트래픽 처리:** 5개의 GPU 인스턴스 내에서 초당 수천 건의 캡차 요청 중 AI/ML 추론이 필요한 부분을 처리하기 위해, 백엔드 API와 AI/ML 파드 사이에 **메시지 큐(Redis)**를 두어 요청을 비동기적으로 처리하고, GPU 파드의 부하를 분산하며 과부하 시에도 시스템 전체의 안정성을 유지합니다.

#### 4.2. 데이터베이스: 카카오클라우드 Managed MySQL 8.0

* **고가용성 (HA):** 마스터-슬레이브 복제 구성(자동 페일오버 지원)으로 데이터 손실 및 서비스 중단을 최소화합니다.
* **스토리지:** "최대한 많은 데이터를 저장하고 이용"해야 하므로, 충분한 용량의 SSD 스토리지를 선택하고, 프로젝트 성장에 따라 스케일 업(용량 증설) 및 스케일 아웃(읽기 복제본 추가)을 계획합니다.
* **읽기 복제본 (Read Replica):** 캡차 유효성 검증 기록 조회, 대시보드 통계 등 읽기 작업이 많은 경우, 읽기 복제본을 추가하여 마스터 DB의 부하를 줄이고 읽기 성능을 향상시킵니다.
* **연결:** KCKMS 파드들은 DB 프라이빗 IP 또는 내부 DNS를 통해 안전하게 연결됩니다.

#### 4.3. 스토리지

* **카카오클라우드 Object Storage:**
    * **정적 웹사이트 호스팅:** 메인 홈페이지, 대시보드 프론트엔드의 React 빌드 결과물, 캡차 서비스의 JS 파일, 실제 캡차 이미지, AI/ML 모델 파일 등을 저장하고 CDN과 연동하여 서비스합니다.
    * **데이터 아카이빙/백업:** DB 백업 파일, 캡차 기록 데이터 등을 장기 보관하는 용도로 활용합니다.
* **Kubernetes Persistent Volume (PVC):**
    * KCKMS 클러스터 내에서 영구적인 스토리지(예: 로그 저장, 특정 설정 파일 등)가 필요한 경우, Persistent Volume Claim을 통해 카카오클라우드의 블록 스토리지를 연결합니다. (FastAPI 백엔드는 일반적으로 stateless하게 설계되므로, 필수적이지 않을 수 있습니다.)

#### 4.4. 네트워킹

* **카카오클라우드 VPC (Virtual Private Cloud):** 클라우드 내 격리된 사설 네트워크를 구성하여 보안을 강화합니다.
    * **Public Subnet:** 로드 밸런서, WAF, DDoS Defender 등 외부에서 접근해야 하는 서비스 위치 (**DMZ 영역**).
    * **Private Subnet:** KCKMS 노드, Managed DB, Managed Redis 등 외부에서 직접 접근할 필요 없는 서비스 위치 (애플리케이션 및 데이터베이스 영역).
* **카카오클라우드 Load Balancer (Public Load Balancer):**
    * 높은 트래픽을 처리하기 위해 필수적입니다.
    * **Layer 7 (HTTP/S) 로드 밸런서**를 사용하여 애플리케이션 레벨의 라우팅(예: `api.yourdomain.com`은 캡차 백엔드, `dashboard.yourdomain.com`은 대시보드 백엔드 등으로 분리) 및 SSL/TLS 종료를 처리합니다.
    * KCKMS Ingress Controller와 연동하여 자동으로 Ingress 규칙에 따라 트래픽을 파드로 분산합니다.
* **CDN (Content Delivery Network):** 카카오클라우드 CDN을 사용하여 정적 파일 (JS, 이미지, HTML) 전송 속도를 높이고 원본 서버 부하를 줄입니다. 클라이언트 웹사이트에서 임포트하는 JS 파일과 캡차 이미지를 CDN을 통해 제공합니다.

#### 4.5. 보안

* **카카오클라우드 DDoS Defender:** L3/L4 및 L7 DDoS 공격으로부터 서비스 가용성을 보호합니다. 로드 밸런서 앞에 위치하여 트래픽을 필터링합니다.
* **Marketplace WAF 솔루션 (예: NGINX App Protect WAF):** 웹 애플리케이션 계층의 공격(SQL 인젝션, XSS 등)을 방어합니다. 로드 밸런서와 KCKMS Ingress Controller 사이에 위치하여 모든 HTTP/S 요청을 검사합니다.
* **IAM (Identity and Access Management):** 최소 권한 원칙(Least Privilege)에 따라 각 서비스 및 팀 구성원에게 필요한 최소한의 클라우드 리소스 접근 권한만 부여합니다.
* **Security Group / Network ACL:** VPC 내에서 인스턴스/서브넷 간 네트워크 트래픽을 제어하여 보안 구역을 설정합니다. Private Subnet의 DB 및 백엔드 파드는 Public Subnet에서 직접 접근할 수 없도록 엄격하게 규칙을 적용합니다.
* **TLS/SSL:** 모든 통신 (사용자-로드 밸런서, 로드 밸런서-KCKMS, KCKMS 내부 통신 등)에 TLS/SSL 암호화를 적용합니다.

#### 4.6. 메시징/캐싱

* **카카오클라우드 Managed Redis:**
    * **메시지 큐:** 캡차 서비스의 트래픽 변동이 심하고 AI/ML 연산이 GPU 리소스 제약으로 인해 병목이 될 수 있으므로, 캡차 챌린지 요청을 Redis의 큐(Queue)에 넣어 비동기적으로 처리하는 것을 강력히 권장합니다. 캡차 Backend API 파드는 요청을 큐에 넣고, Captcha AI/ML Inference 파드가 큐에서 작업을 가져와 처리하는 구조입니다.
    * **캐싱:** API 키 캐싱, 자주 사용되는 캡차 데이터, 세션 관리 등 다양한 캐싱 용도로 활용하여 백엔드 및 DB 부하를 줄이고 응답 속도를 향상시킵니다.

#### 4.7. 모니터링 및 로깅

* **카카오클라우드 Monitoring:** KCKMS, DB, LB, Object Storage 등 모든 클라우드 리소스의 성능 지표(CPU, 메모리, 네트워크, 디스크 I/O)를 모니터링합니다. GPU 노드의 활용률도 중요하게 모니터링해야 합니다.
* **카카오클라우드 Logging Service:** 모든 서비스(KCKMS 파드 로그, LB 액세스 로그, WAF/DDoS Defender 로그 등)에서 발생하는 로그를 중앙 집중적으로 수집하고 분석합니다. 특히 봇/DDoS 대응을 위한 IP, 브라우저 정보 등 이상 트래픽 감지에 활용합니다. **Prometheus** & **Grafana**를 KCKMS 클러스터 내에 배포하여 더욱 상세한 컨테이너 및 파드 레벨의 메트릭을 수집/시각화할 수 있습니다.

### 5. CI/CD 및 GitOps: Argo CD

Kubernetes 환경에서 GitOps를 구현하기 위해 **Argo CD**를 사용합니다.

* **Argo CD 선택 이유:**
    * **직관적인 UI/UX:** 시각적인 대시보드를 통해 배포된 애플리케이션의 상태, 동기화 여부, 리소스 관계 등을 한눈에 파악할 수 있어 GitOps 및 Kubernetes에 대한 학습 곡선을 줄여줍니다. 팀원 간 협업 및 운영 가시성 확보에 매우 유리합니다.
    * **자동 동기화 및 Rollback:** Git 리포지토리의 상태를 지속적으로 모니터링하여 클러스터에 배포된 애플리케이션의 상태와 일치하도록 자동으로 동기화합니다. 잘못된 배포 시에도 이전 버전으로의 롤백이 매우 간단합니다.
    * **다양한 통합 지원:** Helm, Kustomize 등 Kubernetes Manifests를 관리하는 다양한 툴을 지원하며, 여러 클러스터 관리도 용이합니다.

* **GitOps Workflow:**
    1.  **코드 변경 (Git Push):** 개발자가 애플리케이션 코드 (FastAPI, React) 또는 Kubernetes Manifests (`Deployment`, `Service`, `Ingress` 등)를 Git 리포지토리 (예: GitHub, GitLab)의 `main` 브랜치에 푸시합니다.
    2.  **CI (Continuous Integration):** CI 파이프라인 (예: GitHub Actions)이 Git Push를 감지하고 다음을 수행합니다:
        * 코드를 빌드하고 테스트를 실행합니다.
        * FastAPI 애플리케이션 및 캡차 JS 파일을 Docker 이미지로 빌드하여 카카오클라우드 Container Registry에 푸시합니다.
        * Kubernetes Manifest 파일 내의 이미지 태그를 새로 빌드된 이미지 태그로 업데이트하고, 이 Manifest 파일을 별도의 GitOps 리포지토리(예: `deploy-manifests`)에 커밋합니다.
    3.  **CD (Continuous Delivery/Deployment by Argo CD):**
        * Argo CD는 GitOps 리포지토리의 변경 사항을 지속적으로 감지합니다.
        * 새로운 Manifest 파일이 감지되면, Argo CD는 자동으로 KCKMS 클러스터에 변경 사항을 적용하고, 새로운 버전의 파드를 배포합니다 (롤링 업데이트).
        * 배포 상태를 모니터링하고, 문제가 발생할 경우 자동으로 롤백하거나 관리자에게 알립니다.

### 6. 아키텍처 다이어그램

```mermaid
graph TD
    subgraph "사용자/클라이언트 웹사이트"
        A[웹 브라우저/JS Import] --> B{CDN: 카카오클라우드 CDN}
    end

    subgraph "카카오클라우드 (한국 리전)"
        subgraph "VPC (Virtual Private Cloud)"
            subgraph "Public Subnet (DMZ)"
                LB(로드 밸런서: Public LB) --> WAF(Web Application Firewall: Marketplace 솔루션)
                WAF --> DDOS_DEFENDER(DDoS Defender)
                DDOS_DEFENDER --> KCKMS_INGRESS(KCKMS Ingress Controller)
                B --> KCKMS_INGRESS
            end

            subgraph "Private Subnet (Application/Database)"
                KCKMS_INGRESS --> KCKMS(카카오클라우드 Kubernetes Service - KCKMS)
                subgraph "KCKMS Cluster"
                    direction LR
                    NP_GENERAL[일반 노드 풀]
                    NP_GPU[GPU 노드 풀: 5 x gn1i.xlarge]

                    HOMEPAGE_BE[Homepage Backend]
                    DASHBOARD_BE[Dashboard Backend]
                    CAPTCHA_BE_API[Captcha Backend API]
                    CAPTCHA_AI[Captcha AI/ML Inference]

                    NP_GENERAL --- HOMEPAGE_BE
                    NP_GENERAL --- DASHBOARD_BE
                    NP_GENERAL --- CAPTCHA_BE_API

                    CAPTCHA_BE_API --> REDIS_CACHE[Managed Redis<br/>Cache + Message Queue]

                    REDIS_CACHE --> CAPTCHA_AI
                    CAPTCHA_AI --> NP_GPU

                    CAPTCHA_BE_API -- (API 호출) --> CAPTCHA_AI

                    HOMEPAGE_BE --> ManagedDB
                    DASHBOARD_BE --> ManagedDB
                    CAPTCHA_BE_API --> ManagedDB

                    ManagedDB(Managed MySQL 8.0)
                end

                KCKMS --> OBJECT_STORAGE(Object Storage)
            end
        end

        subgraph "보안 및 관리"
            IAM(IAM: 계정 및 권한 관리)
            LOG(Logging Service)
            MON(Monitoring Service)
            BACKUP(Backup Service)
        end

        subgraph "CI/CD & GitOps"
            GIT(Git Repository: GitHub) --> ARGO_CD(Argo CD)
            ARGO_CD --> KCKMS
        end
    end

    B --> OBJECT_STORAGE
    OBJECT_STORAGE[Object Storage: Static FE, JS, Captcha Images, ML Models] --> B

    classDef pod fill:#c1e1ec,stroke:#333,stroke-width:2px,color:#000
    classDef db fill:#fccb00,stroke:#333,stroke-width:2px,color:#000
    classDef firewall fill:#f5ee98,stroke:#333,stroke-width:2px,color:#000
    classDef lb fill:#b8e986,stroke:#333,stroke-width:2px,color:#000
    classDef k8s fill:#d9ed92,stroke:#333,stroke-width:2px,color:#000

    class HOMEPAGE_BE,DASHBOARD_BE,CAPTCHA_BE_API,CAPTCHA_AI pod
    class REDIS_CACHE,ManagedDB,OBJECT_STORAGE db
    class WAF,DDOS_DEFENDER firewall
    class LB lb
    class KCKMS k8s

    style A fill:#e0f2f7,stroke:#333,stroke-width:2px,color:#000
    style B fill:#e0f2f7,stroke:#333,stroke-width:2px,color:#000
    style KCKMS_INGRESS fill:#e0f2f7,stroke:#333,stroke-width:2px,color:#000
    style NP_GENERAL fill:#d9ed92,stroke:#333,stroke-width:2px,color:#000
    style NP_GPU fill:#d9ed92,stroke:#333,stroke-width:2px,color:#000
    style IAM fill:#e0b2d3,stroke:#333,stroke-width:2px,color:#000
    style LOG fill:#e0b2d3,stroke:#333,stroke-width:2px,color:#000
    style MON fill:#e0b2d3,stroke:#333,stroke-width:2px,color:#000
    style BACKUP fill:#e0b2d3,stroke:#333,stroke-width:2px,color:#000
    style GIT fill:#a3d2ca,stroke:#333,stroke-width:2px,color:#000
    style ARGO_CD fill:#a3d2ca,stroke:#333,stroke-width:2px,color:#000
```

### 7. 추가 고려사항

* **비용 최적화:**
    * **GPU 인스턴스**는 비용이 매우 높으므로, 사용 시간 및 수량을 엄격히 관리해야 합니다. 기간 외 또는 시간 외에 불필요하게 실행되지 않도록 자동화된 스케줄링을 구현해야 합니다.
    * KCKMS의 오토 스케일링을 통해 필요할 때만 노드를 확장하고, 유휴 시간에는 축소하여 비용을 절감합니다.
    * Managed DB의 스토리지 및 인스턴스 사양도 초기에는 적정 수준으로 시작하고, 데이터 증가에 따라 점진적으로 확장합니다.
* **장기적인 GPU 전략:** 2025년 9월 22일 이후에도 GPU 연산이 필수적이라면, 해당 시점의 GPU 자원 확보 방안 (예: 더 작은 GPU 인스턴스, 스팟 인스턴스 활용, GPU 서버리스 서비스 검토)을 미리 계획해야 합니다.
* **오픈소스 라이선스:** ImageNet, YOLO 등 사용하시는 AI/ML 모델 및 라이브러리의 라이선스 정책을 확인하여 상업적 이용 가능 여부 및 의무사항을 준수해야 합니다.
* **장애 복구 계획 (DRP):** 카카오클라우드 내 리전/가용성 존 장애 시를 대비한 데이터 백업 및 복구 전략, 멀티 리전 배포 고려 (성장 시).

---

## 모니터링 및 로깅

### 로그 시스템
- 구조화된 로그 (JSON)
- 레벨별 로그 (DEBUG, INFO, WARNING, ERROR)
- 요청/응답 로깅 미들웨어
- 성능 메트릭 (응답 시간)

### 통계 및 분석
- 일일 API 사용량 집계
- 에러 통계 집계
- 엔드포인트별 사용량
- 실시간 대시보드

---


**RealCatcha v1.0.0**  
© 2025 Find Your Humanity. All rights reserved.

