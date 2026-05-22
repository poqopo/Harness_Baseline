---
name: paper_scrapper
description: Search for scientific papers related to a user-provided topic, list candidate papers, and download only publicly available PDFs that are not already present in papers/. Use when Codex is asked to collect papers, scrape PDFs, gather literature, or populate the papers/ folder for a topic.
---

# Paper Scrapper

## 목표
사용자가 준 topic과 관련된 scientific paper 후보를 찾고, `papers/`에 없는 논문 중 공개 PDF를 직접 받을 수 있는 것만 다운로드한다. PDF 접근이 불가능하거나 paywall, login, request-only 상태인 논문은 건너뛴다.

## 언어 규칙
- 기본 출력은 한국어로 작성한다.
- `PDF`, `DOI`, `arXiv`, `PubMed`, `bioRxiv`, `medRxiv`, `preprint`, `review`, `benchmark`, `dataset`처럼 분야에서 자연스러운 용어는 영어를 유지한다.
- paper title, venue, author name은 원문 표기를 유지한다.

## 입력
- 필수: topic 또는 query.
- 선택: 원하는 개수, 기간, paper type, venue, organism/system, method keyword, 제외할 keyword.
- topic이 너무 넓거나 모호하면 검색 전에 한 번만 짧게 확인한다.

## 검색 소스 우선순위
1. arXiv, bioRxiv, medRxiv, PubMed/PMC처럼 공개 PDF 링크가 안정적인 source.
2. publisher page 또는 author/institution page의 direct PDF.
3. Semantic Scholar, Google Scholar 등 metadata discovery source.

## 중복 판단
다운로드 전에 `papers/`를 확인한다.

- title이 거의 같으면 중복으로 본다.
- DOI, arXiv ID, PubMed ID, PMC ID가 같으면 중복으로 본다.
- filename이 title slug 또는 identifier와 명확히 대응하면 중복으로 본다.
- 중복이 애매하면 다운로드하지 말고 최종 요약에 `needs review`로 표시한다.

## 다운로드 규칙
- 공개적으로 접근 가능한 PDF URL만 다운로드한다.
- HTML page, abstract page, supplementary file을 PDF로 오인하지 않는다.
- 다운로드 후 파일이 실제 PDF인지 확인한다. 최소한 file type 또는 `%PDF` header를 확인한다.
- PDF filename은 가능한 한 `year_first-author_short-title.pdf` 형식으로 저장한다.
  - 예: `2023_Li_multi-omic-single-cell-velocity.pdf`
  - identifier가 더 안정적이면 `arxiv_2301.01234_short-title.pdf`처럼 저장해도 된다.
- 같은 filename이 있으면 덮어쓰지 말고 suffix를 붙인다.
- 접근 불가, 인증 필요, rate limit, PDF URL 없음, broken link는 건너뛴다.

## 작업 절차
1. topic을 확정한다.
2. `papers/`의 기존 PDF 목록을 확인한다.
3. topic 관련 paper 후보를 검색한다.
4. 후보별 metadata를 정리한다:
   - title
   - authors
   - year
   - venue/source
   - DOI/arXiv/PubMed/PMC 등 identifier
   - PDF URL
   - relevance reason
5. 기존 `papers/`와 중복되는 후보를 제외한다.
6. 남은 후보 중 direct PDF가 가능한 항목만 다운로드한다.
7. 다운로드한 파일이 PDF인지 검증한다.
8. 최종 결과를 다운로드 성공, 이미 존재, 건너뜀, 확인 필요로 나누어 보고한다.

## 출력 형식
사용자가 다른 형식을 요청하지 않으면 아래 구조를 따른다.

```markdown
수집 결과:
- topic:
- 검색한 후보 수:
- 다운로드 성공:
- 이미 존재:
- 건너뜀:
- 확인 필요:

다운로드한 PDF:
| file | title | year | source |
| --- | --- | --- | --- |

이미 존재해서 건너뛴 논문:
| existing file | title | reason |
| --- | --- | --- |

PDF를 받을 수 없어 건너뛴 논문:
| title | year | reason |
| --- | --- | --- |

확인 필요:
| title | reason |
| --- | --- |
```

## 금지 사항
- PDF를 받을 수 없는 논문을 억지로 우회하지 않는다.
- paywall, institutional login, request access가 필요한 파일을 다운로드하려고 시도하지 않는다.
- `papers/`의 기존 파일을 삭제하거나 덮어쓰지 않는다.
- 사용자가 명시적으로 요청하지 않으면 paper analysis나 slide deck 생성을 시작하지 않는다.
