# VRM Specification (한국어 번역 프로젝트)

본 저장소 및 문서는 공식 [vrm-c/vrm-specification](https://github.com/vrm-c/vrm-specification) 문서를 바탕으로 **개인 학습 목적으로 작성된 비공식 한국어 번역본**입니다.

본 문서는 [vrm-specification](https://github.com/vrm-c/vrm-specification)의 [3942748](https://github.com/vrm-c/vrm-specification/commit/3942748efbc803b258e288e0f6c993c6bb96cebf) 커밋 시점의 사양을 기준으로 작성되었습니다.

---

> [!NOTE]
> **안내 및 면책 조항 (Disclaimer)**
>
> - 본 프로젝트는 원작자(VRM Consortium)의 공식 승인을 받지 않은 **비공식 번역 저장소**입니다.
> - **본 문서 저장소의 기본 파일(`*.md`)은 한국어 번역본입니다.** 영어(`*.en.md`) 및 일본어(`*.ja.md`) 문서는 원본 저장소의 내용을 그대로 유지하고 있으므로, 한국어 번역의 오역이나 불명확한 부분이 있을 경우 원본 문서와 비교하여 참고하실 수 있습니다.
> - 본 문서는 개인 학습 목적 및 한국어 사용자들의 VRM 사양 이해를 돕기 위해 작성되었습니다.
> - 모든 원문 사양의 저작권은 [VRM Consortium (vrm-c)](https://github.com/vrm-c) 및 해당 원작자에게 있습니다.
> - 번역의 정확성을 보장하지 않으며, 정확한 사양 및 최신 정보는 반드시 [공식 vrm-specification 저장소](https://github.com/vrm-c/vrm-specification)를 참고하시기 바랍니다.

---

## 🧭 번역 검수 로드맵 (Translation Review Roadmap)

전체 사양 문서의 번역 검수 진행 상황 체크리스트입니다.

### Part 1. 코어 메인 사양 (`VRMC_vrm-1.0`)

VRM 아바타의 골격, 메타데이터, 표정, 시선 등 핵심 메인 7개 문서

- [x] [VRMC_vrm-1.0 메인 사양](docs/specification/VRMC_vrm-1.0/README.md) — 개요, glTF 2.0 관계, 실행 순서
- [x] [휴머노이드 본 사양 (humanoid)](docs/specification/VRMC_vrm-1.0/humanoid.md) — 인체 뼈대 매핑 및 계층 구조
- [ ] [VRM T-Pose 사양 (tpose)](docs/specification/VRMC_vrm-1.0/tpose.md) — 표준 T-Pose 정의 및 회전값 규칙
- [ ] [모델 정보 및 라이선스 (meta)](docs/specification/VRMC_vrm-1.0/meta.md) — 저작자, 상업적 이용 및 성인용 허용 여부
- [ ] [표정 사양 (expressions)](docs/specification/VRMC_vrm-1.0/expressions.md) — 표정, 립싱크, 눈 깜빡임 제어
- [ ] [시선 제어 사양 (lookAt)](docs/specification/VRMC_vrm-1.0/lookAt.md) — 시선 제어 알고리즘 및 범위 맵
- [ ] [1인칭 설정 (firstPerson)](docs/specification/VRMC_vrm-1.0/firstPerson.md) — 1인칭 시점 및 머리 메시 처리

### Part 2. 재질 및 렌더링 사양

MToon 툰 셰이더 및 발광 재질 3개 문서

- [ ] [MToon 1.0 툰 셰이더 사양](docs/specification/VRMC_materials_mtoon-1.0/README.md) — MToon 1.0 공식 프로퍼티
- [ ] [MToon 버전 간 변경사항 비교](docs/specification/VRMC_materials_mtoon-1.0/MToon_comparision.md) — 0.x 대 1.0 파라미터 비교
- [ ] [HDR 발광 배율 확장 사양](docs/specification/VRMC_materials_hdr_emissiveMultiplier-1.0/README.md) — HDR 발광 배율

### Part 3. 동적 물리 & 보조본 제약조건

물리 흔들림 및 보조 뼈대 제어 3개 문서

- [ ] [SpringBone 1.0 물리 사양](docs/specification/VRMC_springBone-1.0/README.md) — SpringBone 물리 체인 알고리즘 및 콜라이더
- [ ] [SpringBone 확장 콜라이더 사양](docs/specification/VRMC_springBone_extended_collider-1.0/README.md) — 확장 콜라이더(구/평면)
- [ ] [노드 제약조건 사양 (node_constraint)](docs/specification/VRMC_node_constraint-1.0/README.md) — Roll / Aim / Rotation Constraint

### Part 4. 애니메이션 규격 (`VRMC_vrm_animation-1.0`)

VRM 포즈 및 모션 데이터 파일(VRMA) 2개 문서

- [ ] [VRM Animation 1.0 사양](docs/specification/VRMC_vrm_animation-1.0/README.md) — VRMA 확장 규격
- [ ] [휴머노이드 포즈 상호 변환 가이드](docs/specification/VRMC_vrm_animation-1.0/how_to_transform_human_pose.md) — 아바타 간 포즈 변환

### Part 5. 구현 검증용 샘플 문서 (`samples`)

Sample 동작 설명 6개 문서

- [ ] [샘플 모음 개요](docs/samples/README.md)
- [ ] [Seed-san 샘플 아바타](docs/samples/Seed-san/README.md)
- [ ] [Constraint Twist 테스트 샘플](docs/samples/VRM1_Constraint_Twist_Sample/README.md)
- [ ] [MToon UV 애니메이션 테스트 샘플](docs/samples/VRMC_materials_mtoon_UV_Animation_Test/README.md)
- [ ] [Expression isBinary Overrides 테스트 샘플](docs/samples/VRMC_vrm_expressions_isBinary_Overrides/README.md)
- [ ] [Expression isBinary Overridden 테스트 샘플](docs/samples/VRMC_vrm_expressions_isBinary_Overridden/README.md)

### Part 6. 구버전 레거시 사양

- [ ] [VRM 0.0 레거시 사양](docs/specification/0.0/README.md) — 레거시 0.x 사양 (구버전 호환용)

---

## 🤝 기여 (Contribution)

오역 제보, 용어 개선, 문서 보완 등의 **Pull Request(PR)** 및 **Issue** 등록을 언제나 환영합니다!  
더 나은 번역을 함께 만들어가고 싶으시다면 편하게 의견이나 PR을 남겨주세요.
