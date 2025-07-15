# AutoDock-GPU 설치 및 실행 가이드

> 원본 `README` 링크: [https://github.com/ccsb-scripps/AutoDock-GPU/blob/develop/README.md](https://github.com/ccsb-scripps/AutoDock-GPU/blob/develop/README.md)
>
> `autogrid4` 및 `autodock-gpu`의 사용법이 상당히 까다로워, 2025년 7월 15일 현재 실행에 성공한 방법을 기준으로 정리합니다.

---

## 사전 준비
- **NVIDIA GPU**가 탑재된 **리눅스 환경** (예: `Ubuntu 22.04`)
- 도킹에 필요한 원본 데이터 (`pdb`, `csv` 파일 등)

---

## 1단계: 환경 설정 및 필수 도구 설치

### 1. Miniconda 설치
독립적인 파이썬 환경을 구성하기 위해 Miniconda를 설치합니다.
```bash
mkdir -p miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda3/miniconda.sh
bash miniconda3/miniconda.sh -b -u -p miniconda3
rm miniconda3/miniconda.sh
```
> *Miniconda 설치법 참고: [Official Guide](https://www.anaconda.com/docs/getting-started/miniconda/install#macos-linux-installation)*

### 2. 빌드 도구 및 쉘(Shell) 설치
`autogrid4` 컴파일에 필요한 C++ 컴파일러, 빌드 도구, 쉘 등을 설치합니다.
```bash
apt-get update && apt-get install -y build-essential autoconf automake libtool git csh tcsh meson ninja-build
```

### 3. Conda 가상환경 생성 및 라이브러리 설치
`docking`이라는 이름의 가상환경을 만들고, 필요한 파이썬 라이브러리들을 설치합니다.
```bash
# Conda 초기화 (새로운 터미널 세션에서 필요할 수 있음)
source miniconda3/bin/activate

# 가상환경 생성 및 활성화
conda create -n docking python=3.11 -y
conda activate docking

# 파이썬 라이브러리 설치
pip install pandas tqdm rdkit meeko biopython joblib prody jupyterlab ipykernel ipywidgets

# PyTorch 설치 (CUDA 12.x 버전 기준)
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```
> *PyTorch 설치법 참고: [Official Guide](https://pytorch.org/get-started/locally/)*

---

## 2단계: `autogrid4` 소스 코드 직접 컴파일
> *원본 저장소: [https://github.com/ccsb-scripps/AutoGrid](https://github.com/ccsb-scripps/AutoGrid)*

Conda로 설치 시 발생하는 호환성 문제를 해결하기 위해, 소스 코드를 직접 컴파일합니다.

```bash
# 1. 소스 코드 다운로드
git clone https://github.com/ccsb-scripps/AutoGrid.git

# 2. 폴더 이동 및 Meson으로 컴파일
cd AutoGrid
meson setup build
cd build
meson compile
```

---

## 3단계: `AutoDock-GPU` Pre-compiled 바이너리 다운로드
> *릴리즈 노트: [https://github.com/ccsb-scripps/AutoDock-GPU/releases](https://github.com/ccsb-scripps/AutoDock-GPU/releases)*

복잡한 컴파일 과정 없이, 개발자가 제공하는 실행 파일을 직접 다운로드합니다.

```bash
# 1. 현재 작업 폴더로 이동 (예: /DOCKER/)
cd /DOCKER/

# 2. CUDA 12 버전에 맞는 바이너리 다운로드
wget https://github.com/ccsb-scripps/AutoDock-GPU/releases/download/v1.6/adgpu-v1.6_linux_x64_cuda12_128wi

# 3. 실행 권한 부여
chmod +x adgpu-v1.6_linux_x64_cuda12_128wi

# 4. 사용하기 편한 이름으로 변경
mv adgpu-v1.6_linux_x64_cuda12_128wi autodock_gpu_128wi
```
