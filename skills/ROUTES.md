# Skill Routes

Dataset-specific skill routing lives here. Route work first by dataset, then by task type.

## Dataset Routing

| Dataset request | Dataset folder |
| --- | --- |
| 10x embryonic mouse brain, embryonic mouse brain, mouse brain multiome | `skills/10x-embryonic-mouse-brain/` |
| SHARE-seq mouse skin, GSE140203, mouse skin differentiation | `skills/share-seq-mouse-skin/` |
| Human brain multi-ome, GSE162170, fetal/developing human brain | `skills/human-brain-multiome/` |
| Human HSPC 10x Multiome, GSE209878, hematopoietic stem/progenitor | `skills/human-hspc-10x-multiome/` |

## Task Routing

Within each dataset folder, route by task:

| Task request | Skill |
| --- | --- |
| preprocessing, QC, normalization, annotation, data loading, matrix/object preparation | `<dataset>/preprocessing/SKILL.md` |
| model, lag estimation, MultiVelo/MoFlow-style dynamics, feature prediction, evaluation | `<dataset>/model/SKILL.md` |
| visualization, figure plan, plotting, UMAP/trajectory/lag/model performance figures | `<dataset>/visualization/SKILL.md` |

## Dataset Workflow

When the user asks for work on one dataset:

1. Identify the dataset from the user's wording or accession.
2. Identify whether the task is `preprocessing`, `model`, or `visualization`.
3. Use the matching skill under `skills/<dataset>/<task>/SKILL.md`.
4. If the user gives a dataset but no task, start with `preprocessing` unless existing preprocessing outputs are already available.
5. If the user gives a task but no dataset, ask which dataset to use before proceeding.
6. Keep the framing centered on gene-specific `activation lag` and `shutdown lag`.

## Cross-Dataset Workflow

When the user asks for comparison across datasets:

1. Apply the same task skill type across all relevant dataset folders.
2. Normalize terminology across datasets: genome build, annotation source, time axis, lineage/cell state labels, lag definitions.
3. Do not merge outputs until dataset-specific preprocessing/modeling assumptions are recorded.
4. Report dataset-specific uncertainty and missingness before making cross-dataset biological claims.

## Current Skill Tree

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

