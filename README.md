<br>
<p align="center">
<h1 align="center"><strong>StreamVLN: SlowFast 컨텍스트 모델링을 통한 스트리밍 비전-언어 내비게이션</strong></h1>
  <p align="center">
    <a href='https://github.com/kellyiss/' target='_blank'>Meng Wei*</a>&emsp;
    <a href='https://bryce-wan.github.io/' target='_blank'>Chenyang Wan*</a>&emsp;
    <a href='https://scholar.google.com/citations?user=CKWKIscAAAAJ&hl=en' target='_blank'>Xiqian Yu*</a>&emsp;
    <a href='https://tai-wang.github.io/' target='_blank'>Tai Wang*‡</a>&emsp;
    <a href='https://yuqiang-yang.github.io/' target='_blank'>Yuqiang Yang</a>&emsp;
    <a href='https://scholar.google.com/citations?user=-zT1NKwAAAAJ&hl=en' target='_blank'>Xiaohan Mao</a>&emsp;
    <a href='https://zcmax.github.io/' target='_blank'>Chenming Zhu</a>&emsp;
    <a href='https://wzcai99.github.io/' target='_blank'>Wenzhe Cai</a>&emsp;
    <a href='https://hanqingwangai.github.io/' target='_blank'>Hanqing Wang</a>&emsp;
    <a href='https://yilunchen.com/about/' target='_blank'>Yilun Chen</a>&emsp;
    <a href='https://xh-liu.github.io/' target='_blank'>Xihui Liu†</a>&emsp;
    <a href='https://oceanpang.github.io/' target='_blank'>Jiangmiao Pang†</a>&emsp;
    <br>
    Shanghai AI Laboratory&emsp;The University of Hong Kong&emsp;Zhejiang University&emsp;Shanghai Jiao Tong University&emsp;
  </p>
</p>

<div id="top" align="center">


[![arxiv](https://img.shields.io/badge/arXiv_2507.05240-red?logo=arxiv)](http://arxiv.org/abs/2507.05240)
[![project](https://img.shields.io/badge/Project_Page-0065D3?logo=rocket&logoColor=white)](https://streamvln.github.io/)
[![hf](https://img.shields.io/badge/Hugging_Face-FF9D00?logo=huggingface&logoColor=white)](https://huggingface.co/papers/2507.05240/)
[![video-en](https://img.shields.io/badge/YouTube-D33846?logo=youtube)](https://www.youtube.com/watch?v=gG3mpefOBjc)

</div>

## 🏠 소개
<strong><em>StreamVLN</em></strong>은 연속적인 비디오 입력으로부터 온라인 다중 턴 대화 방식으로 행동(action) 출력을 생성합니다. 기본 Video-LLM으로 **LLaVA-Video**를 사용하며, 시각·언어·행동의 인터리브(interleaved) 모델링이 가능하도록 이를 확장했습니다. 긴 시퀀스에서 효과적인 컨텍스트 모델링과 실시간 상호작용을 위한 효율적 연산을 모두 달성하기 위해, StreamVLN은 다음을 갖추고 있습니다: (1) 슬라이딩 윈도우 KV 캐시를 활용한 **빠른 스트리밍(fast-streaming)** 대화 컨텍스트, (2) 토큰 프루닝(token pruning)을 통한 **느린 갱신(slow-updating)** 메모리.
<div style="text-align: center;">
    <img src="assets/teaser.gif" width=100% >
</div>

## 📢 뉴스
[2025-09-28] R2R_VLNCE_v1-3로 학습된 [체크포인트](https://huggingface.co/mengwei0427/StreamVLN_Video_qwen_1_5_r2r_rxr_envdrop_scalevln_v1_3)를 업데이트했습니다. 성능이 향상되어 R2R (NE:4.90, OS:63.6, SR:56.4, SPL:50.2), RxR (NE:5.65, SR:54.4, SPL:45.4, nDTW:63.7)를 달성했습니다. **기존에 R2R_VLNCE_v1을 사용하셨다면, 학습/테스트 데이터를 반드시 R2R_VLNCE_v1-3로 변경해 주세요.**

[2025-08-28] Unitree Go2 로봇 실환경 배포를 위한 코드와 [가이드](realworld/realworld.md)를 공개했습니다.

[2025-08-21] 다음 구성요소의 코드를 공개했습니다: 1) **Dagger 데이터 수집**; 2) LLaVA-Video-178K, ScanQA, MMC4 데이터셋을 활용한 **2단계 공동학습(Stage-Two Co-training)**.

[2025-07-30] 이산 환경 설정을 VLN-CE 형식으로 변환한 약 15만 에피소드 서브셋을 포함한 ScaleVLN 학습 데이터를 공개했습니다. 사용 방법은 [여기](https://huggingface.co/datasets/cywan/StreamVLN-Trajectory-Data/blob/main/README.md#envdrop--scalevln-dataset-note)를 참고하세요.

[2025-07-18] 평가 시 num_history가 모델에 올바르게 전달되지 않아 None으로 설정되던 버그를 수정했습니다. 이 문제는 성능에 큰 영향을 주었습니다. 정확한 평가를 위해 최신 코드를 반드시 pull 해 주세요.

## 🛠 시작하기
다음 환경에서 테스트했습니다:
* Python 3.9
* Pytorch 2.1.2
* CUDA Version 12.4 

1. **`Python3.9` conda 환경 준비 및 habitat-sim, habitat-lab 설치**
    ```bash
    conda create -n streamvln python=3.9
    conda install habitat-sim==0.2.4 withbullet headless -c conda-forge -c aihabitat
    git clone --branch v0.2.4 https://github.com/facebookresearch/habitat-lab.git
    cd habitat-lab
    pip install -e habitat-lab  # install habitat_lab
    pip install -e habitat-baselines # install habitat_baselines
    ```

2. **이 저장소 클론**
    ```bash
    git clone https://github.com/OpenRobotLab/StreamVLN.git
    cd StreamVLN
    ```

<!-- 3. **Data Preparation**

    - You need to download the **Matterport3D (MP3D)** scenes first. Please follow the instructions in the [official project page](https://niessner.github.io/Matterport/). Place them in the `data/scene_datasets` folder.

    - For **evaluation**, please download the VLN-CE episodes: [r2r](https://dl.fbaipublicfiles.com/habitat/data/datasets/vln/mp3d/r2r/v1/vln_r2r_mp3d_v1.zip) and [rxr](https://drive.google.com/file/d/145xzLjxBaNTbVgBfQ8e9EsBAV8W-SM0t/view), and place them in the `data/datasets` folder.

    - For **training**, please downlaod our observation-action pairs from [Hugging Face](https://huggingface.co/datasets/cywan/StreamVLN-Trajectory-Data), extract and place them in the `data/trajectory_data` folder. 

    The data folder should follow this structure:

    ```shell
    data/
    ├── datasets/
    │   ├── r2r
    │   │   ├── train/
    │   │   ├── val_seen/
    │   │   │   └── val_seen.json.gz
    │   │   └── val_unseen/
    │   │       └── val_unseen.json.gz
    │   └── rxr
    │       ├── train/
    │       ├── val_seen/
    │       │   ├── val_seen_guide.json.gz
    │       │   └── ...
    │       └── val_unseen/
    │           ├── val_unseen_guide.json.gz
    │           └── ...  
    ├── scene_datasets/
    │   └── mp3d/                   
    │       ├── 17DRP5sb8fy/        
    │       ├── 1LXtFkjw3qL/
    │       └── ...                 
    └── trajectory_data/
        ├── EnvDrop/
        │   ├── images/
        │   └── annotations.json
        ├── R2R/
        │   ├── images/
        │   └── annotations.json
        └── RxR/
            ├── images/
            └── annotations.json
    ``` -->

## 📁 데이터 준비

시작하려면 세 가지 종류의 데이터를 준비해야 합니다:

1. **Scene 데이터셋**  
   - **R2R**, **RxR**, **EnvDrop**: [공식 프로젝트 페이지](https://niessner.github.io/Matterport/)에서 MP3D 씬을 다운로드한 뒤 `data/scene_datasets/mp3d/` 아래에 배치하세요.
   - **ScaleVLN**: [공식 github 페이지](https://github.com/matterport/habitat-matterport-3dresearch)에서 HM3D 씬을 다운로드한 뒤 `train` split을 `data/scene_datasets/hm3d/` 아래에 배치하세요.

2. **VLN-CE 에피소드**  
   VLN-CE 에피소드를 다운로드하세요:
   - [r2r](https://drive.google.com/file/d/18DCrNcpxESnps1IbXVjXSbGLDzcSOqzD/view) (`R2R_VLNCE_v1/` -> `r2r/` 로 이름 변경)
   - [rxr](https://drive.google.com/file/d/145xzLjxBaNTbVgBfQ8e9EsBAV8W-SM0t/view) (`RxR_VLNCE_v0/` -> `rxr/` 로 이름 변경)
   - [envdrop](https://drive.google.com/file/d/1fo8F4NKgZDH-bPSdVU3cONAkt5EW-tyr/view) (`R2R_VLNCE_v1-3_preprocessed/envdrop/` -> `envdrop/` 로 이름 변경)
   - [scalevln](https://huggingface.co/datasets/cywan/StreamVLN-Trajectory-Data/blob/main/ScaleVLN/scalevln_subset_150k.json.gz) (ScaleVLN 데이터셋의 서브셋을 VLN-CE 형식으로 변환한 것입니다. 원본 데이터셋은 [공식 저장소](https://github.com/wz0919/ScaleVLN)를 참고하세요.)
  
   압축을 해제한 뒤 `data/datasets/` 디렉터리에 넣으세요.

3. **수집된 궤적 데이터**  
  학습용으로 미리 수집된 observation-action 궤적 데이터를 제공합니다. 이 궤적들은 Matterport3D 환경에서 **R2R** 및 **RxR**의 **학습 에피소드**를 사용해 수집되었습니다. **EnvDrop** 및 **ScaleVLN** 서브셋은 직접 수집해야 하며, 방법은 [여기](https://huggingface.co/datasets/cywan/StreamVLN-Trajectory-Data/blob/main/README.md)를 참고하세요.
  [Hugging Face](https://huggingface.co/datasets/cywan/StreamVLN-Trajectory-Data)에서 observation-action 궤적 데이터를 다운로드하여 `data/trajectory_data/`에 압축 해제하세요.

4. **공동학습 데이터 준비**

    각 데이터셋을 공식 소스에서 다운로드한 뒤 `data/co-training_data/`에 배치하세요.

    - LLaVA-Video-178K: Hugging Face의 [lmms-lab/LLaVA-Video-178K](https://huggingface.co/datasets/lmms-lab/LLaVA-Video-178K)에서 받을 수 있습니다.

    - ScanNet: 

      - 메인 데이터셋은 [공식 GitHub 저장소](https://github.com/ScanNet/ScanNet)에서 다운로드할 수 있습니다.

      - [여기](https://huggingface.co/datasets/chchnii/StreamVLN-ScanQA-SQA3D-Data)에서 `scanqa_annotations.json`, `sqa3d_annotations.json` 어노테이션 파일을 다운로드하세요. 이 파일들은 [LLaVA-3D-DATA](https://huggingface.co/datasets/ChaimZhu/LLaVA-3D-Data)의 서브셋입니다.


    - MMC4-core: [공식 GitHub 저장소](https://github.com/allenai/mmc4)에서 받을 수 있습니다.

최종 폴더 구조는 아래와 같아야 합니다:

```bash
data/
├── datasets/
│   ├── r2r/
│   │   ├── train/
│   │   ├── val_seen/
│   │   │   └── val_seen.json.gz
│   │   └── val_unseen/
│   │       └── val_unseen.json.gz
│   ├── rxr/
│   │   ├── train/
│   │   ├── val_seen/
│   │   │   ├── val_seen_guide.json.gz
│   │   │   └── ...
│   │   └── val_unseen/
│   │       ├── val_unseen_guide.json.gz
│   │       └── ...
│   ├── envdrop/
│   │   ├── envdrop.json.gz
│   │   └── ...
│   └── scalevln/
│       └── scalevln_subset_150k.json.gz
├── scene_datasets/
│   └── hm3d/
│       ├── 00000-kfPV7w3FaU5/
│       ├── 00001-UVdNNRcVyV1/
│       └── ...
│   └── mp3d/
│       ├── 17DRP5sb8fy/
│       ├── 1LXtFkjw3qL/
│       └── ...
├── trajectory_data/
│   ├── R2R/
│   │   ├── images/
│   │   └── annotations.json
│   ├── RxR/
│   │   ├── images/
│   │   └── annotations.json
│   ├── EnvDrop/
│   │   ├── images/
│   │   └── annotations.json
│   └── ScaleVLN/
│       ├── images/
│       └── annotations.json
├── dagger_data/
│   ├── R2R/
│   │   ├── images/
│   │   └── annotations.json
│   ├── RxR/
│   │   ├── images/
│   │   └── annotations.json
│   └── EnvDrop/
│       ├── images/
│       └── annotations.json
└── co-training_data/
    ├── ScanNet/
    │   ├── posed_images/
    │   │   ├── scene0000_00/
    │   │   ├── scene0000_01/
    │   │   └── ...
    │   ├── scanqa_annotations.json
    │   └── sqa3d_annotations.json
    ├── LLaVA-Video-178K/
    │   ├── 0_30_s_academic_v0_1/
    │   │   ├── academic_source/
    │   │   │   ├── Charades/
    │   │   │   ├── NextQA/
    │   │   │   └── ...
    │   │   └── 0_30_s_academic_oe_v0_1_qa_processed.json
    │   ├── 30_60_s_academic_v0_1/
    │   │   ├── academic_source/
    │   │   │   ├── Charades/
    │   │   │   ├── NextQA/
    │   │   │   └── ...
    │   │   └── 30_60_s_academic_oe_v0_1_qa_processed.json
    │   └── 0_30_s_youtube_v0_1/
    │       ├── liwei_youtube_videos/
    │       └── 0_30_s_youtube_oe_v0_1_qa_processed.json
    └── MMC4-core/
            ├── images/
            ├── docs_shard_10000_v3.jsonl
            ├── docs_shard_10001_v3.jsonl
            └── ...


```

## 🏆 모델 체크포인트

사용 목적에 따라 두 가지 모델 체크포인트를 제공합니다:

- **벤치마크 재현용**
  VLN-CE 벤치마크 결과 재현에는 이 [체크포인트](https://huggingface.co/mengwei0427/StreamVLN_Video_qwen_1_5_r2r_rxr_envdrop_scalevln_v1_3)를 사용하세요.

- **실환경 배포용**
  물리 로봇 배포에는 이 [체크포인트](https://huggingface.co/mengwei0427/StreamVLN_Video_qwen_1_5_r2r_rxr_envdrop_scalevln_real_world)를 권장합니다.

  다음 두 가지 수정을 적용했습니다:
  1. **불필요한 초기 회전 동작 제거**: 지시문에 없는 초기 좌/우 회전을 제거해 지시 정합성을 높였습니다.
  2. **궤적 안전성 강화**: 장애물 회피를 강화해 실환경 내비게이션의 신뢰성을 높였습니다.

## 🚀 학습

1. **1단계 학습 (Stage-one Training)**

    분산 설정 기반의 **멀티노드 멀티GPU 학습**을 수행하려면 다음을 실행하세요:

    ```bash
    sbatch scripts/streamvln_train_slurm.sh
    ```
2. **Dagger 수집**

    멀티GPU 수집을 수행하려면 다음을 실행하세요:

    ```bash
    sh scripts/streamvln_dagger_collect.sh
    ```
2. **2단계 학습 (Stage-two Training)**

    분산 설정 기반의 **멀티노드 멀티GPU 학습**을 수행하려면 다음을 실행하세요:
    ```bash
    sbatch scripts/streamvln_stage_two_train_slurm.sh
    ```

## 🤖 평가

Key-value cache를 지원하는 멀티GPU 평가를 수행하려면 다음을 실행하세요:

```bash
sh scripts/streamvln_eval_multi_gpu.sh
```

## 배포

Unitree Go2 로봇 실환경 배포는 [realworld/realworld.md](realworld/realworld.md)를 참고하세요.

## 📝 TODO 목록

- ✅ arXiv 논문 공개 (2025년 7월 8일)
- ✅ 추론 스크립트 및 모델 체크포인트 제공
- ✅ 학습 코드 및 설정 공개
- ✅ 학습 데이터 공개
- ✅ LLaVA-Video-178K, ScanQA, MMC4 공동학습 지원
- ✅ Dagger 데이터 수집

## 🙋‍♂️ 질문 또는 이슈

문제가 발생했거나 StreamVLN에 대한 질문이 있다면 [이슈 등록](https://github.com/OpenRobotLab/StreamVLN/issues)을 통해 알려주세요.


## 🔗 인용

본 연구가 도움이 되었다면 이 저장소에 스타(🌟)를 눌러주시고, 아래와 같이 인용해 주세요:

```bibtex
@article{wei2025streamvln,
  title={StreamVLN: Streaming Vision-and-Language Navigation via SlowFast Context Modeling},
  author={Wei, Meng and Wan, Chenyang and Yu, Xiqian and Wang, Tai and Yang, Yuqiang and Mao, Xiaohan and Zhu, Chenming and Cai, Wenzhe and Wang, Hanqing and Chen, Yilun and others},
  journal={arXiv preprint arXiv:2507.05240},
  year={2025}
}
```

## 📄 라이선스
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/80x15.png" /></a>
<br />
이 작업물은 <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">크리에이티브 커먼즈 저작자표시-비영리-동일조건변경허락 4.0 국제 라이선스</a>를 따릅니다.

## 👏 감사의 글

이 저장소는 [LLaVA-NeXT](https://github.com/LLaVA-VL/LLaVA-NeXT)를 기반으로 합니다.
