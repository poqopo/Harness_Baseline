# AGENTS.md

이 저장소는 single-cell multi-omic 또는 time-resolved ATAC/RNA 데이터를 이용해 gene-specific epigenomic lag structure를 분석하는 에이전트 작업 공간입니다. 에이전트의 핵심 목표는 chromatin accessibility 변화와 transcription 변화 사이의 시간차를 정량화하고, 이 lag structure가 perturbation 또는 epigenetic drug response timing을 예측할 수 있는지 검증하는 것입니다.

## 프로젝트 중심 가설

- Chromatin accessibility와 transcription 사이의 lag은 gene마다 다르다.
- Promoter/enhancer accessibility, histone marks, TF occupancy, regulatory architecture는 activation lag과 shutdown lag의 크기를 결정하는 주요 요인이다.
- Gene-specific lag structure를 추정하면 perturbation 또는 epigenetic drug에 대한 gene-level response timing을 예측할 수 있다.

## 핵심 개념

- `activation lag` 또는 `priming time`: chromatin opening 이후 transcription onset이 일어나기까지의 시간차
- `shutdown lag` 또는 `closing lag`: transcription shutdown 이후 chromatin closing이 일어나기까지의 시간차
- `Model 1-like`: chromatin 변화가 먼저 일어나고 transcription 변화가 뒤따르는 패턴
- `Model 2-like`: transcription 변화가 먼저 일어나고 chromatin 변화가 뒤따르는 패턴
- 이 프로젝트는 Model 1/Model 2 이분법을 gene-specific kinetic spectrum으로 확장한다.

## 분석 목표

1. Gene-specific activation lag과 shutdown lag을 정의하고 정량화한다.
2. Baseline epigenomic features로 gene-specific lag structure를 예측하는 모델을 구축한다.
3. 추정된 lag structure가 perturbation 또는 epigenetic drug response timing을 예측하는지 검증한다.

## 우선 분석 단위

- 각 gene에 대해 다음 시간을 추정하거나 입력으로 받는다.
  - chromatin opening time
  - transcription onset time
  - transcription shutdown time
  - chromatin closing time
- 가능한 경우 다음 값을 계산한다.
  - `activation lag = transcription onset time - chromatin opening time`
  - `shutdown lag = chromatin closing time - transcription shutdown time`
- Lag은 연속형 값으로 다루는 것을 기본으로 하되, 분석 목적에 따라 short/long lag class로 이산화할 수 있다.

## Feature Engineering 우선순위

Baseline 상태에서 gene별 feature를 만들 때는 다음 항목을 우선 고려합니다.

- promoter accessibility
- enhancer accessibility
- H3K27ac
- H3K27me3
- H3K4me3
- TF occupancy 또는 motif score
- peak-to-gene linkage
- CpG density와 promoter class

Feature와 label을 만들 때 genome build, gene annotation, promoter/enhancer 정의, peak-to-gene 연결 기준을 반드시 기록합니다.

## 참고 연구와 해석 기준

- MultiVelo, Nature Biotechnology 2023: chromatin/RNA switch time, Model 1/Model 2, priming interval, decoupling interval 개념의 기준점으로 사용합니다.
- MultiVeloVAE, Nature Communications 2025: continuous하고 lineage-specific한 chromatin/RNA dynamics 해석의 참고로 사용합니다.
- MoFlow, Nature Communications 2025/2026 record: gene별 DTW 기반 chromatin-vs-spliced RNA lag와 asynchronous timing 해석의 참고로 사용합니다.
- 기존 연구를 그대로 재현하는 데서 멈추지 말고, activation lag과 shutdown lag이라는 통일된 kinetic variable로 재구성하는 관점을 유지합니다.

## 데이터셋 후보

우선 검토할 baseline multi-omic dataset:

- 10x embryonic mouse brain: embryonic brain differentiation, 10x multiome
- SHARE-seq mouse skin, `GSE140203`: paired chromatin + RNA, skin differentiation
- Human brain multi-ome, `GSE162170`: fetal/developing brain
- Human HSPC 10x Multiome, `GSE209878`: hematopoietic stem/progenitor state

Perturbation 또는 drug response 검증 후보:

- L1000 / CMap CLUE: Vorinostat(SAHA), Trichostatin A, Valproic acid 등 HDAC-related perturbation transcriptomic signatures
- PRISM / DepMap: Vorinostat, Panobinostat, Romidepsin, Entinostat, Azacitidine, Pinometostat, Enasidenib, Dacinostat 등 drug sensitivity/viability screens

최신 accession, portal URL, 데이터 사용 조건, 파일 형식은 분석 시점에 확인합니다.

## 작업 원칙

- 분석 전 입력 데이터 형식, genome build, sample metadata, time/trajectory definition, lineage 또는 cell state annotation, replicate 여부를 확인합니다.
- Lag을 계산할 때 pseudotime, real time, inferred switch time, DTW alignment time 중 어떤 시간축을 쓰는지 명확히 구분합니다.
- Gene-level lag 추정에서 confidence, uncertainty, missingness를 가능한 한 함께 남깁니다.
- 모델 성능은 단순 accuracy보다 ranking, calibration, held-out lineage/dataset generalization, early-vs-late response separation을 중점적으로 평가합니다.
- Perturbation 검증에서는 baseline lag prediction과 실제 response timing 사이의 연결을 명확히 분리해서 해석합니다.
- 대용량 생물정보 파일은 원본을 덮어쓰지 않고 `results/`, `outputs/`, `work/` 같은 별도 산출물 경로를 사용합니다.

## Skill Routing

Dataset-specific routing is delegated to `skills/ROUTES.md`.

When the user asks for dataset work:

1. Read `skills/ROUTES.md`.
2. Route first by dataset, then by task type.
3. Use the matching `skills/<dataset>/<task>/SKILL.md`.
4. Keep this `AGENTS.md` focused on project-level goals and global analysis principles.

## Folder Structure

- `skills/ROUTES.md`: dataset별 skill routing table and workflow
- `skills/10x-embryonic-mouse-brain/preprocessing/SKILL.md`: 10x embryonic mouse brain preprocessing
- `skills/10x-embryonic-mouse-brain/model/SKILL.md`: 10x embryonic mouse brain lag modeling
- `skills/10x-embryonic-mouse-brain/visualization/SKILL.md`: 10x embryonic mouse brain visualization
- `skills/share-seq-mouse-skin/preprocessing/SKILL.md`: SHARE-seq mouse skin preprocessing
- `skills/share-seq-mouse-skin/model/SKILL.md`: SHARE-seq mouse skin lag modeling
- `skills/share-seq-mouse-skin/visualization/SKILL.md`: SHARE-seq mouse skin visualization
- `skills/human-brain-multiome/preprocessing/SKILL.md`: human brain multiome preprocessing
- `skills/human-brain-multiome/model/SKILL.md`: human brain multiome lag modeling
- `skills/human-brain-multiome/visualization/SKILL.md`: human brain multiome visualization
- `skills/human-hspc-10x-multiome/preprocessing/SKILL.md`: human HSPC 10x Multiome preprocessing
- `skills/human-hspc-10x-multiome/model/SKILL.md`: human HSPC 10x Multiome lag modeling
- `skills/human-hspc-10x-multiome/visualization/SKILL.md`: human HSPC 10x Multiome visualization
- `data/`: 입력 데이터 또는 원본 데이터 위치 안내
- `metadata/`: sample sheet, cell annotations, comparison design, genome build 정보
- `scripts/`: 재사용 가능한 분석 스크립트
- `results/`: 최종 결과 테이블, 그림, 리포트
- `work/`: 중간 산출물

## 도구 사용 지침

- 파일 탐색은 `rg`와 `rg --files`를 우선 사용합니다.
- 기존 스크립트나 설정이 있으면 새로 만들기 전에 먼저 읽고, 같은 스타일로 수정합니다.
- FASTQ/BAM/BED/bigWig/MTX/H5AD/H5MU 같은 파일은 확장자만 믿지 말고 가능한 경우 헤더, shape, obs/var metadata, 일부 레코드를 확인합니다.
- 외부 도구, 데이터 포털, 논문 정보가 최신성에 영향을 받으면 공식 문서나 원 출처를 확인합니다.

## 결과 보고 방식

- 변경 파일, 실행 명령, 검증 결과를 간단히 요약합니다.
- 분석 결과를 보고할 때는 사용한 genome build, annotation source, time axis, cutoff, sample/group 정의를 함께 적습니다.
- 생물학적 해석은 데이터와 모델이 지지하는 범위 안에서만 작성하고, 불확실한 부분은 명확히 표시합니다.
